---
title: 자습서 - Terraform을 사용하여 Azure에서 허브 가상 네트워크 어플라이언스 만들기
description: 다른 네트워크 간의 공통 연결 지점 역할을 하는 허브 VNet(가상 네트워크)을 만드는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 03/08/2021
ms.custom: devx-track-terraform
ms.openlocfilehash: b8deb50c29a0fd1cdd317dc2edfe0bd3bf21d8da
ms.sourcegitcommit: 7991f748720673d2dc50baaa8658348ff6cc1044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102604164"
---
# <a name="tutorial-create-a-hub-virtual-network-appliance-in-azure-using-terraform"></a>자습서: Terraform을 사용하여 Azure에서 허브 가상 네트워크 어플라이언스 만들기

*VPN 디바이스* 는 온-프레미스 네트워크에 외부 연결을 제공하는 디바이스입니다. VPN 디바이스는 하드웨어 디바이스 또는 소프트웨어 솔루션일 수 있습니다. 소프트웨어 솔루션의 한 예는 Windows Server 2012의 RRAS(라우팅 및 원격 액세스 서비스)입니다. VPN 어플라이언스에 대한 자세한 내용은 [사이트 간 VPN Gateway 연결을 위한 VPN 디바이스 정보](/azure/vpn-gateway/vpn-gateway-about-vpn-devices)를 참조하세요.

