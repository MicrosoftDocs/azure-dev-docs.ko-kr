---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 09/15/2020
ms.author: tarcher
ms.openlocfilehash: 5351445b63618d072ecf4cf8beeffc9a071bd84d
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90682024"
---
이 섹션에서는 새 Ansible 구성 내에서 테스트 리소스 그룹을 만드는 방법을 보여줍니다. 이렇게 할 필요가 없으면 이 섹션을 건너뛸 수 있습니다.

### <a name="create-an-azure-resource-group"></a>Azure 리소스 그룹 만들기

1. 다음 코드를 `create_rg.yml`로 저장합니다.

    ```yaml
    ---
    - hosts: localhost
      connection: local
      tasks:
        - name: Creating resource group - "{{ name }}"
          azure_rm_resourcegroup:
            name: "{{ name }}"
            location: "{{ location }}"
          register: rg
        - debug:
            var: rg
    ```

1. [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북을 실행합니다. 자리 표시자를 만들 리소스 그룹의 이름 및 위치로 바꿉니다.

    ```bash
    ansible-playbook create_rg.yml --extra-vars "name=<resource_group_name> location=<resource_group_location>"
    ```

    **참고**:

    - 플레이북의 `register` 변수 및 `debug` 섹션으로 인해 명령이 완료되면 결과가 표시됩니다.

### <a name="delete-an-azure-resource-group"></a>Azure 리소스 그룹 삭제

[!INCLUDE [ansible-delete-resource-group.md](ansible-delete-resource-group.md)]
