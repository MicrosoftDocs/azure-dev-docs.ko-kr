---
title: 빠른 시작 - Azure CLI를 사용하여 Ansible 구성
description: 이 빠른 시작에서는 Ubuntu, CentOS 및 SLES에서 Azure 리소스를 관리하기 위해 Ansible을 설치 및 구성하는 방법을 알아봅니다.
keywords: Ansible, Azure, DevOps, Bash, cloudshell, 플레이북, Azure CLI
ms.topic: quickstart
ms.date: 08/13/2020
ms.custom: devx-track-ansible,devx-track-cli
ms.openlocfilehash: bdda836789e9230cffdc14a6ee4bd87ddb2ce5ef
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831177"
---
# <a name="quickstart-configure-ansible-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Ansible 구성

이 빠른 시작에서는 Azure CLI를 사용하여 [Ansible](https://docs.ansible.com/)을 설치하는 방법을 보여줍니다.

이 빠른 시작에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * SSH 키 쌍 만들기
> * 리소스 그룹 만들기
> * CentOS 가상 머신 만들기 
> * 가상 머신에 Ansible 설치
> * SSH를 통해 가상 머신에 연결
> * 가상 머신에서 Ansible 구성

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Linux 또는 Linux 가상 머신에 대한 액세스 권한** - Linux 머신에 액세스할 수 없는 경우 [Linux 가상 머신](/azure/virtual-network/quick-create-cli)을 만듭니다.

## <a name="create-an-ssh-key-pair"></a>SSH 키 쌍 만들기

Linux VM에 연결할 때 암호 인증 또는 키 기반 인증을 사용할 수 있습니다. 키 기반 인증이 암호를 사용하는 것보다 안전합니다. 따라서 이 문서에서는 키 기반 인증을 사용합니다.

키 기반 인증에는 두 가지 키가 있습니다.

- **공개 키**: 공개 키는 VM과 같은 호스트에 저장됩니다(이 문서 참조).
- **프라이빗 키**: 프라이빗 키를 사용하면 호스트에 안전하게 연결할 수 있습니다. 프라이빗 키는 사실상 내 암호이며 내 암호처럼 보호해야 합니다.
        
다음 단계는 SSH 키 쌍을 만드는 과정을 안내합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. [Azure Cloud Shell](/azure/cloud-shell/overview)을 열고(아직 열지 않은 경우) **Bash**로 전환합니다.

1. [ssh-keygen](https://www.ssh.com/ssh/keygen/)을 사용하여 SSH 키를 만듭니다.

    ```bash
    ssh-keygen -m PEM -t rsa -b 2048 -C "azureuser@azure" -f ~/.ssh/ansible_rsa -N ""
    ```

    **참고**:

    - `ssh-keygen` 명령은 생성된 키 파일의 위치를 표시합니다. 가상 머신을 만들 때 이 디렉터리 이름이 필요합니다.
    - 공개 키는 `ansible_rsa.pub`에 저장되고 프라이빗 키는 `ansible_rsa`에 저장됩니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

1. [az group create](/cli/azure/group#az-group-create)를 사용하여 리소스 그룹을 만듭니다. `--location` 매개 변수를 사용자 환경에 적절한 값으로 바꿔야 할 수 있습니다.

    ```azurecli
    az group create --name QuickstartAnsible-rg --location eastus
    ```

1. [az vm create](/cli/azure/vm#az-vm-create)를 사용하여 가상 머신을 만듭니다.

    ```azurecli
    az vm create \
    --resource-group QuickstartAnsible-rg \
    --name QuickstartAnsible-vm \
    --image OpenLogic:CentOS:7.7:latest \
    --admin-username azureuser \
    --ssh-key-values <ssh_public_key_filename>
    ```

1. [az vm list](/cli/azure/vm#az-vm-list)를 사용하여 새 가상 머신의 생성(및 상태)을 확인합니다.

    ```azurecli
    az vm list -d -o table --query "[?name=='QuickstartAnsible-vm']"
    ```

    **참고**:

    - `az vm list` 명령의 출력에는 SSH를 통해 가상 머신에 연결하는 데 사용되는 공용 IP 주소가 포함됩니다.

## <a name="install-ansible-on-the-virtual-machine"></a>가상 머신에 Ansible 설치

[az vm extension set](/cli/azure/vm/extension?#az-vm-extension-set)을 사용하여 Ansible 설치 스크립트를 실행합니다.

```azurecli
az vm extension set \
 --resource-group QuickstartAnsible-rg \
 --vm-name QuickstartAnsible-vm \
 --name customScript \
 --publisher Microsoft.Azure.Extensions \
 --version 2.1 \
 --settings '{"fileUris":["https://raw.githubusercontent.com/MicrosoftDocs/mslearn-ansible-control-machine/master/configure-ansible-centos.sh"]}' \
 --protected-settings '{"commandToExecute": "./configure-ansible-centos.sh"}'
```

**참고:**

- 완료되면, `az vm extension` 명령이 설치 스크립트를 실행한 결과를 표시합니다.

## <a name="connect-to-your-virtual-machine-via-ssh"></a>SSH를 통해 가상 머신에 연결

SSH 명령을 사용하여 가상 머신에 연결합니다.

```azurecli
ssh -i <ssh_private_key_filename> azureuser@<vm_ip_address>
```

## <a name="create-azure-credentials"></a>Azure 자격 증명 만들기

Ansible 자격 증명을 구성하려면 다음 정보가 필요합니다.

* Azure 구독 ID
* 서비스 주체 값

Ansible Tower 또는 Jenkins를 사용하는 경우 서비스 주체 값을 환경 변수로 선언합니다.

다음 방법 중 하나를 사용하여 Ansible 자격 증명을 구성합니다.

- [Ansible 자격 증명 파일 만들기](#file-credentials)
- [Ansible 환경 변수 사용](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Ansible 자격 증명 파일 만들기

이 섹션에서는 Ansible에 자격 증명을 제공하는 로컬 자격 증명 파일을 만듭니다.

Ansible 자격 증명 정의에 대한 자세한 내용은 [Azure 모듈에 자격 증명 제공](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules)을 참조하세요.

1. 개발 환경의 경우 다음과 같이 호스트 가상 머신에 `credentials`라는 파일을 만듭니다.

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. 다음 줄을 파일에 추가합니다. 자리 표시자를 서비스 주체 값으로 바꿉니다.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. 파일을 저장하고 닫습니다.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Ansible 환경 변수 사용

이 섹션에서는 서비스 주체 값을 내보내서 Ansible 자격 증명을 구성합니다.

1. 터미널 창을 엽니다.

1. 다음과 같이 서비스 주체 값을 내보냅니다.

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="test-ansible-installation"></a>Ansible 설치 테스트

이제 Ansible이 설치되고 구성된 가상 머신이 있습니다!

[!INCLUDE [ansible-test-configuration.md](includes/ansible-test-configuration.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure의 Ansible](./index.yml)