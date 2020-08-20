---
title: 자습서 - Ansible을 사용하여 AKS(Azure Kubernetes Service)에서 Azure CNI 네트워킹 구성
description: Ansible을 사용하여 AKS(Azure Kubernetes Service) 클러스터에서 kubenet 네트워킹을 구성하는 방법 알아보기
keywords: ansible, azure, devops, bash, cloudshell, 플레이북, aks, 컨테이너, aks, kubernetes
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: f3892b9c25b952d2d8c71e4e44857557c0a1813b
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88239955"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>자습서: Ansible을 사용하여 AKS(Azure Kubernetes Service)에서 Azure CNI 네트워킹 구성

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../includes/open-source-devops-intro-aks.md)]

AKS를 사용하여 다음 네트워크 모델을 통해 클러스터를 배포할 수 있습니다.

- [Kubenet 네트워킹](/azure/aks/configure-kubenet) - 네트워크 리소스는 일반적으로 AKS 클러스터가 배포될 때 만들어지고 구성됩니다.
- [Azure CNI 네트워킹](/azure/aks/configure-azure-cni) - AKS 클러스터가 기존 VNET(가상 네트워크) 리소스 및 구성에 연결됩니다.

네트워크를 통해 AKS의 애플리케이션에 연결하는 방법에 대한 자세한 내용은 [AKS의 애플리케이션에 대한 네트워크 개념](/azure/aks/concepts-network)을 참조하세요.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * AKS 클러스터 만들기
> * Azure CNI 네트워킹 구성

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 

## <a name="create-a-virtual-network-and-subnet"></a>가상 네트워크 및 서브넷 만들기

이 섹션의 샘플 플레이북 코드는 다음에 사용됩니다.

- 가상 네트워크 만들기
- 가상 네트워크 내에서 서브넷 만들기

다음 플레이북을 `vnet.yml`로 저장합니다.

```yml
- name: Create vnet
  azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefixes_cidr:
          - 10.0.0.0/8

- name: Create subnet
  azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefix_cidr: 10.240.0.0/16
      virtual_network_name: "{{ name }}"
  register: subnet
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>가상 네트워크에서 AKS 클러스터 만들기

이 섹션의 샘플 플레이북 코드는 다음에 사용됩니다.

- 가상 네트워크 내에서 AKS 클러스터 만들기

다음 플레이북을 `aks.yml`로 저장합니다.

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster within a VNet
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
          vnet_subnet_id: "{{ vnet_subnet_id }}"
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      network_profile:
          network_plugin: azure
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.2.0.10
          service_cidr: 10.2.0.0/24
  register: aks
```

다음은 샘플 플레이북을 사용할 때 고려할 몇 가지 주요 참고 사항입니다.

- `azure_rm_aks_version` 모듈을 사용하여 지원되는 버전을 확인합니다.
- `vnet_subnet_id`는 이전 섹션에서 만든 서브넷입니다.
- 플레이북은 `~/.ssh/id_rsa.pub`에서 `ssh_key`를 로드합니다. 수정할 때는 "ssh-rsa"(따옴표 제외)로 시작하는 단일 줄 형식을 사용합니다.
- `client_id` 및 `client_secret` 값은 기본 자격 증명 파일인 `~/.azure/credentials`에서 로드됩니다. 다음과 같이 이러한 값을 서비스 주체로 설정하거나 환경 변수에서 이러한 값을 로드할 수 있습니다.

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>샘플 플레이북 실행

이 섹션의 샘플 플레이북 코드는 이 자습서 전체에 나오는 다양한 기능을 테스트하는 데 사용됩니다.

다음 플레이북을 `aks-azure-cni.yml`로 저장합니다.

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exists
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Show AKS cluster detail
       debug:
           var: aks
```

다음은 샘플 플레이북을 사용할 때 고려할 몇 가지 주요 참고 사항입니다.

- `aksansibletest` 값을 리소스 그룹 이름으로 변경합니다.
- `aksansibletest` 값을 AKS 이름으로 변경합니다.
- `eastus` 값을 리소스 그룹 위치로 변경합니다.

다음과 같이 ansible-playbook 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook aks-azure-cni.yml
```

플레이북을 실행하면 다음 결과와 유사한 출력이 표시됩니다.

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exists] 
changed: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-cni/vnet.yml for localhost

TASK [Create vnet] 
changed: [localhost]

TASK [Create subnet] 
changed: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-cni/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "aks": {
        "aad_profile": {},
        "addon": {},
        "agent_pool_profiles": [
            {
                "count": 3,
                "name": "nodepool1",
                "os_disk_size_gb": 100,
                "os_type": "Linux",
                "storage_profile": "ManagedDisks",
                "vm_size": "Standard_D2_v2",
                "vnet_subnet_id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
            }
        ],
        "changed": true,
        "dns_prefix": "aksansibletest",
        "enable_rbac": false,
        "failed": false,
        "fqdn": "aksansibletest-0272707d.hcp.eastus.azmk8s.io",
        "id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "..."
        },
        "location": "eastus",
        "name": "aksansibletest",
        "network_profile": {
            "dns_service_ip": "10.2.0.10",
            "docker_bridge_cidr": "172.17.0.1/16",
            "network_plugin": "azure",
            "network_policy": null,
            "pod_cidr": null,
            "service_cidr": "10.2.0.0/24"
        },
        "node_resource_group": "MC_aksansibletest_aksansibletest_eastus",
        "provisioning_state": "Succeeded",
        "service_principal_profile": {
            "client_id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
        },
        "tags": null,
        "type": "Microsoft.ContainerService/ManagedClusters",
        "warnings": [
            "Azure API profile latest does not define an entry for ContainerServiceClient",
            "Azure API profile latest does not define an entry for ContainerServiceClient"
        ]
    }
}

PLAY RECAP 
localhost                  : ok=9    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 이 문서에서 만든 리소스를 삭제합니다. 

이 섹션의 샘플 플레이북 코드는 다음에 사용됩니다.

- `vars` 섹션에서 참조된 리소스 그룹을 참조합니다.

다음 플레이북을 `cleanup.yml`로 저장합니다.

```yml
---
- hosts: localhost
  vars:
      resource_group: {{ resource_group_name }}
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

다음은 샘플 플레이북을 사용할 때 고려할 몇 가지 주요 참고 사항입니다.

- `{{ resource_group_name }}` 자리 표시자를 리소스 그룹의 이름으로 바꿉니다.
- 지정된 리소스 그룹 내의 모든 리소스가 삭제됩니다.

다음과 같이 ansible-playbook 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: Ansible을 사용하여 AKS에서 Azure Active Directory 구성](./aks-configure-rbac.md)