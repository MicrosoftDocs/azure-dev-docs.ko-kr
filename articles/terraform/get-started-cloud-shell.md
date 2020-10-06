---
title: 빠른 시작 - Azure Cloud Shell을 사용하여 Terraform 구성
description: 이 빠른 시작에서는 Azure Cloud Shell에서 Terraform을 설치하고 구성하는 방법을 알아봅니다.
keywords: azure devops terraform 설치 구성 cloud shell init 계획 적용 실행 포털 로그인 rbac 서비스 주체 자동화된 스크립트
ms.topic: quickstart
ms.date: 09/27/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: f5b1b242479ede712cccb178a8ee25b0b557173c
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401613"
---
# <a name="quickstart-configure-terraform-using-azure-cloud-shell"></a>빠른 시작: Azure Cloud Shell을 사용하여 Terraform 구성
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

이 문서에서는 [Azure에서 Terraform을 시작](https://www.terraform.io/docs/providers/azurerm/index.html)하는 방법에 대해 설명합니다.

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
> * Azure에 대한 인증
> * Azure CLI를 사용하여 Azure 서비스 주체 만들기
> * 서비스 주체를 사용하여 Azure에 인증
> * 현재 Azure 구독 설정 - 여러 Azure 구독이 있는 경우 사용
> * 기본 Terraform 구성 파일 만들기
> * Terraform 실행 계획 만들기 및 적용
> * 실행 계획 되돌리기

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-your-environment"></a>환경 구성

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. 아직 로그인하지 않은 경우 Azure Portal에는 사용 가능한 Microsoft 계정 목록이 표시됩니다. 하나 이상의 활성 Azure 구독과 연결된 Microsoft 계정을 선택하고 자격 증명을 입력하여 계속합니다.

1. Cloud Shell을 엽니다.

    ![Cloud Shell 액세스](media/install-configure/portal-cloud-shell.png)

1. 이전에 Cloud Shell을 사용하지 않은 경우 환경 및 스토리지 설정을 구성합니다. 이 문서에서는 Bash 환경을 사용합니다.

**참고**:
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

**참고**:

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

**참고**:

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

    **참고**:

    - `az account set`를 호출해도 지정된 Azure 구독으로 전환한 결과가 표시되지 않습니다. 그러나 `az account show`를 사용하여 현재 Azure 구독이 변경되었는지 확인할 수 있습니다.

[!INCLUDE [terraform-create-base-config-file.md](includes/terraform-create-base-config-file.md)]

[!INCLUDE [terraform-create-and-apply-execution-plan.md](includes/terraform-create-and-apply-execution-plan.md)]

[!INCLUDE [terraform-reverse-execution-plan.md](includes/terraform-reverse-execution-plan.md)]

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Terraform을 사용하여 Linux VM 만들기](create-linux-virtual-machine-with-infrastructure.md)