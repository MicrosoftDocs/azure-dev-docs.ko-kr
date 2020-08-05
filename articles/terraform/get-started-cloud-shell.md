---
title: 빠른 시작 - Azure Cloud Shell을 사용하여 Terraform 시작
description: 이 빠른 시작에서는 Terraform을 설치하고 구성하여 Azure 리소스를 만드는 방법을 알아봅니다.
keywords: azure devops terraform 설치 구성 cloud shell init 계획 적용 실행 포털 로그인 rbac 서비스 주체 자동화된 스크립트
ms.topic: quickstart
ms.date: 07/26/2020
ms.openlocfilehash: 381313edf386ac33dca137191c9eefef48f92931
ms.sourcegitcommit: 8cd0ddf1651c3b64bb72dedc2890108c2cfe3bcb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87335270"
---
# <a name="quickstart-get-started-with-terraform-using-azure-cloud-shell"></a>빠른 시작: Azure Cloud Shell을 사용하여 Terraform 시작
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

이 문서에서는 [Azure에서 Terraform을 시작](https://www.terraform.io/docs/providers/azurerm/index.html)하는 방법에 대해 설명합니다.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-your-environment"></a>환경 구성

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. 아직 로그인하지 않은 경우 Azure Portal에는 사용 가능한 Microsoft 계정 목록이 표시됩니다. 하나 이상의 활성 Azure 구독과 연결된 Microsoft 계정을 선택하고 자격 증명을 입력하여 계속합니다.

1. Cloud Shell을 엽니다.

    ![Cloud Shell 액세스](media/install-configure/portal-cloud-shell.png)

1. 이전에 Cloud Shell을 사용하지 않은 경우 환경 및 스토리지 설정을 구성합니다. 이 문서에서는 Bash 환경을 사용합니다.

참고:
- Cloud Shell에는 자동으로 최신 버전의 Terraform이 설치됩니다. 또한 Terraform은 현재 Azure 구독의 정보를 자동으로 사용합니다. 따라서 설치 또는 구성이 필요하지 않습니다.

## <a name="authenticate-to-azure"></a>Azure에 대한 인증

Cloud Shell은 Azure Portal에 로그인하는 데 사용한 Microsoft 계정에서 자동으로 인증됩니다. 계정에 여러 Azure 구독이 있는 경우 [다른 구독 중 하나로 전환](#set-the-current-azure-subscription)할 수 있습니다.

Terraform은 Azure에 인증하기 위한 몇 가지 옵션을 지원합니다. 이 문서에서 설명하는 기술은 다음과 같습니다.

- Terraform을 대화형으로 사용하는 경우 [Microsoft 계정을 통해 인증](#authenticate-via-microsoft-account)하는 것이 좋습니다.
- 코드에서 Terraform을 사용하는 경우 [Azure 서비스 주체를 통해 인증](#authenticate-via-azure-service-principal)하는 것이 좋습니다.

### <a name="authenticate-via-microsoft-account"></a>Microsoft 계정을 통해 인증

매개 변수 없이 `az login`을 호출하면 URL과 코드가 표시됩니다. URL을 찾아 코드를 입력하고 지시에 따라 Microsoft 계정을 사용하여 Azure에 로그인합니다. 로그인되면 포털로 돌아갑니다.

```azurecli
az login
```

참고:

- 로그인에 성공하면 `az login`은 로그인 Microsoft 계정에 연결된 Azure 구독 목록을 표시합니다.
- 사용 가능한 각 Azure 구독의 속성 목록이 표시됩니다. `isDefault` 속성은 사용 중인 Azure 구독을 식별합니다. 다른 Azure 구독으로 전환하는 방법에 대한 자세한 내용은 [현재 Azure 구독 설정](#set-the-current-azure-subscription) 섹션을 참조하세요.

### <a name="authenticate-via-azure-service-principal"></a>Azure 서비스 주체를 통해 인증

**Azure 서비스 주체 만들기**: 서비스 주체를 사용하여 Azure 구독에 로그인하려면 먼저 서비스 주체에 액세스할 수 있어야 합니다. 서비스 주체가 이미 있는 경우 섹션의 이 부분을 건너뛸 수 있습니다.

Azure 서비스를 배포하거나 사용하는 자동화된 도구(예: Terraform)에는 항상 제한된 권한이 있어야 합니다. Azure는 애플리케이션에서 모든 권한이 있는 사용자로 로그인하도록 하는 대신 서비스 주체를 제공합니다. 하지만 로그인할 서비스 주체가 없는 경우 어떻게 될까요? 이 시나리오에서는 사용자 자격 증명을 사용하여 로그인한 다음, 서비스 주체를 만들 수 있습니다. 서비스 주체를 만든 후에는 나중에 로그인을 시도할 때 해당 정보를 사용할 수 있습니다.

[Azure CLI를 사용하여 서비스 주체를 만드는 경우](/cli/azure/create-an-azure-service-principal-azure-cli?) 여러 옵션이 있습니다. 이 문서에서는 [az ad sp create-for-rbac](/cli/azure/ad/sp?#az-ad-sp-create-for-rbac)를 사용하여 **기여자** 역할이 있는 서비스 주체를 만듭니다. **기여자** 역할(기본값)은 Azure 계정에서 읽고 쓸 수 있는 모든 권한을 갖고 있습니다. RBAC(역할 기반 액세스 제어)와 역할에 대한 자세한 내용은 [RBAC: 기본 제공 역할](/azure/active-directory/role-based-access-built-in-roles)을 참조하세요.

다음 명령을 입력하고, `<subscription_id>`를 사용하려는 구독 계정의 ID로 바꿉니다.

```azurecli
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription_id>"
```

참고:

- 성공적으로 완료되면 `az ad sp create-for-rbac`에서 여러 값을 표시합니다. `name`, `password` 및 `tenant` 값은 다음 단계에서 사용됩니다.
- 분실한 암호는 복구할 수 없습니다. 따라서 암호를 안전한 장소에 저장해야 합니다. 암호를 잊어버린 경우 [서비스 주체 자격 증명을 다시 설정](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials)해야 합니다.

**Azure 서비스 주체를 사용한 로그인**: 다음 `az login` 호출의 자리 표시자를 서비스 주체의 정보로 바꿉니다.

```azurecli
az login --service-principal -u <service_principal_name> -p "<service_principal_password>" --tenant "<service_principal_tenant>"
```

## <a name="set-the-current-azure-subscription"></a>현재 Azure 구독 설정

Microsoft 계정은 여러 Azure 구독과 연결할 수 있습니다. 다음 단계에서는 구독 간에 전환할 수 있는 방법을 간략하게 설명합니다.

1. 현재 Azure 구독을 보려면 [az account show](/cli/azure/account#az-account-show)를 사용합니다.

    ```azurecli
    az account show
    ```

1. 사용 가능한 여러 Azure 구독에 대한 액세스 권한이 있는 경우 [az account list](/cli/azure/account#az-account-list)를 사용하여 구독 이름 ID 값 목록을 표시합니다.

    ```azurecli
    az account list --query "[].{name:name, subscriptionId:id}"
    ```

1. 현재 Cloud Shell 세션에 특정 Azure 구독을 사용하려면 [az account set](/cli/azure/account#az-account-set)를 사용합니다. `<subscription_id>` 자리 표시자를 사용하려는 구독의 ID(또는 이름)로 바꿉니다.

    ```azurecli
    az account set --subscription="<subscription_id>"
    ```

    참고:

    - `az account set`를 호출해도 지정된 Azure 구독으로 전환한 결과가 표시되지 않습니다. 그러나 `az account show`를 사용하여 현재 Azure 구독이 변경되었는지 확인할 수 있습니다.

## <a name="create-a-terraform-configuration-file"></a>Terraform 구성 파일 만들기

이 섹션에서는 Azure 리소스 그룹을 만드는 Terraform 구성 파일을 만드는 방법에 대해 알아봅니다.

1. Cloud Shell의 작업이 지속되는 탑재된 파일 공유에 대한 디렉터리를 변경합니다. Cloud Shell 파일을 유지하는 방법에 대한 자세한 내용은 [Microsoft Azure Files 스토리지 연결](/azure/cloud-shell/overview#connect-your-microsoft-azure-files-storage)을 참조하세요.

    ```bash
    cd clouddrive
    ```

1. 이 데모에 대한 Terraform 파일을 저장할 디렉터리를 만듭니다.

    ```bash
    mkdir QuickstartTerraformTest
    ```

1. 디렉터리를 데모 디렉터리로 변경합니다.

    ```bash
    cd QuickstartTerraformTest
    ```

1. 선호하는 편집기를 사용하여 Terraform 구성 파일을 만듭니다. 이 문서에서는 기본 제공 [Cloud Shell 편집기](/azure/cloud-shell/using-cloud-shell-editor)를 사용합니다.

    ```bash
    code QuickstartTerraformTest.tf
    ```
 
1. 다음 HCL을 새 파일에 붙여 넣습니다.

    ```hcl
    provider "azurerm" {
      # The "feature" block is required for AzureRM provider 2.x.
      # If you are using version 1.x, the "features" block is not allowed.
      version = "~>2.0"
      features {}
    }
    resource "azurerm_resource_group" "rg" {
            name = "QuickstartTerraformTest-rg"
            location = "eastus"
    }
    ```

    참고:

    - `provider` 블록은 [Azure 공급자(`azurerm`)](https://www.terraform.io/docs/providers/azurerm/index.html)를 사용하도록 지정합니다.
    - `azurerm` 공급자 블록 내에서 `version` 및 `features` 특성이 설정됩니다. 주석에 언급된 대로, 용도는 버전에 따라 다릅니다. 환경에 이러한 특성을 설정하는 방법에 대한 자세한 내용은 [AzureRM 공급자의 v2.0](https://www.terraform.io/docs/providers/azurerm/guides/2.0-upgrade-guide.html)을 참조하세요.
    - [resource declaration](https://www.terraform.io/docs/configuration/resources.html)은 [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html) 리소스 종류에 대해서만 적용됩니다. `azure_resource_group`에 필요한 두 개의 인수는 `name` 및 `location`입니다.

1. 파일을 저장합니다( **&lt;Ctrl>S**).

1. 편집기를 종료합니다( **&lt;Ctrl > Q**).

## <a name="create-and-apply-a-terraform-execution-plan"></a>Terraform 실행 계획 만들기 및 적용

구성 파일이 만들어지면 이 섹션에서 *실행 계획*을 만들고 클라우드 인프라에 적용하는 방법에 대해 설명합니다.

1. [terraform init](https://www.terraform.io/docs/commands/init.html)를 사용하여 Terraform 배포를 초기화합니다. 이 단계에서는 Azure 리소스 그룹을 만드는 데 필요한 Azure 모듈을 다운로드합니다.

    ```bash
    terraform init
    ```

1. Terraform을 사용하면 [terraform plan](https://www.terraform.io/docs/commands/plan.html)을 통해 완료되는 작업을 미리 볼 수 있습니다.

    ```bash
    terraform plan
    ```

    참고:

    - `terraform plan` 명령은 실행 계획을 만들지만 실행하지는 않습니다. 대신 구성 파일에 지정된 구성을 만드는 데 필요한 작업을 결정합니다.
    - `terraform plan` 명령을 사용하면 실제 리소스를 변경하기 전에 실행 계획이 예상과 일치하는지 확인할 수 있습니다.
    - 선택 사항인 `-out` 매개 변수를 사용하여 계획의 출력 파일을 지정할 수 있습니다. `-out` 매개 변수를 사용하는 방법에 대한 자세한 내용은 [이후 배포를 위해 실행 계획 유지](#persist-an-execution-plan-for-later-deployment) 섹션을 참조하세요.

1. [terraform apply](https://www.terraform.io/docs/commands/apply.html)를 사용하여 실행 계획을 적용합니다.

    ```bash
    terraform apply
    ```

1. Terraform은 실행 계획을 적용하고 실행을 확인해야 할 경우 어떻게 되는지 보여줍니다. `yes`를 입력하고 **Enter** 키를 눌러 명령을 확인합니다.

1. 계획 실행이 확인되면 [az group show](/cli/azure/group?#az-group-show)를 사용하여 리소스 그룹이 성공적으로 만들어졌는지 테스트합니다.

    ```azurecli
    az group show -n "QuickstartTerraformTest-rg"
    ```

    참고:

    - 성공하면 `az group show`는 새로 만든 리소스 그룹의 다양한 특성을 표시합니다.

## <a name="persist-an-execution-plan-for-later-deployment"></a>이후 배포를 위해 실행 계획 유지

이전 섹션에서는 [terraform plan](https://www.terraform.io/docs/commands/plan.html)을 실행하여 실행 계획을 만드는 방법을 살펴보았습니다. 그런 다음, [terraform apply](https://www.terraform.io/docs/commands/apply.html)를 사용하여 해당 계획을 적용하는 것을 살펴보았습니다. 이 패턴은 단계가 대화식이고 순차적일 때 효과적입니다.

더 복잡한 시나리오의 경우 실행 계획을 파일에 유지할 수 있습니다. 이후 또는 다른 머신에서 실행하는 경우에도 해당 실행 계획을 적용할 수 있습니다.

이 기능을 사용하는 경우 [자동화에서 Terraform 실행](https://learn.hashicorp.com/terraform/development/running-terraform-in-automation) 문서를 참조하는 것이 좋습니다.

다음 단계에서는 이 기능을 사용하기 위한 기본 패턴을 보여줍니다.

1. [terraform init](https://www.terraform.io/docs/commands/init.html)를 실행합니다.

    ```bash
    terraform init
    ```

1. `-out` 매개 변수를 사용하여 `terraform plan`을 실행합니다.

    ```bash
    terraform plan -out QuickstartTerraformTest.tfplan
    ```

1. 이전 단계에서 파일 이름을 지정하여 `terraform apply`를 실행합니다.

    ```bash
    terraform apply QuickstartTerraformTest.tfplan
    ```

참고:

- 자동화와 함께 사용하면 `terraform apply <filename>`을 실행할 때 확인이 필요하지 않습니다.
- 이 기능을 사용하려면 [보안 경고 섹션](https://www.terraform.io/docs/commands/plan.html#security-warning)을 읽습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 이 문서에서 만든 리소스를 삭제합니다.

1. [terraform plan](https://www.terraform.io/docs/commands/plan.html)을 실행하여 Terraform 구성 파일에 표시된 리소스를 삭제하는 실행 계획을 만듭니다.

    ```bash
    terraform plan -destroy -out QuickstartTerraformTest.destroy.tfplan
    ```

    **참고:**
    - `terraform plan` 명령은 실행 계획을 만들지만 실행하지는 않습니다. 대신 구성 파일에 지정된 구성을 만드는 데 필요한 작업을 결정합니다. 이 패턴을 사용하면 실제 리소스를 변경하기 전에 실행 계획이 예상과 일치하는지 확인할 수 있습니다.
    - `-destroy` 매개 변수는 리소스를 삭제하는 계획을 생성합니다.
    - 선택 사항인 `-out` 매개 변수를 사용하여 계획의 출력 파일을 지정할 수 있습니다. 검토한 계획이 정확히 적용되도록 `-out` 매개 변수를 항상 사용해야 합니다.
    - 실행 계획 및 보안을 유지하는 방법에 대한 자세한 내용은 [보안 경고 섹션](https://www.terraform.io/docs/commands/plan.html#security-warning)을 참조하세요.

1. [terraform apply](https://www.terraform.io/docs/commands/apply.html)를 실행하여 실행 계획을 적용합니다.

    ```bash
    terraform apply QuickstartTerraformTest.destroy.tfplan
    ```

1. [az group show](/cli/azure/group?#az-group-show)를 사용하여 리소스 그룹이 삭제되었는지 확인합니다.

    ```azurecli
    az group show -n "QuickstartTerraformTest-rg"
    ```

    **참고**:
    - 성공하면 `az group show`에서 리소스 그룹이 없다는 사실을 표시합니다.

1. 디렉터리를 상위 디렉터리로 변경하고 데모 디렉터리를 제거합니다. `-r` 매개 변수는 디렉터리를 제거하기 전에 디렉터리 콘텐츠를 제거합니다. 디렉터리 콘텐츠에는 이전에 만든 구성 파일과 Terraform 상태 파일이 포함됩니다.

    ```bash
    cd .. && rm -r QuickstartTerraformTest
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Terraform으로 Azure VM 만들기](create-linux-virtual-machine-with-infrastructure.md)