---
title: 자습서 - Ansible을 사용하여 Azure 가상 머신 확장 집합의 사용자 지정 이미지 업데이트
description: Ansible을 사용하여 Azure의 Virtual Machine Scale Sets를 사용자 지정 이미지로 업데이트하는 방법 알아보기
keywords: ansible, azure, devops, bash, 플레이북, 가상 머신, 가상 머신 확장 집합, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 85fe3ec53b0a32a06ded0bbe0da47c62e15fcf41
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90681834"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>자습서: Ansible을 사용하여 Azure Virtual Machine Scale Sets의 사용자 지정 이미지 업데이트

[!INCLUDE [ansible-27-note.md](includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vm-scale-set.md](../includes/open-source-devops-intro-vm-scale-set.md)]

VM이 배포된 후에는 앱에 필요한 소프트웨어를 사용하여 VM을 구성합니다. 각 VM에 대해 이 구성 작업을 수행하는 대신, 사용자 지정 이미지를 만들 수 있습니다. 사용자 지정 이미지는 설치된 모든 소프트웨어를 포함하는 기존 VM의 스냅샷입니다. [확장 집합을 구성](./vm-scale-set-configure.md)할 때 해당 확장 집합의 VM에 사용할 이미지를 지정합니다. 사용자 지정 이미지를 사용하면 각 VM 인스턴스가 앱에 대해 동일하게 구성됩니다. 경우에 따라 확장 집합의 사용자 지정 이미지를 업데이트해야 할 수 있습니다. 이 작업이 자습서의 핵심 부분입니다.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * HTTPD를 사용하여 두 VM 구성
> * 기존 VM에서 사용자 지정 이미지 만들기
> * 이미지에서 확장 집합 만들기
> * 사용자 지정 이미지 업데이트

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>두 VM 구성

이 섹션의 플레이북 코드는 HTTPD가 설치된 두 가상 머신을 만듭니다. 

각 VM의 `index.html` 페이지는 다음 테스트 문자열을 표시합니다.

* 첫 번째 VM은 값 `Image A`를 표시합니다.
* 두 번째 VM은 값 `Image B`를 표시합니다.

이 문자열은 다른 소프트웨어를 사용하여 각 VM 구성을 모방하는 것을 의미합니다.

다음과 같은 두 가지 방법으로 샘플 플레이북을 가져올 수 있습니다.

* [플레이북을 다운로드](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml)하여 `create_vms.yml`에 저장합니다.
* `create_vms.yml`이라는 새 파일을 만들고 다음 콘텐츠를 이 파일에 복사합니다.

```yml
- name: Create two VMs (A and B) with HTTPS
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
    admin_username: testuser
    admin_password: Pass123$$$abx!
    location: eastus
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      address_prefixes: "10.0.0.0/16"

  - name: Create subnets for VM A and B
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      virtual_network: "{{ vm_name }}"
      name: "{{ vm_name }}"
      address_prefix: "10.0.1.0/24"

  - name: Create Network Security Group that allows HTTP
    azure_rm_securitygroup:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      rules:
        - name: HTTP
          protocol: Tcp
          destination_port_range: 80
          access: Allow
          priority: 1002
          direction: Inbound

  - name: Create public IP addresses for VM A and B
    azure_rm_publicipaddress:
      resource_group: "{{ resource_group }}"
      allocation_method: Static
      name: "{{ vm_name }}_{{ item }}"
    loop:
      - A
      - B
    register: pip_output

  - name: Create virtual network inteface cards for VM A and B
    azure_rm_networkinterface:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}_{{ item }}"
      virtual_network: "{{ vm_name }}"
      subnet: "{{ vm_name }}"
      public_ip_name: "{{ vm_name }}_{{ item }}"
      security_group: "{{ vm_name }}"
    loop:
      - A
      - B

  - name: Create VM A and B
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      vm_size: Standard_B1ms
      network_interfaces: "{{ vm_name }}_{{ item }}"
      image:
        offer: UbuntuServer
        publisher: Canonical
        sku: 16.04-LTS
        version: latest
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "sudo apt-get -y install apache2"}
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "printf '<html><body><h1>Image {{ item }}</h1></body></html>' >> index.html; sudo cp index.html /var/www/html/"}
    loop:
      - A
      - B

  - debug:
      msg: "Public IP Address A: {{ pip_output.results[0].state.ip_address }}"

  - debug:
      msg: "Public IP Address B: {{ pip_output.results[1].state.ip_address }}"
```

`myrg`를 리소스 그룹 이름으로 바꾸고 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

플레이북의 `debug` 섹션 때문에 `ansible-playbook` 명령은 각 VM의 IP 주소를 인쇄합니다. 나중에 사용하기 위해 이러한 IP 주소를 복사합니다.

