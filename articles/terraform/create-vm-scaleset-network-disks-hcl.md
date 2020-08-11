---
title: Terraform을 사용하여 Azure 가상 머신 확장 집합 만들기
description: Terraform을 사용하여 Azure 가상 머신 확장 집합을 구성하고 버전 관리하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 11/07/2019
ms.openlocfilehash: 75481c04038b13d81fa273e53bd1950c84fa5053
ms.sourcegitcommit: e451e4360d9c5956cc6a50880b3a7a55aa4efd2f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87478503"
---
# <a name="create-an-azure-virtual-machine-scale-set-using-terraform"></a>Terraform을 사용하여 Azure 가상 머신 확장 집합 만들기

[Azure 가상 머신 확장 집합](/azure/virtual-machine-scale-sets)을 사용하면 동일한 VM을 구성할 수 있습니다. VM 인스턴스 수는 수요 또는 일정에 따라 조정할 수 있습니다. 자세한 내용은 [Azure Portal에 설정된 가상 머신 자동 조정](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-portal)을 참조하세요.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Terraform 배포 설정
> * Terraform 배포용 변수 및 출력 사용
> * 네트워크 인프라 만들기 및 배포
> * 가상 머신 확장 집합을 만들어 배포하고 네트워크에 연결
> * jumpbox를 만들어 배포하고 SSH를 통해 VM에 연결

> [!NOTE]
> 이 문서에서 사용되는 Terraform 구성 파일의 최신 버전은 [GitHub의 Awesome Terraform 리포지토리](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss)에 있습니다.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

- **Terraform 설치**: [Terraform 및 Azure에 액세스 구성](get-started-cloud-shell.md) 문서의 지침을 따릅니다.

- **SSH 키 쌍 만들기**: 자세한 내용은 [Azure에서 Linux VM용 SSH 공개 및 프라이빗 키 쌍을 만들고 사용하는 방법](/azure/virtual-machines/linux/mac-create-ssh-keys)을 참조하세요.

