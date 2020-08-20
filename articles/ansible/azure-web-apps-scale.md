---
title: 자습서 - Azure App Service에서 Ansible을 사용하여 앱 크기 조정
description: Azure App Service에서 앱을 강화하는 방법 알아보기
keywords: Ansible, Azure, Devops, Bash, 플레이북, Azure App Service, Web App, 크기 조정, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 6c472db1f2d0b7cdc52694895f521c40202adaac
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240405"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>자습서: Ansible을 사용하여 Azure App Service에서 앱 크기 조정

[!INCLUDE [ansible-27-note.md](includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 기존 App Service 계획의 팩트 가져오기
> * 3명의 작업자가 있는 S2로 App Service 계획 강화

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Azure App Service 앱** -Azure App Service 앱이 없는 경우 [Ansible을 사용하여 Azure App Service에서 앱을 구성](azure-web-apps-configure.md)합니다.

## <a name="scale-up-an-app"></a>앱 강화

크기 조정에는 *강화* 및 *규모 확장*의 두 가지 워크플로가 있습니다.

**강화:** 강화하는 것은 더 많은 리소스를 획득하는 것을 의미합니다. 이러한 리소스에는 CPU, 메모리, 디스크 공간, VM 등이 포함됩니다. 앱이 속한 App Service 계획의 가격 책정 계층을 변경하여 앱을 강화합니다. 
**규모 확장:** 규모 확장은 앱을 실행하는 VM 인스턴스 수를 늘리는 것을 의미합니다. App Service 계획 가격 책정 계층에 따라 최대 20개의 인스턴스로 확장할 수 있습니다. [자동 크기 조정](/azure/azure-monitor/platform/autoscale-get-started)을 사용하면 미리 정의된 규칙 및 일정에 따라 인스턴스 수가 자동으로 조정됩니다.

이 섹션의 플레이북 코드는 다음 작업을 정의합니다.

* 기존 App Service 계획의 팩트 가져오기
* 세 명의 작업자에서 S2로 App Service 계획 업데이트

다음 플레이북을 `webapp_scaleup.yml`로 저장합니다.

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App service plan
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku

  - name: Scale up the App service plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      is_linux: true
      sku: S2
      number_of_workers: 3
      
  - name: Get facts
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku
```

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook webapp_scaleup.yml
```

플레이북을 실행하면 다음 결과와 유사한 출력이 표시됩니다.

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get facts of existing App service plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] 
changed: [localhost]

TASK [Get facts] 
ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP 
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure의 Ansible](/azure/ansible/)