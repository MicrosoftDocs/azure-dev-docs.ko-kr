---
title: 자습서 - Ansible을 사용하여 Azure Shared Image Gallery에서 VM 또는 가상 머신 확장 집합 만들기
description: Ansible을 사용하여 Shared Image Gallery에서 일반화된 이미지를 기반으로 하여 VM 또는 가상 머신 확장 집합을 만드는 방법을 알아봅니다.
keywords: Ansible, Azure, DevOps, Bash, 플레이북, 가상 머신, 가상 머신 확장 집합, Shared Image Gallery
ms.topic: tutorial
ms.date: 10/14/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: ad8bfca4030583e67423144dbc650eecb22e5060
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90681963"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>자습서: Ansible을 사용하여 Azure Shared Image Gallery에서 VM 또는 가상 머신 확장 집합 만들기

[!INCLUDE [ansible-29-note.md](includes/ansible-29-note.md)]

[Shared Image Gallery](/azure/virtual-machines/windows/shared-image-galleries)는 사용자 지정 관리 이미지를 쉽게 관리, 공유 및 구성할 수 있는 서비스입니다. 이 기능은 많은 이미지를 유지 관리하고 공유하는 시나리오에 유용합니다. 사용자 지정 이미지는 구독 및 Azure Active Directory 테넌트 간에 공유할 수 있습니다. 이미지를 여러 지역에 복제하여 배포를 더 빠르게 크기 조정할 수도 있습니다.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 일반화된 VM 및 사용자 지정 이미지 만들기
> * Shared Image Gallery 만들기
> * 공유 이미지 및 이미지 버전 만들기
> * 일반화된 이미지를 사용하여 VM 만들기
> * 일반화된 이미지를 사용하여 가상 머신 확장 집합 만들기
> * Shared Image Gallery, 이미지 및 버전에 대한 정보 가져오기

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>샘플 플레이북 가져오기

샘플 플레이북의 전체 세트를 가져오는 두 가지 방법은 다음과 같습니다.

- [SIG 폴더를 다운로드](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image)하여 로컬 머신에 저장합니다.
- 각 섹션에 대한 새 파일을 만들고, 샘플 플레이북을 복사합니다.

`vars.yml` 파일에는 이 자습서의 모든 샘플 플레이북에서 사용되는 변수가 포함되어 있습니다. 이 파일을 편집하여 고유한 이름과 값을 제공할 수 있습니다.

첫 번째 `00-prerequisites.yml` 샘플 플레이북은 이 자습서를 완료하는 데 필요한 항목을 만듭니다.
- 리소스 그룹 - Azure 리소스를 배포하고 관리하는 논리적 컨테이너입니다.
- 가상 네트워크, 서브넷, VM의 공용 IP 주소 및 네트워크 인터페이스 카드
- 원본 Virtual Machine - 일반화된 이미지를 만드는 데 사용됩니다.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create resource group if doesn't exist
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ virtual_network_name }}"
        address_prefixes: "10.0.0.0/16"

    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ subnet_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ virtual_network_name }}"

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ ip_name }}"

    - name: Create virtual network inteface cards for VM A and B
      azure_rm_networkinterface:
        resource_group: "{{ resource_group }}"
        name: "{{ network_interface_name }}"
        virtual_network: "{{ virtual_network_name }}"
        subnet: "{{ subnet_name }}"

    - name: Create VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        admin_username: testuser
        admin_password: "Password1234!"
        vm_size: Standard_B1ms
        network_interfaces: "{{ network_interface_name }}"
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
```

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook 00-prerequisites.yml
```

