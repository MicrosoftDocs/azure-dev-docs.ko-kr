---
title: '자습서: Ansible에서 Linux 가상 머신으로 Azure Key Vault 사용'
description: Ansible에서 Azure Key Vault를 사용하여 VM 보안을 구성하는 방법 알아보기
keywords: ansible, azure, devops, 키 자격 증명 모음, 보안, 자격 증명, 비밀, 키, 인증서, azure용 ansible 모듈, 리소스 그룹, azure_rm_resourcegroup,
ms.topic: tutorial
ms.date: 04/20/2020
ms.custom: devx-track-ansible
ms.openlocfilehash: 4891b277f8c1f9fcd7fe4c1d54ed13b39f19d2e4
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90682014"
---
# <a name="tutorial-use-azure-key-vault-with-a-linux-virtual-machine-in-ansible"></a>자습서: Ansible에서 Linux 가상 머신으로 Azure Key Vault 사용

[!INCLUDE [ansible-29-note.md](includes/ansible-29-note.md)]

이 자습서에서는 [Azure Key Vault](/azure/key-vault/general/overview)를 사용할 때 Azure 모듈용 Ansible 컬렉션을 사용하는 방법을 보여줍니다. Azure Key Vault를 사용하면 자격 증명(예: 애플리케이션 비밀, 키, 인증서) 스토리지를 중앙 집중식으로 관리할 수 있습니다. 자격 증명과 애플리케이션 코드를 분리하면 시스템 보안을 강화할 수 있습니다. 또한, 자동 만료 날짜로 회전 자격 증명 관리 패턴을 구현하면 관리하기가 훨씬 더 쉬워집니다.

> [!div class="checklist"]
>
> * Azure CLI를 사용하여 Azure 구독 및 서비스 주체 값 가져오기
> * 키 값을 Linux 환경 변수로 저장
> * Ansible 플레이북에서 Linux 환경 변수 가져오기
> * 키 자격 증명 모음 만들기
> * 키 자격 증명 모음에 대한 액세스 정책 설정
> * Azure Portal을 사용하여 키 자격 증명 모음에 액세스 정책 추가
> * 키 자격 증명 모음 비밀 만들기
> * Ansible 셸 모듈을 사용하여 키 자격 증명 모음 비밀 가져오기
> * 가상 머신의 모든 구성 요소와 함께 가상 머신 만들기

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
[!INCLUDE [ansible-configure-azure-collection.md](includes/ansible-configure-azure-collection.md)]
    
## <a name="get-azure-subscription-info"></a>Azure 구독 정보 가져오기

Azure CLI를 사용하여 Azure용 Ansible 모듈을 사용할 때 필요한 Azure 구독 정보를 가져옵니다. 

