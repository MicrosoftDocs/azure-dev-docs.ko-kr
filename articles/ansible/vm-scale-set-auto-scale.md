---
title: 자습서 - Azure에서 Ansible을 사용하여 가상 머신 확장 집합 자동 크기 조정
description: Azure에서 Ansible을 사용하여 가상 머신 확장 집합의 크기를 자동으로 저장하는 방법 알아보기
keywords: Ansible, Azure, DevOps, bash, 플레이북, 크기 조정, 자동 크기 조정, 가상 머신, 가상 머신 확장 집합, VMSS
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 62b2eb84ba7328e87896f9da448717856cb75c80
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90681892"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>자습서: Azure에서 Ansible을 사용하여 가상 머신 확장 집합 자동 크기 조정

[!INCLUDE [ansible-27-note.md](includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vm-scale-set.md](../includes/open-source-devops-intro-vm-scale-set.md)]

VM 인스턴스 수를 자동으로 조정하는 기능을 [자동 크기 조정](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)이라고 합니다. 자동 크기 조정을 사용하면 애플리케이션의 성능을 모니터링하고 최적화하는 관리 오버헤드가 감소한다는 이점이 있습니다. 수요 또는 정의된 일정에 대응하는 자동 크기 조정을 구성할 수 있습니다. Ansible을 사용하면 납득할 수 있는 수준의 성능을 정의하는 규칙을 만들어서 긍정적인 고객 경험을 제공할 수 있습니다.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 자동 크기 조정 프로필 정의
> * 되풀이 일정에 따라 자동 크기 조정
> * 앱 성능에 따라 자동 크기 조정
> * 자동 크기 조정 설정 정보 검색 
> * 자동 크기 조정 설정 해제

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>일정에 따라 자동 크기 조정

확장 집합에서 자동 크기 조정을 활성화하려면 먼저 자동 크기 조정 프로필을 정의합니다. 이 프로필은 기본, 최소, 최대 확장 집합 용량을 정의합니다. 이러한 제한을 통해 연속적으로 VM 인스턴스를 만들지 않고 비용을 제어하고, 축소 이벤트에 유지되는 최소 인스턴스 수로 허용 가능한 성능의 균형을 유지할 수 있습니다. 

Ansible을 사용하면 특정 날짜에 또는 되풀이 일정에 따라 확장 집합의 크기를 조정할 수 있습니다.

이 섹션의 플레이북 코드는 월요일 10:00마다 VM 인스턴스 수를 3으로 늘립니다.

다음 플레이북을 `vmss-auto-scale.yml`로 저장합니다.

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks: 
    - name: Create autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>성능 데이터에 따라 자동 크기 조정

애플리케이션 수요가 증가하면 확장 집합의 VM 인스턴스 부하가 증가합니다. 증가된 로드가 단순한 요구가 아닌 일관된 요구인 경우 확장 집합의 VM 인스턴스 수를 늘리도록 자동 크기 조정 규칙을 구성할 수 있습니다. 이러한 VM 인스턴스를 만들고 애플리케이션을 배포하면 확장 집합이 부하 분산 장치를 통해 트래픽을 분산하기 시작합니다. Ansible을 사용하면 CPU 사용량, 디스크 사용량, 앱 로드 시간 등 모니터링할 메트릭을 제어할 수 있습니다. 성능 메트릭 임계값에 따라, 되풀이 일정에 따라 또는 특정 날짜에 확장 집합을 확장하고 감축할 수 있습니다. 

이 섹션의 플레이북 코드는 월요일 18:00마다 이전 10분의 CPU 워크로드를 확인합니다. 

CPU 백분율 메트릭에 따라 플레이북이 다음 작업 중 하나를 수행합니다.

- VM 인스턴스 수를 4개로 확장
- VM 인스턴스 수를 1개로 감축

다음 플레이북을 `vmss-auto-scale-metrics.yml`로 저장합니다.

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get scale set resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create autoscaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>자동 크기 조정 설정 정보 얻기 

이 섹션의 플레이북 코드는 `azure_rm_autoscale_facts` 모듈을 사용하여 자동 크기 조정 설정의 세부 정보를 검색합니다.

다음 플레이북을 `vmss-auto-scale-get-settings.yml`로 저장합니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve autoscale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>자동 크기 조정 설정 해제

자동 크기 조정 설정을 해제하는 두 가지 방법이 있습니다. 하나는 `enabled`키를 `true`에서 `false`로 변경하는 것입니다. 두 번째 방법은 설정을 삭제하는 것입니다.

이 섹션의 플레이북 코드는 자동 크기 조정 설정을 삭제합니다. 

다음 플레이북을 `vmss-auto-scale-delete-setting.yml`로 저장합니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [자습서: Ansible을 사용하여 Azure 가상 머신 확장 집합의 사용자 지정 이미지 업데이트](./vm-scale-set-update-image.md)