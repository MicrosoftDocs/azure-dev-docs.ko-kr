---
title: 자습서 - Terraform 및 Azure를 사용한 호환성 테스트
description: BDD(동작 기반 개발) 스타일 호환성 테스트를 Terraform 구성에 적용하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 07/31/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 7abb4072d923d4d5ec4fa3df6251f07576dba3bc
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88241315"
---
# <a name="tutorial-compliance-testing-with-terraform-and-azure"></a>자습서: Terraform 및 Azure를 사용한 호환성 테스트

[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

이 문서에서는 다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * 호환성 테스트를 사용하는 경우에 대한 이해
> * 호환성을 검사하는 방법 알아보기

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Terraform 설치**: 사용자 환경에 따라 [Terraform을 다운로드하여 설치](https://www.terraform.io/downloads.html)합니다.
- **Docker**: [Docker 설치](https://docs.docker.com/get-docker/)
- **terraform-compliance**: [Terraform 규정 준수 도구를 설치합니다](https://terraform-compliance.com/pages/installation/docker).
- **테스트 샘플 포크**: [GitHub에서 Terraform 샘플 프로젝트](https://github.com/Azure/terraform)를 포크하고 개발/테스트 컴퓨터에 복제합니다.

## <a name="what-is-compliance-testing"></a>호환성 테스트 정의

호환성 테스트는 시스템에서 규정된 표준을 충족하는지 여부를 확인하는 비기능 테스트 기술입니다. 호환성 테스트는 *적합성 테스트*라고도 합니다.

대부분의 소프트웨어 팀에서는 표준이 적절하게 적용되고 구현되는지 확인하기 위해 분석을 수행합니다. 표준을 개선하기 위해 동시에 작업하는 경우가 많으며, 이에 따라 품질이 향상됩니다.

호환성 테스트는 각 개발 수명 주기 단계의 출력이 합의된 요구 사항을 준수하는지 확인합니다.

호환성 검사는 프로젝트의 시작 부분에 있는 개발 주기에 통합되어야 합니다. 요구 사항 자체가 적절하게 문서화되지 않으면 이후 단계에서 호환성 검사를 추가하는 것이 점점 더 어려워집니다.

## <a name="understanding-compliance-checks"></a>호환성 검사 이해

호환성 검사를 수행하는 것은 간단합니다. 개발 수명 주기의 각 단계에 대한 표준 및 절차 세트가 개발되고 문서화됩니다. 각 단계의 출력은 문서화된 요구 사항과 비교됩니다. 테스트 결과는 미리 결정된 표준을 준수하지 않는 "차이"입니다. 호환성 테스트는 검사 프로세스를 통해 수행되며, 검토 프로세스의 결과가 문서화됩니다.

특정 예를 살펴보겠습니다.

일반적인 문제는 여러 개발자가 호환되지 않는 변경 내용을 적용하는 경우 중단되는 환경입니다. 한 사용자가 변경 작업을 수행하고 테스트 환경에서 VM을 만드는 것과 같은 리소스를 적용한다고 가정해 보겠습니다. 그런 다음, 다른 사용자가 해당 VM의 다른 버전을 프로비저닝하는 다른 버전의 코드를 적용합니다. 여기에 필요한 것은 규정된 규칙을 따르도록 보장하기 위한 감독입니다.

이 문제를 해결하는 한 가지 방법은 리소스에 태그(예: `role` 및 `creator` 태그)를 지정하는 정책을 정의하는 것입니다. 정책이 정의되면 [Terraform-compliance](https://terraform-compliance.com)와 같은 도구를 사용하여 정책을 준수하도록 합니다.

Terraform-compliance는 *부정적 테스트*에 중점을 둡니다. 부정적 테스트는 시스템에서 예기치 않은 입력 또는 원치 않는 동작을 정상적으로 처리할 수 있는지 확인하는 프로세스입니다. *퍼지*는 부정적 테스트의 한 예입니다. 퍼지를 사용하면 입력을 받는 시스템을 테스트하여 예기치 않은 입력을 안전하게 처리할 수 있는지 확인합니다.

다행히 Terraform은 클라우드 인프라 엔터티를 만들거나, 업데이트하거나, 제거하는 모든 API에 대한 추상화 계층입니다. 또한 Terraform은 로컬 구성과 원격 API 응답이 동기화되도록 합니다. Terraform은 대부분 클라우드 API에 사용되므로 인프라에 대해 배포되는 코드에서 특정 정책을 따르도록 하는 방법이 여전히 필요합니다. 무료 오픈 소스 도구인 Terraform-compliance는 이 기능을 Terraform 구성에 제공합니다.

VM 예제를 사용하는 경우 준수 정책은 *"Azure 리소스를 만드는 경우 태그를 포함해야 합니다."* 와 같을 수 있습니다.

Terraform-compliance 도구는 예제와 같은 정책을 만들 수 있는 테스트 프레임워크를 제공합니다. 그런 다음, Terraform 실행 계획에 대해 해당 정책을 실행합니다.

Terraform-compliance를 사용하면 BDD 또는 *동작 기반 개발* 원칙을 적용할 수 있습니다. BDD는 모든 관련자가 협력하여 시스템이 수행해야 하는 작업을 정의하는 협업 프로세스입니다. 이러한 관련자에는 일반적으로 개발자, 테스터 및 개발 중인 시스템과 관련하여 소유권이 있거나 영향을 받는 사용자가 포함됩니다. BDD의 목표는 팀에서 시스템의 작동 방식에 대한 일반적인 이해를 나타내는 구체적인 예제를 빌드하도록 장려하는 것입니다.

## <a name="looking-at-an-example"></a>예제 살펴보기

이 문서의 앞부분에서 테스트 환경에 사용할 VM을 만드는 호환성 테스트 예제에 대해 살펴보았습니다. 이 섹션에서는 해당 예제를 BDD 기능 및 시나리오로 변환하는 방법을 보여 줍니다. 규칙은 먼저 BDD를 지원하는 데 사용되는 도구인 *Cucumber*를 사용하여 표현됩니다.

```Cucumber
when creating Azure resources, every new resource should have a tag
```

이전 규칙은 다음과 같이 변환됩니다.

```Cucumber
If the resource supports tags
Then it must contain a tag
And its value must not be null
```

그러면 Terraform HCL 코드에서 다음과 같은 규칙을 준수합니다.

```hcl
resource "random_uuid" "uuid" {}

resource "azurerm_resource_group" "rg" {
  name     = "rg-hello-tf-${random_uuid.uuid.result}"
  location = var.location

  tags = {
    environment = "dev"
    application = "Azure Compliance"
  } 
}
```

첫 번째 정책은 다음과 같이 [BDD 기능 시나리오](https://cucumber.io/docs/gherkin/reference/)로 작성될 수 있습니다.

```Cucumber
Feature: Test tagging compliance  # /target/src/features/tagging.feature
    Scenario: Ensure all resources have tags
        If the resource supports tags
        Then it must contain a tag
        And its value must not be null
```

다음 코드에서는 특정 태그에 대한 테스트를 보여 줍니다.

```Cucumber
Scenario Outline: Ensure that specific tags are defined
    If the resource supports tags
    Then it must contain a tag <tags>
    And its value must match the "<value>" regex

    Examples:
      | tags        | value              |
      | Creator     | .+                 |
      | Application | .+                 |
      | Role        | .+                 |
      | Environment | ^(prod\|uat\|dev)$ |
```

## <a name="running-the-sample"></a>샘플 실행

이 섹션에서는 예제를 다운로드하고 테스트합니다.

1. [호환성 테스트 샘플을 다운로드](https://github.com/Azure/terraform/tree/master/samples/compliance-testing)합니다.

1. 디렉터리를 `src` 디렉터리로 변경합니다.

1. [terraform init](https://www.terraform.io/docs/commands/init.html)를 실행하여 작업 디렉터리를 초기화합니다. 이 단계에서는 Azure 리소스 그룹을 만드는 데 필요한 Azure 모듈을 다운로드합니다.

    ```bash
    terraform init
    ```
    
1. [terraform validate](https://www.terraform.io/docs/commands/validate.html)를 실행하여 구성 파일의 구문에 대한 유효성을 검사합니다.

    ```bash
    terraform validate
    ```
    
1. [terraform plan](https://www.terraform.io/docs/commands/plan.html)을 실행하여 실행 계획을 만듭니다.

    ```bash
    terraform plan -out tf.out
    ```
    
1. [terraform apply](https://www.terraform.io/docs/commands/apply.html)를 실행하여 실행 계획을 적용합니다.

    ```bash
    terraform apply -target=random_uuid.uuid
    ```
    
1. [docker pull](https://docs.docker.com/engine/reference/commandline/pull/)을 실행하여 terraform-compliance 이미지를 다운로드합니다.

    ```bash
    docker pull eerkunt/terraform-compliance
    ```
    
1. [docker run](https://docs.docker.com/engine/reference/commandline/run/)을 실행하여 Docker 컨테이너에서 테스트를 실행합니다. **테스트가 실패**합니다. 태그의 존재를 요구하는 첫 번째 규칙은 성공합니다. 그러나 `Role` 및 `Creator` 태그가 없으므로 두 번째 규칙은 실패합니다.

    ```bash
    docker run --rm -v $PWD:/target -it eerkunt/terraform-compliance -f features -p tf.out
    ```
    
    ![실패한 테스트의 예](media/best-practices-compliance-testing/best-practices-compliance-testing-tagging-fail.png)

1. 오류를 수정하려면 `main.tf`를 다음과 같이 수정합니다.

    ```terraform
      tags = {
        Environment = "dev"
        Application = "Azure Compliance"
        Creator     = "Azure Compliance"
        Role        = "Azure Compliance"
      } 
    
    ```
    
1. `terraform validate`를 다시 실행하여 구문을 확인합니다.

    ```bash
    terraform validate
    ```
    
1. `terraform plan`을 다시 실행하여 새 실행 계획을 만듭니다.

    ```bash
    terraform plan -out tf.out
    ```
    
1. [docker run](https://docs.docker.com/engine/reference/commandline/run/)을 다시 실행하여 구성을 테스트합니다. 이번에는 전체 사양이 구현되었으므로 테스트가 성공합니다.

    ```bash
    docker run --rm -v $PWD:/target -it eerkunt/terraform-compliance -f features -p tf.out
    ```

    ![성공한 테스트 예제](media/best-practices-compliance-testing/best-practices-compliance-testing-tagging-succeed.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Terraform 프로젝트에서 엔드투엔드 테스트 만들기 및 실행](best-practices-end-to-end-testing.md)
