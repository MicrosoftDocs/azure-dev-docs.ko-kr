---
title: 자습서 - Azure App Service에서 Ansible을 사용하여 앱 구성
description: Azure App Service에서 Java 8 및 Tomcat 컨테이너 런타임을 사용하여 웹앱을 만드는 방법 알아보기
keywords: Ansible, Azure, Devops, Bash, 플레이북, Azure App Service, Web App, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 13ef1291f2d61f5792d2d21fa105a1dcd9f1e2d3
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90681745"
---
# <a name="tutorial-configure-apps-in-azure-app-service-using-ansible"></a>자습서: Azure App Service에서 Ansible을 사용하여 앱 구성

[!INCLUDE [ansible-27-note.md](includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Azure App Service에서 Java 8 및 Tomcat 컨테이너 런타임을 사용하여 앱 만들기
> * Azure Traffic Manager 프로필 만들기
> * 만든 앱을 사용하여 Traffic Manager 엔드포인트 정의

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-basic-app-service"></a>기본 앱 서비스 만들기

이 섹션의 플레이북 코드는 다음 리소스를 정의합니다.

* App Service 계획 및 앱이 배포되는 Azure 리소스 그룹
* Java 8 및 Tomcat 컨테이너 런타임을 사용하는 Linux의 App Service

다음 플레이북을 `firstwebapp.yml`로 저장합니다.

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    webapp_name: myfirstWebApp
    plan_name: myAppServicePlan
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create App Service on Linux with Java Runtime
      azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        frameworks:
          - name: "java"
            version: "8"
            settings:
              java_container: tomcat
              java_container_version: 8.5
```

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook firstwebapp.yml
```

플레이북을 실행하면 다음 결과와 유사한 출력이 표시됩니다.

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-and-use-azure-traffic-manager"></a>앱을 만들고 Azure Traffic Manager 사용

[Azure Traffic Manager](/azure/app-service/web-sites-traffic-manager)를 사용하면 웹 클라이언트의 요청을 Azure App Service의 앱에 분산하는 방법을 제어할 수 있습니다. App Service 엔드포인트가 Azure Traffic Manager 프로필에 추가되면 Traffic Manager는 App Service 앱의 상태를 추적합니다. 상태에는 실행 중, 중지됨 및 삭제됨이 있습니다. Traffic Manager는 트래픽을 수신할 엔드포인트를 결정하는 데 사용됩니다.

App Service의 앱은 [App Service 계획](/azure/app-service/overview-hosting-plans)에서 실행됩니다. App Service 계획은 앱에서 실행할 컴퓨팅 리소스 세트를 정의합니다. 다른 그룹에서 App Service 계획 및 웹앱을 관리할 수 있습니다.

이 섹션의 플레이북 코드는 다음 리소스를 정의합니다.

* App Service 계획이 배포되는 Azure 리소스 그룹
* App Service 계획
* 앱이 배포되는 Azure 리소스 그룹
* Java 8 및 Tomcat 컨테이너 런타임을 사용하는 Linux의 App Service
* Traffic Manager 프로필
* 만든 앱을 사용하는 Traffic Manager 엔드포인트

다음 플레이북을 `webapp.yml`로 저장합니다.

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group_webapp: myResourceGroupWebapp
    resource_group: myResourceGroup
    webapp_name: myLinuxWebApp
    plan_name: myAppServicePlan
    location: eastus
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group_webapp }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group_webapp }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        app_settings:
          testkey: "testvalue"
        frameworks:
          - name: java
            version: 8
            settings:
              java_container: "Tomcat"
              java_container_version: "8.5"

  - name: Get web app facts
    azure_rm_webapp_facts:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ webapp_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ traffic_manager_profile_name }}"
      location: global
      routing_method: performance
      dns_config:
        relative_name: "{{ traffic_manager_profile_name }}"
        ttl:  60
      monitor_config:
        protocol: HTTPS
        port: 80
        path: '/'

  - name: Add endpoint to traffic manager profile, using created web site
    azure_rm_trafficmanagerendpoint:
      resource_group: "{{ resource_group_webapp }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"
```

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook webapp.yml
```

플레이북을 실행하면 다음 결과와 유사한 출력이 표시됩니다.

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create resource group for app service plan] 
changed: [localhost]

TASK [Create App Service Plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
changed: [localhost]

TASK [Get web app facts] 
ok: [localhost]

TASK [Create Traffic Manager Profile] 
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] 
changed: [localhost]

TASK [Get Traffic Manager Profile facts] 
ok: [localhost]

PLAY RECAP 
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [자습서: Azure App Service에서 Ansible을 사용하여 앱 크기 조정](/azure/ansible/ansible-scale-azure-web-apps)