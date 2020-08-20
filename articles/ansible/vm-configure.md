---
title: 빠른 시작 - Ansible을 사용하여 Azure에서 Linux 가상 머신 구성
description: 이 빠른 시작에서는 Ansible을 사용하여 Azure에서 Linux 가상 머신을 만드는 방법을 알아봅니다.
keywords: ansible, azure, devops, 가상 머신
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: c9f6d9a954b8bdbd05ede42cc69a0078eae3cea2
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240435"
---
# <a name="quickstart-configure-linux-virtual-machines-in-azure-using-ansible"></a>빠른 시작: Ansible을 사용하여 Azure에서 Linux 가상 머신 구성

선언적 언어인 Ansible을 사용하면 Ansible *플레이북*을 통해 자동으로 Azure 리소스를 만들고, 구성하고, 배포할 수 있습니다. 이 문서에서는 Linux 가상 머신을 구성하기 위한 샘플 Ansible 플레이북을 제공합니다. [전체 Ansible 플레이북](#complete-sample-ansible-playbook)은 이 문서의 마지막 부분에 나열되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)]

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Ansible을 사용하려면 리소스가 배포되는 그룹이 필요합니다. 다음 샘플 Ansible 플레이북 섹션에서는 `eastus` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```yaml
- name: Create resource group
  azure_rm_resourcegroup:
    name: myResourceGroup
    location: eastus
```

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

Azure 가상 머신을 만들 때 [가상 네트워크](/azure/virtual-network/virtual-networks-overview)를 만들거나 기존 가상 네트워크를 사용해야 합니다. 또한 가상 네트워크에서 가상 머신에 액세스하는 방법도 결정해야 합니다. 다음 샘플 Ansible 플레이북 섹션에서는 `10.0.0.0/16` 주소 공간에 `myVnet`이라는 가상 네트워크를 만듭니다.

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

가상 네트워크에 배포된 모든 Azure 리소스는 가상 네트워크 내의 [서브넷](/azure/virtual-network/virtual-network-manage-subnet)에 배포됩니다. 

다음 샘플 Ansible 플레이북 섹션에서는 `myVnet` 가상 네트워크에 `mySubnet`이라는 가상 네트워크를 만듭니다.

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```

## <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기





[공용 IP 주소](/azure/virtual-network/virtual-network-ip-addresses-overview-arm)를 통해 인터넷 리소스가 Azure 리소스에 대한 인바운드와 통신할 수 있습니다. 또한 공용 IP 주소를 사용하면 Azure 리소스에서 공용 Azure 서비스에 아웃바운드로 통신할 수 있습니다. 두 시나리오 모두에서 액세스하려는 리소스에 IP 주소가 할당되어 있습니다. 이 주소는 사용자가 할당을 취소할 때까지 해당 리소스에만 전용됩니다. 공용 IP 주소가 리소스에 할당되지 않아도 리소스는 여전히 인터넷에 아웃바운드로 통신할 수 있습니다. Azure에서는 연결이 설정되고, 사용 가능한 IP 주소가 동적으로 할당됩니다. 동적으로 할당된 주소는 해당 리소스에만 전용되지 않습니다.

다음 샘플 Ansible 플레이북 섹션에서는 `myPublicIP`라는 공용 IP 주소를 만듭니다.

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

[네트워크 보안 그룹](/azure/virtual-network/security-overview)은 가상 네트워크의 Azure 리소스 간 네트워크 트래픽을 필터링합니다. Azure 리소스에서 나오는 아웃바운드 트래픽과 Azure 리소스로 들어오는 인바운드 트래픽을 제어하는 보안 규칙이 정의됩니다. Azure 리소스 및 네트워크 보안 그룹에 대한 자세한 내용은 [Azure 서비스의 가상 네트워크 통합](/azure/virtual-network/virtual-network-for-azure-services)을 참조하세요.

다음 플레이북에서는 `myNetworkSecurityGroup`이라는 네트워크 보안 그룹을 만듭니다. 네트워크 보안 그룹에는 TCP 포트 22에서 SSH 트래픽을 허용하는 규칙이 포함됩니다.

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: Tcp
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```

## <a name="create-a-virtual-network-interface-card"></a>가상 네트워크 인터페이스 카드 만들기

가상 네트워크 인터페이스 카드는 지정된 가상 네트워크, 공용 IP 주소 및 네트워크 보안 그룹에 가상 머신을 연결합니다. 

다음 샘플 Ansible 플레이북 섹션에서는 사용자가 만든 가상 네트워킹 리소스에 연결되는 `myNIC`라는 가상 네트워크 인터페이스 카드를 만듭니다.

```yaml
- name: Create virtual network interface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

마지막 단계는 이 문서의 이전 섹션에서 만든 모든 리소스를 사용하는 가상 머신을 만드는 것입니다. 

이 섹션에 제공된 샘플 Ansible 플레이북 섹션에서는 `myVM`이라는 가상 머신을 만들어서 `myNIC`라는 가상 네트워크 인터페이스 카드에 연결합니다. &lt;your-key-data> 자리 표시자를 본인의 완전한 공개 키 데이터로 바꿉니다.

```yaml
- name: Create VM
  azure_rm_virtualmachine:
    resource_group: myResourceGroup
    name: myVM
    vm_size: Standard_DS1_v2
    admin_username: azureuser
    ssh_password_enabled: false
    ssh_public_keys:
      - path: /home/azureuser/.ssh/authorized_keys
        key_data: <your-key-data>
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.5'
      version: latest
```

## <a name="complete-sample-ansible-playbook"></a>전체 샘플 Ansible 플레이북

이 섹션에서는 이 문서의 과정에서 작성한 전체 샘플 Ansible 플레이북을 나열합니다. 

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: myResourceGroup
      location: eastus
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
    register: output_ip_address
  - name: Dump public IP for VM which will be created
    debug:
      msg: "The public IP is {{ output_ip_address.state.ip_address }}."
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network interface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys:
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: <your-key-data>
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

## <a name="run-the-sample-ansible-playbook"></a>샘플 Ansible 플레이북 실행

이 섹션에서는 이 문서에 제공된 샘플 Ansible 플레이북을 실행하는 방법을 안내합니다.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. [Cloud Shell](/azure/cloud-shell/overview)을 엽니다.

1. 다음과 같이 플레이북을 포함할 `azure_create_complete_vm.yml`이라는 파일을 만들고 VI 편집기에서 엽니다.

   ```bash
   vi azure_create_complete_vm.yml
   ```

1. **I** 키를 선택하여 삽입 모드를시작합니다.

1. 편집기에 [전체 샘플 Ansible 플레이북](#complete-sample-ansible-playbook)을 붙여넣습니다.

1. **Esc** 키를 선택하여 삽입 모드를 종료합니다.

1. 파일을 저장하고 다음 명령을 입력하여 vi 편집기를 종료합니다.

    ```bash
    :wq
    ```

1. 샘플 Ansible 플레이북을 실행합니다.

   ```bash
   ansible-playbook azure_create_complete_vm.yml
   ```

1. 출력은 가상 머신이 성공적으로 만들어졌음을 보여주는 다음 예제와 유사합니다.

   ```bash
   PLAY [Create Azure VM] ****************************************************

   TASK [Gathering Facts] ****************************************************
   ok: [localhost]

   TASK [Create resource group] *********************************************
   changed: [localhost]

   TASK [Create virtual network] *********************************************
   changed: [localhost]

   TASK [Add subnet] *********************************************************
   changed: [localhost]

   TASK [Create public IP address] *******************************************
   changed: [localhost]

   TASK [Dump public IP for VM which will be created] ********************************************************************
   ok: [localhost] => {
      "msg": "The public IP is <ip-address>."
   }

   TASK [Create Network Security Group that allows SSH] **********************
   changed: [localhost]

   TASK [Create virtual network interface card] *******************************
   changed: [localhost]

   TASK [Create VM] **********************************************************
   changed: [localhost]

   PLAY RECAP ****************************************************************
   localhost                  : ok=8    changed=7    unreachable=0    failed=0
   ```

1. SSH 명령은 Linux VM에 액세스하는 데 사용됩니다. &lt;ip-address> 자리 표시자를 이전 단계의 IP 주소로 바꿉니다.

    ```bash
    ssh azureuser@<ip-address>
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [빠른 시작: Azure에서 Ansible을 사용하여 Linux 가상 머신 관리](./vm-manage.md)
