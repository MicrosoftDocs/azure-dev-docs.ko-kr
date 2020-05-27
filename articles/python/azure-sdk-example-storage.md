---
title: Python용 Azure SDK에서 Azure Storage 프로비저닝 및 사용
description: Python용 Azure SDK 라이브러리를 사용하여 Azure Storage 계정에서 Blob 컨테이너를 프로비저닝한 다음, 이 컨테이너에 파일을 업로드합니다.
ms.date: 05/12/2020
ms.topic: conceptual
ms.openlocfilehash: 9b26dd4c5708231c807ea57979bed6bdcd9de25f
ms.sourcegitcommit: 2cdf597e5368a870b0c51b598add91c129f4e0e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2020
ms.locfileid: "83405106"
---
# <a name="example-use-the-azure-sdk-with-azure-storage"></a>예제: Azure Storage에서 Azure SDK 사용

이 문서에서는 Python 스크립트로 Azure SDK 관리 라이브러리를 사용하여 Azure Storage 계정과 Blob Storage 컨테이너가 포함된 리소스 그룹을 프로비저닝하는 방법을 알아봅니다. 그런 다음, Python 애플리케이션 코드로 Azure SDK 클라이언트 라이브러리를 사용하여 해당 Blob Storage 컨테이너에 파일을 업로드하는 방법을 알아봅니다.

이 문서의 모든 명령은 언급되지 않는 한 Linux/Mac OS bash 및 Windows 명령 셸에서 동일하게 작동합니다.

## <a name="1-set-up-your-local-development-environment"></a>1: 로컬 개발 환경 설정

아직 수행하지 않은 경우 [Azure에 대한 로컬 Python 개발 환경 구성](configure-local-development-environment.md)의 모든 지침을 따르세요.

로컬 개발을 위한 서비스 주체를 만들고 이 프로젝트의 가상 환경을 만들어 활성화해야 합니다.

## <a name="2-install-the-needed-management-libraries"></a>2: 필요한 관리 라이브러리 설치

1. 이 예제에서 사용된 관리 라이브러리를 나열하는 *requirements.txt* 파일을 만듭니다.

    ```txt
    azure-mgmt-resource
    azure-mgmt-storage
    azure-cli-core
    ```

1. 가상 환경이 활성화된 터미널에서 다음 요구 사항을 설치합니다.

    ```bash
    pip install -r requirements.txt
    ```

## <a name="3-write-code-to-provision-storage-resources"></a>3: 스토리지 리소스를 프로비저닝하는 코드 작성

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
RESOURCE_GROUP_NAME = "PythonSDKExample-Storage-rg"
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

STORAGE_ACCOUNT_NAME = f"pythonsdkstorage{random.randint(1,100000):05}"

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

## <a name="4-run-the-script"></a>4. 스크립트 실행:

```bash
python provision_blob.py
```

스크립트를 완료하는 데 1~2분 정도 걸립니다.

## <a name="5-verify-the-resources"></a>5: 리소스 확인

