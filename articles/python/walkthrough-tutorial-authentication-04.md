---
title: '연습, 4부: Azure 서비스를 사용하여 Python 앱 인증'
description: 기본 앱의 구현에 대한 개요이며, 모든 코드를 포함합니다.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 0b7ffa1fb855d4b676813f49c545071209f6ce79
ms.sourcegitcommit: 324da872a9dfd4c55b34739824fc6a6598f2ae12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379519"
---
# <a name="part-4-example-main-application-implementation"></a>4부. 기본 애플리케이션 구현 예

[이전 파트: 타사 API 구현 예](walkthrough-tutorial-authentication-03.md)

이 시나리오의 기본 앱은 Azure App Service에 배포된 간단한 Flask 앱입니다. 앱은 */api/v1/getcode*라는 공용 API 엔드포인트를 제공하며, 앱에서 다른 용도로 코드를 생성합니다(예: 인간 사용자에 대한 2단계 인증 사용).

작동 중인 엔드포인트를 보려면 브라우저에서 [https://msdocs-main-app-example.azurewebsites.net/api/v1/getcode](https://msdocs-main-app-example.azurewebsites.net/api/v1/getcode)를 방문하거나 curl을 사용하여 요청합니다.

기본 앱은 API 엔드포인트 링크를 표시하는 간단한 홈 페이지도 제공합니다. [https://msdocs-main-app-example.azurewebsites.net](https://msdocs-main-app-example.azurewebsites.net)에서 앱의 이 부분을 볼 수 있습니다.

샘플의 프로비저닝 스크립트는 다음 단계를 수행합니다.

1. App Service 호스트를 만들고 Azure CLI 명령 [`az webapp up`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-up)을 사용하여 코드를 배포합니다.

1. 기본 앱에 대한 Azure Storage 계정을 프로비저닝합니다([`az storage account create`](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) 사용).

1. "code-requests"라는 스토리지 계정에 큐를 만듭니다([`az storage queue create`](/cli/azure/storage/queue?view=azure-cli-latest#az-storage-queue-create) 사용).

1. 앱이 큐에 쓸 수 있도록 하려면 [`az role assignment create`](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)를 사용하여 "Storage 큐 데이터 기여자" 역할을 앱에 할당합니다. 역할에 대한 자세한 내용은 [역할 권한을 할당하는 방법](how-to-assign-role-permissions.md)을 참조하세요.

기본 앱 코드는 다음과 같습니다. 중요한 세부 정보에 대한 설명은 이 시리즈의 다음 파트에서 제공됩니다.

```python
from flask import Flask, request, jsonify
import requests, random, string, os
from datetime import datetime
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential
from azure.storage.queue import QueueClient

app = Flask(__name__)

# Retrieve the URL of the third-party API endpoint we invoke.
number_url = os.environ["THIRD_PARTY_API_ENDPOINT"]

# Authenticate with Azure. First, obtain the credential object. To run locally,
# you must have the necessary environment variables set up to provide the
# local service principal information. When deployed to the cloud, the app must
# have managed identity enabled in Azure App Service.
credential = DefaultAzureCredential()

# Next, get a client object for the Key Vault. The client object is strictly
# a client-side construct provided by the Azure libraries as a layer on top
# of the Azure REST API.
key_vault_url = os.environ["KEY_VAULT_URL"]
keyvault_client = SecretClient(vault_url=key_vault_url, credential=credential)

# Obtain the secret: for this step to work you must add the app's identity to
# the key vault's access policies for secret management. When deployed to the cloud
# the identity is the name of the app in App Service; when running locally, the
# identity is the local service principal.
api_secret_name = os.environ["THIRD_PARTY_API_SECRET_NAME"]
vault_secret = keyvault_client.get_secret(api_secret_name)

# The "secret" from Key Vault is an object with multiple properties. The access key
# we want for the third-party API is in the secret's value property.
access_key = vault_secret.value

#Set up the Storage queue client to which we write messages
queue_url = os.environ["STORAGE_QUEUE_URL"]
queue_client = QueueClient.from_queue_url(queue_url=queue_url, credential=credential)


@app.route('/', methods=['GET'])
def home():
    return f'Home page of the main app. Make a request to <a href="./api/v1/getcode">/api/v1/getcode</a>.'


def random_char(num):
       return ''.join(random.choice(string.ascii_letters) for x in range(num))


@app.route('/api/v1/getcode', methods=['GET'])
def get_code():
    headers = {
        'Content-Type': 'application/json',
        'x-functions-key': access_key
        }

    r = requests.get(url = number_url, headers = headers)

    if (r.status_code != 200):
        return "Could not get you a code.", r.status_code

    data = r.json()
    chars1 = random_char(3)
    chars2 = random_char(3)
    code_value = f"{chars1}-{data['value']}-{chars2}"
    code = { "code": code_value, "timestamp" : str(datetime.utcnow()) }

    # Log a queue message with the code for, say, a process that invalidates
    # the code after a certain period of time.
    queue_client.send_message(code)

    return jsonify(code)


if __name__ == '__main__':
    app.run()
```

> [!div class="nextstepaction"]
> [5부 - 기본 앱 종속성, import, 환경 변수 >>>](walkthrough-tutorial-authentication-05.md)
