---
title: Python용 Azure SDK에서 Azure Storage 프로비저닝 및 사용
description: Python 라이브러리용 Azure SDK를 사용하여 Azure Storage 계정에서 미리 프로비저닝된 Blob 컨테이너를 액세스한 다음, 이 컨테이너에 파일을 업로드합니다.
ms.date: 05/29/2020
ms.topic: conceptual
ms.openlocfilehash: 50d9b781e0ec3a1a9f752a9ec193720d99b88f4d
ms.sourcegitcommit: db56786f046a3bde1bd9b0169b4f62f0c1970899
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329661"
---
# <a name="example-use-the-azure-libraries-with-azure-storage"></a>예: Azure Storage와 함께 Azure 라이브러리 사용

이 예는 Python 애플리케이션 코드로 Azure 클라이언트 라이브러리를 사용하여 해당 Blob Storage 컨테이너에 파일을 업로드하는 방법을 보여줍니다. 이 예에서는 [예: Azure Storage 프로비저닝](azure-sdk-example-storage.md)에 표시된 리소스를 프로비저닝했다고 가정합니다.

이 문서의 모든 명령은 언급되지 않는 한 Linux/Mac OS bash 및 Windows 명령 셸에서 동일하게 작동합니다.

## <a name="1-set-up-your-local-development-environment"></a>1: 로컬 개발 환경 설정

아직 수행하지 않은 경우 [Azure에 대한 로컬 Python 개발 환경 구성](configure-local-development-environment.md)의 모든 지침을 따르세요.

로컬 개발을 위한 서비스 주체를 만들고 이 프로젝트의 가상 환경을 만들어 활성화해야 합니다.

## <a name="2-install-library-packages"></a>2: 라이브러리 패키지 설치

1. *requirements.txt* 파일에서 필요한 클라이언트 라이브러리 패키지에 대한 줄을 추가하고 파일을 저장합니다.

    ```text
    azure-storage-blob
    azure-identity
    ```

1. 터미널 또는 명령 프롬프트에서 요구 사항을 다시 설치합니다.

    ```cmd
    pip install -r requirements.txt
    ```

## <a name="3-create-a-file-to-upload"></a>3: 업로드할 파일 만들기

다음과 같은 콘텐츠가 포함된 *sample-source.txt*라는 소스 파일(코드에 필요함)을 만듭니다.

```text
Hello there, Azure Storage. I'm a friendly file ready to be stored in a blob.
```

## <a name="4-use-blob-storage-from-app-code"></a>4: 앱 코드에서 Blob Storage 사용

다음 섹션(번호 4a 및 4b)에서는 Blob 컨테이너에 액세스하는 두 가지 방법을 보여줍니다.

