---
title: '연습, 6부: Azure 서비스를 사용하여 Python 앱 인증'
description: API 엔드포인트에 필요한 DefaultAzureCredential 개체 및 클라이언트 개체를 설정하는 기본 앱의 시작 코드를 검사합니다.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 17d5e007c65572ef301a4aa682260af6f492a626
ms.sourcegitcommit: 5ab6e90e20a87f9a8baea652befc74158a9b6613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614281"
---
# <a name="part-6-main-app-startup-code"></a>6부: 기본 앱 시작 코드

[이전 파트: 종속성, import 문, 환경 변수](walkthrough-tutorial-authentication-05.md)

`import` 문 뒤에 나오는 앱의 시작 코드는 HTTP 요청을 처리하는 함수에 사용되는 다양한 변수를 초기화합니다.

먼저 Flask 앱 개체를 만들고 환경 변수에서 타사 API 엔드포인트 URL을 검색합니다.

```python
app = Flask(__name__)

number_url = os.environ["THIRD_PARTY_API_ENDPOINT"]
```

다음으로 Azure 서비스를 사용하여 인증할 때 사용할 권장 자격 증명인 [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python
) 개체를 가져옵니다. [Python 앱을 인증하는 방법](azure-sdk-authenticate.md#authenticate-with-defaultazurecredential)을 참조하세요.

```python
credential = DefaultAzureCredential()
```

로컬에서 실행하는 경우 `DefaultAzureCredential`은 로컬 서비스 주체에 대한 정보가 포함된 `AZURE_TENANT_ID`, `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` 환경 변수를 찾습니다. 클라우드에서 실행하는 경우 `DefaultAzureCredential`은 앱에 등록된 서비스 주체(일반적으로 관리 ID 내에 포함됨)를 자동으로 사용합니다.

다음으로 코드는 Azure Key Vault에서 타사 API의 액세스 키를 검색합니다. 프로비저닝 스크립트에서 Key Vault는 [`az keyvault create`](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)를 사용하여 만들어지고 비밀은 [`az keyvault secret set`](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)를 사용하여 저장됩니다.

Key Vault 리소스 자체는 `KEY_VAULT_URL` 환경 변수에서 로드되는 URL을 통해 액세스됩니다.

```python
key_vault_url = os.environ["KEY_VAULT_URL"]
```

키 자격 증명 모음에 연결하려면 적절한 클라이언트 개체를 만들어야 합니다. 비밀을 검색해야 하기 때문에 [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python)를 사용하며, 여기에는 앱이 실행되는 ID를 나타내는 자격 증명 개체와 키 자격 증명 모음 URL이 필요합니다.

```python
keyvault_client = SecretClient(vault_url=key_vault_url, credential=credential)
```

`SecretClient` 개체를 만들어도 자격 증명을 인증하지는 않습니다. `SecretClient`는 리소스 URL과 자격 증명을 내부적으로 관리하는 클라이언트 쪽 구문일 뿐입니다. 인증 및 권한 부여는 Azure 리소스에 대한 REST API 호출을 생성하는 [`get_secret`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-)와 같은 클라이언트를 통해 작업을 호출할 때만 발생합니다.

```python
api_secret_name = os.environ["THIRD_PARTY_API_SECRET_NAME"]
vault_secret = keyvault_client.get_secret(api_secret_name)

# The "secret" from Key Vault is an object with multiple properties. The key we
# want for the third-party API is in the value property. 
access_key = vault_secret.value
```

키 자격 증명 모음에 액세스할 수 있는 권한이 앱 ID에 있더라도, 비밀에 액세스할 수 있는 권한이 명확하게 부여되어야 합니다.  그러지 않으면 `get_secret` 호출이 실패합니다. 이런 이유 때문에 프로비저닝 스크립트는 Azure CLI 명령 [`az keyvault set-policy`](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)를 사용하여 앱에 대한 "get secrets" 액세스 정책을 설정합니다. 자세한 내용은 [Key Vault 인증](/azure/key-vault/general/authentication) 및 [Key Vault에 앱 액세스 권한 부여](/azure/key-vault/general/managed-identity#grant-your-app-access-to-key-vault)를 참조하세요. 후자 문서에서는 Azure Portal을 사용하여 액세스 정책을 설정하는 방법을 보여줍니다. (이 문서는 관리 ID용으로 작성되었지만 개발에 사용되는 로컬 서비스 주체에도 동일하게 적용됩니다.)

마지막으로 앱 코드는 클라이언트 개체를 설정하며, 이를 통해 Azure Storage Queue에 메시지를 쓸 수 있습니다. 큐의 URL은 환경 변수 `STORAGE_QUEUE_URL`에 있습니다.

```python
queue_url = os.environ["STORAGE_QUEUE_URL"]
queue_client = QueueClient.from_queue_url(queue_url=queue_url, credential=credential)
```

Key Vault와 마찬가지로 Azure 라이브러리의 특정 클라이언트 개체, [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient?view=azure-python) 및 [`from_queue_url`](/python/api/azure-storage-queue/azure.storage.queue.queueclient?view=azure-python#from-queue-url-queue-url--credential-none----kwargs-) 메서드를 사용하여 해당 URL에 있는 리소스에 연결합니다. 다시 한 번, 이 클라이언트 개체를 만들려고 하면 자격 증명이 나타내는 앱 ID가 큐에 액세스할 수 있는 권한이 있는지 확인합니다. 앞서 언급했듯이 이 권한은 기본 앱에 "Storage 큐 데이터 기여자" 역할을 할당하여 부여되었습니다.

모든 시작 코드가 성공했다고 가정하면, */api/v1/getcode* API 엔드포인트를 지원하기 위한 모든 내부 변수가 앱에 있습니다.

> [!div class="nextstepaction"]
> [7부 - 기본 앱 API 엔드포인트 >>>](walkthrough-tutorial-authentication-07.md)
