---
title: 자습서 - AKS(Azure Kubernetes Service)에서 Ansible을 사용하여 kubenet 네트워킹 구성
description: Ansible을 사용하여 AKS(Azure Kubernetes Service) 클러스터에서 kubenet 네트워킹을 구성하는 방법 알아보기
keywords: ansible, azure, devops, bash, cloudshell, 플레이북, aks, 컨테이너, aks, kubernetes
ms.topic: tutorial
ms.date: 10/23/2019
ms.custom: devx-track-ansible,fasttrack-edit
ms.openlocfilehash: 55b3f2ec248e3163a6916c8a6067957f6a0c9ae1
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88239775"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>자습서: AKS(Azure Kubernetes Service)에서 Ansible을 사용하여 kubenet 네트워킹 구성

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../includes/open-source-devops-intro-aks.md)]

AKS를 사용하여 다음 네트워크 모델을 통해 클러스터를 배포할 수 있습니다.

- [Kubenet 네트워킹](/azure/aks/configure-kubenet) - 네트워크 리소스는 일반적으로 AKS 클러스터가 배포될 때 만들어지고 구성됩니다.
- [Azure CNI(컨테이너 네트워킹 인터페이스) 네트워킹](/azure/aks/configure-azure-cni) - AKS 클러스터가 기존 가상 네트워크 리소스 및 구성에 연결됩니다.

네트워크를 통해 AKS의 애플리케이션에 연결하는 방법에 대한 자세한 내용은 [AKS의 애플리케이션에 대한 네트워크 개념](/azure/aks/concepts-network)을 참조하세요.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * AKS 클러스터 만들기
> * Azure kubenet 네트워킹 구성

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>가상 네트워크 및 서브넷 만들기

이 섹션의 플레이북 코드는 다음 Azure 리소스 그룹을 만듭니다.

- 가상 네트워크
- 가상 네트워크 내부의 서브넷

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

이 섹션의 플레이북 코드는 가상 네트워크 내부에 AKS 클러스터를 만듭니다. 

다음 플레이북을 `aks.yml`로 저장합니다.

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with vnet
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
          network_plugin: kubenet
          pod_cidr: 192.168.0.0/16
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.0.0.10
          service_cidr: 10.0.0.0/16
  register: aks
```

다음은 샘플 플레이북을 사용할 때 고려할 몇 가지 주요 참고 사항입니다.

- `azure_rm_aks_version` 모듈을 사용하여 지원되는 버전을 찾습니다.
- `vnet_subnet_id`는 이전 섹션에서 만든 서브넷입니다.
- `network_profile`은 kubenet 네트워크 플러그 인의 속성을 정의합니다.
- `service_cidr`은 AKS 클러스터의 내부 서비스를 IP 주소에 할당하는 데 사용됩니다. 이 IP 주소 범위는 AKS 클러스터 외부에서 사용되지 않는 주소 공간이어야 합니다. 하지만 여러 AKS 클러스터에 동일한 서비스 CIDR을 다시 사용할 수 있습니다. 
- `dns_service_ip` 주소는 서비스 IP 주소 범위의 ".10" 주소여야 합니다.
- `pod_cidr`은 네트워크 환경의 다른 위치에서 사용되지 않는 큰 주소 공간이어야 합니다. 이 주소 범위는 앞으로 강화할 노드 수를 수용할 수 있을 정도로 커야 합니다. 클러스터가 배포된 후에는 이 주소 범위를 변경할 수 없습니다. 서비스 CIDR과 마찬가지로, 이 IP 범위는 AKS 클러스터 외부에 존재하면 안 되지만 클러스터 전체에서 안전하게 다시 사용할 수 있습니다.
- Pod IP 주소 범위는 /24 주소 공간을 클러스터의 각 노드에 할당하는 데 사용됩니다. 다음 예제에서 192.168.0.0/16의 `pod_cidr`은 첫 번째 노드 192.168.0.0/24, 두 번째 노드 192.168.1.0/24 및 세 번째 노드 192.168.2.0/24를 할당합니다.
- 클러스터가 확장 또는 업그레이드되면 Azure는 새로운 각 노드에 Pod IP 주소 범위를 계속 할당합니다.
- 플레이북은 `~/.ssh/id_rsa.pub`에서 `ssh_key`를 로드합니다. 수정할 때는 "ssh-rsa"(따옴표 제외)로 시작하는 단일 줄 형식을 사용합니다.
- `client_id` 및 `client_secret` 값은 기본 자격 증명 파일인 `~/.azure/credentials`에서 로드됩니다. 다음과 같이 이러한 값을 서비스 주체로 설정하거나 환경 변수에서 이러한 값을 로드할 수 있습니다.

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>네트워크 리소스 연결

AKS 클러스터를 만들면 네트워크 보안 그룹 및 경로 테이블이 생성됩니다. 이러한 리소스는 AKS를 통해 관리되며, 사용자가 서비스를 만들고 노출할 때 업데이트됩니다. 다음과 같이 네트워크 보안 그룹 및 경로 테이블을 가상 네트워크 서브넷에 연결합니다. 

다음 플레이북을 `associate.yml`로 저장합니다.

```yml
- name: Get route table
  azure_rm_routetable_facts:
      resource_group: "{{ node_resource_group }}"
  register: routetable

