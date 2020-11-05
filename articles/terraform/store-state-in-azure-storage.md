---
title: 자습서 - Terraform 상태를 Azure Storage에 저장
description: Terraform 상태를 Azure Storage에 저장하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 11/07/2019
ms.custom: devx-track-terraform
ms.openlocfilehash: d3d2ab0ff605883926260928d3e7174a5c526781
ms.sourcegitcommit: 5541f993c01ce356e1b0eaa8f95aea9051c3c21e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93278418"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>자습서: Terraform 상태를 Azure Storage에 저장

Terraform 상태는 Terraform 구성을 사용하여 배포된 리소스를 조정하는 데 사용됩니다. 상태를 통해 Terraform은 추가, 업데이트 또는 삭제할 Azure 리소스를 파악합니다. 기본적으로 Terraform 상태는 `terraform apply` 명령을 실행할 때 로컬로 저장됩니다. 이 구성은 다음과 같은 이유로 적합하지 않습니다.

- 로컬 상태는 팀 또는 협업 환경에서 잘 작동하지 않습니다.
- Terraform 상태는 중요한 정보를 포함할 수 있습니다.
- 상태를 로컬로 저장하면 실수로 삭제할 가능성이 높아집니다.

Terraform은 원격 스토리지에서 상태의 지속을 지원합니다. 이러한 지원되는 백 엔드 중 하나는 Azure Storage입니다. 이 문서에서는 이 목적을 위해 Azure Storage를 구성하고 사용하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-storage-account"></a>스토리지 계정 구성

Azure Storage를 백 엔드로 사용하기 전에 스토리지 계정을 만들어야 합니다. 스토리지 계정은 Azure Portal, PowerShell, Azure CLI 또는 자체 Terraform을 사용하여 만들 수 있습니다. Azure CLI를 사용하여 스토리지 계정을 구성하려면 다음 샘플을 사용합니다.

```azurecli
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query '[0].value' -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

스토리지 계정 이름, 컨테이너 이름 및 스토리지 액세스 키를 적어둡니다. 이러한 값은 원격 상태를 구성할 때 필요합니다.

## <a name="configure-state-back-end"></a>상태 백 엔드 구성

Terraform 상태 백 엔드는 `terraform init` 명령을 실행할 때 구성됩니다. 상태 백 엔드를 구성하려면 다음 데이터가 필요합니다.

- **storage_account_name** : Azure Storage 계정의 이름입니다.
- **container_name** : Blob 컨테이너의 이름입니다.
- **키** : 만들 상태 저장소 파일의 이름입니다.
- **access_key** : 스토리지 액세스 키입니다.

이러한 각 값은 Terraform 구성 파일이나 명령줄에서 지정할 수 있습니다. `access_key` 값에는 환경 변수를 사용하는 것이 좋습니다. 환경 변수를 사용하면 키가 디스크에 쓰이는 것을 방지할 수 있습니다.

Azure Storage 액세스 키의 값을 사용하여 `ARM_ACCESS_KEY`라는 환경 변수를 만듭니다.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Azure Storage 계정 액세스 키를 추가로 보호하려면 Azure Key Vault에 저장합니다. 그런 다음, 다음과 비슷한 명령을 사용하여 환경 변수를 설정할 수 있습니다. Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault 설명서](/azure/key-vault/secrets/quick-create-cli)를 참조하세요.

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

백 엔드를 사용하도록 Terraform을 구성하려면 다음 단계를 수행해야 합니다.
- `azurerm` 형식의 `backend` 구성 블록을 포함합니다.
- 구성 블록에 `storage_account_name` 값을 추가합니다.
- 구성 블록에 `container_name` 값을 추가합니다.
- 구성 블록에 `key` 값을 추가합니다.

다음 예제에서는 Terraform 백 엔드를 구성하고 Azure 리소스 그룹을 만듭니다.

```hcl
terraform {
  backend "azurerm" {
    resource_group_name   = "tstate"
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

다음 단계를 수행하여 구성을 초기화합니다.

1. `terraform init` 명령을 실행합니다.
1. `terraform apply` 명령을 실행합니다.

이제 Azure Storage Blob에서 상태 파일을 찾을 수 있습니다.

## <a name="state-locking"></a>상태 잠금

Azure Storage Blob은 상태를 기록하는 모든 작업 이전에 자동으로 잠깁니다. 이 패턴은 손상을 일으킬 수 있는 동시 상태 작업을 방지합니다. 

자세한 내용은 Terraform 설명서의 [상태 잠금](https://www.terraform.io/docs/state/locking.html)을 참조하세요.

Azure Portal 또는 기타 Azure 관리 도구를 통해 Blob을 검토할 때는 잠겨져 있습니다.

![잠금을 사용한 Azure blob](media/store-state-in-azure-storage/lock.png)

## <a name="encryption-at-rest"></a>휴지 상태의 암호화

Azure Blob에 저장된 데이터는 영구 저장되기 전에 암호화됩니다. 필요한 경우 Terraform은 백 엔드에서 상태를 검색하여 로컬 메모리에 저장합니다. 이 패턴을 사용하면 상태는 로컬 디스크에 기록되지 않습니다.

Azure Storage 암호화에 대한 자세한 내용은 [미사용 데이터에 대한 Azure Storage 서비스 암호화](/azure/storage/common/storage-service-encryption)를 참조하세요.

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure에서 Terraform을 사용하는 방법에 대해 자세히 알아보기](/azure/terraform)