[Azure Portal](https://portal.azure.com)에서 `vars.yml`에 지정한 리소스 그룹을 확인하여 새 가상 머신과 만든 다양한 리소스를 확인합니다.

## <a name="generalize-the-vm-and-create-a-custom-image"></a>VM 일반화 및 사용자 지정 이미지 만들기

다음 `01a-create-generalized-image.yml` 플레이북은 이전 단계에서 만든 원본 VM을 일반화한 다음, 이를 기반으로 하여 사용자 지정 이미지를 만듭니다.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Generalize VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        generalized: yes

    - name: Create custom image
      azure_rm_image:
        resource_group: "{{ resource_group }}"
        name: "{{ image_name }}"
        source: "{{ source_vm_name }}"
```

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook 01a-create-generalized-image.yml
```

리소스 그룹을 확인하고, `testimagea`가 표시되는지 확인합니다.

## <a name="create-the-shared-image-gallery"></a>Shared Image Gallery 만들기

이미지 갤러리는 이미지를 공유하고 관리하기 위한 리포지토리입니다. `02-create-shared-image-gallery.yml`의 샘플 플레이북 코드는 Shared Image Gallery를 리소스 그룹에 만듭니다.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image gallery
      azure_rm_gallery:
        resource_group: "{{ resource_group }}"
        name: "{{ shared_gallery_name }}"
        location: "{{ location }}"
        description: This is the gallery description.
```

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

이제 새 `myGallery` 갤러리가 리소스 그룹에 표시됩니다.

## <a name="create-a-shared-image-and-image-version"></a>공유 이미지 및 이미지 버전 만들기

다음 `03a-create-shared-image-generalized.yml` 플레이북은 이미지 정의와 이미지 버전을 만듭니다.

이미지 정의에는 이미지 형식(Windows 또는 Linux), 릴리스 정보 및 최소/최대 메모리 요구 사항이 포함됩니다. 이미지 버전은 이미지의 버전입니다. 갤러리, 이미지 정의 및 이미지 버전은 이미지를 논리 그룹으로 구성하는 데 도움이 됩니다. 

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image
      azure_rm_galleryimage:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        name: "{{ shared_image_name }}"
        location: "{{ location }}"
        os_type: linux
        os_state: generalized
        identifier:
          publisher: myPublisherName
          offer: myOfferName
          sku: mySkuName
        description: Image description
    
    - name: Create or update a simple gallery image version.
      azure_rm_galleryimageversion:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        gallery_image_name: "{{ shared_image_name }}"
        name: "{{ shared_image_version }}"
        location: "{{ location }}"
        publishing_profile:
          end_of_life_date: "2020-10-01t00:00:00+00:00"
          exclude_from_latest: yes
          replica_count: 3
          storage_account_type: Standard_LRS
          target_regions:
            - name: West US
              regional_replica_count: 1
            - name: East US
              regional_replica_count: 2
              storage_account_type: Standard_ZRS
          managed_image:
            name: "{{ image_name }}"
            resource_group: "{{ resource_group }}"
      register: output

    - debug:
        var: output
```

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

이제 리소스 그룹에 갤러리에 대한 이미지 정의와 이미지 버전이 있습니다.

## <a name="create-a-vm-based-on-the-generalized-image"></a>일반화된 이미지를 기반으로 하여 VM 만들기

마지막으로 `04a-create-vm-using-generalized-image.yml`을 실행하여 이전 단계에서 만든 일반화된 이미지를 기반으로 하여 VM을 만듭니다.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create VM using shared image
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>일반화된 이미지를 기반으로 하여 가상 머신 확장 집합 만들기

일반화된 이미지를 기반으로 하여 가상 머신 확장 집합도 만들 수 있습니다. 이렇게 하려면 `05a-create-vmss-using-generalized-image.yml`을 실행합니다.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create a virtual machine scale set using a shared image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      capacity: 2
      virtual_network_name: "{{ virtual_network_name }}"
      upgrade_policy: Manual
      subnet_name: "{{ subnet_name }}"
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>갤러리에 대한 정보 가져오기

`06-get-info.yml`을 실행하여 갤러리, 이미지 정의 및 버전에 대한 정보를 가져올 수 있습니다.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Get Shared Image Gallery information
    azure_rm_gallery_info:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
  - name: Get shared image information
    azure_rm_galleryimage_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
  - name: Get Shared Image Gallery image version information
    azure_rm_galleryimageversion_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
```

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>공유 이미지 삭제

갤러리 리소스를 삭제하려면 `07-delete-gallery.yml` 샘플 플레이북을 참조하세요. 리소스를 반대 순서로 삭제합니다. 먼저 이미지 버전을 삭제합니다. 모든 이미지 버전이 삭제되면 이미지 정의를 삭제할 수 있습니다. 모든 이미지 정의가 삭제되면 갤러리를 삭제할 수 있습니다.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Delete gallery image version.
    azure_rm_galleryimageversion:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
      state: absent

  - name: Delete gallery image
    azure_rm_galleryimage:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
      state: absent

  - name: Delete a simple gallery.
    azure_rm_gallery:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
      state: absent
```

[ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)을 사용하여 플레이북 실행

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [ansible-delete-resource-group.md](includes/ansible-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure의 Ansible](/azure/ansible/)