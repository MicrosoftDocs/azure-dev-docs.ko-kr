---
title: 자습서 - Ansible을 사용하여 Azure Cosmos DB 계정 구성
description: Ansible을 사용하여 Azure Cosmos DB를 만들고 구성하는 방법 알아보기
keywords: ansible, azure, devops, bash, playbook, cosmo db, database
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 7c09f0dd28dbcbdf62219a62295b05758b4c8372
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240345"
---
# <a name="tutorial-configure-azure-cosmos-db-accounts-using-ansible"></a>자습서: Ansible을 사용하여 Azure Cosmos DB 계정 구성

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[Azure Cosmos DB](/azure/cosmos-db/)는 여러 데이터베이스 유형을 지원하는 데이터베이스 서비스입니다. 이러한 데이터베이스 유형에는 문서, 키-값, 전체 열 및 그래프기 포함됩니다. Ansible을 사용하면 사용자 환경에서 리소스의 배포 및 구성을 자동화할 수 있습니다.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 계정 만들기
> * 계정 키 검색
> * 계정 삭제

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-random-postfix"></a>임의 접미사 만들기

샘플 플레이북 코드 조각은 임의 접미사를 만듭니다. 이 접미사는 Azure Cosmos DB 계정 이름의 일부로 사용됩니다.

```yml
  - hosts: localhost
    tasks:
      - name: Prepare random postfix
        set_fact:
          rpfx: "{{ 1000 | random }}"
        run_once: yes
```

## <a name="create-resource-group"></a>리소스 그룹 만들기 

샘플 플레이북 코드 조각은 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
```

## <a name="create-virtual-network-and-subnet"></a>가상 네트워크 및 서브넷 만들기

다음 코드는 Azure Cosmos DB 계정의 가상 네트워크 및 서브넷을 만듭니다.

```yml
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name }}"
      address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
      dns_servers:
        - 127.0.0.1
        - 127.0.0.3

  - name: Add subnet
    azure_rm_subnet:
      name: "{{ subnet_name }}"
      virtual_network_name: "{{ vnet_name }}"
      resource_group: "{{ resource_group }}"
      address_prefix_cidr: "10.1.0.0/24"
```

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

다음 코드는 Cosmos DB 계정을 만듭니다.

```yml
  - name: Create instance of Cosmos DB Account
    azure_rm_cosmosdbaccount:
      resource_group: "{{ resource_group }}"
      name: "{{ cosmosdbaccount_name }}"
      location: eastus
      kind: global_document_db
      geo_rep_locations:
        - name: eastus
          failover_priority: 0
        - name: westus
          failover_priority: 1
      database_account_offer_type: Standard
      is_virtual_network_filter_enabled: yes
      virtual_network_rules:
        - subnet:
            resource_group: "{{ resource_group }}"
            virtual_network_name: "{{ vnet_name }}"
            subnet_name: "{{ subnet_name }}"
          ignore_missing_vnet_service_endpoint: yes
      enable_automatic_failover: yes
```

계정 생성을 완료하는 데는 몇 분 정도가 소요됩니다.

## <a name="retrieve-the-keys"></a>키 검색

다음 코드는 앱에서 사용할 키를 가져옵니다.

```yml
  - name: Get Cosmos DB Account facts with keys
    azure_rm_cosmosdbaccount_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ cosmosdbaccount_name }}"
      retrieve_keys: all
    register: output

  - name: Display Cosmos DB Acccount facts output
    debug:
      var: output
```

## <a name="delete-the-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 삭제

마지막으로, 마지막 코드 조각은 Azure Cosmos DB 계정을 삭제하는 방법을 표시합니다.

```yml
  - name: Delete instance of Cosmos DB Account
    azure_rm_cosmosdbaccount:
      resource_group: "{{ resource_group }}"
      name: "{{ cosmosdbaccount_name }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>샘플 플레이북 가져오기

전체 샘플 플레이북을 가져오는 방법에는 다음 두 가지가 있습니다.
- [플레이북을 다운로드](https://github.com/Azure-Samples/ansible-playbooks/blob/master/cosmosdb_create.yml)하여 `cosmosdb.yml`에 저장합니다.
- `cosmosdb.yml`이라는 새 파일을 만들고 다음 콘텐츠를 이 파일에 복사합니다.

```yml
---
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- hosts: localhost
#  roles:
#    - azure.azure_preview_modules
  vars:
    resource_group: "{{ resource_group_name }}"
    location: eastus
    vnet_name: myVirtualNetwork
    subnet_name: mySubnet
    cosmosdbaccount_name: cosmos{{ rpfx }}

  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name }}"
        address_prefixes_cidr:
          - 10.1.0.0/16
          - 172.100.0.0/16
        dns_servers:
          - 127.0.0.1
          - 127.0.0.3

    - name: Add subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"

    - name: Create instance of Cosmos DB Account
      azure_rm_cosmosdbaccount:
        resource_group: "{{ resource_group }}"
        name: "{{ cosmosdbaccount_name }}"
        location: eastus
        kind: global_document_db
        geo_rep_locations:
          - name: eastus
            failover_priority: 0
          - name: westus
            failover_priority: 1
        database_account_offer_type: Standard
        is_virtual_network_filter_enabled: yes
        virtual_network_rules:
          - subnet:
              resource_group: "{{ resource_group }}"
              virtual_network_name: "{{ vnet_name }}"
              subnet_name: "{{ subnet_name }}"
            ignore_missing_vnet_service_endpoint: yes
        enable_automatic_failover: yes

    - name: Get Cosmos DB Account facts with keys
      azure_rm_cosmosdbaccount_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ cosmosdbaccount_name }}"
        retrieve_keys: all
      register: output

    - name: Display Cosmos DB Account facts output
      debug:
        var: output

    - name: Delete instance of Cosmos DB Account
      azure_rm_cosmosdbaccount:
        resource_group: "{{ resource_group }}"
        name: "{{ cosmosdbaccount_name }}"
        state: absent
```

## <a name="run-the-sample-playbook"></a>샘플 플레이북 실행

이 섹션에서는 플레이북을 실행하여 이 문서에 나와 있는 다양한 기능을 테스트합니다.

플레이북을 실행하기 전에 다음과 같이 변경합니다.
- `vars` 섹션에서 `{{ resource_group_name }}` 자리 표시자를 리소스 그룹의 이름으로 바꿉니다.
- cosmosdbaccount_name이 소문자만 포함하고 전역적으로 고유한지 확인합니다.

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook cosmosdb.yml
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 이 문서에서 만든 리소스를 삭제합니다. 

다음 코드를 `cleanup.yml`로 저장합니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure의 Ansible](/azure/ansible/)