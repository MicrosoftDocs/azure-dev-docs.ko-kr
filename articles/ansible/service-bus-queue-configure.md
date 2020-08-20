---
title: 자습서 - Ansible을 사용하여 Azure Service Bus에 큐 구성
description: Ansible을 사용하여 Azure Service Bus 큐를 만드는 방법 알아보기
keywords: Ansible, Azure, DevOps, Bash, 플레이북, 서비스 버스, 큐
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 438c5787469a31403285f8884452d5293f1460f4
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88239995"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>자습서: Ansible을 사용하여 Azure Service Bus에 큐 구성

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 큐 만들기
> * SAS 정책 만들기
> * 네임스페이스 정보 검색
> * 큐 정보 검색
> * 큐 SAS 정책 철회

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Service Bus 큐 만들기

샘플 플레이북 코드는 다음과 같은 리소스를 만듭니다.
- Azure 리소스 그룹
- 리소스 그룹 내 Service Bus 네임스페이스
- 네임스페이스 내 Service Bus 큐

다음 플레이북을 `servicebus_queue.yml`로 저장합니다.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: queue
    - debug:
          var: queue
```

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>SAS 정책 만들기

[SAS(공유 액세스 서명)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)은 토큰을 사용하는 클레임 기반 권한 부여 메커니즘입니다. 

샘플 코드 예제 코드는 서로 다른 권한으로 Service Bus 큐에 대해 2개의 SAS 정책을 생성합니다.

다음 플레이북을 `servicebus_queue_policy.yml`로 저장합니다.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: listen_send
      register: policy
    - debug:
          var: policy
```

플레이북을 실행하기 전에 다음 정보를 참조하세요.
- `rights` 값은 사용자가 큐에 대해 갖는 권한을 나타냅니다. `manage`, `listen`, `send` 또는 `listen_send` 중 하나를 값으로 지정하세요.

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>네임스페이스 정보 검색

샘플 플레이북 코드는 네임스페이스 정보를 쿼리합니다.

다음 플레이북을 `servicebus_namespace_info.yml`로 저장합니다.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
  tasks:
    - name: Get a namespace's information
      azure_rm_servicebus_facts:
          type: namespace
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: ns
    - debug:
          var: ns
```

플레이북을 실행하기 전에 다음 정보를 참조하세요.
- `show_sas_policies` 값은 지정된 네임스페이스 아래에 SAS 정책을 표시할지 여부를 나타냅니다. 추가 네트워크 오버헤드를 방지하기 위해 이 값은 기본적으로 `False`입니다.

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>큐 정보 검색

샘플 플레이북 코드는 큐 정보를 쿼리합니다. 

다음 플레이북을 `servicebus_queue_info.yml`로 저장합니다.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Get a queue's information
      azure_rm_servicebus_facts:
          type: queue
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: queue
    - debug:
          var: queue
```

플레이북을 실행하기 전에 다음 정보를 참조하세요.
- `show_sas_policies` 값은 지정된 큐 아래에 SAS 정책을 표시할지 여부를 나타냅니다. 추가 네트워크 오버헤드를 방지하기 위해 이 값은 기본적으로 `False`로 설정됩니다.

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>큐 SAS 정책 철회

샘플 플레이북 코드는 큐 SAS 정책을 삭제합니다.

다음 플레이북을 `servicebus_queue_policy_delete.yml`로 저장합니다.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 이 문서에서 만든 리소스를 삭제합니다. 

다음 코드를 `cleanup.yml`로 저장합니다.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Delete queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
    - name: Delete resource group
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          state: absent
          force_delete_nonempty: yes
```

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [자습서: Ansible을 사용하여 Azure Service Bus에서 토픽 구성](service-bus-topic-configure.md)