- name: Get network security group
  azure_rm_securitygroup_facts:
      resource_group: "{{ node_resource_group }}"
  register: nsg

- name: Parse subnet id
  set_fact:
      subnet_name: "{{ vnet_subnet_id | regex_search(subnet_regex, '\\1') }}"
      subnet_rg: "{{ vnet_subnet_id | regex_search(rg_regex, '\\1') }}"
      subnet_vn: "{{ vnet_subnet_id | regex_search(vn_regex, '\\1') }}"
  vars:
      subnet_regex: '/subnets/(.+)'
      rg_regex: '/resourceGroups/(.+?)/'
      vn_regex: '/virtualNetworks/(.+?)/'

- name: Associate network resources with the node subnet
  azure_rm_subnet:
      name: "{{ subnet_name[0] }}"
      resource_group: "{{  subnet_rg[0] }}"
      virtual_network_name: "{{ subnet_vn[0] }}"
      security_group: "{{ nsg.ansible_facts.azure_securitygroups[0].id }}"
      route_table: "{{ routetable.route_tables[0].id }}"
```

다음은 샘플 플레이북을 사용할 때 고려할 몇 가지 주요 참고 사항입니다.

- `node_resource_group`은 AKS 노드가 만들어지는 리소스 그룹 이름입니다.
- `vnet_subnet_id`는 이전 섹션에서 만든 서브넷입니다.


## <a name="run-the-sample-playbook"></a>샘플 플레이북 실행

이 섹션에서는 이 문서에서 만드는 작업을 호출하는 완전한 샘플 플레이북을 나열합니다. 

다음 플레이북을 `aks-kubenet.yml`로 저장합니다.

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Associate network resources with the node subnet
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
           node_resource_group: "{{ aks.node_resource_group }}"
       include_tasks: associate.yml

     - name: Get details of the AKS
       azure_rm_aks_facts:
           name: "{{ name }}"
           resource_group: "{{ resource_group }}"
           show_kubeconfig: user
       register: output

     - name: Show AKS cluster detail
       debug:
           var: output.aks[0]
```

`vars` 섹션에서 다음과 같이 변경합니다.

- `resource_group` 키에서 `aksansibletest` 값을 리소스 그룹 이름으로 변경합니다.
- `name` 키에서 `aksansibletest` 값을 AKS 이름으로 변경합니다.
- `Location` 키에서 `eastus` 값을 리소스 그룹 위치로 변경합니다.

`ansible-playbook` 명령을 사용하여 완전한 플레이북을 실행합니다.

```bash
ansible-playbook aks-kubenet.yml
```

플레이북을 실행하면 다음 출력과 비슷한 결과가 표시됩니다.

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exist] 
ok: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-kubenet/vnet.yml for localhost

TASK [Create vnet] 
ok: [localhost]

TASK [Create subnet] 
ok: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-kubenet/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Associate network resources with the node subnet] 
included: /home/devops/aks-kubenet/associate.yml for localhost

TASK [Get route table] 
ok: [localhost]

TASK [Get network security group] 
ok: [localhost]

TASK [Parse subnet id] 
ok: [localhost]

TASK [Associate network resources with the node subnet] 
changed: [localhost]

TASK [Get details of the AKS] 
ok: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "output.aks[0]": {
        "id": /subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "apiVersion: ...",
        "location": "eastus",
        "name": "aksansibletest",
        "properties": {
            "agentPoolProfiles": [
                {
                    "count": 3,
                    "maxPods": 110,
                    "name": "nodepool1",
                    "osDiskSizeGB": 100,
                    "osType": "Linux",
                    "storageProfile": "ManagedDisks",
                    "vmSize": "Standard_D2_v2",
                    "vnetSubnetID": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
                }
            ],
            "dnsPrefix": "aksansibletest",
            "enableRBAC": false,
            "fqdn": "aksansibletest-cda2b56c.hcp.eastus.azmk8s.io",
            "kubernetesVersion": "1.12.6",
            "linuxProfile": {
                "adminUsername": "azureuser",
                "ssh": {
                    "publicKeys": [
                        {
                            "keyData": "ssh-rsa ..."
                        }
                    ]
                }
            },
            "networkProfile": {
                "dnsServiceIP": "10.0.0.10",
                "dockerBridgeCidr": "172.17.0.1/16",
                "networkPlugin": "kubenet",
                "podCidr": "192.168.0.0/16",
                "serviceCidr": "10.0.0.0/16"
            },
            "nodeResourceGroup": "MC_aksansibletest_pcaksansibletest_eastus",
            "provisioningState": "Succeeded",
            "servicePrincipalProfile": {
                "clientId": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
            }
        },
        "type": "Microsoft.ContainerService/ManagedClusters"
    }
}

PLAY RECAP 
localhost                  : ok=15   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 이 문서에서 만든 리소스를 삭제합니다. 

다음 코드를 `cleanup.yml`로 저장합니다.

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

`vars` 섹션에서 `{{ resource_group_name }}` 자리 표시자를 리소스 그룹의 이름으로 바꿉니다.

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서 - AKS에서 Ansible을 사용하여 Azure CNI(컨테이너 네트워킹 인터페이스) 구성](./aks-configure-cni-networking.md)
