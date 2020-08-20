---
title: 자습서 - Terraform 및 Azure를 사용한 통합 테스트
description: 통합 테스트 및 Azure DevOps를 사용하여 Terraform 프로젝트에 대한 연속 통합을 구성하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 07/31/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 3d305fb63deffb8f56ebd2cb1503bac543c5b84b
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88241305"
---
# <a name="tutorial-configure-integration-tests-for-terraform-projects-in-azure"></a>자습서: Azure에서 Terraform 프로젝트에 대한 통합 테스트 구성

[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

이 문서에서는 다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * Terraform 프로젝트에 대한 통합 테스트의 기본 사항에 대해 알아봅니다.
> * Azure DevOps를 사용하여 연속 통합 파이프라인을 구성합니다.
> * Terraform 코드에 대한 정적 코드 분석을 실행합니다.
> * `terraform validate`를 실행하여 로컬 컴퓨터에서 Terraform 구성 파일의 유효성을 검사합니다.
> * `terraform plan`을 실행하여 원격 서비스 관점에서 Terraform 구성 파일의 유효성을 검사합니다.
> * Azure Pipeline을 사용하여 연속 통합을 자동화합니다.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Azure DevOps 조직 및 프로젝트**: 조직이 없는 경우 [Azure DevOps 조직을 만듭니다](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops&tabs=preview-page).
- **Terraform Build & Release Tasks 확장**: [Terraform Build & Release Tasks 확장](https://marketplace.visualstudio.com/items?itemName=charleszipp.azure-pipelines-tasks-terraform)을 Azure DevOps 조직에 설치합니다.
- **Azure DevOps 액세스 권한을 Azure 구독에 부여**: Azure Pipelines에서 Azure 구독에 연결할 수 있도록 `terraform-basic-testing-azure-connection`이라는 [Azure 서비스 연결](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops)을 만듭니다.
- **Terraform 설치**: 사용자 환경에 따라 [Terraform을 다운로드하여 설치](https://www.terraform.io/downloads.html)합니다.
- **테스트 샘플 포크**: [GitHub에서 Terraform 샘플 프로젝트](https://github.com/Azure/terraform)를 포크하고 개발/테스트 컴퓨터에 복제합니다.

## <a name="validate-alocal-terraform-configuration"></a>로컬 Terraform 구성 유효성 검사

[terraform validate](https://www.terraform.io/docs/commands/validate.html) 명령은 Terraform 파일이 포함된 디렉터리의 명령줄에서 실행됩니다. 이 명령의 주요 목표는 구문 유효성 검사입니다.

1. 원하는 명령줄 환경을 엽니다. 많은 코드 편집기(예: Visual Studio Code)에서 명령줄 인터페이스를 제공합니다.

1. 디렉터리를 로컬 리포지토리의 `samples/integration-testing/src` 디렉터리로 변경합니다. 여기에는 간단한 Terraform 프로젝트가 포함되어 있습니다.

1. [terraform init](https://www.terraform.io/docs/commands/init.html)를 사용하여 Terraform 배포를 초기화합니다. 이 단계에서는 Azure 리소스 그룹을 만드는 데 필요한 Azure 모듈을 다운로드합니다.

    ```bash
    terraform init
    ```

1. [terraform validate](https://www.terraform.io/docs/commands/validate.html)를 사용하여 Terraform 테스트 파일의 유효성을 검사합니다.

    ```bash
    terraform validate
    ```

    구성이 유효함을 나타내는 메시지가 표시됩니다.

1. 코드 편집기에서 `main.tf` 파일을 엽니다.

1. 5번 줄에서 구문을 무효화하는 오타를 삽입합니다. 예를 들어 `var.location`을 `var.loaction`으로 바꿉니다.

1. 파일을 저장합니다.

1. 유효성 검사를 다시 실행합니다.

    ```bash
    terraform validate
    ```

    이번에는 잘못된 줄을 나타내는 오류 메시지 및 해당 오류에 대한 설명이 표시됩니다.

여기서 알 수 있듯이 Terraform은 구성 코드의 구문에서 문제를 검색했습니다. 이 문제로 인해 구성이 배포되지 않습니다.

버전 제어 시스템으로 푸시하기 전에 항상 Terraform 파일에 대해 `terraform validate`를 실행하는 것이 좋습니다. 또한 이 수준의 유효성 검사는 연속 통합 파이프라인의 일부가 되어야 합니다. [자동으로 유효성을 검사하도록 Azure 파이프라인을 구성](#automate-integration-tests-using-azure-pipeline)하는 방법은 이 자습서의 뒷부분에서 살펴보겠습니다.

## <a name="validate-terraform-configuration-can-be-deployed-on-azure"></a>Terraform 구성을 Azure에 배포할 수 있는지 확인

이전 섹션에서는 Terraform 구성의 유효성을 검사하는 방법을 살펴보았습니다. 이러한 테스트 수준은 구문에만 적용되었습니다. 이 테스트는 Azure에 이미 배포되었을 수 있는 항목을 고려하지 않았습니다.

Terraform은 원하는 항목을 최종 결과로 선언한다는 것을 의미하는 *선언적 언어*입니다. 예를 들어 리소스 그룹에 10개의 가상 머신이 있다고 가정해 보겠습니다. 그런 다음, 세 개의 가상 머신을 정의하는 Terraform 파일을 만듭니다. 이 계획을 적용해도 총 수는 13개로 증가하지 않습니다. 대신 Terraform에서 7개의 가상 머신을 삭제하여 3개로 끝납니다. `terraform plan`을 실행하면 실행 계획을 적용한 잠재적 결과를 확인하여 예기치 않은 결과를 방지할 수 있습니다.

Terraform 실행 계획을 생성하려면 [terraform plan](https://www.terraform.io/docs/commands/plan.html)을 실행합니다. 이 명령은 대상 Azure 구독에 연결하여 이미 배포된 구성 부분을 확인합니다. 그런 다음, Terraform에서 Terraform 파일에 명시된 요구 사항을 충족하는 데 필요한 변경 내용을 결정합니다. 이 단계에서는 Terraform에서 아무것도 배포하지 않습니다. 계획을 적용하는 경우 발생하는 상황을 알려주는 것입니다.

자습서를 따르고 이전 섹션의 단계를 완료한 경우 `terraform plan` 명령을 실행합니다.

```bash
terraform plan
```

`terraform plan`이 실행되면 Terraform에서 실행 계획을 적용한 잠재적 결과를 표시합니다. 출력은 추가, 변경 및 제거될 Azure 리소스를 나타냅니다.

기본적으로 Terraform은 상태를 Terraform 파일과 동일한 로컬 디렉터리에 저장합니다. 이 패턴은 단일 사용자 시나리오에서 잘 작동합니다. 그러나 여러 사용자가 동일한 Azure 리소스에서 작업하는 경우 로컬 상태 파일이 동기화되지 않을 수 있습니다. 이 문제를 해결하기 위해 Terraform에서는 상태 파일을 원격 데이터 저장소(예: Azure Storage)에 쓰도록 지원합니다. 이 시나리오에서는 로컬 컴퓨터에서 `terraform plan`을 실행하고 원격 컴퓨터를 대상으로 지정하는 것이 문제가 될 수 있습니다. 따라서 [연속 통합 파이프라인의 일부로 이 유효성 검사 단계를 자동화](#automate-integration-tests-using-azure-pipeline)하는 것이 적합할 수 있습니다.

## <a name="run-static-code-analysis"></a>정적 코드 분석 실행

정적 코드 분석은 실행하지 않고 Terraform 구성 코드에서 직접 수행할 수 있습니다. 이 분석은 보안 문제 및 규정 준수 불일치와 같은 문제를 검색하는 데 유용할 수 있습니다.

Terraform 파일에 대한 정적 분석을 제공하는 도구는 다음과 같습니다.

- [Checkov](https://github.com/bridgecrewio/checkov/)
- [Terrascan](https://github.com/cesar-rodriguez/terrascan)
- [tfsec](https://github.com/liamg/tfsec) 
- [Deepsource](https://deepsource.io/blog/release-terraform-static-analysis/) 

정적 분석이 연속 통합 파이프라인의 일부로 실행되는 경우가 많습니다. 이러한 테스트에서 실행 계획 또는 배포를 만들 필요가 없습니다. 결과적으로 다른 테스트보다 빠르게 실행되며 일반적으로 연속 통합 프로세스에서 먼저 실행됩니다.

## <a name="automate-integration-tests-using-azure-pipeline"></a>Azure Pipeline을 사용하여 통합 테스트 자동화

연속 통합에는 변경 내용이 도입될 때 전체 시스템을 테스트하는 작업이 포함됩니다. 이 섹션에서는 연속 통합을 구현하는 데 사용되는 Azure Pipeline 구성을 볼 수 있습니다.

1. 원하는 편집기를 사용하여 [GitHub에서 Terraform 샘플 프로젝트](https://github.com/Azure/terraform)의 로컬 복제본으로 이동합니다.

1. `samples/integration-testing/src/azure-pipeline.yaml` 파일을 엽니다.

1. **steps** 섹션까지 아래로 스크롤합니다. 여기서는 다양한 설치 및 유효성 검사 루틴을 실행하는 데 사용되는 표준 단계 집합을 볼 수 있습니다.

1. **Step 1: run the Checkov Static Code Analysis(1단계: Checkov 정적 코드 분석 실행)** 라는 줄을 검토합니다. 이 단계에서는 앞에서 언급한 `Checkov` 프로젝트에서 Terraform 구성 샘플에 대해 정적 코드 분석을 실행합니다. 

    ```yaml
    - bash: $(terraformWorkingDirectory)/checkov.sh $(terraformWorkingDirectory)
      displayName: Checkov Static Code Analysis
    ```
    
    참고:
    
    - 이 스크립트는 Docker 컨테이너 내에 탑재된 Terraform 작업 영역에서 Checkov를 실행합니다. Microsoft에서 관리하는 에이전트는 Docker를 사용하도록 설정되어 있습니다. Docker 컨테이너 내에서 도구를 실행하는 것이 더 쉽고, Checkov를 Azure Pipeline 에이전트에 설치할 필요가 없습니다.
    - `$(terraformWorkingDirectory)` 변수는 `azure-pipeline.yaml` 파일에 정의되어 있습니다.

1. **Step 2: install Terraform on the Azure Pipelines agent(2단계: Azure Pipelines 에이전트에 Terraform 설치)** 라는 줄을 검토합니다. 이전에 설치한 [Terraform Build & Release Task 확장](https://marketplace.visualstudio.com/items?itemName=charleszipp.azure-pipelines-tasks-terraform)에는 Azure Pipeline을 실행하는 에이전트에 Terraform을 설치하라는 명령이 있습니다. 이 작업은 이 단계에서 수행되는 작업입니다.

    ```yaml
    - task: charleszipp.azure-pipelines-tasks-terraform.azure-pipelines-tasks-terraform-installer.TerraformInstaller@0
      displayName: 'Install Terraform'
      inputs:
        terraformVersion: $(terraformVersion)
    ```
    
    참고:

    - 설치할 Terraform 버전은 `terraformVersion`이라는 Azure Pipeline 변수를 통해 지정되고 `azure-pipeline.yaml` 파일에 정의됩니다.

1. **Step 3: run Terraform init to initialize the workspace(3단계: terraform init를 실행하여 작업 영역 초기화)** 라는 줄을 검토합니다. 이제 Terraform이 에이전트에 설치되었으므로 Terraform 디렉터리를 초기화할 수 있습니다.

    ```yaml
    - task: charleszipp.azure-pipelines-tasks-terraform.azure-pipelines-tasks-terraform-cli.TerraformCLI@0
      displayName: 'Run terraform init'
      inputs:
        command: init
        workingDirectory: $(terraformWorkingDirectory)
    ```
    
    참고:

    - `command` 입력은 실행할 Terraform 명령을 지정합니다.
    - `workingDirectory` 입력은 Terraform 디렉터리의 경로를 나타냅니다.
    - `$(terraformWorkingDirectory)` 변수는 `azure-pipeline.yaml` 파일에 정의되어 있습니다.

1. **Step 4: run Terraform validate to validate HCL syntax(4단계: terraform validate를 실행하여 HCL 구문 유효성 검사)** 라는 줄을 검토합니다. 프로젝트 디렉터리가 초기화되면 `terraform validate`를 실행하여 서버 구성의 유효성을 검사합니다.

    ```yaml
    - task: charleszipp.azure-pipelines-tasks-terraform.azure-pipelines-tasks-terraform-cli.TerraformCLI@0
      displayName: 'Run terraform validate'
      inputs:
        command: validate
        workingDirectory: $(terraformWorkingDirectory)
    ```
    
1. **Step 5: run Terraform plan to validate HCL syntax(5단계: terraform plan을 실행하여 HCL 구문 유효성 검사)** 라는 줄을 검토합니다. 앞에서 설명한 대로 배포하기 전에 Terraform 구성이 유효한지 확인하기 위해 실행 계획을 생성합니다.

    ```yaml
    - task: charleszipp.azure-pipelines-tasks-terraform.azure-pipelines-tasks-terraform-cli.TerraformCLI@0
      displayName: 'Run terraform plan'
      inputs:
        command: plan
        workingDirectory: $(terraformWorkingDirectory)
        environmentServiceName: $(serviceConnection)
        commandOptions: -var location=$(azureLocation)
    ```
    
    참고:

    - `environmentServiceName` 입력은 [필수 조건](#prerequisites)에서 만든 Azure 서비스 연결의 이름을 나타냅니다. 이 연결을 통해 Terraform에서 Azure 구독에 액세스할 수 있습니다.
    - `commandOptions` 입력은 인수를 Terraform 명령에 전달하는 데 사용됩니다. 이 경우 위치를 지정합니다. `$(azureLocation)` 변수는 YAML 파일의 앞부분에 정의됩니다.

### <a name="import-the-pipeline-into-azure-devops"></a>Azure DevOps로 파이프라인 가져오기

1. Azure DevOps 프로젝트를 열고, Azure Pipelines 섹션으로 이동합니다.
 
1. **파이프라인 만들기** 단추를 선택합니다.

1. **코드는 어디에 있나요?** 옵션에서 **GitHub(YAML)** 를 선택합니다.

    ![코드는 어디에 있나요?](media/best-practices-integration-testing/new-pipeline-where-github-yaml.png)

1. 이 시점에서 조직에 대한 액세스 권한을 Azure DevOps에 부여해야 할 수 있습니다. 이 항목에 대한 자세한 내용은 [GitHub 리포지토리 빌드](/azure/devops/pipelines/repos/github?view=azure-devops&tabs=yaml) 문서를 참조하세요.

1. 리포지토리 목록에서 GitHub 조직에서 만든 리포지토리의 포크를 선택합니다.

1. **파이프라인 구성** 단계에서 기존 YAML 파이프라인에서 시작하도록 선택합니다.

    ![기존 YAML 파이프라인](media/best-practices-integration-testing/new-pipeline-existing-yaml.png)

1. **기존 YAML 파이프라인 선택** 페이지가 표시되면 `master` 분기를 지정하고, YAML 파이프라인의 경로(`samples/integration-testing/src/azure-pipeline.yaml`)를 입력합니다.

    ![기존 YAML 파이프라인 선택](media/best-practices-integration-testing/select-existing-yaml-pipeline.png)

1. **계속**을 선택하여 GitHub에서 Azure YAML 파이프라인을 로드합니다.

1. **파이프라인 YAML 검토** 페이지가 표시되면 **실행**을 선택하여 처음으로 파이프라인을 만들고 수동으로 트리거합니다.

    ![Azure Pipeline 실행](media/best-practices-integration-testing/run-pipeline.png)

### <a name="run-the-pipeline"></a>파이프라인 실행

Azure DevOps UI에서 파이프라인을 수동으로 실행할 수 있습니다. 그러나 이 문서의 요점은 자동화된 연속 통합을 보여주는 것입니다. 포크된 리포지토리의 `samples/integration-testing/src` 폴더에 대한 변경 내용을 커밋하여 프로세스를 테스트합니다. 이렇게 변경하면 코드를 푸시하는 분기에서 새 파이프라인이 자동으로 트리거됩니다.

![GitHub에서 실행되는 파이프라인](media/best-practices-integration-testing/pipeline-running-from-github.png)

이 단계가 완료되었으면 Azure DevOps의 세부 정보에 액세스하여 모든 항목이 올바르게 실행되었는지 확인합니다.

![Azure DevOps 파이프라인(녹색)](media/best-practices-integration-testing/azure-devops-green-pipeline.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Terraform 프로젝트에서 호환성 테스트 만들기 및 실행](best-practices-compliance-testing.md)