Azure는 선택할 수 있는 광범위한 네트워크 가상 어플라이언스를 지원합니다. 이 문서에서는 Ubuntu 이미지가 사용됩니다. Azure에서 지원되는 다양한 디바이스 솔루션에 대해 자세히 알아보려면 [네트워크 어플라이언스 홈페이지](https://azure.microsoft.com/solutions/network-appliances/)를 참조하세요.

이 문서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * HCL(HashiCorp Language)을 사용하여 허브 스포크 토폴로지에서 Hub VNet을 구현
> * Terraform을 사용하여 어플라이언스 역할을 하는 Hub Network Virtual Machine 만들기
> * Terraform을 사용하여 CustomScript 확장을 통해 경로를 사용하도록 설정
> * Terraform을 사용하여 Hub 및 Spoke 게이트웨이 경로 테이블 만들기

## <a name="prerequisites"></a>사전 요구 사항

1. [Azure에서 Terraform을 사용하여 허브 및 스포크 하이브리드 네트워크 토폴로지를 만듭니다](./hub-spoke-introduction.md).
1. [Azure에서 Terraform을 사용하여 온-프레미스 가상 네트워크를 만듭니다](./hub-spoke-on-prem.md).
1. [Azure에서 Terraform을 사용하여 허브 가상 네트워크를 만듭니다](./hub-spoke-hub-network.md).

## <a name="create-the-directory-structure"></a>디렉터리 구조 만들기

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. [Azure Cloud Shell](/azure/cloud-shell/overview)을 엽니다. 이전에 환경을 선택하지 않은 경우 환경으로 **Bash** 를 선택합니다.

    ![Cloud Shell 프롬프트](./media/common/azure-portal-cloud-shell-button-min.png)

1. 디렉터리를 `clouddrive` 디렉터리로 변경합니다.

    ```bash
    cd clouddrive
    ```

1. 디렉터리를 새 디렉터리로 변경합니다.

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-hub-network-appliance"></a>허브 네트워크 어플라이언스 선언

온-프레미스 가상 네트워크를 선언하는 Terraform 구성 파일을 만듭니다.

1. Cloud Shell에서 이름이 `hub-nva.tf`인 새 파일을 만듭니다.

    ```bash
    code hub-nva.tf
    ```

1. 다음 코드를 편집기에 붙여 넣습니다.
    
    ```hcl
    locals {
      prefix-hub-nva         = "hub-nva"
      hub-nva-location       = "eastus"
      hub-nva-resource-group = "hub-nva-rg"
    }

    resource "azurerm_resource_group" "hub-nva-rg" {
      name     = "${local.prefix-hub-nva}-rg"
      location = local.hub-nva-location

      tags = {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_network_interface" "hub-nva-nic" {
      name                 = "${local.prefix-hub-nva}-nic"
      location             = azurerm_resource_group.hub-nva-rg.location
      resource_group_name  = azurerm_resource_group.hub-nva-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-hub-nva
        subnet_id                     = azurerm_subnet.hub-dmz.id
        private_ip_address_allocation = "Static"
        private_ip_address            = "10.0.0.36"
      }

      tags = {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_virtual_machine" "hub-nva-vm" {
      name                  = "${local.prefix-hub-nva}-vm"
      location              = azurerm_resource_group.hub-nva-rg.location
      resource_group_name   = azurerm_resource_group.hub-nva-rg.name
      network_interface_ids = [azurerm_network_interface.hub-nva-nic.id]
      vm_size               = var.vmsize

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-hub-nva}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags = {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_virtual_machine_extension" "enable-routes" {
      name                 = "enable-iptables-routes"
      virtual_machine_id   = azurerm_virtual_machine.hub-nva-vm.id
      publisher            = "Microsoft.Azure.Extensions"
      type                 = "CustomScript"
      type_handler_version = "2.0"


      settings = <<SETTINGS
        {
            "fileUris": [
            "https://raw.githubusercontent.com/mspnp/reference-architectures/master/scripts/linux/enable-ip-forwarding.sh"
            ],
            "commandToExecute": "bash enable-ip-forwarding.sh"
        }
    SETTINGS

      tags = {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_route_table" "hub-gateway-rt" {
      name                          = "hub-gateway-rt"
      location                      = azurerm_resource_group.hub-nva-rg.location
      resource_group_name           = azurerm_resource_group.hub-nva-rg.name
      disable_bgp_route_propagation = false

      route {
        name           = "toHub"
        address_prefix = "10.0.0.0/16"
        next_hop_type  = "VnetLocal"
      }

      route {
        name                   = "toSpoke1"
        address_prefix         = "10.1.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      route {
        name                   = "toSpoke2"
        address_prefix         = "10.2.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      tags = {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_subnet_route_table_association" "hub-gateway-rt-hub-vnet-gateway-subnet" {
      subnet_id      = azurerm_subnet.hub-gateway-subnet.id
      route_table_id = azurerm_route_table.hub-gateway-rt.id
      depends_on = [azurerm_subnet.hub-gateway-subnet]
    }

    resource "azurerm_route_table" "spoke1-rt" {
      name                          = "spoke1-rt"
      location                      = azurerm_resource_group.hub-nva-rg.location
      resource_group_name           = azurerm_resource_group.hub-nva-rg.name
      disable_bgp_route_propagation = false

      route {
        name                   = "toSpoke2"
        address_prefix         = "10.2.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      route {
        name           = "default"
        address_prefix = "0.0.0.0/0"
        next_hop_type  = "vnetlocal"
      }

      tags = {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_subnet_route_table_association" "spoke1-rt-spoke1-vnet-mgmt" {
      subnet_id      = azurerm_subnet.spoke1-mgmt.id
      route_table_id = azurerm_route_table.spoke1-rt.id
      depends_on = [azurerm_subnet.spoke1-mgmt]
    }

    resource "azurerm_subnet_route_table_association" "spoke1-rt-spoke1-vnet-workload" {
      subnet_id      = azurerm_subnet.spoke1-workload.id
      route_table_id = azurerm_route_table.spoke1-rt.id
      depends_on = [azurerm_subnet.spoke1-workload]
    }

    resource "azurerm_route_table" "spoke2-rt" {
      name                          = "spoke2-rt"
      location                      = azurerm_resource_group.hub-nva-rg.location
      resource_group_name           = azurerm_resource_group.hub-nva-rg.name
      disable_bgp_route_propagation = false

      route {
        name                   = "toSpoke1"
        address_prefix         = "10.1.0.0/16"
        next_hop_in_ip_address = "10.0.0.36"
        next_hop_type          = "VirtualAppliance"
      }

      route {
        name           = "default"
        address_prefix = "0.0.0.0/0"
        next_hop_type  = "vnetlocal"
      }

      tags = {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_subnet_route_table_association" "spoke2-rt-spoke2-vnet-mgmt" {
      subnet_id      = azurerm_subnet.spoke2-mgmt.id
      route_table_id = azurerm_route_table.spoke2-rt.id
      depends_on = [azurerm_subnet.spoke2-mgmt]
    }

    resource "azurerm_subnet_route_table_association" "spoke2-rt-spoke2-vnet-workload" {
      subnet_id      = azurerm_subnet.spoke2-workload.id
      route_table_id = azurerm_route_table.spoke2-rt.id
      depends_on = [azurerm_subnet.spoke2-workload]
    }
    ```

1. 파일을 저장하고 편집기를 종료합니다.

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure에서 Terraform을 사용하여 스포크 가상 네트워크 만들기](./hub-spoke-spoke-network.md)