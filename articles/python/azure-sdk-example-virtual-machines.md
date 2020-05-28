---
title: Python용 Azure SDK를 사용하여 가상 머신 프로비전
description: Python 및 Azure SDK 관리 라이브러리를 사용하여 Azure 가상 머신을 프로비저닝하는 방법입니다.
ms.date: 05/12/2020
ms.topic: conceptual
ms.openlocfilehash: f21495cc42f3bb228e460f1c591c9aa037dd8123
ms.sourcegitcommit: 9330d5af796b4b114466bbe75b8e18a9206f218e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83862786"
---
# <a name="example-use-the-azure-sdk-to-provision-a-virtual-machine"></a>예제: Azure SDK를 사용하여 가상 머신 프로비전

이 예제에서는 Python 스크립트에서 Azure SDK 관리 라이브러리를 사용하여 Linux 가상 머신이 포함된 리소스 그룹을 만드는 방법을 보여줍니다.

가상 머신에는 관리 인터페이스만 있으므로 이 예제에는 클라이언트 라이브러리가 없습니다.

이 문서의 모든 명령은 언급되지 않는 한 Linux/Mac OS bash 및 Windows 명령 셸에서 동일하게 작동합니다.

> [!NOTE]
> 코드를 통해 가상 머신을 프로비저닝하는 작업은 가상 머신에 필요한 여러 가지 다른 리소스의 프로비전을 포함하는 여러 단계의 프로세스입니다. 명령줄에서 이러한 코드를 실행하는 경우 [`az vm create`](/cli/azure/vm?view=azure-cli-latest#az-vm-create) 명령을 사용하는 것이 훨씬 쉽습니다. 이 명령은 생략하도록 선택한 설정에 대해 이러한 보조 리소스를 기본값으로 자동 프로비저닝합니다. 필요한 인수는 리소스 그룹, VM 이름, 이미지 이름 및 로그인 자격 증명뿐입니다. 자세한 내용은 [Azure CLI를 사용하여 가상 머신 빠른 생성](/azure/virtual-machines/scripts/virtual-machines-windows-cli-sample-create-vm-quick-create)을 참조하세요.

## <a name="1-set-up-your-local-development-environment"></a>1: 로컬 개발 환경 설정

아직 수행하지 않은 경우 [Azure에 대한 로컬 Python 개발 환경 구성](configure-local-development-environment.md)의 모든 지침을 따르세요.

로컬 개발을 위한 서비스 주체를 만들고 이 프로젝트의 가상 환경을 만들어 활성화해야 합니다.

## <a name="2-install-the-needed-sdk-libraries"></a>2: 필요한 SDK 라이브러리 설치

1. 이 예제에서 사용된 관리 라이브러리를 나열하는 *requirements.txt* 파일을 만듭니다.

    ```txt
    azure-mgmt-resource
    azure-mgmt-network
    azure-mgmt-compute
    azure-cli-core
    ```

1. 가상 환경이 활성화된 터미널 또는 명령 프롬프트에서 *requirements.txt*에 나열된 관리 라이브러리를 설치합니다.

    ```bash
    pip install -r requirements.txt
    ```

## <a name="3-write-code-to-provision-a-virtual-machine"></a>3: 가상 머신을 프로비저닝하는 코드 작성

다음 코드를 사용하여 *provision_vm.py*라는 Python 파일을 만듭니다. 주석은 세부 정보를 설명합니다.

```python
# Import the needed management objects from the libraries. The azure.common library
# is installed automatically with the other libraries.
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.network import NetworkManagementClient
from azure.mgmt.compute import ComputeManagementClient

print(f"Provisioning a virtual machine...some operations might take a minute or two.")

# Step 1: Provision a resource group

# Obtain the management object for resources, using the credentials from the CLI login.
resource_client = get_client_from_cli_profile(ResourceManagementClient)

# Constants we need in multiple places: the resource group name and the region
# in which we provision resources. You can change these values however you want.
RESOURCE_GROUP_NAME = "PythonSDKExample-VM-rg"
LOCATION = "centralus"

# Provision the resource group.
rg_result = resource_client.resource_groups.create_or_update(RESOURCE_GROUP_NAME,
    {
        "location": LOCATION
    }
)


print(f"Provisioned resource group {rg_result.name} in the {rg_result.location} region")

# For details on the previous code, see Example: Provision a resource group
# at https://docs.microsoft.com/azure/developer/python/azure-sdk-example-resource-group

# Step 2: provision a virtual network

# A virtual machine requires a network interface client (NIC). A NIC requires
# a virtual network and subnet along with an IP address. Therefore we must provision
# these downstream components first, then provision the NIC, after which we
# can provision the VM.

# Network and IP address names
VNET_NAME = "python-example-vnet"
SUBNET_NAME = "python-example-subnet"
IP_NAME = "python-example-ip"
IP_CONFIG_NAME = "python-example-ip-config"
NIC_NAME = "python-example-nic"

# Obtain the management object for networks
network_client = get_client_from_cli_profile(NetworkManagementClient)

# Provision the virtual network and wait for completion
poller = network_client.virtual_networks.create_or_update(RESOURCE_GROUP_NAME,
    VNET_NAME,
    {
        "location": LOCATION,
        "address_space": {
            "address_prefixes": ["10.0.0.0/16"]
        }
    }
)

vnet_result = poller.result()

print(f"Provisioned virtual network {vnet_result.name} with address prefixes {vnet_result.address_space.address_prefixes}")

# Step 3: Provision the subnet and wait for completion
poller = network_client.subnets.create_or_update(RESOURCE_GROUP_NAME, 
    VNET_NAME, SUBNET_NAME,
    { "address_prefix": "10.0.0.0/24" }
)
subnet_result = poller.result()

print(f"Provisioned virtual subnet {subnet_result.name} with address prefix {subnet_result.address_prefix}")

# Step 4: Provision an IP address and wait for completion
poller = network_client.public_ip_addresses.create_or_update(RESOURCE_GROUP_NAME,
    IP_NAME,
    {
        "location": LOCATION,
        "sku": { "name": "Standard" },
        "public_ip_allocation_method": "Static",
        "public_ip_address_version" : "IPV4"
    }
)

ip_address_result = poller.result()

print(f"Provisioned public IP address {ip_address_result.name} with address {ip_address_result.ip_address}")

# Step 5: Provision the network interface client
poller = network_client.network_interfaces.create_or_update(RESOURCE_GROUP_NAME,
    NIC_NAME, 
    {
        "location": LOCATION,
        "ip_configurations": [ {
            "name": IP_CONFIG_NAME,
            "subnet": { "id": subnet_result.id },
            "public_ip_address": {"id": ip_address_result.id }
        }]
    }
)

nic_result = poller.result()

print(f"Provisioned network interface client {nic_result.name}")

# Step 6: Provision the virtual machine

# Obtain the management object for virtual machines
compute_client = get_client_from_cli_profile(ComputeManagementClient)

VM_NAME = "ExampleVM"
USERNAME = "azureuser"
PASSWORD = "ChangePa$$w0rd24"

print(f"Provisioning virtual machine {VM_NAME}; this operation might take a few minutes.")

# Provision the VM specifying only minimal arguments, which defaults to an Ubuntu 18.04 VM
# on a Standard DS1 v2 plan with a public IP address and a default virtual network/subnet.

poller = compute_client.virtual_machines.create_or_update(RESOURCE_GROUP_NAME, VM_NAME,
    {
        "location": LOCATION,
        "storage_profile": {
            "image_reference": {
                "publisher": 'Canonical',
                "offer": "UbuntuServer",
                "sku": "16.04.0-LTS",
                "version": "latest"
            }
        },
        "hardware_profile": {
            "vm_size": "Standard_DS1_v2"
        },
        "os_profile": {
            "computer_name": VM_NAME,
            "admin_username": USERNAME,
            "admin_password": PASSWORD
        },
        "network_profile": {
            "network_interfaces": [{
                "id": nic_result.id,
            }]
        }
    }
)

vm_result = poller.result()

print(f"Provisioned virtual machine {vm_result.name}")
```

이 코드는 Azure CLI에서 직접 수행할 수 있는 작업을 보여주므로 CLI 기반 인증 메서드(`get_client_from_cli_profile`)를 사용합니다. 두 경우 모두 인증에 동일한 ID를 사용합니다.

프로덕션 스크립트에서 이러한 코드를 사용하려면(예: VM 관리를 자동화하려면) [Azure 서비스로 Python 앱을 인증하는 방법](azure-sdk-authenticate.md)에 설명된 대로 `DefaultAzureCredential`(권장) 또는 서비스 주체 기반 메서드를 사용합니다.

## <a name="4-run-the-script"></a>4. 스크립트 실행

```bash
python provision_vm.py
```

프로비저닝 프로세스를 완료하는 데 몇 분 정도가 소요됩니다.

## <a name="5-verify-the-resources"></a>5. 리소스 확인

[Azure Portal](https://portal.azure.com)을 열고 "PythonSDKExample-VM-rg" 리소스 그룹으로 이동하여 가상 머신, 가상 디스크, 네트워크 보안 그룹, 공용 IP 주소, 네트워크 인터페이스 및 가상 네트워크를 확인합니다.

![가상 머신 및 관련 리소스를 표시하는 새 리소스 그룹의 Azure Portal 페이지](media/azure-sdk-example-virtual-machines/portal-vm-resources.png)

### <a name="for-reference-equivalent-azure-cli-commands"></a>참조용: 해당 Azure CLI 명령

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
# Provision the resource group

az group create -n PythonSDKExample-VM-rg -l centralus

# Provision a virtual network and subnet

az network vnet create -g PythonSDKExample-VM-rg -n python-example-vnet \
    --address-prefix 10.0.0.0/16 --subnet-name python-example-subnet \
    --subnet-prefix 10.0.0.0/24

# Provision a public IP address

az network public-ip create -g PythonSDKExample-VM-rg -n python-example-ip \
    --allocation-method Dynamic --version IPv4

# Provision a network interface client

az network nic create -g PythonSDKExample-VM-rg --vnet-name python-example-vnet \
    --subnet python-example-subnet -n python-example-nic \
    --public-ip-address python-example-ip

# Provision the virtual machine

az vm create -g PythonSDKExample-VM-rg -n ExampleVM -l "centralus" \
    --nics python-example-nic --image UbuntuLTS \
    --admin-username azureuser --admin-password ChangePa$$w0rd24

```

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
# Provision the resource group

az group create -n PythonSDKExample-VM-rg -l centralus

# Provision a virtual network and subnet

az network vnet create -g PythonSDKExample-VM-rg -n python-example-vnet ^
    --address-prefix 10.0.0.0/16 --subnet-name python-example-subnet ^
    --subnet-prefix 10.0.0.0/24

# Provision a public IP address

az network public-ip create -g PythonSDKExample-VM-rg -n python-example-ip ^
    --allocation-method Dynamic --version IPv4

# Provision a network interface client

az network nic create -g PythonSDKExample-VM-rg --vnet-name python-example-vnet ^
    --subnet python-example-subnet -n python-example-nic ^
    --public-ip-address python-example-ip

# Provision the virtual machine

az vm create -g PythonSDKExample-VM-rg -n ExampleVM -l "centralus" ^
    --nics python-example-nic --image UbuntuLTS ^
    --admin-username azureuser --admin-password ChangePa$$w0rd24
```

---

## <a name="6-clean-up-resources"></a>6: 리소스 정리

```azurecli
az group delete -n PythonSDKExample-VM-rg
```

이 예제에서 생성된 리소스를 유지할 필요가 없으며 구독에서 지속적인 요금을 방지하려면 이 명령을 실행합니다.

## <a name="see-also"></a>참고 항목

다음 리소스 컨테이너는 Python을 사용하여 가상 머신을 만드는 보다 포괄적인 예제입니다.

- [Python을 사용하여 Azure에서 Windows VM을 만들고 관리합니다](/azure/virtual-machines/windows/python). 이 예제를 통해 `storage_profile` 매개 변수를 변경하여 Linux VM을 만들 수 있습니다.
- [SAzure Virtual Machines 관리 샘플 - Python](https://github.com/Azure-Samples/virtual-machines-python-manage)(GitHub). 이 샘플에서는 VM 시작 및 다시 시작, VM 중지 및 삭제, 디스크 크기 증가, 데이터 디스크 관리 등의 추가 관리 작업을 보여줍니다.
