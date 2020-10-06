---
title: Terraform을 사용하여 Azure VM 클러스터 구성
description: Terraform 모듈을 사용하여 Azure에서 Windows 가상 머신 클러스터를 만드는 방법을 알아봅니다.
keywords: azure devops terraform vm 가상 머신 클러스터 모듈 레지스트리
ms.topic: how-to
ms.date: 09/27/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 73f375090a2178b38b0fc7e0afd4eb8c6b514672
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401590"
---
# <a name="configure-an-azure-vm-cluster-using-terraform"></a>Terraform을 사용하여 Azure VM 클러스터 구성

이 문서에서는 Azure에서 VM 클러스터를 만들기 위한 Terraform 코드 예제를 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

[!INCLUDE [terraform-configure-environment.md](includes/terraform-configure-environment.md)]

## <a name="configure-an-azure-vm-cluster"></a>Azure VM 클러스터 구성

```hcl
module "windowsservers" {
  source              = "Azure/compute/azurerm"
  resource_group_name = azurerm_resource_group.rg.name
  is_windows_image    = true
  vm_hostname         = "mywinvm"                         // Line can be removed if only one VM module per resource group
  admin_password      = "ComplxP@ssw0rd!"                 // See note following code about storing passwords in config files
  vm_os_simple        = "WindowsServer"
  public_ip_dns       = ["winsimplevmips"]                // Change to a unique name per data center region
  vnet_subnet_id      = module.network.vnet_subnets[0]
    
  depends_on = [azurerm_resource_group.rg]
}

module "network" {
  source              = "Azure/network/azurerm"
  resource_group_name = azurerm_resource_group.rg.name
  subnet_prefixes     = ["10.0.1.0/24"]
  subnet_names        = ["subnet1"]

  depends_on = [azurerm_resource_group.rg]
}

output "windows_vm_public_name" {
  value = module.windowsservers.public_ip_dns_name
}

output "vm_public_ip" {
  value = module.windowsservers.public_ip_address
}

output "vm_private_ips" {
  value = module.windowsservers.network_interface_private_ip
}
```

**참고**:

- 위의 코드 예제에서는 간단한 설명을 위해 `admin_password` 변수에 리터럴 값이 할당됩니다. 암호와 같은 중요한 데이터를 저장하는 방법은 여러 가지가 있습니다. 데이터를 보호하는 방법은 특정 환경과 관련된 개별 선택과 적절한 데이터 노출 수준에 따라 결정됩니다. 예를 들어 소스 제어에 이와 같은 파일을 저장하면 다른 사용자가 암호를 볼 수 있다는 위험이 있습니다. 이 주제에 대한 자세한 내용은 [입력 변수를 선언](https://www.terraform.io/docs/configuration/variables.html)하는 다양한 방법 및 [중요한 데이터(예: 암호)를 관리](https://www.terraform.io/docs/state/sensitive-data.html)하는 기법에 대해 설명하는 HashiCorp의 문서를 참조하세요.

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure에서 Terraform을 사용하는 방법에 대해 자세히 알아보기](/azure/terraform)
