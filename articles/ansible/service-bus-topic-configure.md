---
title: 자습서 - Ansible을 사용하여 Azure Service Bus에 토픽 구성
description: Ansible을 사용하여 Azure Service Bus 토픽을 만드는 방법 알아보기
keywords: ansible, azure, devops, bash, playbook, service bus, topics, subscriptions
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: a6cc84b17a5971ba487e304822de9f366826274b
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90681975"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>자습서: Ansible을 사용하여 Azure Service Bus에 토픽 구성

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 토픽 만들기
> * 구독 만들기
> * SAS 정책 만들기
> * 네임스페이스 정보 검색
> * 토픽 및 구독 정보 검색
> * SAS 정책 해지

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Service Bus 토픽 만들기

샘플 플레이북 코드는 다음과 같은 리소스를 만듭니다.
- Azure 리소스 그룹
- 리소스 그룹 내 Service Bus 네임스페이스
- 네임스페이스가 있는 Service Bus 토픽

다음 플레이북을 `servicebus_topic.yml`로 저장합니다.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: topic
    - debug:
          var: topic
```

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>구독 만들기

샘플 플레이북 코드는 Service Bus 토픽 아래에 구독을 만듭니다. Azure Service Bus 토픽에는 여러 구독이 있을 수 있습니다. 토픽에 대한 구독자는 각 메시지의 복사본을 해당 토픽에 전송할 수 있습니다. 구독은 지속적으로 생성되지만 필요에 따라 만료되는 엔터티 이름이 지정됩니다.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Create a subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs
    - debug:
          var: subs
```

다음 플레이북을 `servicebus_subscription.yml`로 저장합니다.

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>SAS 정책 만들기

[SAS(공유 액세스 서명)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)은 토큰을 사용하는 클레임 기반 권한 부여 메커니즘입니다. 

샘플 코드 예제 코드는 서로 다른 권한으로 Service Bus 큐에 대해 2개의 SAS 정책을 생성합니다.

다음 플레이북을 `servicebus_topic_policy.yml`로 저장합니다.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-{{ item }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: "{{ item }}"
      with_items:
        - send
        - listen
      register: policy
    - debug:
          var: policy
```

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook servicebus_topic_policy.yml
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

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-topic-and-subscription-information"></a>토픽 및 구독 정보 검색

샘플 플레이북 코드는 다음 정보를 쿼리합니다.
- Service Bus 토픽 정보
- 토픽에 대한 구독 세부 정보 목록
 
다음 플레이북을 `servicebus_list.yml`로 저장합니다.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Get a topic's information
      azure_rm_servicebus_facts:
          type: topic
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: topic_fact
    - name: "List subscriptions under topic {{ topic }}"
      azure_rm_servicebus_facts:
          type: subscription
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs_fact
    - debug:
          var: "{{ item }}"
      with_items:
        - topic_fact.servicebuses[0]
        - subs_fact.servicebuses
```

플레이북을 실행하기 전에 다음 정보를 참조하세요.
- `show_sas_policies` 값은 지정된 큐 아래에 SAS 정책을 표시할지 여부를 나타냅니다. 추가 네트워크 오버헤드를 방지하기 위해 이 값은 기본적으로 `False`로 설정됩니다.

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook servicebus_list.yml
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
      topic: servicebustesttopic
  tasks:
    - name: Delete a policy
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-policy"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook servicebus_topic_policy_delete.yml
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
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Delete subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
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

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [Azure의 Ansible](/azure/ansible/)