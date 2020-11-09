---
title: 자습서 - Ansible을 사용하여 Azure 리소스의 동적 인벤토리 구성
description: Ansible을 사용하여 Azure 동적 인벤토리를 관리하는 방법을 알아봅니다.
keywords: Ansible, Azure, DevOps, Bash, Cloud Shell, 동적 인벤토리
ms.topic: tutorial
ms.date: 10/30/2020
ms.custom: devx-track-ansible, devx-track-azurecli
ms.openlocfilehash: dd9a6f2b76c6d653eba9542d3b5dfdda4cb75ba5
ms.sourcegitcommit: e1175aa94709b14b283645986a34a385999fb3f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93192355"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>자습서: Ansible을 사용하여 Azure 리소스의 동적 인벤토리 구성

Ansible은 다양한 원본(Azure와 같은 클라우드 원본 포함)에서 *동적 인벤토리* 로 인벤토리 정보를 가져오는 데 사용할 수 있습니다. 

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 두 개의 테스트 가상 머신 구성
> * 가상 머신 중 하나에 태그 지정
> * 태그가 지정된 가상 머신에 Nginx 설치
> * 구성된 Azure 리소스를 포함하는 동적 인벤토리 구성

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>테스트 VM 만들기

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. [Cloud Shell](/azure/cloud-shell/overview)을 엽니다.

1. 이 자습서의 가상 머신을 보관할 Azure 리소스 그룹을 만듭니다.

    > [!IMPORTANT]
    > 이 단계에서 만든 Azure 리소스 그룹에는 모두 소문자로 구성된 이름이 있어야 합니다. 그렇지 않으면 동적 인벤토리를 생성하지 못합니다.

    ```azurecli
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. 다음 방법 중 하나를 사용하여 Azure에 두 개의 Linux 가상 머신을 만듭니다.

    - **Ansible 플레이북** - [Azure에서 Ansible을 사용하여 기본 가상 머신 만들기](./vm-configure.md) 문서에서 Ansible 플레이북으로부터 가상 머신을 만드는 방법을 보여 줍니다. 플레이북을 사용하여 두 개의 가상 머신 중 하나 또는 둘 다를 정의하는 경우 암호 대신 SSH 연결이 사용되는지 확인합니다.

    - **Azure CLI** - Cloud Shell에서 다음 명령을 각각 실행하여 두 가상 머신을 만듭니다.

        ```azurecli
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-vm"></a>VM에 태그 지정

