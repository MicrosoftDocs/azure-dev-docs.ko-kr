---
title: 자습서 - Ansible을 사용하여 AKS(Azure Kubernetes Service)에서 RBAC(역할 기반 액세스 제어) 역할 구성
description: Ansible을 사용하여 AKS(Azure Kubernetes Service) 클러스터에서 RBAC를 구성하는 방법 알아보기
keywords: ansible, azure, devops, bash, cloudshell, 플레이북, aks, 컨테이너, kubernetes, azure active directory, rbac
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 7fef950e1b0b953d73ffd28f59e23fd2c715db2c
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90681788"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>자습서: Ansible을 사용하여 AKS(Azure Kubernetes Service)에서 RBAC(역할 기반 액세스 제어) 역할 구성

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../includes/open-source-devops-intro-aks.md)]

사용자 인증에 [Azure AD(Active Directory)](/azure/active-directory/)를 사용하도록 AKS를 구성할 수 있습니다. 구성되면 Azure AD 인증 토큰을 사용하여 AKS 클러스터에 로그인합니다. RBAC는 사용자의 ID 또는 디렉터리 그룹 멤버 자격을 기반으로 할 수 있습니다.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Azure AD 사용 AKS 클러스터 만들기
> * 클러스터에서 RBAC 역할 구성

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **RedHat OpenShift 라이브러리 설치** - `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>AKS 인증을 위해 Azure AD 구성

AKS 인증을 위해 Azure AD를 구성할 때 두 개의 Azure AD 애플리케이션을 구성합니다. Azure 테넌트 관리자가 이 작업을 완료해야 합니다. 자세한 내용은 [AKS와 Azure Active Directory 통합](/azure/aks/aad-integration#create-the-server-application)을 참조하세요. 

Azure 테넌트 관리자에서 다음 값을 가져옵니다.

- 서버 앱 비밀
- 서버 앱 ID
- 클라이언트 앱 ID 
- 테넌트 ID

샘플 플레이북을 실행하려면 이러한 값이 필요합니다.  

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

이 섹션에서는 [Azure AD 애플리케이션](#configure-azure-ad-for-aks-authentication)을 사용하여 AKS를 만듭니다.

다음은 샘플 플레이북을 사용할 때 고려할 몇 가지 주요 참고 사항입니다.

- 플레이북은 `~/.ssh/id_rsa.pub`에서 `ssh_key`를 로드합니다. 수정할 때는 "ssh-rsa"(따옴표 제외)로 시작하는 단일 줄 형식을 사용합니다.
- `client_id` 및 `client_secret` 값은 기본 자격 증명 파일인 `~/.azure/credentials`에서 로드됩니다. 다음과 같이 이러한 값을 서비스 주체로 설정하거나 환경 변수에서 이러한 값을 로드할 수 있습니다.

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

다음 플레이북을 `aks-create.yml`로 저장합니다.

```yml
- name: Create resource group
  azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

- name: List supported kubernetes version from Azure
  azure_rm_aksversion_facts:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with RBAC enabled
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      enable_rbac: yes
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      aad_profile:
          client_app_id: "{{ client_app_id }}"
          server_app_id: "{{ server_app_id }}"
          server_app_secret: "{{ server_app_secret }}"
          tenant_id: "{{ app_tenant_id }}"
  register: aks

- name: Save cluster user config
  copy:
      content: "{{ aks.kube_config }}"
      dest: "aks-{{ name }}-kubeconfig-user"

- name: Get admin config of AKS
  azure_rm_aks_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      show_kubeconfig: admin
  register: aks

- name: Save the kubeconfig
  copy:
      content: "{{ aks.aks[0].kube_config }}"
      dest: "aks-{{ name }}-kubeconfig"
```

## <a name="get-the-azure-ad-object-id"></a>Azure AD 개체 ID 가져오기

RBAC 바인딩을 만들려면 먼저 Azure AD 개체 ID를 가져와야 합니다. 

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. 페이지 맨 위에 있는 검색 필드에 `Azure Active Directory`를 입력합니다. 

1. `Enter`을 클릭합니다.

1. **관리** 메뉴에서 **사용자**를 선택합니다.

1. 이름 필드에서 계정을 검색합니다.

1. **이름** 열에서 계정의 링크를 선택합니다.

1. **ID** 섹션에서 **개체 ID**를 복사합니다.

    ![Azure AD 개체 ID를 복사합니다.](./media/aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>RBAC 바인딩 만들기

이 섹션에서는 AKS에서 역할 바인딩 또는 클러스터 역할 바인딩을 만듭니다. 

다음 플레이북을 `kube-role.yml`로 저장합니다.

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: <your-aad-account>
```

`<your-aad-account>` 자리 표시자를 Azure AD 테넌트 [개체 ID](#get-the-azure-ad-object-id)로 바꿉니다.

새 역할을 AKS에 배포하는 다음 플레이북을 `aks-kube-deploy.yml`로 저장합니다.

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>샘플 플레이북 실행

이 섹션에서는 이 문서에서 만드는 작업을 호출하는 완전한 샘플 플레이북을 나열합니다. 

다음 플레이북을 `aks-rbac.yml`로 저장합니다.

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

     - name: Create AKS
       vars:
           client_app_id: <client id>
           server_app_id: <server id>
           server_app_secret: <server secret>
           app_tenant_id: <tenant id>
       include_tasks: aks-create.yml

     - name: Enable RBAC
       include_tasks: aks-kube-deploy.yml
```

`vars` 섹션에서 다음 자리 표시자를 해당하는 Azure AD 정보로 바꿉니다.

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

`ansible-playbook` 명령을 사용하여 완전한 플레이북을 실행합니다.

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>결과 확인

이 섹션에서는 kubectl을 사용하여 이 문서에서 만드는 노드를 나열합니다.

터미널 프롬프트에 다음 명령을 입력합니다.

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

이 명령은 인증 페이지로 안내합니다. Azure 계정으로 로그인합니다.

인증되면 kubectl은 다음 결과와 비슷한 방식으로 노드를 나열합니다.

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 이 문서에서 만든 리소스를 삭제합니다. 

다음 코드를 `cleanup.yml`로 저장합니다.

```yml
---
- hosts: localhost
  vars:
      name: aksansibletest
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
      - name: Remove kubeconfig
        file:
            state: absent
            path: "aks-{{ name }}-kubeconfig"
```

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure의 Ansible](/azure/ansible/)
