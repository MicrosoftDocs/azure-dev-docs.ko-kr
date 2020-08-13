---
title: Python용 Azure 라이브러리로 Azure Storage 프로비저닝
description: Python용 Azure SDK 라이브러리를 사용하여 Azure Storage 계정에서 Blob 컨테이너를 프로비저닝한 다음, 이 컨테이너에 파일을 업로드합니다.
ms.date: 05/29/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 6a956cf0f4f4689e653307f95d5f0900c8d01589
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87983435"
---
# <a name="example-provision-azure-storage-using-the-azure-libraries-for-python"></a>예: Python용 Azure 라이브러리를 사용하여 Azure Storage 프로비저닝

이 문서에서는 Python 스크립트로 Azure 관리 라이브러리를 사용하여 Azure Storage 계정과 Blob Storage 컨테이너가 포함된 리소스 그룹을 프로비저닝하는 방법을 알아봅니다. ([동등 Azure CLI 명령](#for-reference-equivalent-azure-cli-commands)은 이 문서의 뒷부분에 있습니다.)

리소스를 프로비저닝한 후 [예: Azure Storage 사용](azure-sdk-example-storage-use.md)을 참조하여 Python 애플리케이션 코드에서 Azure 클라이언트 라이브러리를 사용하여 Blob Storage 컨테이너에 파일을 업로드합니다.

이 문서의 모든 명령은 언급되지 않는 한 Linux/Mac OS bash 및 Windows 명령 셸에서 동일하게 작동합니다.

## <a name="1-set-up-your-local-development-environment"></a>1: 로컬 개발 환경 설정

아직 수행하지 않은 경우 [Azure에 대한 로컬 Python 개발 환경 구성](configure-local-development-environment.md)의 모든 지침을 따르세요.

로컬 개발을 위한 서비스 주체를 만들고 이 프로젝트의 가상 환경을 만들어 활성화해야 합니다.

## <a name="2-install-the-needed-azure-library-packages"></a>2: 필요한 Azure 라이브러리 패키지 설치

1. 이 예제에서 사용된 관리 라이브러리를 나열하는 *requirements.txt* 파일을 만듭니다.

    ```txt
    azure-mgmt-resource
    azure-mgmt-storage
    azure-cli-core
    ```

1. 가상 환경이 활성화된 터미널에서 다음 요구 사항을 설치합니다.

    ```cmd
    pip install -r requirements.txt
    ```

## <a name="3-write-code-to-provision-storage-resources"></a>3: 스토리지 리소스를 프로비저닝하는 코드 작성

이 섹션에서는 Python 코드에서 스토리지 리소스를 프로비저닝하는 방법을 설명합니다. 원하는 경우 Azure Portal을 통해 또는 [동등 Azure CLI 명령](#for-reference-equivalent-azure-cli-commands)를 통해 리소스를 프로비저닝할 수도 있습니다.

다음 코드를 사용하여 *provision_blob.py*라는 Python 파일을 만듭니다. 주석은 세부 정보를 설명합니다.

```python
import os, random

# Import the needed management objects from the libraries. The azure.common library
# is installed automatically with the other libraries.
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.storage import StorageManagementClient

# Obtain the management object for resources, using the credentials from the CLI login.
resource_client = get_client_from_cli_profile(ResourceManagementClient)

# Constants we need in multiple places: the resource group name and the region
# in which we provision resources. You can change these values however you want.
RESOURCE_GROUP_NAME = "PythonAzureExample-Storage-rg"
LOCATION = "centralus"

# Step 1: Provision the resource group.
rg_result = resource_client.resource_groups.create_or_update(RESOURCE_GROUP_NAME,
    { "location": LOCATION })

print(f"Provisioned resource group {rg_result.name}")

# For details on the previous code, see Example: Provision a resource group
# at https://docs.microsoft.com/azure/developer/python/azure-sdk-example-resource-group

# Step 2: Provision the storage account, starting with a management object.

storage_client = get_client_from_cli_profile(StorageManagementClient)

# This example uses the CLI profile credentials because we assume the script
# is being used to provision the resource in the same way the Azure CLI would be used.

STORAGE_ACCOUNT_NAME = f"pythonazurestorage{random.randint(1,100000):05}"

# You can replace the storage account here with any unique name. A random number is used
# by default, but note that the name changes every time you run this script.
# The name must be 3-24 lower case letters and numbers only.


# Check if the account name is available. Storage account names must be unique across
# Azure because they're used in URLs.
availability_result = storage_client.storage_accounts.check_name_availability(STORAGE_ACCOUNT_NAME)

if not availability_result.name_available:
    print(f"Storage name {STORAGE_ACCOUNT_NAME} is already in use. Try another name.")
    exit()

# The name is available, so provision the account
poller = storage_client.storage_accounts.create(RESOURCE_GROUP_NAME, STORAGE_ACCOUNT_NAME,
    {
        "location" : LOCATION,
        "kind": "StorageV2",
        "sku": {"name": "Standard_LRS"}
    }
)

# Long-running operations return a poller object; calling poller.result()
# waits for completion.
account_result = poller.result()
print(f"Provisioned storage account {account_result.name}")

# Step 3: Retrieve the account's primary access key and generate a connection string.
keys = storage_client.storage_accounts.list_keys(RESOURCE_GROUP_NAME, STORAGE_ACCOUNT_NAME)

print(f"Primary key for storage account: {keys.keys[0].value}")

conn_string = f"DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName={STORAGE_ACCOUNT_NAME};AccountKey={keys.keys[0].value}"

print(f"Connection string: {conn_string}")

# Step 4: Provision the blob container in the account (this call is synchronous)
CONTAINER_NAME = "blob-container-01"
container = storage_client.blob_containers.create(RESOURCE_GROUP_NAME, STORAGE_ACCOUNT_NAME, CONTAINER_NAME, {})

# The fourth argument is a required BlobContainer object, but because we don't need any
# special values there, so we just pass empty JSON.

print(f"Provisioned blob container {container.name}")
```

이 코드는 Azure CLI에서 직접 수행할 수 있는 작업을 보여주므로 CLI 기반 인증 메서드(`get_client_from_cli_profile`)를 사용합니다. 두 경우 모두 인증에 동일한 ID를 사용합니다.

프로덕션 스크립트에서 이러한 코드를 사용하려면 대신 [Azure 서비스로 Python 앱을 인증하는 방법](azure-sdk-authenticate.md)에 설명된 대로 `DefaultAzureCredential`(권장) 또는 서비스 주체 기반 메서드를 사용해야 합니다.

### <a name="reference-links-for-classes-used-in-the-code"></a>코드에 사용된 클래스에 대한 참조 링크

- [ResourceManagementClient(azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient?view=azure-python)
- [StorageManagementClient(azure.mgmt.storage)](/python/api/azure-mgmt-storage/azure.mgmt.storage.storagemanagementclient?view=azure-python)

## <a name="4-run-the-script"></a>4. 스크립트 실행

```cmd
python provision_blob.py
```

스크립트를 완료하는 데 1~2분 정도 걸립니다.

## <a name="5-verify-the-resources"></a>5: 리소스 확인

1. [Azure Portal](https://portal.azure.com)을 열고 리소스 그룹과 스토리지 계정이 예상대로 프로비저닝되었는지 확인합니다. Python 스크립트에서 프로비저닝된 스토리지 계정을 보려면 리소스 그룹에서 **숨겨진 형식 표시**를 선택해야 할 수도 있습니다.

    ![스토리지 계정을 보여주는 새 리소스 그룹의 Azure Portal 페이지](media/azure-sdk-example-storage/portal-show-hidden-types.png)

1. 스토리지 계정을 선택한 다음, 왼쪽 메뉴에서 **Blob service** > **컨테이너**를 선택하여 "bloc-container-01"이 나타나는지 확인합니다.

    ![Blob 컨테이너를 보여주는 스토리지 계정의 Azure Portal 페이지](media/azure-sdk-example-storage/portal-show-blob-containers.png)

1. 애플리케이션 코드에서 이러한 프로비저닝된 리소스를 사용하려는 경우 [예: Azure Storage 사용](azure-sdk-example-storage-use.md)을 시도해보세요.

Azure Storage 관리 라이브러리를 사용하는 다른 예는 [Python 스토리지 샘플 관리](https://docs.microsoft.com/samples/azure-samples/storage-python-manage/storage-python-manage/)를 참조하세요.

### <a name="for-reference-equivalent-azure-cli-commands"></a>참조용: 해당 Azure CLI 명령

다음 Azure CLI 명령은 Python 스크립트와 동일한 프로비저닝 단계를 완료합니다.

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
rem Provision the resource group

az group create -n PythonAzureExample-Storage-rg -l centralus

rem Provision the storage account

az storage account create -g PythonAzureExample-Storage-rg -l centralus ^
    -n pythonazurestorage12345 --kind StorageV2 --sku Standard_LRS

rem Retrieve the connection string

az storage account show-connection-string -g PythonAzureExample-Storage-rg ^
    -n pythonazurestorage12345

rem Provision the blob container; NOTE: this command assumes you have an environment variable
rem named AZURE_STORAGE_CONNECTION_STRING with the connection string for the storage account.

set AZURE_STORAGE_CONNECTION_STRING=<connection_string>
az storage container create --account-name pythonazurestorage12345 -n blob-container-01
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
# Provision the resource group

az group create -n PythonAzureExample-Storage-rg -l centralus

# Provision the storage account

az storage account create -g PythonAzureExample-Storage-rg -l centralus \
    -n pythonazurestorage12345 --kind StorageV2 --sku Standard_LRS

# Retrieve the connection string

az storage account show-connection-string -g PythonAzureExample-Storage-rg \
    -n pythonazurestorage12345

# Provision the blob container; NOTE: this command assumes you have an environment variable
# named AZURE_STORAGE_CONNECTION_STRING with the connection string for the storage account.

AZURE_STORAGE_CONNECTION_STRING=<connection_string>
az storage container create --account-name pythonazurestorage12345 -n blob-container-01
```

---

## <a name="6-clean-up-resources"></a>6: 리소스 정리

[예: Azure Storage 사용](azure-sdk-example-storage-use.md) 문서를 따라 앱 코드에서 리소스를 사용하려는 경우 해당 리소스를 제 위치에 그대로 둡니다.

그렇지 않으면 다음 명령을 실행하여 구독 요금이 계속 청구되지 않도록 합니다.

```azurecli
az group delete -n PythonAzureExample-Storage-rg
```

[`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-) 메서드를 사용하여 코드에서 리소스 그룹을 삭제할 수도 있습니다.

## <a name="see-also"></a>참고 항목

- [예: Azure Storage 사용](azure-sdk-example-storage-use.md)
- [예: 리소스 그룹 프로비저닝](azure-sdk-example-resource-group.md)
- [예: 웹앱 프로비저닝 및 코드 배포](azure-sdk-example-web-app.md)
- [예: 데이터베이스 프로비저닝 및 쿼리](azure-sdk-example-database.md)
- [예: 가상 머신 프로비저닝](azure-sdk-example-virtual-machines.md)
