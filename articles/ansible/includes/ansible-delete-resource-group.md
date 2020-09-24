---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 09/15/2020
ms.author: tarcher
ms.openlocfilehash: 885764615beed7a623db03499b4ff6a6ab705ba7
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831176"
---
#### <a name="ansible"></a>[Ansible](#tab/ansible)

1. 다음 코드를 `delete_rg.yml`로 저장합니다.

    ```yml
    ---
    - hosts: localhost
      tasks:
        - name: Deleting resource group - "{{ name }}"
          azure_rm_resourcegroup:
            name: "{{ name }}"
            state: absent
          register: rg
        - debug:
            var: rg
    ```

1. [ansible-playbook](https://docs.ansible.com/ansible/latest/user_guide/playbooks.html) 명령을 사용하여 플레이북을 실행합니다. 자리 표시자를 삭제할 리소스 그룹의 이름으로 바꿉니다. 리소스 그룹 내의 모든 리소스가 삭제됩니다.

    ```bash
    ansible-playbook delete_rg.yml --extra-vars "name=<resource_group>"
    ```

    **참고**:

    - 플레이북의 `register` 변수 및 `debug` 섹션으로 인해 명령이 완료되면 결과가 표시됩니다.
    
#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [az group delete](/cli/azure/group#az_group_delete)를 실행하여 리소스 그룹을 삭제합니다. 리소스 그룹 내의 모든 리소스가 삭제됩니다.

    ```azurecli
    az group delete --name <resource_group>
    ```

1. [az group show](/cli/azure/group#az_group_show)를 사용하여 리소스 그룹이 삭제되었는지 확인합니다.

    ```azurecli
    az group show --name <resource_group>
    ```

#### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

1. [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)을 실행하여 리소스 그룹을 삭제합니다. 리소스 그룹 내의 모든 리소스가 삭제됩니다.

    ```azurepowershell
    Remove-AzResourceGroup -Name <resource_group>
    ```

1. [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)을 사용하여 리소스 그룹이 삭제되었는지 확인합니다.

    ```azurepowershell
    Get-AzResourceGroup -Name <resource_group>
    ```

---