---
title: 빠른 시작 - Ansible을 사용하여 Azure에서 Linux 가상 머신 관리
description: 이 빠른 시작에서는 Ansible을 사용하여 Azure에서 Linux 가상 머신을 관리하는 방법을 알아봅니다.
keywords: Ansible, Azure, DevOps, Bash, cloudshell, 플레이북, Bash
ms.topic: quickstart
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 354c54191d108dc7a58f2813225a40b75968a4b2
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240625"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>빠른 시작: Ansible을 사용하여 Azure에서 Linux 가상 머신 관리

Ansible을 사용하면 사용자 환경에서 리소스의 배포 및 구성을 자동화할 수 있습니다. 이 문서에서는 Ansible 플레이북을 사용하여 Linux 가상 머신을 시작하고 중지합니다. 

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>가상 머신 중지

이 섹션에서는 Ansible을 사용하여 Azure 가상 머신의 할당을 취소(중지)합니다.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. [Cloud Shell](/azure/cloud-shell/overview)을 엽니다.

1. 다음과 같이 `azure-vm-stop.yml`이라는 파일을 만들고 편집기에서 엽니다.

    ```bash
    code azure-vm-stop.yml
    ```

1. 다음 샘플 코드를 편집기에 붙여넣습니다.

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Stop virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
            allocated: no
    ```

1. `{{ resource_group_name }}` 및 `{{ vm_name }}` 자리 표시자를 해당 값으로 바꿉니다.

1. 파일을 저장하고 편집기를 종료합니다.

1. 다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. 플레이북을 실행하면 다음 결과와 유사한 출력이 표시됩니다.

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="start-a-virtual-machine"></a>가상 머신 시작

이 섹션에서는 Ansible을 사용하여 할당 취소된(중지된) Azure 가상 머신을 시작합니다.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. [Cloud Shell](/azure/cloud-shell/overview)을 엽니다.

1. 다음과 같이 `azure-vm-start.yml`이라는 파일을 만들고 편집기에서 엽니다.

    ```bash
    code azure-vm-start.yml
    ```

1. 다음 샘플 코드를 편집기에 붙여넣습니다.

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Start virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
    ```

1. `{{ resource_group_name }}` 및 `{{ vm_name }}` 자리 표시자를 해당 값으로 바꿉니다.

1. 파일을 저장하고 편집기를 종료합니다.

1. 다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. 플레이북을 실행하면 다음 결과와 유사한 출력이 표시됩니다.

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [자습서: Ansible을 사용하여 Azure 동적 인벤토리 관리](./dynamic-inventory-configure.md)