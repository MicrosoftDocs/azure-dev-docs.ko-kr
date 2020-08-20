---
title: Terraform을 사용하여 Azure에서 인프라를 갖춘 Linux VM 만들기
description: Terraform을 사용하여 Azure에서 완전한 Linux 가상 머신 환경을 만들고 관리하는 방법을 알아봅니다.
keywords: azure devops terraform linux vm 가상 머신
ms.topic: how-to
ms.date: 06/14/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 387f53b9fb1a78a66b9628346564132002b77fb0
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88241225"
---
# <a name="create-a-linux-vm-with-infrastructure-in-azure-using-terraform"></a>Terraform을 사용하여 Azure에서 인프라를 갖춘 Linux VM 만들기

Terraform을 사용하면 Azure에서 완전한 인프라를 정의하고 만들 수 있습니다. 일관되고 재현 가능한 방식으로 Azure 리소스를 만들고 구성하는 Terraform 템플릿을 이해하기 쉬운 형태로 빌드할 수 있습니다. 이 문서에서는 Terraform을 사용하여 전체 Linux 환경 및 지원 리소스를 만드는 방법을 보여 줍니다. [Terraform 설치 및 구성](get-started-cloud-shell.md) 방법도 알아봅니다.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="create-azure-connection-and-resource-group"></a>Azure 연결 및 리소스 그룹 만들기

Terraform 템플릿의 각 섹션을 살펴보겠습니다. 복사하여 붙여넣을 수 있는 [Terraform 템플릿](#complete-terraform-script)의 전체 버전을 확인할 수 있습니다.

`provider` 섹션은 Azure 공급자를 사용하도록 Terraform에 알립니다. `subscription_id`, `client_id`, `client_secret` 및 `tenant_id`에 대한 값을 가져오려면 [Terraform 설치 및 구성](get-started-cloud-shell.md)을 참조하세요.

> [!TIP]
> 값에 대한 환경 변수를 만들거나 [Azure Cloud Shell Bash 환경](/azure/cloud-shell/overview)을 사용하는 경우 이 섹션에서 변수 선언을 포함시킬 필요가 없습니다.

```hcl
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x.
    # If you're using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
}
```

다음 섹션에서는 `eastus` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

추가 섹션에서 `azurerm_resource_group.myterraformgroup.name`을 사용하여 리소스 그룹을 참조합니다.

## <a name="create-virtual-network"></a>가상 네트워크 만들기

다음 섹션에서는 `10.0.0.0/16` 주소 공간에 `myVnet`이라는 가상 네트워크를 만듭니다.

```hcl
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}
```

다음 섹션에서는 `myVnet` 가상 네트워크에 `mySubnet`이라는 서브넷을 만듭니다.

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefixes       = ["10.0.2.0/24"]
}
```

## <a name="create-public-ip-address"></a>공용 IP 주소 만들기

인터넷을 통해 리소스에 액세스하려면 공용 IP 주소를 만들어 VM에 할당합니다. 다음 섹션에서는 `myPublicIP`라는 공용 IP 주소를 만듭니다.

```hcl
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="create-network-security-group"></a>네트워크 보안 그룹 만들기

네트워크 보안 그룹은 VM 내/외부 네트워크 트래픽의 흐름을 제어합니다. 다음 섹션에서는 `myNetworkSecurityGroup`이라는 네트워크 보안 그룹을 만들고 TCP 포트 22에서 SSH 트래픽을 허용하는 규칙을 정의합니다.