[첫 번째 방법(4a)](#4a-use-blob-storage-with-authentication)은 [Python 앱을 인증하는 방법](azure-sdk-authenticate.md#authenticate-with-defaultazurecredential)의 설명대로 `DefaultAzureCredential`을 사용하여 앱을 인증합니다. 이 방법을 사용하면 먼저 앱 ID에 적절한 권한을 할당해야 합니다. 이것이 권장되는 방식입니다.

[두 번째 방법(4b)](#4b-use-blob-storage-with-a-connection-string)은 연결 문자열을 사용하여 스토리지 계정에 직접 액세스합니다. 이 방법이 더 간단해 보이지만 두 가지 중요한 결점이 있습니다.

- 연결 문자열은 본질적으로 스토리지 계정을 사용하여(해당 계정 내의 개별 리소스를 사용하지 않고) 연결 에이전트를 인증합니다. 결과적으로 연결 문자열은 필요 이상으로 광범위한 권한을 부여합니다.

- 연결 문자열에는 액세스 키가 일반 텍스트 형식으로 포함되기 때문에 구성이나 보안이 적절하게 설정되지 않으면 잠재적인 취약점이 있습니다. 이러한 연결 문자열이 노출되면 스토리지 계정 내에서 광범위한 리소스에 액세스하는 데 사용될 수 있습니다.

이런 이유 때문에 프로덕션 코드에는 인증 메서드를 사용하는 것이 좋습니다.

### <a name="4a-use-blob-storage-with-authentication"></a>4a: 인증에 Blob Storage 사용

1. `STORAGE_BLOB_URL`라는 환경 변수를 만듭니다.

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set STORAGE_BLOB_URL=https://pythonazurestorage12345.blob.core.windows.net
    ```

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    STORAGE_BLOB_URL=https://pythonazurestorage12345.blob.core.windows.net
    ```

    ---

    "pythonazurestorage12345"를 특정 스토리지 계정의 이름으로 바꿉니다.

    이 환경 변수는 이 예에서만 사용되며 Azure 라이브러리에서 사용되지 않습니다.

1. 다음 코드를 사용하여 *use_blob_auth.py*라는 파일을 만듭니다. 주석은 단계를 설명합니다.

    ```python
    import os
    from azure.identity import DefaultAzureCredential

    # Import the client object from the Azure library
    from azure.storage.blob import BlobClient

    credential = DefaultAzureCredential()

    # Retrieve the storage blob service URL, which is of the form
    # https://pythonazurestorage12345.blob.core.windows.net/
    storage_url = os.environ["STORAGE_BLOB_URL"]

    # Create the client object using the storage URL and the credential
    blob_client = BlobClient(storage_url, container_name="blob-container-01", blob_name="sample-blob.txt", credential=credential)

    # Open a local file and upload its contents to Blob Storage
    with open("./sample-source.txt", "rb") as data:
        blob_client.upload_blob(data)
    ```

    참조 링크:
      - [DefaultAzureCredential(azure.identity)](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python)
      - [BlobClient(azure.storage.blob)](/python/api/azure-storage-blob/azure.storage.blob.blobclient?view=azure-python)

1. 의도적으로 실패하는 코드 실행을 시도합니다.

    ```cmd
    python use_blob_auth.py
    ```

    사용 중인 로컬 서비스 주체에 Blob 컨테이너에 액세스할 수 있는 권한이 없기 때문에 다음 오류가 표시됩니다. "이 요청은 이 권한을 사용하여 이 작업을 수행할 수 있는 권한이 없습니다."

1. 서비스 주체에 컨테이너에 대한 권한을 부여하려면, Azure CLI 명령 [az role assignment create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)를 사용합니다.

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```azurecli
    az role assignment create --assignee %AZURE_CLIENT_ID% ^
        --role "Storage Blob Data Contributor" ^
        --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345/blobServices/default/containers/blob-container-01"
    ```

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli
    az role assignment create --assignee $AZURE_CLIENT_ID \
        --role "Storage Blob Data Contributor" \
        --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345/blobServices/default/containers/blob-container-01"
    ```

    ---

    `--scope` 인수는 이 역할 할당이 적용되는 위치를 식별합니다. 이 예제에서는 "Storage Blob 데이터 기여자"라는 역할을 "blob-container-01"이라는 *특정* 컨테이너에 부여합니다.

    `pythonazurestorage12345`를 스토리지 계정의 정확한 이름으로 바꿉니다. 필요하면 리소스 그룹과 Blob 컨테이너의 이름도 조정할 수 있습니다. 잘못된 이름을 사용하면 "중첩된 리소스에 대해 요청된 작업을 수행할 수 없습니다. 부모 리소스 'pythonazurestorage12345'를 찾을 수 없습니다."라는 오류가 표시됩니다.

    이 명령의 `--scope` 인수에는 AZURE_CLIENT_ID와 AZURE_SUBSCRIPTION_ID 환경 변수도 사용되며, 이러한 변수는 [Azure를 위한 로컬 Python 개발 환경 구성](configure-local-development-environment.md)에 따라 서비스 주체에 대해 로컬 환경에 이미 설정되어 있어야 합니다.

1. 권한이 전파될 때까지 1~2분 정도 기다린 후 코드를 다시 실행하여 이제 코드가 작동하는지 확인합니다. 권한 오류가 다시 표시되면 조금 더 기다린 후 코드를 다시 시도합니다.

범위 및 역할 할당에 대한 자세한 내용은 [역할 권한을 할당하는 방법](how-to-assign-role-permissions.md)을 참조하세요.

### <a name="4b-use-blob-storage-with-a-connection-string"></a>4b: 연결 문자열에 Blob Storage 사용

1. 스토리지 계정의 전체 연결 문자열의 값인 `AZURE_STORAGE_CONNECTION_STRING`이라는 환경 변수를 생성합니다. (이 환경 변수는 다양한 Azure CLI 설명에도 사용됩니다.)

1. 다음 코드를 사용하여 *use_blob_conn_string.py*라는 Python 파일을 만듭니다. 주석은 단계를 설명합니다.

    ```python
    import os

    # Import the client object from the Azure library
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

## <a name="5-verify-blob-creation"></a>5. Blob 생성 확인

두 방법 중 하나의 코드를 실행한 후 [Azure Portal](https://portal.azure.com)에서 Blob 컨테이너로 이동하여 *sample-source.txt* 파일과 콘텐츠가 동일한 *sample-blob.txt*라는 새 Blob이 있는지 확인합니다.

![업로드한 파일을 보여주는 Blob 컨테이너의 Azure Portal 페이지](media/azure-sdk-example-storage/portal-blob-container-file.png)

## <a name="6-clean-up-resources"></a>6: 리소스 정리

```azurecli
az group delete -n PythonAzureExample-Storage-rg
```

이 예제에서 프로비저닝된 리소스를 유지할 필요가 없으며 구독에서 지속적인 요금을 방지하려면 이 명령을 실행합니다.

[`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-) 메서드를 사용하여 코드에서 리소스 그룹을 삭제할 수도 있습니다.

## <a name="see-also"></a>참고 항목

- [예: 리소스 그룹 프로비저닝](azure-sdk-example-resource-group.md)
- [예: 웹앱 프로비저닝 및 코드 배포](azure-sdk-example-web-app.md)
- [예: Azure Storage 프로비저닝](azure-sdk-example-storage.md)
- [예: MySQL 데이터베이스 프로비저닝 및 사용](azure-sdk-example-database.md)
- [예: 가상 머신 프로비저닝](azure-sdk-example-virtual-machines.md)