1. [Azure Portal](https://portal.azure.com)을 열고 리소스 그룹과 스토리지 계정이 예상대로 프로비저닝되었는지 확인합니다. Python 스크립트에서 프로비저닝된 스토리지 계정을 보려면 리소스 그룹에서 **숨겨진 형식 표시**를 선택해야 할 수도 있습니다.

    ![스토리지 계정을 보여주는 새 리소스 그룹의 Azure Portal 페이지](media/azure-sdk-example-storage/portal-show-hidden-types.png)

1. 스토리지 계정을 선택한 다음, 왼쪽 메뉴에서 **Blob service** > **컨테이너**를 선택하여 "bloc-container-01"이 나타나는지 확인합니다.

    ![Blob 컨테이너를 보여주는 스토리지 계정의 Azure Portal 페이지](media/azure-sdk-example-storage/portal-show-blob-containers.png)

Azure Storage 관리 라이브러리를 사용하는 다른 예는 [Python 스토리지 샘플 관리](https://docs.microsoft.com/samples/azure-samples/storage-python-manage/storage-python-manage/)를 참조하세요.

### <a name="for-reference-equivalent-azure-cli-commands"></a>참조용: 해당 Azure CLI 명령

다음 Azure CLI 명령은 Python 스크립트와 동일한 프로비저닝 단계를 완료합니다.

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
# Provision the resource group

az group create -n PythonSDKExample-Storage-rg -l centralus

# Provision the storage account

az storage account create -g PythonSDKExample-Storage-rg -l centralus \
    -n pythonsdkstorage12345 --kind StorageV2 --sku Standard_LRS

# Retrieve the connection string

az storage account show-connection-string -g PythonSDKExample-Storage-rg \
    -n pythonsdkstorage12345

# Provision the blob container; NOTE: this command assumes you have an environment variable
# named AZURE_STORAGE_CONNECTION_STRING with the connection string for the storage account.

AZURE_STORAGE_CONNECTION_STRING=<connection_string>
az storage container create --account-name pythonsdkstorage12345 -n blob-container-01
```

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
# Provision the resource group

az group create -n PythonSDKExample-Storage-rg -l centralus

# Provision the storage account

az storage account create -g PythonSDKExample-Storage-rg -l centralus ^
    -n pythonsdkstorage12345 --kind StorageV2 --sku Standard_LRS

# Retrieve the connection string

az storage account show-connection-string -g PythonSDKExample-Storage-rg ^
    -n pythonsdkstorage12345

# Provision the blob container; NOTE: this command assumes you have an environment variable
# named AZURE_STORAGE_CONNECTION_STRING with the connection string for the storage account.

set AZURE_STORAGE_CONNECTION_STRING=<connection_string>
az storage container create --account-name pythonsdkstorage12345 -n blob-container-01
```

---

## <a name="6-use-resources-through-the-sdk-client-libraries"></a>6: SDK 클라이언트 라이브러리를 통한 리소스 사용

다음 섹션에서는 이전 섹션에서 프로비저닝된 Blob 컨테이너에 액세스하는 두 가지 방법을 보여줍니다. 이 예제는 적절한 SDK 클라이언트 라이브러리를 사용하여 파일 Blob을 해당 컨테이너에 업로드합니다.

코드를 직접 시도하려면 [일반적인 단계](#common-steps-for-both-methods)를 수행하세요.

첫 번째 방법은 [Python 앱을 인증하는 방법](azure-sdk-authenticate.md#authenticate-with-defaultazurecredential)의 설명대로 `DefaultAzureCredential`을 사용하여 앱을 인증합니다. 이 방법을 사용하면 먼저 앱 ID에 적절한 권한을 할당해야 합니다. 이것이 권장되는 방식입니다.

두 번째 방법은 연결 문자열을 사용하여 스토리지 계정에 직접 액세스합니다. 이 방법이 더 간단해 보이지만 두 가지 중요한 결점이 있습니다.

- 연결 문자열은 본질적으로 스토리지 계정을 사용하여(해당 계정 내의 개별 리소스를 사용하지 않고) 연결 에이전트를 인증합니다. 결과적으로 연결 문자열은 필요 이상으로 광범위한 권한을 부여합니다.

- 연결 문자열에는 액세스 키가 일반 텍스트 형식으로 포함되기 때문에 구성이나 보안이 적절하게 설정되지 않으면 잠재적인 취약점이 있습니다. 이러한 연결 문자열이 노출되면 스토리지 계정 내에서 광범위한 리소스에 액세스하는 데 사용될 수 있습니다.

이런 이유 때문에 프로덕션 코드에는 인증 방법을 사용해야 합니다. 단, 실험을 위해 연결 문자열을 사용하는 것은 괜찮습니다.

### <a name="common-steps-for-both-methods"></a>두 방법의 공통 단계

1. *requirements.txt* 파일에서 필요한 클라이언트 라이브러리에 대한 줄을 추가하고 파일을 저장합니다.

    ```text
    azure-storage-blob
    azure-identity
    ```

1. 터미널 또는 명령 프롬프트에서 요구 사항을 다시 설치합니다.

    ```bash
    pip install -r requirements.txt
    ```

1. 다음과 같은 콘텐츠가 포함된 *sample-source.txt*라는 소스 파일(코드에 필요함)을 만듭니다.

    ```text
    Hello there, Azure Storage. I'm a friendly file ready to be stored in a blob.
    ```

### <a name="use-blob-storage-with-authentication"></a>인증에 Blob Storage 사용

1. `AZURE_STORAGE_BLOB_URL`라는 환경 변수를 만듭니다.

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    AZURE_STORAGE_BLOB_URL=https://pythonsdkstorage12345.blob.core.windows.net
    ```

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set AZURE_STORAGE_BLOB_URL=https://pythonsdkstorage12345.blob.core.windows.net
    ```

    ---

    "pythonsdkstorage12345"를 특정 스토리지 계정의 이름으로 바꿉니다.

1. 다음 코드를 사용하여 *use_blob_auth.py*라는 파일을 만듭니다. 주석은 단계를 설명합니다.

    ```python
    import os
    from azure.identity import DefaultAzureCredential

    # Import the client object from the SDK library
    from azure.storage.blob import BlobClient

    credential = DefaultAzureCredential()

    # Retrieve the storage blob service URL, which is of the form
    # https://pythonsdkstorage12345.blob.core.windows.net/
    storage_url = os.environ["AZURE_STORAGE_BLOB_URL"]

    # Create the client object using the storage URL and the credential
    blob_client = BlobClient(storage_url, container_name="blob-container-01", blob_name="sample-blob.txt", credential=credential)

    # Open a local file and upload its contents to Blob Storage
    with open("./sample-source.txt", "rb") as data:
        blob_client.upload_blob(data)
    ```

1. 코드 실행을 시도합니다.

    ```bash
    python use_blob_auth.py
    ```

    사용 중인 로컬 서비스 주체에 Blob 컨테이너에 액세스할 수 있는 권한이 없기 때문에 다음 오류가 표시됩니다. "이 요청은 이 권한을 사용하여 이 작업을 수행할 수 있는 권한이 없습니다."

1. 서비스 주체에 컨테이너에 대한 권한을 부여하려면, Azure CLI 명령 [az role assignment create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)를 사용합니다.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli
    az role assignment create --assignee $AZURE_CLIENT_ID \
        --role "Storage Blob Data Contributor" \
        --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonSDKExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonsdkstorage12345/blobServices/default/containers/blob-container-01"
    ```

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```azurecli
    az role assignment create --assignee %AZURE_CLIENT_ID% ^
        --role "Storage Blob Data Contributor" ^
        --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonSDKExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonsdkstorage12345/blobServices/default/containers/blob-container-01"
    ```

    ---

    `--scope` 인수는 이 역할 할당이 적용되는 위치를 식별합니다. 이 예제에서는 "Storage Blob 데이터 기여자"라는 역할을 "blob-container-01"이라는 *특정* 컨테이너에 부여합니다.

    `pythonsdkstorage12345`를 스토리지 계정의 정확한 이름으로 바꿉니다. 필요하면 리소스 그룹과 Blob 컨테이너의 이름도 조정할 수 있습니다. 잘못된 이름을 사용하면 "중첩된 리소스에 대해 요청된 작업을 수행할 수 없습니다. 부모 리소스 'pythonsdkstorage12345'를 찾을 수 없습니다."라는 오류가 표시됩니다.

    이 명령의 `--scope` 인수에는 AZURE_CLIENT_ID와 AZURE_SUBSCRIPTION_ID 환경 변수도 사용되며, 이러한 변수는 [Azure를 위한 로컬 Python 개발 환경 구성](configure-local-development-environment.md)에 따라 서비스 주체에 대해 로컬 환경에 이미 설정되어 있어야 합니다.

1. 코드를 다시 실행하여 이제 작동하는지 확인합니다. 권한 오류가 다시 보이면 권한이 전파될 때까지 잠시 기다렸다가 코드 실행을 다시 시도합니다.

범위 및 역할 할당에 대한 자세한 내용은 [역할 권한을 할당하는 방법](how-to-assign-role-permissions.md)을 참조하세요.

### <a name="use-blob-storage-with-a-connection-string"></a>연결 문자열에 Blob Storage 사용

1. 다음 코드를 사용하여 *use_blob_conn_string.py*라는 Python 파일을 만듭니다. 주석은 단계를 설명합니다.

    ```python
    import os

    # Import the client object from the SDK library
    from azure.storage.blob import BlobClient

    # Retrieve the connection string from an environment variable.
    conn_string = os.environ["AZURE_STORAGE_CONNECTION_STRING"]

    # Create the client object for the resource identified by the connection string,
    # indicating also the blob container and the name of the specific blob we want.
    blob_client = BlobClient.from_connection_string(conn_string, container_name="blob-container-01", blob_name="sample-blob.txt")

    # Open a local file and upload its contents to Blob Storage
    with open("./sample-source.txt", "rb") as data:
        blob_client.upload_blob(data)
    ```

1. 코드를 실행합니다.

    ```bash
    python use_blob_conn_string.py
    ```

이 방법은 간단하지만 연결 문자열은 스토리지 계정의 모든 작업 권한을 부여합니다. 프로덕션 코드를 사용하는 경우 이전 섹션의 설명대로 특정 권한을 사용하는 것이 좋습니다.

### <a name="verify-blob-creation"></a>Blob 생성 확인

두 방법 중 하나의 코드를 실행한 후 [Azure Portal](https://portal.azure.com)에서 Blob 컨테이너로 이동하여 *sample-source.txt* 파일과 콘텐츠가 동일한 *sample-blob.txt*라는 새 Blob이 있는지 확인합니다.

![업로드한 파일을 보여주는 Blob 컨테이너의 Azure Portal 페이지](media/azure-sdk-example-storage/portal-blob-container-file.png)

## <a name="7-clean-up-resources"></a>7: 리소스 정리

```azurecli
az group delete -n PythonSDKExample-Storage-rg
```

이 예제에서 프로비저닝된 리소스를 유지할 필요가 없으며 구독에서 지속적인 요금을 방지하려면 이 명령을 실행합니다.

[`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-) 메서드를 사용하여 코드에서 리소스 그룹을 삭제할 수도 있습니다.

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [예: 웹앱 프로비저닝 및 코드 배포 >>>](azure-sdk-example-web-app.md)
