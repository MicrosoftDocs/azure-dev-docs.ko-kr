---
title: 자습서 - Ansible을 사용하여 Azure Application Gateway로 웹 트래픽 관리
description: Ansible을 사용하여 웹 트래픽을 관리하도록 Azure Application Gateway를 만들고 구성하는 방법 알아보기
keywords: Ansible, Azure, DevOps, Bash, 플레이북, 애플리케이션 게이트웨이, 부하 분산 장치, 웹 트래픽
ms.topic: tutorial
ms.date: 06/19/2020
ms.custom: devx-track-ansible
ms.openlocfilehash: cfeba71085443afb978ceb6b7c381a1e74e723fb
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240475"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>자습서: Ansible을 사용하여 Azure Application Gateway로 웹 트래픽 관리

[!INCLUDE [ansible-27-note.md](includes/ansible-27-note.md)]

[Azure Application Gateway](/azure/application-gateway/overview)는 웹 애플리케이션에 대한 트래픽을 관리할 수 있도록 하는 웹 트래픽 부하 분산 장치입니다. 기존의 부하 분산 장치는 원본 IP 주소 및 포트에 따라 대상 IP 주소 및 포트에 트래픽을 라우팅합니다. Application Gateway는 URL에 따라 트래픽을 라우팅할 수 있는 세밀한 컨트롤을 제공합니다. 예를 들어 `images`가 URL의 경로인 경우 이미지에 대해 구성된 특정 서버 세트(풀이라고 함)에 트래픽이 라우팅됩니다.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 네트워크 설정
> * HTTPD 이미지를 사용하여 두 개의 Azure 컨테이너 인스턴스 만들기
> * 서버 풀에서 Azure 컨테이너 인스턴스를 사용하여 애플리케이션 게이트웨이 만들기

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

이 섹션의 플레이북 코드는 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 구성되는 논리적 컨테이너입니다.  

다음 플레이북을 `rg.yml`로 저장합니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

플레이북을 실행하기 전에 다음 정보를 참조하세요.

- 리소스 그룹 이름은 `myResourceGroup`입니다. 이 값은 자습서 전체에서 사용됩니다.
- 리소스 그룹이 `eastus` 위치에 생성됩니다.

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기

이 섹션의 플레이북 코드는 애플리케이션 게이트웨이가 다른 리소스와 통신할 수 있도록 가상 네트워크를 만듭니다.

다음 플레이북을 `vnet_create.yml`로 저장합니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

플레이북을 실행하기 전에 다음 정보를 참조하세요.

* `vars` 섹션에는 네트워크 리소스를 만드는 데 사용되는 값이 포함되어 있습니다. 
* 해당 환경에 맞게 이러한 값을 변경해야 합니다.

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>서버 만들기

이 섹션의 플레이북 코드는 애플리케이션 게이트웨이의 웹 서버로 사용될 HTTPD 이미지가 있는 두 개의 Azure 컨테이너 인스턴스를 만듭니다.  

다음 플레이북을 `aci_create.yml`로 저장합니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Application Gateway 만들기

이 섹션의 플레이북 코드는 `myAppGateway`라는 애플리케이션 게이트웨이를 만듭니다.  

다음 플레이북을 `appgw_create.yml`로 저장합니다.

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 1
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

플레이북을 실행하기 전에 다음 정보를 참조하세요.

* `appGatewayIP`은 `gateway_ip_configurations` 블록에 정의됩니다. 서브넷 참조는 게이트웨이의 IP 구성에 필요합니다.
* `appGatewayBackendPool`은 `backend_address_pools` 블록에 정의됩니다. 애플리케이션 게이트웨이에 백 엔드 주소 풀이 하나 이상 있어야 합니다.
* `appGatewayBackendHttpSettings`은 `backend_http_settings_collection` 블록에 정의됩니다. 포트 80 및 HTTP 프로토콜을 통신에 사용하도록 지정합니다.
* `appGatewayHttpListener`은 `backend_http_settings_collection` 블록에 정의됩니다. appGatewayBackendPool에 연결되는 기본 수신기입니다.
* `appGatewayFrontendIP`은 `frontend_ip_configurations` 블록에 정의됩니다. myAGPublicIPAddress를 appGatewayHttpListener에 할당합니다.
* `rule1`은 `request_routing_rules` 블록에 정의됩니다. appGatewayHttpListener에 연결되는 기본 회람 규칙입니다.

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook appgw_create.yml
```

애플리케이션 게이트웨이가 생성될 때까지 몇 분 정도 걸릴 수 있습니다.

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

1. [리소스 그룹 만들기](#create-a-resource-group) 섹션에서 위치를 지정합니다. 값을 적어 둡니다.

1. [네트워크 리소스 만들기](#create-network-resources) 섹션에서 도메인을 지정합니다. 값을 적어 둡니다.

1. 테스트 URL의 경우 `http://<domain>.<location>.cloudapp.azure.com` 패턴을 위치 및 도메인으로 바꿉니다.

1. 테스트 URL로 이동합니다.

1. 다음 페이지가 표시되면 애플리케이션 게이트웨이가 예상대로 작동합니다.

    ![작업 애플리케이션 게이트웨이의 성공적인 테스트](media/application-gateway-configure/demo.png)

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
        state: absent
```

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure의 Ansible](/azure/ansible/)