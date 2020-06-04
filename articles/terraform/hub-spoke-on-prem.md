---
title: 자습서 - Terraform을 사용하여 Azure에서 온-프레미스 가상 네트워크 만들기
description: 로컬 리소스를 보관하는 Azure에서 온-프레미스 VNet(가상 네트워크)을 구현하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 0f336a9dae0c063cb8498f0aae4db3d82d1dbcac
ms.sourcegitcommit: db56786f046a3bde1bd9b0169b4f62f0c1970899
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329601"
---
# <a name="tutorial-create-on-premises-virtual-network-in-azure-using-terraform"></a>자습서: Terraform을 사용하여 Azure에서 온-프레미스 가상 네트워크 만들기

이 자습서에서는 Azure VNet(가상 머신)을 사용하여 온-프레미스 네트워크를 구현하는 방법을 보여줍니다. Azure VNet은 자체 프라이빗 가상 네트워크로 대체할 수 있습니다. 이렇게 하려면 서브넷에 있는 적절한 IP 주소를 매핑합니다.

다음 작업에 대해 설명합니다.

> [!div class="checklist"]
> * HCL(HashiCorp Language)을 사용하여 허브 스포크 토폴로지에서 온-프레미스 VNet 구현
> * Terraform을 사용하여 허브 네트워크 어플라이언스 리소스 만들기
> * Terraform을 사용하여 온-프레미스 가상 머신 만들기
> * Terraform을 사용하여 온-프레미스 가상 사설망 게이트웨이 만들기

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>사전 요구 사항

1. [Azure에서 Terraform을 사용하여 허브 및 스포크 하이브리드 네트워크 토폴로지를 만듭니다](./hub-spoke-introduction.md).

## <a name="create-the-directory-structure"></a>디렉터리 구조 만들기

온-프레미스 네트워크를 시뮬레이션하려면 Azure Virtual Network를 만듭니다. 데모 VNet은 실제 프라이빗 온-프레미스 네트워크를 대체합니다. 기존 온-프레미스 네트워크와 동일한 작업을 수행하려면 서브넷에 있는 적절한 IP 주소를 매핑하세요.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. [Azure Cloud Shell](/azure/cloud-shell/overview)을 엽니다. 이전에 환경을 선택하지 않은 경우 환경으로 **Bash**를 선택합니다.

    ![Cloud Shell 프롬프트](./media/common/azure-portal-cloud-shell-button-min.png)

1. 디렉터리를 `clouddrive` 디렉터리로 변경합니다.

    ```bash
    cd clouddrive
    ```

1. 디렉터리를 새 디렉터리로 변경합니다.

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-on-premises-vnet"></a>온-프레미스 VNet 선언

온-프레미스 VNet을 선언하는 Terraform 구성 파일을 만듭니다.

1. Cloud Shell에서 `on-prem.tf`라는 새 파일을 엽니다.

    ```bash
    code on-prem.tf
    ```

1. 다음 코드를 편집기에 붙여 넣습니다.

    ```hcl
    locals {
      onprem-location       = "SouthCentralUS"
      onprem-resource-group = "onprem-vnet-rg"
      prefix-onprem         = "onprem"
    }

    resource "azurerm_resource_group" "onprem-vnet-rg" {
      name     = local.onprem-resource-group
      location = local.onprem-location
    }

    resource "azurerm_virtual_network" "onprem-vnet" {
      name                = "onprem-vnet"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name
      address_space       = ["192.168.0.0/16"]

      tags = {
        environment = local.prefix-onprem
      }
    }

    resource "azurerm_subnet" "onprem-gateway-subnet" {
      name                 = "GatewaySubnet"
      resource_group_name  = azurerm_resource_group.onprem-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.onprem-vnet.name
      address_prefix       = "192.168.255.224/27"
    }

    resource "azurerm_subnet" "onprem-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.onprem-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.onprem-vnet.name
      address_prefix       = "192.168.1.128/25"
    }

    resource "azurerm_public_ip" "onprem-pip" {
        name                         = "${local.prefix-onprem}-pip"
        location            = azurerm_resource_group.onprem-vnet-rg.location
        resource_group_name = azurerm_resource_group.onprem-vnet-rg.name
        allocation_method   = "Dynamic"

        tags = {
            environment = local.prefix-onprem
        }
    }

    resource "azurerm_network_interface" "onprem-nic" {
      name                 = "${local.prefix-onprem}-nic"
      location             = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name  = azurerm_resource_group.onprem-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-onprem
        subnet_id                     = azurerm_subnet.onprem-mgmt.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.onprem-pip.id
      }
    }

    # Create Network Security Group and rule
    resource "azurerm_network_security_group" "onprem-nsg" {
        name                = "${local.prefix-onprem}-nsg"
        location            = azurerm_resource_group.onprem-vnet-rg.location
        resource_group_name = azurerm_resource_group.onprem-vnet-rg.name

        security_rule {
            name                       = "SSH"
            priority                   = 1001
            direction                  = "Inbound"
            access                     = "Allow"
            protocol                   = "Tcp"
            source_port_range          = "*"
            destination_port_range     = "22"
          source_address_prefix      = "*"
            destination_address_prefix = "*"
        }

        tags = {
            environment = "onprem"
        }
    }

    resource "azurerm_subnet_network_security_group_association" "mgmt-nsg-association" {
      subnet_id                 = azurerm_subnet.onprem-mgmt.id
      network_security_group_id = azurerm_network_security_group.onprem-nsg.id
    }

    resource "azurerm_virtual_machine" "onprem-vm" {
      name                  = "${local.prefix-onprem}-vm"
      location              = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name   = azurerm_resource_group.onprem-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.onprem-nic.id]
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
        computer_name  = "${local.prefix-onprem}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags = {
        environment = local.prefix-onprem
      }
    }

    resource "azurerm_public_ip" "onprem-vpn-gateway1-pip" {
      name                = "${local.prefix-onprem}-vpn-gateway1-pip"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name

      allocation_method = "Dynamic"
    }

    resource "azurerm_virtual_network_gateway" "onprem-vpn-gateway" {
      name                = "onprem-vpn-gateway1"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name

      type     = "Vpn"
      vpn_type = "RouteBased"

      active_active = false
      enable_bgp    = false
      sku           = "VpnGw1"

      ip_configuration {
        name                          = "vnetGatewayConfig"
        public_ip_address_id          = azurerm_public_ip.onprem-vpn-gateway1-pip.id
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = azurerm_subnet.onprem-gateway-subnet.id
      }
      depends_on = [azurerm_public_ip.onprem-vpn-gateway1-pip]

    }
    ```

1. 파일을 저장하고 편집기를 종료합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure에서 Terraform을 사용하여 허브 가상 네트워크를 만듭니다](./hub-spoke-hub-network.md).