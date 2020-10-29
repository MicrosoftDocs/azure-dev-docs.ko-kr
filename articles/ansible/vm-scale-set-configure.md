---
title: 자습서 - Ansible을 사용하여 Azure에서 가상 머신 확장 집합 구성
description: Azure에서 Ansible을 사용하여 Virtual Machine Scale Sets를 만들고 구성하는 방법 알아보기
keywords: ansible, azure, devops, bash, 플레이북, 가상 머신, 가상 머신 확장 집합, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible, devx-track-azurecli
ms.openlocfilehash: 64bf3cbd79f2b2b1e68a3019669bce1115ac7333
ms.sourcegitcommit: 1ddcb0f24d2ae3d1f813ec0f4369865a1c6ef322
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92689220"
---
# <a name="tutorial-configure-virtual-machine-scale-sets-in-azure-using-ansible"></a>자습서: Ansible을 사용하여 Azure에서 가상 머신 확장 집합 구성

[!INCLUDE [ansible-27-note.md](includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vm-scale-set.md](../includes/open-source-devops-intro-vm-scale-set.md)]

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * VM의 리소스 구성
> * 확장 집합 구성
> * VM 인스턴스 수를 늘려 확장 집합 크기 조정 

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-a-scale-set"></a>확장 집합 구성

이 섹션의 플레이북 코드는 다음 리소스를 정의합니다.

* 모든 리소스가 배포될 **리소스 그룹**
* 10.0.0.0/16 주소 공간의 **가상 네트워크**
* 가상 네트워크 내의 **서브넷**
* 인터넷을 통해 리소스에 액세스할 수 있는 **공용 IP 주소**
* 확장 집합 내/외부 네트워크 트래픽의 흐름을 제어하는 **네트워크 보안 그룹**
* 부하 분산 장치 규칙을 사용하여 정의된 VM 집합 전역에 트래픽을 분산하는 **부하 분산 장치**
* 생성된 모든 리소스를 사용하는 **가상 머신 확장 집합**

다음과 같은 두 가지 방법으로 샘플 플레이북을 가져올 수 있습니다.

* [플레이북을 다운로드](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml)하여 `vmss-create.yml`에 저장합니다.
* `vmss-create.yml`이라는 새 파일을 만들고 다음 콘텐츠를 이 파일에 복사합니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    vmss_lb_name: myScaleSetLb
    location: eastus
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefixes: "10.0.0.0/16"
    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ vmss_name }}"
    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
    - name: Create Network Security Group that allows SSH
      azure_rm_securitygroup:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        rules:
          - name: SSH
            protocol: Tcp
            destination_port_range: 22
            access: Allow
            priority: 1001
            direction: Inbound

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_lb_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 8080
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 8080
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create Scale Set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vmss_name }}"
        subnet_name: "{{ vmss_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
        load_balancer: "{{ vmss_lb_name }}"
        data_disks:
          - lun: 0
            disk_size_gb: 20
            managed_disk_type: Standard_LRS
            caching: ReadOnly
          - lun: 1
            disk_size_gb: 30
            managed_disk_type: Standard_LRS
            caching: ReadOnly
```

플레이북을 실행하기 전에 다음 정보를 참조하세요.

* `vars` 섹션에서 `{{ admin_password }}` 자리 표시자를 해당 암호로 바꿉니다.

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook vmss-create.yml
```

플레이북을 실행하면 다음 결과와 유사한 출력이 표시됩니다.

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Create public IP address] 
changed: [localhost]

TASK [Create Network Security Group that allows SSH] 
changed: [localhost]

TASK [Create a load balancer] 
changed: [localhost]

TASK [Create Scale Set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=8    changed=7    unreachable=0    failed=0

```

## <a name="view-the-number-of-vm-instances"></a>VM 인스턴스 수 확인

[구성된 확장 집합](#configure-a-scale-set)에는 현재 두 인스턴스가 있습니다. 다음 단계는 해당 값을 확인하는 데 사용됩니다.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. 구성한 확장 집합으로 이동합니다.

1. 괄호로 묶은 인스턴스 수가 포함된 확장 집합 이름(`Standard_DS1_v2 (2 instances)`)이 표시됩니다.

1. 다음 명령을 실행하여 [Azure Cloud Shell](https://shell.azure.com/)에서 인스턴스 수를 확인할 수도 있습니다.

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    Cloud Shell에서 Azure CLI 명령을 실행한 결과는 이제 세 개의 인스턴스가 있음을 보여줍니다. 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="scale-out-a-scale-set"></a>확장 집합 규모 확장

이 섹션의 플레이북 코드는 확장 집합에 대한 정보를 검색하고 해당 용량을 2에서 3으로 변경합니다.

다음과 같은 두 가지 방법으로 샘플 플레이북을 가져올 수 있습니다.

* [플레이북을 다운로드](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml)하여 `vmss-scale-out.yml`에 저장합니다.
* `vmss-scale-out.yml`이라는 새 파일을 만들고 다음 콘텐츠를 이 파일에 복사합니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
  tasks: 
    - name: Get scaleset info
      azure_rm_virtualmachine_scaleset_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        format: curated
      register: output_scaleset

    - name: Dump scaleset info
      debug:
        var: output_scaleset

    - name: Modify scaleset (change the capacity to 3)
      set_fact:
        body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

    - name: Update something in that scale set
      azure_rm_virtualmachinescaleset: "{{ body }}"
```

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook vmss-scale-out.yml
```

플레이북을 실행하면 다음 결과와 유사한 출력이 표시됩니다.

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get scaleset info] 
ok: [localhost]

TASK [Dump scaleset info] 
ok: [localhost] => {
    "output_scaleset": {
        "ansible_facts": {
            "azure_vmss": [
                {
                    ......
                }
            ]
        },
        "changed": false,
        "failed": false
    }
}

TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] 
ok: [localhost]

TASK [Update something in that scale set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=5    changed=1    unreachable=0    failed=0
```

## <a name="verify-the-results"></a>결과 확인

Azure Portal을 통해 작업 결과를 확인합니다.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. 구성한 확장 집합으로 이동합니다.

1. 괄호로 묶은 인스턴스 수가 포함된 확장 집합 이름(`Standard_DS1_v2 (3 instances)`)이 표시됩니다. 

1. 다음 명령을 실행하여 [Azure Cloud Shell](https://shell.azure.com/)에서 변경을 확인할 수도 있습니다.

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    Cloud Shell에서 Azure CLI 명령을 실행한 결과는 이제 세 개의 인스턴스가 있음을 보여줍니다. 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [자습서: Ansible을 사용하여 Azure에서 가상 머신 확장 집합에 앱 배포](./vm-scale-set-deploy-app.md)