사용자 정의 범주별로 [태그를 사용하여 Azure 리소스를 구성](/azure/azure-resource-manager/resource-group-using-tags#azure-cli)할 수 있습니다.

### <a name="using-ansible-version--28"></a>Ansible 2.8 이전 버전 사용
다음 [az resource tag](/cli/azure/resource#az-resource-tag) 명령을 입력하여 `ansible-inventory-test-vm1` 가상 머신에 `nginx` 키를 사용한 태그를 지정합니다.

```azurecli
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

### <a name="using-ansible-version--28"></a>Ansible 2.8 이상 버전 사용
다음 [az resource tag](/cli/azure/resource#az-resource-tag) 명령을 입력하여 `ansible-inventory-test-vm1` 가상 머신에 `Ansible=nginx` 키를 사용한 태그를 지정합니다.

```azurecli
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>동적 인벤토리 생성

가상 머신이 정의되고 태그가 지정되면 동적 인벤토리를 생성해야 합니다.

### <a name="using-ansible-version--28"></a>Ansible 2.8 이전 버전 사용

Ansible은 Azure 리소스의 동적 인벤토리를 생성하는 [azure_rm.py](https://github.com/ansible-collections/community.general/blob/main/scripts/inventory/azure_rm.py)라는 Python 스크립트를 제공합니다. 다음 단계에서는 `azure_rm.py` 스크립트를 사용하여 두 개의 테스트 Azure Virtual Machines에 연결하는 과정을 안내합니다.

1. GNU `wget` 명령을 사용하여 `azure_rm.py` 스크립트를 검색합니다.

    ```bash
    wget https://raw.githubusercontent.com/ansible-collections/azure/dev/plugins/inventory/azure_rm.py
    ```

1. `chmod` 명령을 사용하여 `azure_rm.py` 스크립트에 대한 액세스 권한을 변경합니다. 다음 명령은 `+x` 매개 변수를 사용하여 지정된 파일(`azure_rm.py`)의 실행을 허용합니다.

    ```bash
    chmod +x azure_rm.py
    ```

1. [ansible 명령](https://docs.ansible.com/ansible/2.4/ansible.html)을 사용하여 리소스 그룹에 연결합니다.

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```

1. 연결되면 만들어지는 가상 머신의 결과를 나타내는 결과가 표시됩니다.

### <a name="ansible-version--28"></a>Ansible 2.8 이상 버전

Ansible 2.8부터 Ansible은 [Azure 동적 인벤토리 플러그 인](https://github.com/ansible/ansible/blob/stable-2.9/lib/ansible/plugins/inventory/azure_rm.py)을 제공합니다. 다음 단계는 이 플러그 인을 사용하는 과정을 안내합니다.

1. 인벤토리 플러그 인에는 구성 파일이 필요합니다. 구성 파일은 `azure_rm`으로 끝나야 하고 확장명이 `yml` 또는 `yaml`이어야 합니다. 이 자습서 예제에서는 다음 플레이북을 `myazure_rm.yml`로 저장합니다.

    ```yml
        plugin: azure_rm
        include_vm_resource_groups:
        - ansible-inventory-test-rg
        auth_source: auto
    
        keyed_groups:
        - prefix: tag
          key: tags
    ```

1. 다음 명령을 실행하여 리소스 그룹의 VM을 Ping합니다.

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. 위의 명령을 실행하면 다음과 같은 오류가 발생할 수 있습니다. 이 오류는 다음과 같은 이유로 인해 호스트에 연결하지 못했음을 나타냅니다. 

    ```output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    "호스트 키 확인" 오류가 발생하면 Ansible 구성 파일에 다음 줄을 추가합니다. Ansible 구성 파일은 `/etc/ansible/ansible.cfg` 또는 `~/.ansible.cfg`에 있습니다.

    ```bash
    host_key_checking = False
    ```

1. 플레이북이 실행되면 다음 출력과 비슷한 결과가 표시됩니다.
  
    ```output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>VM 태그를 사용하도록 설정

### <a name="if-youre-using-ansible--28"></a>Ansible 2.8 미만을 사용하는 경우

- 태그를 설정한 경우 태그를 "사용하도록 설정"해야 합니다. 태그를 사용하도록 설정하는 한 가지 방법은 `export` 명령을 통해 환경 변수 `AZURE_TAGS`에 태그를 내보내는 것입니다.

    ```console
    export AZURE_TAGS=nginx
    ```
    
- 다음 명령 실행:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```
    
    이제 하나의 가상 머신(태그가 `AZURE_TAGS` 환경 변수로 내보낸 값과 일치하는 가상 머신)만 표시됩니다.

    ```output
       ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="if-youre-using-ansible---28"></a>Ansible 2.8 이상을 사용하는 경우

- `ansible-inventory -i myazure_rm.yml --graph` 명령을 실행하여 다음 출력을 가져옵니다.

    ```output
        @all:
          |--@tag_Ansible_nginx:
          |  |--ansible-inventory-test-vm1_9e2f
          |--@ungrouped:
          |  |--ansible-inventory-test-vm2_7ba9
    ```

- 다음 명령을 실행하여 Nginx VM에 대한 연결을 테스트할 수도 있습니다.
  
    ```bash
    ansible -i ./myazure_rm.yml -m ping tag_Ansible_nginx
    ```


## <a name="set-up-nginx-on-the-tagged-vm"></a>태그가 지정된 VM에 Nginx 설정

태그의 목적은 가상 머신의 하위 그룹을 빠르고 쉽게 사용할 수 있도록 하는 것입니다. 예를 들어 `nginx` 태그를 할당한 가상 머신에만 Nginx를 설치하려고 한다고 가정해 보겠습니다. 다음 단계에서는 수행하는 방법이 쉽다는 것을 보여 줍니다.

1. `nginx.yml` 파일을 만듭니다.

   ```console
   code nginx.yml
   ```

1. 다음 샘플 코드를 편집기에 붙여넣습니다.

    ```yml
        ---
        - name: Install and start Nginx on an Azure virtual machine
          hosts: all
          become: yes
          tasks:
          - name: install nginx
            apt: pkg=nginx state=present
            notify:
            - start nginx
    
          handlers:
            - name: start nginx
              service: name=nginx state=started
    ```

1. 파일을 저장하고 편집기를 종료합니다.

1. [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

   - Ansible 2.8 이전 버전:

     ```bash
     ansible-playbook -i azure_rm.py nginx.yml
     ```

   - Ansible 2.8 이상 버전:

     ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml --limit=tag_Ansible_nginx
     ```

1. 플레이북을 실행하면 다음 결과와 유사한 출력이 표시됩니다.

    ```output
    PLAY [Install and start Nginx on an Azure virtual machine] 

    TASK [Gathering Facts] 
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] 
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] 
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP 
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Nginx 설치 테스트

이 섹션에서는 가상 머신에 Nginx가 설치되었는지 테스트하는 한 가지 방법을 설명합니다.

1. [az vm list-ip-addresses](/cli/azure/vm#az-vm-list-ip-addresses) 명령을 사용하여 `ansible-inventory-test-vm1` 가상 머신의 IP 주소를 검색합니다. 그런 다음, 반환된 값(가상 머신의 IP 주소)은 가상 머신에 연결하기 위한 SSH 명령에 대한 매개 변수로 사용됩니다.

    ```azurecli
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. `ansible-inventory-test-vm1` 가상 머신에 연결된 상태에서 [nginx -v](https://nginx.org/en/docs/switches.html) 명령을 실행하여 Nginx가 설치되었는지 확인합니다.

    ```console
    nginx -v
    ```

1. `nginx -v` 명령이 실행되면 Nginx가 설치되었음을 나타내는 Nginx 버전(두 번째 줄)이 표시됩니다.

    ```output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)

    tom@ansible-inventory-test-vm1:~$
    ```

1. SSH 세션의 연결을 끊으려면 `<Ctrl>D` 키보드 조합을 누릅니다.

1. `ansible-inventory-test-vm2` 가상 머신에 대해 앞의 단계를 수행하면 Nginx를 얻을 수 있는 위치를 나타내는 정보 메시지가 표시됩니다(현재 설치되어 있지 않음을 나타냄).

    ```output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [ansible-delete-resource-group.md](includes/ansible-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [빠른 시작: Ansible을 사용하여 Azure에서 Linux 가상 머신 구성](./vm-configure.md)