![가상 머신 IP 주소](media/vm-scale-set-update-image/ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>두 VM에 연결

이 섹션에서는 각 VM에 연결합니다. 이전 섹션에서 설명한 것처럼 문자열 `Image A` 및 `Image B`는 구성이 각기 다른 두 개의 고유한 VM을 모방합니다.

이전 섹션의 IP 주소를 사용하여 두 VM에 연결합니다.

![가상 머신 A의 스크린샷](media/vm-scale-set-update-image/vm-a.png)

![가상 머신 B의 스크린샷](media/vm-scale-set-update-image/vm-b.png)

## <a name="create-images-from-each-vm"></a>각 VM에서 이미지 만들기

이제 두 VM의 구성(해당 `index.html` 파일)이 약간 다릅니다.

이 섹션의 플레이북 코드는 각 VM에 대해 사용자 지정 이미지를 만듭니다.

* `image_vmforimageA` - 홈페이지에 `Image A`를 표시하는 VM에 대해 생성된 사용자 지정 이미지입니다.
* `image_vmforimageB` - 홈페이지에 `Image B`를 표시하는 VM에 대해 생성된 사용자 지정 이미지입니다.

다음과 같은 두 가지 방법으로 샘플 플레이북을 가져올 수 있습니다.

* [플레이북을 다운로드](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml)하여 `capture-images.yml`에 저장합니다.
* `capture-images.yml`이라는 새 파일을 만들고 다음 콘텐츠를 이 파일에 복사합니다.

```yml
- name: Capture VM Images
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
  tasks:

  - name: Stop and generalize VMs
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      generalized: yes
    loop:
      - A
      - B

  - name: Create an images from a VMs
    azure_rm_image:
      resource_group: "{{ resource_group }}"
      name: "image_{{ vm_name }}{{ item }}"
      source: "{{ vm_name }}{{ item }}"
    loop:
      - A
      - B
```

`myrg`를 리소스 그룹 이름으로 바꾸고 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>이미지 A를 사용하여 확장 집합 만들기

이 섹션에서는 플레이북을 사용하여 다음과 같은 Azure 리소스를 구성합니다.

* 공용 IP 주소
* 부하 분산 장치
* `image_vmforimageA`를 참조하는 확장 집합

다음과 같은 두 가지 방법으로 샘플 플레이북을 가져올 수 있습니다.

* [플레이북을 다운로드](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml)하여 `create-vmss.yml`에 저장합니다.
* `create-vmss.yml`이라는 새 파일을 만들고 다음 콘텐츠에 복사합니다.

```yml
---
- hosts: localhost
  vars:
    vmss_name: vmsstest
    location: eastus
    admin_username: vmssadmin
    admin_password: User123!!!abc
    vm_name: vmforimage
    image_name: "image_vmforimageA"

  tasks:

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
      register: pip_output

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_name }}lb"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 80
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 80
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create a scale set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vm_name }}"
        subnet_name: "{{ vm_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          name: "{{ image_name }}"
          resource_group: "{{ resource_group }}"
        load_balancer: "{{ vmss_name }}lb"

    - debug:
        msg: "Scale set public IP address: {{ pip_output.state.ip_address }}"
```

`myrg`를 리소스 그룹 이름으로 바꾸고 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

플레이북의 `debug` 섹션 때문에 `ansible-playbook` 명령은 확장 집합의 IP 주소를 인쇄합니다. 나중에 사용할 수 있게 이 IP 주소를 복사합니다.

![공용 IP 주소](media/vm-scale-set-update-image/public-ip.png)

## <a name="connect-to-the-scale-set"></a>확장 집합에 연결

이 섹션에서는 확장 집합에 연결합니다. 

이전 섹션의 IP 주소를 사용하여 확장 집합에 연결합니다.

이전 섹션에서 설명한 것처럼 문자열 `Image A` 및 `Image B`는 구성이 각기 다른 두 개의 고유한 VM을 모방합니다.

확장 집합은 `image_vmforimageA`라는 사용자 지정 이미지를 참조합니다. 해당 홈페이지에 `Image A`가 표시되는 VM에서 사용자 지정 이미지 `image_vmforimageA`를 만들었습니다.

결과적으로 홈페이지에 `Image A`가 표시됩니다.

![확장 집합은 첫 번째 VM과 연결됩니다.](media/vm-scale-set-update-image/initial-vm-scale-set.png)

다음 섹션을 계속 진행하게 되므로 브라우저 창을 열어 둡니다.

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>확장 집합의 사용자 지정 이미지 변경 및 인스턴스 업그레이드

이 섹션의 플레이북 코드는 확장 집합의 이미지를 `image_vmforimageA`에서 `image_vmforimageB`로 변경합니다. 또한 확장 집합에서 배포한 모든 현재 가상 머신이 업데이트됩니다.

다음과 같은 두 가지 방법으로 샘플 플레이북을 가져올 수 있습니다.

* [플레이북을 다운로드](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml)하여 `update-vmss-image.yml`에 저장합니다.
* `update-vmss-image.yml`이라는 새 파일을 만들고 다음 콘텐츠를 이 파일에 복사합니다.

```yml
- name: Update scale set image reference
  hosts: localhost
  connection: local
  vars:
    vmss_name: vmsstest
    image_name: image_vmforimageB
    admin_username: vmssadmin
    admin_password: User123!!!abc
  tasks:

  - name: Update scale set - second image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      ssh_password_enabled: true
      capacity: 3
      virtual_network_name: "{{ vmss_name }}"
      subnet_name: "{{ vmss_name }}"
      upgrade_policy: Manual
      tier: Standard
      managed_disk_type: Standard_LRS
      os_disk_caching: ReadWrite
      image:
        name: "{{ image_name }}"
        resource_group: "{{ resource_group }}"
      load_balancer: "{{ vmss_name }}lb"

  - name: List all of the instances
    azure_rm_virtualmachinescalesetinstance_facts:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
    register: instances

  - debug:
      var: instances

  - name: manually upgrade all the instances 
    azure_rm_virtualmachinescalesetinstance:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
      instance_id: "{{ item.instance_id }}"
      latest_model: yes
    with_items: "{{ instances.instances }}"
```

`myrg`를 리소스 그룹 이름으로 바꾸고 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

브라우저로 돌아가서 페이지를 새로 고칩니다. 

가상 컴퓨터의 기본 사용자 지정 이미지가 업데이트된 것을 볼 수 있습니다.

![확장 집합은 두 번째 VM과 연결됩니다.](media/vm-scale-set-update-image/updated-vm-scale-set.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [ansible-delete-resource-group.md](includes/ansible-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure의 Ansible](/azure/ansible)