---
title: 자습서 - Ansible을 사용하여 Azure 경로 테이블 구성
description: Ansible을 사용하여 Azure 경로 테이블을 생성, 관리 및 삭제하는 방법을 알아봅니다. 또한 경로를 만들고 삭제하는 방법에 대해 알아봅니다.
keywords: Ansible, Azure, DevOps, Bash, 플레이북, 네트워킹, 경로, 경로 테이블
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 28302e5e8fc6a46193d96c791080797559a566e9
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240025"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>자습서: Ansible을 사용하여 Azure 경로 테이블 구성

[!INCLUDE [ansible-27-note.md](includes/ansible-28-note.md)]

Azure는 Azure 서브넷, 가상 네트워크 및 온-프레미스 네트워크 간에 트래픽을 자동으로 라우트합니다. 환경의 라우팅을 보다 강력하게 제어해야 할 경우 [경로 테이블](/azure/virtual-network/virtual-networks-udr-overview)을 만들 수 있습니다. 

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> 경로 테이블 만들기 가상 네트워크 및 서브넷 만들기 경로 테이블을 서브넷에 연결 서브넷에서 경로 테이블 분리 경로 삭제 경로 테이블 쿼리 경로 테이블 삭제

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>경로 테이블 만들기

이 섹션의 플레이북 코드는 경로 테이블을 만듭니다. 경로 테이블 제한에 대한 내용은 [Azure 제한](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits)을 참조하세요. 

다음 플레이북을 `route_table_create.yml`로 저장합니다.

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>서브넷에 경로 테이블 연결

이 섹션의 플레이북 코드:

* 가상 네트워크를 만듭니다.
* 가상 네트워크 내에서 서브넷 만들기
* 서브넷에 경로 테이블 연결

경로 테이블은 가상 네트워크에 연결되지 않습니다. 대신, 경로 테이블은 가상 네트워크의 서브넷에 연결됩니다.

가상 네트워크 및 라우팅 테이블은 동일한 Azure 위치 및 구독에 공존해야 합니다.

서브넷 및 경로 테이블에는 일대다 관계가 형성됩니다. 서브넷은 연결된 경로 테이블 없이 또는 경로 테이블 1개를 사용하여 정의할 수 있습니다. 경로 테이블은 하나 또는 여러 개의 서브넷에 연결되거나 서브넷에 연결되지 않을 수 있습니다. 

서브넷의 트래픽은 다음을 기준으로 라우팅됩니다.

- 경로 테이블 내에 정의된 경로
- [기본 경로](/azure/virtual-network/virtual-networks-udr-overview#default)
- 온-프레미스 네트워크에서 전파된 경로

가상 네트워크는 Azure Virtual Network 게이트웨이에 연결되어야 합니다. VPN Gateway에서 BGP를 사용하는 경우 게이트웨이는 ExpressRoute 또는 VPN일 수 있습니다.

다음 플레이북을 `route_table_associate.yml`로 저장합니다.

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>서브넷에서 경로 테이블 분리

이 섹션의 플레이북 코드는 서브넷에서 경로 테이블을 분리합니다.

서브넷에서 경로 테이블을 분리하고, 서브넷의 `route_table`을 `None`으로 설정합니다. 

다음 플레이북을 `route_table_dissociate.yml`로 저장합니다.

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>경로 만들기

이 섹션의 플레이북 코드는 경로 테이블 내에 경로를 만듭니다. 

다음 플레이북을 `route_create.yml`로 저장합니다.

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```

플레이북을 실행하기 전에 다음 정보를 참조하세요.

* `virtual_network_gateway`는 `next_hop_type`으로 정의됩니다. Azure의 경로 선택 방법에 대한 자세한 내용은 [라우팅 개요](/azure/virtual-network/virtual-networks-udr-overview)를 참조하세요.
* `address_prefix`는 `10.1.0.0/16`으로 정의됩니다. 경로 테이블 내에서 접두사는 중복될 수 없습니다.

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>경로 삭제

이 섹션의 플레이북 코드는 경로 테이블에서 경로를 삭제합니다.

다음 플레이북을 `route_delete.yml`로 저장합니다.

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>경로 테이블 정보 가져오기

이 섹션의 플레이북 코드는 Ansible 모듈 `azure_rm_routetable_facts`를 사용하여 경로 테이블 정보를 검색합니다.

다음 플레이북을 `route_table_facts.yml`로 저장합니다.

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>경로 테이블 삭제

이 섹션의 플레이북 코드는 경로 테이블을 삭제합니다.

경로 테이블을 삭제하면 해당 경로도 모두 삭제됩니다.

서브넷과 연결된 경로 테이블은 삭제할 수 없습니다. 경로 테이블을 삭제하기 전에 [서브넷에서 경로 테이블을 분리](#dissociate-a-route-table-from-a-subnet)합니다. 

다음 플레이북을 `route_table_delete.yml`로 저장합니다.

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [Azure의 Ansible](/azure/ansible/)