```hcl
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
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
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>가상 네트워크 인터페이스 카드 만들기

가상 NIC(네트워크 인터페이스 카드)는 지정된 가상 네트워크, 공용 IP 주소 및 네트워크 보안 그룹에 VM을 연결합니다. Terraform 템플릿의 다음 섹션에서는 사용자가 만든 가상 네트워킹 리소스에 연결된 `myNIC`라는 가상 NIC를 만듭니다.

```hcl
resource "azurerm_network_interface" "myterraformnic" {
    name                        = "myNIC"
    location                    = "eastus"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = azurerm_subnet.myterraformsubnet.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.myterraformpublicip.id
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}
```

## <a name="create-storage-account-for-diagnostics"></a>진단을 위한 스토리지 계정 만들기

VM을 위한 부트 진단을 저장하려면 스토리지 계정이 필요합니다. 이러한 부트 진단은 문제를 해결하고 VM의 상태를 모니터링하는 데 도움을 줄 수 있습니다. 사용자가 만든 스토리지 계정은 부팅 진단 데이터를 저장하기 위한 것입니다. 각 스토리지 계정에는 고유한 이름이 있어야 합니다. 다음 섹션에서는 일부 임의 텍스트를 생성합니다.

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}
```

이제 스토리지 계정을 만들 수 있습니다. 다음 섹션에서는 이전 단계에서 생성되는 임의의 텍스트에 따른 이름으로 스토리지 계정을 만듭니다.

```hcl
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_replication_type    = "LRS"
    account_tier                = "Standard"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>가상 머신 만들기

마지막 단계에서는 VM을 만들고 생성한 모든 리소스를 사용합니다. 다음 섹션에서는 `myVM`이라는 VM을 만들고 `myNIC`라는 가상 NIC를 연결합니다. 최신 `Ubuntu 16.04-LTS` 이미지를 사용하고, 암호 인증을 사용하지 않도록 설정된 `azureuser`라는 사용자가 만들어집니다.

 SSH 키 데이터는 `ssh_keys` 섹션에서 제공됩니다. `key_data` 필드에 공용 SSH 키를 제공합니다.

```hcl
resource "tls_private_key" "example_ssh" {
  algorithm = "RSA"
  rsa_bits = 4096
}

output "tls_private_key" { value = "tls_private_key.example_ssh.private_key_pem" }

resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = tls_private_key.example_ssh.public_key_openssh
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>전체 Terraform 스크립트

이러한 모든 섹션을 함께 가져오거나 작동 중인 Terraform을 보려면 `terraform_azure.tf`라는 파일을 만들고 다음 콘텐츠를 붙여 넣습니다.

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you're using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
}

# Create a resource group if it doesn't exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefixes       = ["10.0.1.0/24"]
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
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
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = azurerm_resource_group.myterraformgroup.name

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = azurerm_subnet.myterraformsubnet.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.myterraformpublicip.id
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create (and display) an SSH key
resource "tls_private_key" "example_ssh" {
  algorithm = "RSA"
  rsa_bits = 4096
}
output "tls_private_key" { value = "${tls_private_key.example_ssh.private_key_pem}" }

# Create virtual machine
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = tls_private_key.example_ssh.public_key_openssh
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="build-and-deploy-the-infrastructure"></a>인프라 빌드 및 배포

만든 Terraform 템플릿을 사용한 첫 번째 단계는 Terraform을 초기화하는 것입니다. 이 단계를 사용하면 Azure에서 템플릿을 빌드하기 위한 모든 필수 구성 요소를 Terraform에 갖출 수 있습니다.

```bash
terraform init
```

다음 단계는 Terraform에서 템플릿을 검토하고 유효성을 검사하는 것입니다. 이 단계는 요청된 리소스를 Terraform에서 저장한 상태 정보와 비교한 후, 계획된 실행을 출력합니다. 이 시점에서 Azure 리소스가 생성되지 않습니다.

```bash
terraform plan
```

이전 명령을 실행한 후에 다음과 같은 화면이 표시되어야 합니다.

```console
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn't specify an "-out" parameter to save this plan, so when
"apply" is called, Terraform can't guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

모든 것이 제대로 표시되고 Azure에서 인프라를 빌드할 준비가 되면 Terraform에 템플릿을 적용합니다.

```bash
terraform apply
```

Terraform이 완료되면 VM 인프라가 준비됩니다. [az vm show](/cli/azure/vm#az-vm-show)를 사용하여 VM의 공용 IP 주소를 가져옵니다.

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] -o tsv
```

그런 다음 VM에 SSH할 수 있습니다.

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure에서 Terraform을 사용하는 방법에 대해 자세히 알아보기](/azure/terraform)