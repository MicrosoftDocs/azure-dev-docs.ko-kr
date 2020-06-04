---
title: 자습서 - Azure Terraform Visual Studio Code 확장 구성
description: Visual Studio Code에서 Azure Terraform 확장을 설치하고 사용하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 9fd6d6b93537795109117198aa20063b556b8368
ms.sourcegitcommit: db56786f046a3bde1bd9b0169b4f62f0c1970899
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329571"
---
# <a name="tutorial-configure-the-azure-terraform-visual-studio-code-extension"></a>자습서: Azure Terraform Visual Studio Code 확장 구성

Azure Terraform Visual Studio Code 확장을 사용하면 편집기에서 Terraform으로 작업할 수 있습니다. 이 확장을 통해 Terraform 구성을 작성, 테스트 및 실행할 수 있습니다. 이 확장은 리소스 그래프 시각화도 지원합니다.

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
> * Terraform을 사용하여 Azure 서비스 프로비저닝 자동화
> * Azure 서비스용 Terraform Visual Studio Code 확장을 설치하고 사용합니다.
> * Visual Studio Code를 사용하여 Terraform 계획을 작성, 계획 및 실행합니다.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Terraform**: [Terraform을 설치 및 구성합니다](getting-started-cloud-shell.md).
- **Visual Studio Code**: 환경에 적합한 [Visual Studio Code](https://code.visualstudio.com/download) 버전을 설치합니다.

## <a name="prepare-your-dev-environment"></a>개발 환경 준비

### <a name="install-git"></a>Git 설치

이 문서의 연습을 완료하려면 [Git을 설치](https://git-scm.com/)해야 합니다.

### <a name="install-hashicorp-terraform"></a>HashiCorp Terraform 설치

다음 내용을 포함하는 HashiCorp [Terraform 설치](https://www.terraform.io/intro/getting-started/install.html) 웹 페이지의 지침을 따릅니다.

- Terraform 다운로드
- Terraform 설치
- Terraform이 제대로 설치되어 있는지 확인

>[!Tip]
>PATH 시스템 변수 설정에 대한 지침을 따릅니다.

### <a name="install-nodejs"></a>Node.js 설치

Cloud Shell에서 Terraform을 사용하려면 [Node.js 6.0 이상을 설치](https://nodejs.org/)해야 합니다.

>[!NOTE]
>Node.js가 설치되어 있는지 확인하려면 터미널 창을 열고 `node -v`를 입력합니다.

### <a name="install-graphviz"></a>GraphViz 설치

Terraform 시각화 함수를 사용하려면 [GraphViz를 설치](https://graphviz.org/)해야 합니다.

>[!NOTE]
>GraphViz가 설치되어 있는지 확인하려면 터미널 창을 열고 `dot -V`를 입력합니다.

### <a name="install-the-azure-terraform-visual-studio-code-extension"></a>Azure Terraform Visual Studio Code 확장 설치

1. Visual Studio Code를 시작합니다.

1. **확장**을 섡택합니다.

    ![확장 단추](media/configure-vs-code-extension-for-terraform/tf-vscode-extensions-button.png)

1. **Search Extensions in Marketplace**(Marketplace에서 확장 검색) 텍스트 상자를 사용하여 Azure Terraform 확장을 검색합니다.

    ![Marketplace에서 Visual Studio Code 확장 검색](media/configure-vs-code-extension-for-terraform/tf-search-extensions.png)

1. **설치**를 선택합니다.

    >[!NOTE]
    >Azure Terraform 확장에 대해 **설치**를 선택하면 Visual Studio Code에서 해당 Azure 계정 확장을 자동으로 설치합니다. Azure 계정은 Azure 구독 인증 및 Azure 관련 코드 확장을 수행하기 위해 Azure Terraform 확장이 사용하는 종속성 파일입니다.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Visual Studio Code에서 Terraform 확장이 설치되었는지 확인

1. **확장**을 섡택합니다.

1. 검색 텍스트 상자에 `@installed`를 입력합니다.

    ![설치된 확장](media/configure-vs-code-extension-for-terraform/tf-installed-extensions.png)

Azure Terraform 확장이 설치된 확장 목록에 표시됩니다.

![설치된 Terraform 확장](media/configure-vs-code-extension-for-terraform/tf-installed-terraform-extension-button.png)

이제 Visual Studio Code 내에서 Cloud Shell 환경의 지원되는 모든 Terraform 명령을 실행할 수 있습니다.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>연습 1: 기본 Terraform 명령 연습

이 연습에서는 새 Azure 리소스 그룹을 프로비전하는 기본 Terraform 구성 파일을 만들고 실행합니다.

### <a name="prepare-a-test-plan-file"></a>테스트 계획 파일 준비

1. Visual Studio Code의 메뉴 모음에서 **파일 > 새 파일**을 선택합니다.

1. 브라우저에서 [Terraform azurerm_resource_group page](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#)로 이동하여 **Example Usage** 코드 블록의 코드를 복사합니다.

    ![Example Usage](media/configure-vs-code-extension-for-terraform/tf-azurerm-resource-group-example-usage.png)

1. 복사한 코드를 Visual Studio Code에서 만든 새 파일에 붙여넣습니다.

    ![Example Usage 코드 붙여넣기](media/configure-vs-code-extension-for-terraform/tf-paste-example-usage-code.png)

    >[!NOTE]
    >리소스 그룹의 **name** 값을 변경할 수 있지만 Azure 구독에 고유해야 합니다.

1. 메뉴 모음에서 **파일 > 다른 이름으로 저장**을 선택합니다.

1. **다른 이름으로 저장** 대화 상자에서 원하는 위치로 이동한 다음, **새 폴더**를 선택합니다. (새 폴더의 이름을 *새 폴더*보다 좀 더 자세한 설명을 포함하는 이름으로 변경합니다.)

    >[!NOTE]
    >이 예제에서는 폴더 이름이 TERRAFORM-TEST-PLAN으로 지정되었습니다.

1. 새 폴더가 강조 표시(선택)되어 있는지 확인한 다음, **열기**를 선택합니다.

1. **다른 이름으로 저장** 대화 상자에서 파일의 기본 이름을 *main.tf*로 변경합니다.

    ![main.tf로 저장](media/configure-vs-code-extension-for-terraform/tf-save-as-main.png)

1. **저장**을 선택합니다.
1. 메뉴 모음에서 **파일 > 폴더 열기**를 선택합니다. 만든 새 폴더로 이동한 후 선택합니다.

### <a name="run-terraform-init-command"></a>Terraform *init* 명령 실행

1. Visual Studio Code를 시작합니다.

1. Visual Studio Code 메뉴 모음에서 **파일 > 폴더 열기...** 를 선택하고 *main.tf* 파일을 찾아 선택합니다.

    ![main.tf 파일](media/configure-vs-code-extension-for-terraform/tf-main-tf.png)

1. 메뉴 모음에서 **보기 > 명령 팔레트... > Azure Terraform: Init**를 선택합니다.

1. 확인이 표시되면 **확인**을 선택합니다.

    ![Do you want to open Cloud Shell?(Cloud Shell을 열까요?)](media/configure-vs-code-extension-for-terraform/tf-do-you-want-to-open-cloud-shell.png)

1. 새 폴더에서 Cloud Shell을 처음 시작하면 웹 애플리케이션을 만들라는 메시지가 표시됩니다. **열기**를 선택합니다.

    ![Cloud Shell 처음 시작](media/configure-vs-code-extension-for-terraform/tf-first-launch-of-cloud-shell.png)

1. Azure Cloud Shell 시작 페이지가 열립니다. Bash 또는 PowerShell을 선택합니다.

    ![Azure Cloud Shell 시작](media/configure-vs-code-extension-for-terraform/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >이 예제에서는 Bash(Linux)를 선택했습니다.

1. 아직 Azure Storage 계정을 설정하지 않은 경우 다음 화면이 나타납니다. **스토리지 만들기**를 선택합니다.

    ![탑재된 스토리지가 없음](media/configure-vs-code-extension-for-terraform/tf-you-have-no-storage-mounted.png)

1. Azure Cloud Shell은 사용자가 이전에 선택한 셸에서 시작되며, 방금 만든 클라우드 드라이브에 대한 정보를 표시합니다.

    ![클라우드 드라이브가 만들어졌습니다.](media/configure-vs-code-extension-for-terraform/tf-your-cloud-drive-has-been-created-in.png)

1. 이제 Cloud Shell를 종료할 수 있습니다.

1. 메뉴 모음에서 **보기** > **명령 팔레트** > **Azure Terraform: init**를 선택합니다.

    ![Terraform이 초기화되었습니다.](media/configure-vs-code-extension-for-terraform/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>계획 시각화

이 자습서의 앞부분에서 GraphViz를 설치했습니다. Terraform은 GraphViz를 사용하여 구성 또는 실행 계획의 시각적 표현을 생성할 수 있습니다. Azure Terraform Visual Studio Code 확장은 *visualize* 명령을 통해 이 기능을 구현합니다.

- 메뉴 모음에서 **보기 > 명령 팔레트 > Azure Terraform: 시각화**를 선택합니다.

    ![계획 시각화](media/configure-vs-code-extension-for-terraform/tf-graph.png)

### <a name="run-terraform-plan-command"></a>Terraform *plan* 명령 실행

Terraform *plan* 명령은 변경 집합에 대한 실행 계획이 사용자의 의도대로 진행될지 여부를 확인하는 데 사용됩니다.

>[!NOTE]
>Terraform *plan*은 실제 Azure 리소스를 변경하지 않습니다. 계획에 포함된 변경을 실제로 수행하려면 Terraform *apply* 명령을 사용합니다.

- 메뉴 모음에서 **보기** > **명령 팔레트** > **Azure Terraform: plan**을 선택합니다.

    ![Terraform 계획](media/configure-vs-code-extension-for-terraform/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>Terraform *apply* 명령 실행

Terraform *plan*의 결과가 만족스러우면 *apply* 명령을 실행할 수 있습니다.

1. 메뉴 모음에서 **보기** > **명령 팔레트** > **Azure Terraform: apply**를 선택합니다.

    ![Terraform Apply](media/configure-vs-code-extension-for-terraform/tf-terraform-apply.png)

1. `yes`를 입력합니다.

    ![Terraform apply yes](media/configure-vs-code-extension-for-terraform/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Terraform plan이 실행되었는지 확인

새 Azure 리소스 그룹이 성공적으로 생성되었는지 확인하려면

1. Azure Portal을 엽니다.

1. 왼쪽 탐색 창에서 **리소스 그룹**을 선택합니다.

    ![새 리소스 확인](media/configure-vs-code-extension-for-terraform/tf-verify-resource-group-created.png)

새 리소스 그룹이 **NAME** 열에 나열되어야 합니다.

>[!NOTE]
>다음 단계에서 사용되므로 지금은 Azure Portal 창을 열어둡니다.

### <a name="run-terraform-destroy-command"></a>Terraform *destroy* 명령 실행

1. 메뉴 모음에서 **보기** > **명령 팔레트** > **Azure Terraform: destroy**를 선택합니다.

    ![Terraform destroy](media/configure-vs-code-extension-for-terraform/tf-terraform-destroy.png)

1. *예*를 입력합니다.

    ![Terraform destroy yes](media/configure-vs-code-extension-for-terraform/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>리소스 그룹이 제거되었는지 확인

Terraform이 새 리소스 그룹을 성공적으로 제거했는지 확인하려면

1. Azure Portal **리소스 그룹** 페이지에서 **새로 고침**을 선택합니다.

1. 리소스 그룹이 더 이상 나열되지 않습니다.

    ![리소스 그룹이 제거되었는지 확인](media/configure-vs-code-extension-for-terraform/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>연습 2: Terraform *컴퓨팅* 모듈

이 연습에서는 Terraform *컴퓨팅* 모듈을 Visual Studio Code 환경에 로드하는 방법을 알아봅니다.

### <a name="clone-the-terraform-azurerm-compute-module"></a>terraform-azurerm-compute 모듈 복제

1. [이 링크](https://github.com/Azure/terraform-azurerm-compute)를 사용하여 GitHub의 Terraform Azure Rm Compute 모듈에 액세스합니다.

1. **복제 또는 다운로드**를 선택합니다.

    ![복제 또는 다운로드](media/configure-vs-code-extension-for-terraform/tf-clone-with-https.png)

    >[!NOTE]
    >이 예제에서는 폴더 이름이 *terraform-azurerm-compute*입니다.

### <a name="open-the-folder-in-visual-studio-code"></a>Visual Studio Code에서 폴더 열기

1. Visual Studio Code를 시작합니다.

1. 메뉴 모음에서 **파일 > 폴더 열기**를 선택하고 이전 단계에서 만든 폴더로 이동하여 선택합니다.

    ![terraform-azurerm-compute 폴더](media/configure-vs-code-extension-for-terraform/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Terraform 초기화

Visual Studio Code 내에서 Terraform 명령을 사용하려면 먼저 두 Azure 공급자인 random 및 azurerm에 대한 플러그 인을 다운로드합니다.

1. Visual Studio Code IDE의 터미널 창에서 `terraform init`를 입력합니다.

    ![terraform init 명령](media/configure-vs-code-extension-for-terraform/tf-terraform-init-command.png)

1. `az login`을 입력하고, **<Enter**를 누르고, 화면 지침을 따릅니다.

### <a name="module-test-lint"></a>모듈 테스트: *lint*

1. 메뉴 모음에서 **보기 > 명령 팔레트 > Azure Terraform: 테스트 실행**을 선택합니다.

1. 테스트 형식 옵션 목록에서 **lint**를 선택합니다.

    ![테스트 유형 선택](media/configure-vs-code-extension-for-terraform/tf-select-type-of-test-lint.png)

1. 확인이 표시되면 **확인**을 선택하고, 화면 지침을 따릅니다.

    ![Do you want to open Cloud Shell?(Cloud Shell을 열까요?)](media/configure-vs-code-extension-for-terraform/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>**lint** 또는 **end to end** 테스트를 실행할 경우 Azure는 컨테이너 서비스를 사용하여 실제 테스트를 수행하기 위해 테스트 컴퓨터를 프로비전합니다. 따라서 일반적으로 테스트 결과가 반환되는 데 몇 분 정도 걸릴 수 있습니다.

몇 분 후 다음 예제와 비슷한 터미널 창에 목록이 표시됩니다.

![Lint 테스트 결과](media/configure-vs-code-extension-for-terraform/tf-lint-test-results.png)

### <a name="test-the-module"></a>모듈 테스트

1. 메뉴 모음에서 **보기 > 명령 팔레트 > Azure Terraform: 테스트 실행**을 선택합니다.

1. 테스트 형식 옵션 목록에서 **end to end**를 선택합니다.

    ![테스트 유형 선택](media/configure-vs-code-extension-for-terraform/tf-select-type-of-test-end-to-end.png)

1. 확인이 표시되면 **확인**을 선택하고, 화면 지침을 따릅니다.

    ![Do you want to open Cloud Shell?(Cloud Shell을 열까요?)](media/configure-vs-code-extension-for-terraform/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>**lint** 또는 **end to end** 테스트를 실행할 경우 Azure는 컨테이너 서비스를 사용하여 실제 테스트를 수행하기 위해 테스트 컴퓨터를 프로비전합니다. 따라서 일반적으로 테스트 결과가 반환되는 데 몇 분 정도 걸릴 수 있습니다.

몇 분 후 다음 예제와 비슷한 터미널 창에 목록이 표시됩니다.

![테스트 결과](media/configure-vs-code-extension-for-terraform/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure(및 기타 지원되는 공급자)에 대해 사용 가능한 Terraform 모듈의 목록](https://registry.terraform.io/)