1. `az account show` 명령을 사용하여 Azure 구독 ID 및 Azure 구독 테넌트 ID를 가져옵니다. `<Subscription>` 자리 표시자에는 Azure 구독 이름 또는 Azure 구독 ID를 지정합니다. 이 명령은 기본 Azure 구독과 연결된 여러 키 값을 표시합니다. 구독이 여러 개인 경우 [az account set](/cli/azure/account#az-account-set) 명령을 통해 현재 구독을 설정해야 할 수도 있습니다. 명령의 출력에서 **ID**와 **tenantID** 값을 둘 다 적어둡니다.

    ```azurecli
    az account show --subscription "<Subscription>" --query tenantId
    ```

1. Azure 구독에 대한 서비스 주체가 없는 경우, [Azure CLI를 사용하여 Azure 서비스 주체를 만듭니다](/cli/azure/create-an-azure-service-principal-azure-cli). 명령의 출력에서 **appId** 값을 적어둡니다.

1. `az ad sp show` 명령을 사용하여 서비스 주체의 개체 ID를 가져옵니다. `<ApplicationID>` 자리 표시자에는 서비스 주체 appId를 지정합니다. `--query` 매개 변수는 *stdout*에 출력할 값을 나타냅니다. 이 경우에는 서비스 주체 개체 ID입니다.

    ```azurecli
    az ad sp show --id <ApplicationID> --query objectId
    ```
    
1. 다음 값을 환경 변수로 저장합니다. Azure 구독 ID, Azure 구독 테넌트 ID 및 서비스 주체 개체 ID. 플레이북을 실행하는 방법, 구독 및 자격 증명 값을 저장하는 위치, 이러한 값을 검색하는 방법은 사용자의 환경에 따라 달라집니다. 이 데모에서는 Azure Cloud Shell을 사용했고, `~/.bashrc` 파일의 끝에 다음 줄을 추가하여 필요한 Azure 값을 저장했습니다.

    ```bash
    export AZ_SUBSCRIPTION_ID="<subscriptionID>"
    export AZ_TENANT_ID="<tenantID>"
    export AZ_OBJECT_ID="<objectID>"
    export AZ_CLIENT_ID="<appID>"
    ```

## <a name="declare-the-azure-collection"></a>Azure 컬렉션 선언

[최신 Azure 컬렉션을 다운로드](#prerequisites)한 후 `collections` 키를 통해 사용을 지정합니다.

```yml
- hosts: all
  collections:
    - azure.azcollection
```

## <a name="create-azure-resource-group-for-the-key-vault"></a>키 자격 증명 모음용 리소스 그룹 만들기

다음 플레이북 코드 조각은 키 자격 증명 모음이 생성될 리소스 그룹을 고유한 이름으로 생성합니다. 

```yml
---
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 10000 | random }}"
      run_once: yes
      
- hosts: localhost
  vars:
    kv_rg: kv_rg_{{ rpfx }}
    kv_rg_loc: eastus

  tasks:
    - name: Set facts
      set_fact:
        az_sub_id: "{{ lookup('env', 'AZ_SUBSCRIPTION_ID') }}"

    - name: Create a resource group to hold the key vault
      azure_rm_resourcegroup:
        subscription_id: "{{ az_sub_id }}"
        name: "{{ kv_rg }}"
        location: "{{ kv_rg_loc }}"

    - debug:
        msg: "New resource group = {{ kv_rg }}"
```

**참고:**

- 이 데모에서 키 자격 증명 모음은 리소스 그룹의 유일한 리소스로 만들어집니다. 키 자격 증명 모음은 이것을 사용할 리소스와 분리하는 것이 일반적입니다. 이 패턴을 사용하면 다른 리소스를 삭제할 때 키 자격 증명 모음이 실수로 삭제되는 것을 방지합니다.
- 키 자격 증명 모음 이름은 Azure에서 고유해야 하기 때문에 데모에서는 임의의 *postfix* 값을 만듭니다. 이 값은 키 자격 증명 모음 리소스 그룹 이름과 키 자격 증명 모음(다음 섹션에서 만들어짐)에 추가됩니다. `Prepare random postfix` 작업의 코드는 `rpfx` 변수에 할당되는 임의의 postfix 값을 생성합니다.
- `Set facts` 작업에서 `lookup` 명령은 환경 변수로 저장된 Azure 구독 ID를 검색하는 데 사용됩니다.
- [azure_rm_resourcegroup 모듈](https://docs.ansible.com/ansible/latest/modules/azure_rm_resourcegroup_module.html)은 새 리소스 그룹을 만드는 데 사용됩니다.
- 플레이북의 끝에 있는 `debug` 작업은 새 리소스 그룹의 이름을 나타냅니다.

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

이전 섹션에서 언급했듯이 키 자격 증명 모음 이름은 Azure 전체에서 고유해야 합니다. 이러한 방식으로 다음 플레이북 코드 조각은 `kv` 변수에 리터럴 `kv` 및 `rpfx` 값의 연결을 지정합니다.

```yml
vars:
  kv: "kv{{ rpfx }}"
  kv_rg: "kv_rg_{{ rpfx }}"

tasks:
  - name: Set facts
    set_fact:
      az_object_id: "{{ lookup('env', 'AZ_OBJECT_ID') }}"
      az_tenant_id: "{{ lookup('env', 'AZ_TENANT_ID') }}"

  - name: Create a key vault
    azure_rm_keyvault:
      subscription_id: "{{ az_sub_id }}"
      resource_group: "{{ kv_rg }}"
      vault_name: "{{ kv }}"
      vault_tenant: "{{ az_tenant_id }}"
      enabled_for_deployment: yes
      sku:
        name: standard
        family: A
      access_policies:
        - object_id: "{{ az_object_id }}"
          tenant_id: "{{ az_tenant_id }}"
          secrets:
            - get
            - list
            - set
  - debug:
      msg: "New key vault name = {{ kv }} within the {{ kv_rg }} resource group"

```

**참고:**

- [azure_rm_keyvault 모듈](https://docs.ansible.com/ansible/latest/modules/azure_rm_keyvault_module.html)은 키 자격 증명 모음을 만드는 데 사용됩니다.
- 키 자격 증명 모음의 일부로 액세스 정책을 만들 때 개체 ID와 테넌트 ID가 제공됩니다. 이러한 값은 Azure 구독과 연결된 특정 서비스 주체에 대한 액세스 정책을 정의합니다. 하지만 Azure Portal로 이동하여 키 자격 증명 모음의 비밀을 보려고 하면 오류 메시지가 나타날 수 있습니다. 이러한 메시지는 ""목록" 작업은 이 키 자격 증명 모음의 액세스 정책에서 사용하도록 설정되어 있지 않습니다" 및 "이 콘텐츠를 볼 수 있는 권한이 없습니다"와 유사할 수 있습니다. 이러한 메시지가 표시되면 Active Directory 사용자로 액세스할 수 있는 권한이 없다는 의미입니다. 다음 섹션에서는 키 자격 증명 모음에 대한 액세스 정책을 스스로 추가하는 방법을 보여줍니다.
- 플레이북의 끝에 있는 `debug` 작업은 새 키 자격 증명 모음의 이름을 나타냅니다.

## <a name="add-yourself-to-key-vault-access-policy"></a>키 자격 증명 모음 액세스 정책에 자신을 추가

키 자격 증명 모음의 비밀을 보려는 경우 또는 데모 단계를 진행하는 경우, 자신의 Azure Active Directory ID에 대한 액세스 정책을 추가해야 합니다. 다음 단계는 키 자격 증명 모음에 사용자 자신을 위한 액세스 정책을 추가하는 과정을 안내합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. 페이지의 기본 검색 상자에 `key vaults`를 입력하고 **서비스**에서 **키 자격 증명 모음**을 선택합니다.

1. 이전 섹션에서 만든 키 자격 증명 모음을 선택합니다. (이 이름은 플레이북에서 stdout에 출력되었습니다.)

1. **액세스 정책**을 선택합니다.

1. 지정한 서비스 주체를 나타내는 Azure Active Directory ID와 함께 단일 액세스 정책이 표시됩니다.

1. **액세스 정책 추가**를 선택합니다.

1. **주체 선택**을 선택합니다.

1. **주체** 탭의 검색 상자에 자신의 이메일 주소를 입력합니다.

1. 필터링된 목록에서 적절한 항목을 선택합니다.

1. 선택한 사용자에 대한 정보가 **선택한 멤버** 목록에 복사됩니다. **선택**을 선택합니다.

1. **키 권한**, **비밀 권한**, **인증서 권한**에 대해 적절한 옵션을 선택합니다. 이 데모에서는 **비밀 권한**을 선택한 다음, **Get**, **List**, **Set**을 선택하면 충분합니다.

1. **추가**를 선택합니다.

1. 이제 선택한 사용자에 대한 새로운 액세스 정책이 **액세스 정책** 페이지에 표시됩니다.

1. **저장**을 선택합니다.

1. 포털의 오른쪽 위 모서리에서 **알림**을 선택합니다. 액세스 정책이 업데이트될 때까지 기다린 후, 다음 단계를 진행합니다.

## <a name="create-a-key-vault-secret"></a>키 자격 증명 모음 비밀 만들기

다음 Ansible 플레이북 코드 조각은 키 자격 증명 모음 비밀을 만드는 방법을 보여줍니다.

```yml
  vars:
    kv_secret_name: testsecret
    kv_secret_value: MySecret007$

  tasks:
    - name: Set facts
      set_fact:
        az_client_id: "{{ lookup('env', 'AZ_CLIENT_ID') }}"

    - name: Create a secret
      azure_rm_keyvaultsecret:
        subscription_id: "{{ az_sub_id }}"
        client_id: "{{ az_client_id }}"
        keyvault_uri: "{{ kv_uri }}"
        secret_name: "{{ kv_secret_name }}"
        secret_value: "{{ kv_secret_value }}"
```

**참고:**

- [azure_rm_keyvaultsecret 모듈](https://docs.ansible.com/ansible/latest/modules/azure_rm_keyvaultsecret_module.html)은 키 자격 증명 모음 비밀을 만드는 데 사용됩니다.
- 간단한 설명을 위해 데모에는 `secret_name`과 `secret_value`가 포함됩니다. 하지만 플레이북은 프로젝트의 다른 소스 코드와 마찬가지로 IaC(infrastructure-as-code) 파일입니다. 따라서, 이러한 값은 프로덕션 환경에서 사용할 경우 일반 텍스트 파일에 저장하지 않아야 합니다.
- 이 코드를 실행한 후에는 키 자격 증명 모음의 **비밀** 탭에 `testsecret`이라는 새로 추가된 비밀이 나열됩니다. 이것을 보려면 비밀을 선택하고, 현재 버전을 선택하고 **비밀 값 표시**를 선택합니다.

## <a name="get-a-key-vault-secret"></a>키 자격 증명 모음 비밀 가져오기

다음 Ansible 플레이북 코드 조각은 최신 버전의 키 자격 증명 모음 비밀을 가져오는 방법을 보여줍니다.

```yml
  vars:
    kv_secret_name: testsecret
    kv_secret_value: MySecret007$

tasks:
    - name: Get latest version of a secret
      azure_rm_keyvaultsecret_info:
        vault_uri: "{{ kv_uri }}"
        name: "{{ kv_secret_name }}"
      register: output
    - debug:
        var: output['secrets'][0]['secret']
```

**참고:**

- **azure_rm_keyvaultsecret_info 모듈**은 키 자격 증명 모음 비밀을 가져오는 데 사용됩니다. 이 모듈은 Azure 모듈용 Ansible 컬렉션을 사용하는 경우에만 사용할 수 있습니다. 
- 이 코드 조각을 실행하는 동안 오류가 발생하면 [사전 요구 사항 섹션](#prerequisites)의 모든 지침을 따랐는지 확인하세요.
- 간단한 설명을 위해 데모에는 `secret_name`과 `secret_value`가 포함됩니다. 하지만 플레이북은 프로젝트의 다른 소스 코드와 마찬가지로 IaC(infrastructure-as-code) 파일입니다. 따라서, 이러한 값은 프로덕션 환경에서 사용할 경우 일반 텍스트 파일에 저장하지 않아야 합니다.

## <a name="run-the-complete-playbook"></a>완전한 플레이북 실행

키 자격 증명 모음과 비밀을 설정하고 나면 가상 머신과 같은 Azure 리소스를 보호할 때 이 정보를 사용할 수 있습니다. 다음 Ansible 플레이북은 이 자습서 전체에 표시된 작업을 수행하고 완전한 가상 머신을 만듭니다. 

```yml
---
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 10000 | random }}"
      run_once: yes
      
- hosts: localhost
  collections:
    - azure.azcollection
  vars:
    kv_rg: kv_rg_{{ rpfx }}
    kv_rg_loc: eastus
    kv: "kv{{ rpfx }}"
    kv_uri: "https://{{ kv }}.vault.azure.net"
    kv_secret_name: testsecret
    kv_secret_value: MySecret007$

    # Test VM vars
    test_vm_rg: kv_test_vm_rg
    test_vm_rg_loc: eastus
    test_vm: kvtestvm
    test_vm_vnet: "kv_test_vm_vnet"
    test_vm_subnet: kv_test_vm_subnet
    test_vm_public_ip: kv_test_vm_public_ip
    test_vm_nsg: kv_test_vm_nsg
    test_vm_nsg_list: 
      - name: Allow-SSH
        access: Allow
        protocol: Tcp
        direction: Inbound
        priority: 300
        port: 22 
        source_address_prefix: Internet
      - name: Allow-HTTP
        access: Allow
        protocol: Tcp
        direction: Inbound
        priority: 100
        port: 80
        source_address_prefix: Internet 
    test_vm_nic: kv_test_vnic
    admin_username: testadmin

  tasks:
    - name: Set facts
      set_fact:
        az_sub_id: "{{ lookup('env', 'AZ_SUBSCRIPTION_ID') }}"
        az_object_id: "{{ lookup('env', 'AZ_OBJECT_ID') }}"
        az_tenant_id: "{{ lookup('env', 'AZ_TENANT_ID') }}"
        az_client_id: "{{ lookup('env', 'AZ_CLIENT_ID') }}"

    - name: Create a resource group to hold the Key Vault instance
      azure_rm_resourcegroup:
        subscription_id: "{{ az_sub_id }}"
        name: "{{ kv_rg }}"
        location: "{{ kv_rg_loc }}"

    - debug:
        msg: "New resource group = {{ kv_rg }}"

    - name: Create instance of Key Vault
      azure_rm_keyvault:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ kv_rg }}"
        vault_name: "{{ kv }}"
        vault_tenant: "{{ az_tenant_id }}"
        enabled_for_deployment: yes
        sku:
          name: standard
          family: A
        access_policies:
          - object_id: "{{ az_object_id }}"
            tenant_id: "{{ az_tenant_id }}"
            secrets:
              - get
              - list
              - set

    - debug:
        msg: "New Key Vault instance name = {{ kv }} within the {{ kv_rg }} resource group"

    - name: Create a secret
      azure_rm_keyvaultsecret:
        subscription_id: "{{ az_sub_id }}"
        client_id: "{{ az_client_id }}"
        keyvault_uri: "{{ kv_uri }}"
        secret_name: "{{ kv_secret_name }}"
        secret_value: "{{ kv_secret_value }}"

    - name: Register Key Vault provider.
      shell:
        az provider register -n Microsoft.KeyVault

    - name: Get latest version of a secret
      azure_rm_keyvaultsecret_info:
        vault_uri: "{{ kv_uri }}"
        name: "{{ kv_secret_name }}"
      register: output
    - debug:
        var: output['secrets'][0]['secret']

    - name: Create resource group for test VM.
      azure_rm_resourcegroup:
        subscription_id: "{{ az_sub_id }}"
        name: "{{ test_vm_rg }}"
        location: "{{ test_vm_rg_loc }}"

    - name: Create virtual network.
      azure_rm_virtualnetwork:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        name: "{{ test_vm_vnet }}"
        address_prefixes: "172.16.0.0/16"

    - name: Create subset within virtual network.
      azure_rm_subnet:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        virtual_network_name: "{{ test_vm_vnet }}"
        name: "{{ test_vm_subnet }}"
        address_prefix_cidr:  "172.16.10.0/24"

    - name: Create public IP address.
      azure_rm_publicipaddress:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        allocation_method: Static
        name: "{{ test_vm_public_ip }}"

    - name: Create Network Security Group and rules.
      azure_rm_securitygroup:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        name: "{{ test_vm_nsg}}"
        rules:
          - name: "{{ item.name }}"
            access: "{{ item.access }}"
            protocol: "{{ item.protocol }}"
            direction: "{{ item.direction }}"
            destination_port_range: "{{ item.port }}"
            priority: "{{ item.priority }}"
            source_address_prefix: "{{ item.source_address_prefix }}"
      loop: "{{ test_vm_nsg_list }}"

    - name: Create virtual network interface card (NIC).
      azure_rm_networkinterface:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        name: "{{ test_vm_nic }}"
        virtual_network: "{{ test_vm_vnet }}"
        subnet: "{{ test_vm_subnet }}"
        ip_configurations:
          - name: ipconfig
            public_ip_address_name: "{{ test_vm_public_ip }}"
            primary: yes
        security_group: "{{ test_vm_nsg }}"

    - name: Create virtual machine.
      azure_rm_virtualmachine:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        name: "{{ test_vm }}"
        admin_username: " {{ admin_username }} "
        admin_password: " {{ output['secrets'][0]['secret'] }}"
        vm_size: Standard_B1ms
        network_interfaces: "{{ test_vm_nic }}"
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest

```

**참고:**

- 가상 머신의 관리자 암호는 키 자격 증명 모음 비밀로 설정됩니다.
- 전체 플레이북을 한 번에 실행할 수 있는 기능은 테스트 환경에 따라 달라집니다. 키를 만들기 전에 키 자격 증명 모음 액세스 정책에 자신을 수동으로 추가해야 할 수도 있습니다. 이 작업은 [키 자격 증명 모음 만들기](#create-a-key-vault)와 [키 자격 증명 모음 액세스 정책에 자신을 추가](#add-yourself-to-key-vault-access-policy) 섹션에 설명되어 있습니다.
- 여기에서 볼 수 있듯이 다양한 Ansible 모듈이 Azure 가상 머신과 이 머신의 모든 구성 요소를 만드는 데 사용됩니다. 가상 머신을 만드는 데 사용되는 다양한 Ansible 모듈에 대한 자세한 내용은 다음 목록을 참조하세요.
    - [Azure 리소스 그룹 모듈(azure_rm_resourcegroup)](https://docs.ansible.com/ansible/latest/modules/azure_rm_resourcegroup_module.html)
    - [Azure 가상 네트워크 모듈(azure_rm_virtualnetwork)](https://docs.ansible.com/ansible/latest/modules/azure_rm_virtualnetwork_module.html)
    - [Azure 가상 네트워크 서브넷 모듈(azure_rm_subnet)](https://docs.ansible.com/ansible/latest/modules/azure_rm_subnet_module.html)
    - [Azure 공용 IP 모듈(azure_rm_publicipaddress)](https://docs.ansible.com/ansible/latest/modules/azure_rm_publicipaddress_module.html)
    - [Azure 네트워크 보안 그룹 모듈(azure_rm_securitygroup)](https://docs.ansible.com/ansible/latest/modules/azure_rm_securitygroup_module.html)
    - [Azure 네트워크 인터페이스(azure_rm_networkinterface)](https://docs.ansible.com/ansible/latest/modules/azure_rm_networkinterface_module.html)
    - [Azure 가상 머신(azure_rm_virtualmachine)](https://docs.ansible.com/ansible/latest/modules/azure_rm_virtualmachine_module.html)
    
## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 이 문서에서 만든 리소스를 삭제합니다. `<kv_rg>` 자리 표시자를 데모 키 자격 증명 모음을 보유하는 데 사용된 리소스 그룹으로 바꿉니다. 

```yml  
- hosts: localhost  
  vars: 
    kv_rg: <kv_rg>  
    test_vm_rg: kv_test_vm_rg   
  tasks:    
    - name: Delete the key vault resource group 
      azure_rm_resourcegroup:   
        name: "{{ kv_rg }}" 
        force_delete_nonempty: yes  
        state: absent   
    - name: Delete the test vm resource group   
      azure_rm_resourcegroup:   
        name: "{{ test_vm_rg }}"    
        force_delete_nonempty: yes  
        state: absent
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure Key Vault 보안 개요](/azure/key-vault/general/overview-security)
