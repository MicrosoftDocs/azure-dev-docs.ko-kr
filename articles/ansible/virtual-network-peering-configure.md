---
title: 자습서 - Ansible을 사용하여 Azure 가상 네트워크 피어링 구성
description: Ansible을 사용하여 가상 네트워크 피어링으로 가상 네트워크를 연결하는 방법을 알아봅니다.
keywords: ansible, azure, devops, bash, playbook, networking, peering
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 747b11c9727e0844ac9d9c7b07a8355e8163c75e
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90681957"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>자습서: Ansible을 사용하여 Azure Virtual Network 피어링 구성

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[VNet(가상 네트워크) 피어링](/azure/virtual-network/virtual-network-peering-overview)을 사용하여 두 개의 Azure Virtual Network를 원활하게 연결할 수 있습니다. 연결하기 위해 두 가상 네트워크가 피어링되면 하나로 표시됩니다. 

트래픽은 개인 IP 주소를 통해 동일한 가상 네트워크의 VM 간에 라우팅됩니다. 마찬가지로, 피어링된 가상 네트워크에 있는 VM 간 트래픽은 Microsoft 백본 인프라를 통해 라우팅됩니다. 결과적으로, 다른 가상 네트워크의 VM은 서로 통신할 수 있습니다.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 두 가상 네트워크 만들기
> * 2개 가상 네트워크 피어링
> * 두 네트워크 간에 피어링 삭제

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>두 리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

이 섹션의 샘플 플레이북 코드는 다음에 사용됩니다.

- 두 리소스 그룹 만들기 

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
```

## <a name="create-the-first-virtual-network"></a>첫 번째 가상 네트워크 만들기

이 섹션의 샘플 플레이북 코드는 다음에 사용됩니다.

- 가상 네트워크 만들기
- 가상 네트워크 내에서 서브넷 만들기

```yml
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
```

## <a name="create-the-second-virtual-network"></a>두 번째 가상 네트워크 만들기

이 섹션의 샘플 플레이북 코드는 다음에 사용됩니다.

- 가상 네트워크 만들기
- 가상 네트워크 내에서 서브넷 만들기

```yml
  - name: Create second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
```

## <a name="peer-the-two-virtual-networks"></a>2개 가상 네트워크 피어링

이 섹션의 샘플 플레이북 코드는 다음에 사용됩니다.

- 가상 네트워크 피어링 초기화
- 이전에 만든 두 가상 네트워크 피어링

```yml
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true
```

## <a name="delete-the-virtual-network-peering"></a>가상 네트워크 피어링 삭제

이 섹션의 샘플 플레이북 코드는 다음에 사용됩니다.

- 이전에 만든 가상 네트워크 간의 피어링 삭제

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>샘플 플레이북 가져오기

전체 샘플 플레이북을 가져오는 방법에는 다음 두 가지가 있습니다.

- [플레이북을 다운로드](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml)하여 `vnet_peering.yml`에 저장합니다.
- `vnet_peering.yml`이라는 새 파일을 만들고 다음 콘텐츠를 이 파일에 복사합니다.

```yml
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- name: Connect virtual networks with virtual network peering
  hosts: localhost
  connection: local
  vars:
    resource_group: "{{ resource_group_name }}"
    resource_group_secondary: "{{ resource_group_name }}2"
    vnet_name1: "myVnet{{ rpfx }}"
    vnet_name2: "myVnet{{ rpfx }}2"
    peering_name: peer1
    location: eastus2
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
  - name: Create second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="run-the-sample-playbook"></a>샘플 플레이북 실행

이 섹션의 샘플 플레이북 코드는 이 자습서 전체에 나오는 다양한 기능을 테스트하는 데 사용됩니다.

다음은 샘플 플레이북을 사용할 때 고려할 몇 가지 주요 참고 사항입니다.

- `vars` 섹션에서 `{{ resource_group_name }}` 자리 표시자를 리소스 그룹의 이름으로 바꿉니다.

다음과 같이 ansible-playbook 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook vnet_peering.yml
```

플레이북을 실행하면 다음 결과와 유사한 출력이 표시됩니다.

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Prepare random postfix] 
ok: [localhost]

PLAY [Connect virtual networks with virtual network peering] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create secondary resource group] 
changed: [localhost]

TASK [Create first virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Create second virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Initial vnet peering] 
changed: [localhost]

TASK [Connect vnet peering] 
changed: [localhost]

TASK [Delete vnet peering] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=12   changed=9    unreachable=0    failed=0    skipped=0   rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 이 문서에서 만든 리소스를 삭제합니다.    

이 섹션의 샘플 플레이북 코드는 다음에 사용됩니다.    

- 이전에 만든 두 리소스 그룹 삭제   

다음 플레이북을 `cleanup.yml`로 저장합니다.   

```bash 
- hosts: localhost  
  vars: 
    resource_group: "{{ resource_group_name-1 }}"   
    resource_group_secondary: "{{ resource_group_name-2 }}" 
  tasks:    
    - name: Delete a resource group 
      azure_rm_resourcegroup:   
        name: "{{ resource_group }}"    
        force_delete_nonempty: yes  
        state: absent   
    - name: Delete a resource group 
      azure_rm_resourcegroup:   
        name: "{{ resource_group_secondary }}"  
        force_delete_nonempty: yes  
        state: absent   
``` 

다음은 샘플 플레이북을 사용할 때 고려할 몇 가지 주요 참고 사항입니다.  

- `{{ resource_group_name-1 }}` 자리 표시자를 처음 만든 리소스 그룹의 이름으로 바꿉니다.  
- `{{ resource_group_name-2 }}` 자리 표시자를 두 번째로 만든 리소스 그룹의 이름으로 바꿉니다. 
- 지정된 두 리소스 그룹 내의 모든 리소스가 삭제됩니다.   

다음과 같이 ansible-playbook 명령을 사용하여 플레이북을 실행합니다.    

```bash 
ansible-playbook cleanup.yml    
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure의 Ansible](/azure/ansible/)