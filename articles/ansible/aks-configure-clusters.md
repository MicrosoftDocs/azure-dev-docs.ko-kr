---
title: 자습서 - Azure에서 Ansible을 사용하여 AKS(Azure Kubernetes Service) 클러스터 구성
description: Ansible을 사용하여 Azure에서 Azure Kubernetes Service 클러스터를 만들고 관리하는 방법 알아보기
keywords: ansible, azure, devops, bash, cloudshell, 플레이북, aks, 컨테이너, aks, kubernetes
ms.topic: tutorial
ms.date: 06/17/2020
ms.custom: devx-track-ansible
ms.openlocfilehash: 0e7b42776c0405acf335dc75508ef2759838da21
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240045"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>자습서: Azure에서 Ansible을 사용하여 AKS(Azure Kubernetes Service) 클러스터 구성

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../includes/open-source-devops-intro-aks.md)]

사용자 인증에 [Azure AD(Active Directory)](/azure/active-directory/)를 사용하도록 AKS를 구성할 수 있습니다. 구성되면 Azure AD 인증 토큰을 사용하여 AKS 클러스터에 로그인합니다. RBAC는 사용자의 ID 또는 디렉터리 그룹 멤버 자격을 기반으로 할 수 있습니다.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * AKS 클러스터 만들기
> * AKS 클러스터 구성

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>관리되는 AKS 클러스터 만들기

이 샘플 플레이북은 리소스 그룹 및 리소스 그룹 내부의 AKS 클러스터를 만듭니다.

다음 플레이북을 `azure_create_aks.yml`로 저장합니다.

```yml
- name: Create Azure Kubernetes Service
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
    aks_version: aks_version
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create a managed Azure Container Services (AKS) cluster
    azure_rm_aks:
      name: "{{ aks_name }}"
      location: "{{ location }}"
      resource_group: "{{ resource_group }}"
      dns_prefix: "{{ aks_name }}"
      kubernetes_version: "{{aks_version}}"
      linux_profile:
        admin_username: "{{ username }}"
        ssh_key: "{{ ssh_key }}"
      service_principal:
        client_id: "{{ client_id }}"
        client_secret: "{{ client_secret }}"
      agent_pool_profiles:
        - name: default
          count: 2
          vm_size: Standard_D2_v2
      tags:
        Environment: Production
```

플레이북을 실행하기 전에 다음 정보를 참조하세요.

- `tasks` 내부의 첫 번째 섹션은 `eastus` 위치 내에 `myResourceGroup`이라는 리소스 그룹을 정의합니다.
- `tasks` 내부의 두 번째 섹션은 `myResourceGroup` 리소스 그룹 내에 `myAKSCluster`라는 AKS 클러스터를 정의합니다.
- `your_ssh_key` 자리 표시자의 경우 "ssh-rsa"로 시작하는 단일 줄 형식으로 RSA 공개 키를 입력합니다(따옴표 제외).
- `aks_version` 자리 표시자의 경우 [az aks get-versions](/cli/azure/aks?view=azure-cli-latest#az-aks-get-versions) 명령을 사용합니다.

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook azure_create_aks.yml
```

플레이북을 실행하면 다음 출력과 비슷한 결과가 표시됩니다.

```Output
PLAY [Create AKS] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create an Azure Container Services (AKS) cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="scale-aks-nodes"></a>AKS 노드 크기 조정

이전 섹션에서 샘플 플레이북은 두 노드를 정의합니다. `agent_pool_profiles` 블록에서 `count` 값을 수정하여 노드 수를 조정합니다.

다음 플레이북을 `azure_configure_aks.yml`로 저장합니다.

```yml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

플레이북을 실행하기 전에 다음 정보를 참조하세요.

- `your_ssh_key` 자리 표시자의 경우 "ssh-rsa"로 시작하는 단일 줄 형식으로 RSA 공개 키를 입력합니다(따옴표 제외).

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook azure_configure_aks.yml
```

플레이북을 실행하면 다음 출력과 비슷한 결과가 표시됩니다.

```Output
PLAY [Scale AKS cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Scaling an existed AKS cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="delete-a-managed-aks-cluster"></a>관리되는 AKS 클러스터 삭제

샘플 플레이북은 AKS 클러스터를 삭제합니다.

다음 플레이북을 `azure_delete_aks.yml`로 저장합니다.


```yml
- name: Delete a managed Azure Container Services (AKS) cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    aks_name: myAKSCluster
  tasks:
  - name:
    azure_rm_aks:
      name: "{{ aks_name }}"
      resource_group: "{{ resource_group }}"
      state: absent
  ```

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook azure_delete_aks.yml
```

플레이북을 실행하면 다음 출력과 비슷한 결과가 표시됩니다.

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: AKS(Azure Kubernetes Service)에서 애플리케이션 크기 조정](/azure/aks/tutorial-kubernetes-scale)