## <a name="create-the-directory-structure"></a>디렉터리 구조 만들기

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. [Azure Cloud Shell](/azure/cloud-shell/overview)을 엽니다. 이전에 환경을 선택하지 않은 경우 환경으로 **Bash**를 선택합니다.

    ![Cloud Shell 프롬프트](./media/create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. 디렉터리를 `clouddrive` 디렉터리로 변경합니다.

    ```bash
    cd clouddrive
    ```

1. 이름이 `vmss`인 디렉터리를 만듭니다.

    ```bash
    mkdir vmss
    ```

1. 디렉터리를 새 디렉터리로 변경합니다.

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>변수 정의 파일 만들기
이 섹션에서는 Terraform에서 만든 리소스를 사용자 지정하는 변수를 정의합니다.

Azure Cloud Shell 내에서 다음 단계를 수행합니다.

1. `variables.tf`라는 파일을 만듭니다.

    ```bash
    code variables.tf
    ```

1. 다음 코드를 편집기에 붙여 넣습니다.

   ```hcl
   variable "location" {
    description = "The location where resources will be created"
   }

   variable "tags" {
    description = "A map of the tags to use for the resources that are deployed"
    type        = map(string)

    default = {
      environment = "codelab"
    }
   }

   variable "resource_group_name" {
    description = "The name of the resource group in which the resources will be created"
    default     = "myResourceGroup"
   }
   ```

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

## <a name="create-the-output-definitions-file"></a>출력 정의 파일 만들기
이 섹션에서는 배포 후 출력을 설명하는 파일을 만듭니다.

Azure Cloud Shell 내에서 다음 단계를 수행합니다.

1. `output.tf`라는 파일을 만듭니다.

    ```bash
    code output.tf
    ```

1. 편집기에 다음 코드를 붙여넣어 가상 머신의 FQDN(정규화된 도메인 이름)을 표시합니다.
   :

   ```hcl
    output "vmss_public_ip" {
        value = azurerm_public_ip.vmss.fqdn
    }
   ```

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

## <a name="define-the-network-infrastructure-in-a-template"></a>템플릿에 네트워크 인프라 정의
이 섹션에서는 새 Azure 리소스 그룹에 다음과 같은 네트워크 인프라를 만듭니다.

  - 주소 공간이 10.0.0.0/16인 VNET(가상 네트워크) 1개
  - 주소 공간이 10.0.2.0/24인 서브넷 1개
  - 2개의 공용 IP 주소. 하나는 가상 머신 확장 집합 부하 분산 장치에서 사용되고 다른 하나는 SSH jumpbox에 연결하는 데 사용됩니다.

Azure Cloud Shell 내에서 다음 단계를 수행합니다.

1. `vmss.tf`라는 파일을 만들어 가상 머신 확장 집합 인프라를 설명합니다.

    ```bash
    code vmss.tf
    ```

1. 파일 끝에 다음 코드를 붙여넣어 가상 머신의 FQDN(정규화된 도메인 이름)을 표시합니다.

   ```hcl
   resource "azurerm_resource_group" "vmss" {
    name     = var.resource_group_name
    location = var.location
    tags     = var.tags
   }

   resource "random_string" "fqdn" {
    length  = 6
    special = false
    upper   = false
    number  = false
   }

   resource "azurerm_virtual_network" "vmss" {
    name                = "vmss-vnet"
    address_space       = ["10.0.0.0/16"]
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name
    tags                = var.tags
   }

   resource "azurerm_subnet" "vmss" {
    name                 = "vmss-subnet"
    resource_group_name  = azurerm_resource_group.vmss.name
    virtual_network_name = azurerm_virtual_network.vmss.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "vmss" {
    name                         = "vmss-public-ip"
    location                     = var.location
    resource_group_name          = azurerm_resource_group.vmss.name
    allocation_method = "Static"
    domain_name_label            = random_string.fqdn.result
    tags                         = var.tags
   }
   ```

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

## <a name="provision-the-network-infrastructure"></a>네트워크 인프라 프로비전
구성 파일(.tf)을 만든 디렉터리에서 Azure Cloud Shell을 사용하여 다음 단계를 수행합니다.

1. Terraform을 초기화합니다.

   ```bash
   terraform init
   ```

1. 다음 명령을 실행하여 Azure에 정의된 인프라를 배포합니다.

   ```bash
   terraform apply
   ```

   `location` 변수가 `variables.tf`에 정의되어 있으므로 Terraform에서 `location` 값을 묻는 메시지를 표시하지만 설정은 되지 않습니다. "West US"와 같은 유효한 위치를 입력한 다음 Enter를 선택할 수 있습니다. 공백이 포함된 모든 값 주위에 괄호를 사용합니다.

1. Terraform은 `output.tf` 파일에 정의된 대로 출력을 인쇄합니다. 다음 스크린샷과 같이 FQDN은 `<ID>.<location>.cloudapp.azure.com` 형식을 사용합니다. ID는 계산된 값이고 위치는 Terraform을 실행할 때 입력한 값입니다.

   ![가상 머신 확장 집합의 공용 IP 주소에 대한 정규화된 도메인 이름](./media/create-vm-scaleset-network-disks-hcl/fqdn.png)

1. Azure Portal의 주 메뉴에서 **리소스 그룹**을 선택합니다.

1. **리소스 그룹** 탭에서 **myResourceGroup**을 선택하여 Terraform에서 만든 리소스를 표시합니다.
   ![가상 머신 확장 집합 네트워크 리소스](./media/create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>가상 머신 확장 집합 추가

이 섹션에서는 다음 리소스를 템플릿에 추가하는 방법에 대해 알아봅니다.

- 하나의 Azure Load Balancer 및 애플리케이션을 제공하여 이 문서의 앞에서 구성된 공용 IP 주소에 연결하는 규칙
- Azure 백 엔드 주소 풀 및 이를 부하 분산 장치에 할당
- 애플리케이션에서 사용되고 부하 분산 장치에 구성된 상태 프로브 포트
- 이 문서의 앞부분에서 배포한 VNET에서 실행 중인, 부하 분산 장치 뒤에 있는 가상 머신 확장 집합
- [cloud-init](https://cloudinit.readthedocs.io/en/latest/)를 사용하는 가상 머신 확장의 노드에 있는 [Nginx](https://nginx.org/)

Cloud Shell에서 다음 단계를 수행합니다.

1. `vmss.tf` 구성 파일을 엽니다.

   ```bash
   code vmss.tf
   ```

1. 파일 끝으로 이동하고 A 키를 선택하여 추가 모드를 시작합니다.

1. 파일 끝에 다음 코드를 추가합니다.

   ```hcl
   resource "azurerm_lb" "vmss" {
    name                = "vmss-lb"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name

    frontend_ip_configuration {
      name                 = "PublicIPAddress"
      public_ip_address_id = azurerm_public_ip.vmss.id
    }

    tags = var.tags
   }

   resource "azurerm_lb_backend_address_pool" "bpepool" {
    resource_group_name = azurerm_resource_group.vmss.name
    loadbalancer_id     = azurerm_lb.vmss.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_lb_probe" "vmss" {
    resource_group_name = azurerm_resource_group.vmss.name
    loadbalancer_id     = azurerm_lb.vmss.id
    name                = "ssh-running-probe"
    port                = var.application_port
   }

   resource "azurerm_lb_rule" "lbnatrule" {
      resource_group_name            = azurerm_resource_group.vmss.name
      loadbalancer_id                = azurerm_lb.vmss.id
      name                           = "http"
      protocol                       = "Tcp"
      frontend_port                  = var.application_port
      backend_port                   = var.application_port
      backend_address_pool_id        = azurerm_lb_backend_address_pool.bpepool.id
      frontend_ip_configuration_name = "PublicIPAddress"
      probe_id                       = azurerm_lb_probe.vmss.id
   }

   resource "azurerm_virtual_machine_scale_set" "vmss" {
    name                = "vmscaleset"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name
    upgrade_policy_mode = "Manual"

    sku {
      name     = "Standard_DS1_v2"
      tier     = "Standard"
      capacity = 2
    }

    storage_profile_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_profile_os_disk {
      name              = ""
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    storage_profile_data_disk {
      lun          = 0
      caching        = "ReadWrite"
      create_option  = "Empty"
      disk_size_gb   = 10
    }

    os_profile {
      computer_name_prefix = "vmlab"
      admin_username       = var.admin_user
      admin_password       = var.admin_password
      custom_data          = file("web.conf")
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    network_profile {
      name    = "terraformnetworkprofile"
      primary = true

      ip_configuration {
        name                                   = "IPConfiguration"
        subnet_id                              = azurerm_subnet.vmss.id
        load_balancer_backend_address_pool_ids = [azurerm_lb_backend_address_pool.bpepool.id]
        primary = true
      }
    }

    tags = var.tags
   }
   ```

1. 파일을 저장하고 다음 명령을 입력하여 vi 편집기를 종료합니다.

    ```bash
    :wq
    ```

1. 확장 집합의 일부인 가상 머신의 cloud-init 구성 역할을 수행할 `web.conf` 파일을 만듭니다.

    ```bash
    code web.conf
    ```

1. 다음 코드를 편집기에 붙여 넣습니다.

   ```hcl
   #cloud-config
   packages:
    - nginx
   ```

1. 파일을 저장하고 다음 명령을 입력하여 vi 편집기를 종료합니다.

     ```bash
     :wq
     ```

1. `variables.tf` 구성 파일을 엽니다.

    ```bash
    code variables.tf
    ```

1. 파일 끝으로 이동하고 A 키를 선택하여 추가 모드를 시작합니다.

1. 파일 끝에 다음 코드를 붙여넣어 배포를 사용자 지정합니다.

    ```hcl
    variable "application_port" {
       description = "The port that you want to expose to the external load balancer"
       default     = 80
    }

    variable "admin_user" {
       description = "User name to use as the admin account on the VMs that will be part of the VM Scale Set"
       default     = "azureuser"
    }

    variable "admin_password" {
       description = "Default password for admin account"
    }
    ```

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

1. 가상 머신 확장 집합 배포를 시각화할 Terraform 계획을 만듭니다. (리소스의 암호와 위치를 지정해야 합니다.)

    ```bash
    terraform plan
    ```

    명령의 출력은 다음 스크린샷과 유사해야 합니다.

    ![가상 머신 확장 집합 만들기의 출력](./media/create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Azure에서 새 리소스를 배포합니다.

    ```bash
    terraform apply
    ```

    명령의 출력은 다음 스크린샷과 유사해야 합니다.

    ![Terraform 가상 머신 확장 집합 리소스 그룹](./media/create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. 브라우저를 열고 명령에 의해 반환된 FQDN에 연결합니다.

    ![FQDN 검색 결과](./media/create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>SSH jumpbox 추가
SSH *jumpbox*는 네트워크의 다른 서버에 액세스하기 위해 "점프"하는 단일 서버입니다. 이 단계에서는 다음 리소스를 구성합니다.

- 가상 머신 확장 집합과 동일한 서브넷에 연결된 네트워크 인터페이스(또는 Jumpbox)

- 이 네트워크 인터페이스와 연결된 가상 컴퓨터. 이 'jumpbox'는 원격으로 액세스할 수 있습니다. 연결되면 확장 집합의 가상 머신에 SSH를 추가할 수 있습니다.

1. `vmss.tf` 구성 파일을 엽니다.

   ```bash
   code vmss.tf
   ```

1. 파일 끝으로 이동하고 A 키를 선택하여 추가 모드를 시작합니다.

1. 파일 끝에 다음 코드를 추가합니다.

   ```hcl
   resource "azurerm_public_ip" "jumpbox" {
    name                         = "jumpbox-public-ip"
    location                     = var.location
    resource_group_name          = azurerm_resource_group.vmss.name
    allocation_method = "Static"
    domain_name_label            = "${random_string.fqdn.result}-ssh"
    tags                         = var.tags
   }

   resource "azurerm_network_interface" "jumpbox" {
    name                = "jumpbox-nic"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name

    ip_configuration {
      name                          = "IPConfiguration"
      subnet_id                     = azurerm_subnet.vmss.id
      private_ip_address_allocation = "dynamic"
      public_ip_address_id          = azurerm_public_ip.jumpbox.id
    }

    tags = var.tags
   }

   resource "azurerm_virtual_machine" "jumpbox" {
    name                  = "jumpbox"
    location              = var.location
    resource_group_name   = azurerm_resource_group.vmss.name
    network_interface_ids = [azurerm_network_interface.jumpbox.id]
    vm_size               = "Standard_DS1_v2"

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "jumpbox-osdisk"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    os_profile {
      computer_name  = "jumpbox"
      admin_username = var.admin_user
      admin_password = var.admin_password
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = var.tags
   }
   ```

1. `output.tf` 구성 파일을 엽니다.

   ```bash
   code output.tf
   ```

1. 파일 끝으로 이동하고 A 키를 선택하여 추가 모드를 시작합니다.

1. 파일 끝에 다음 코드를 붙여넣어 배포가 완료될 때 jumpbox의 호스트 이름을 표시합니다.

   ```hcl
   output "jumpbox_public_ip" {
      value = azurerm_public_ip.jumpbox.fqdn
   }
   ```

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

1. jumpbox를 배포합니다.

   ```bash
   terraform apply
   ```

배포가 완료된 후 리소스 그룹의 콘텐츠는 다음 스크린샷과 비슷합니다.

![Terraform 가상 머신 확장 집합 리소스 그룹](./media/create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> 배포한 가상 머신 확장 집합 및 jumpbox에서 암호를 사용하여 로그인하는 기능이 사용하지 않도록 설정되었습니다. 가상 머신에 액세스하려면 SSH를 사용하여 로그인합니다.

## <a name="environment-cleanup"></a>환경 정리

이 문서에서 만든 Terraform 리소스를 삭제하려면 Cloud Shell에서 다음 명령을 입력합니다.

```bash
terraform destroy
```

소멸 프로세스를 완료하는 데 몇 분 정도가 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure에서 Terraform을 사용하는 방법에 대해 자세히 알아보기](/azure/terraform)
