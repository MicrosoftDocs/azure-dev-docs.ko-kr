---
title: '연습, 5부: Azure 서비스를 사용하여 Python 앱 인증'
description: 기본 앱의 종속성(주로 Azure SDK 라이브러리), 필요한 import 문 및 설정해야 하는 환경 변수에 대한 설명입니다.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 9c6204afd17d86cd8677022a59641e5343c6a543
ms.sourcegitcommit: 29b161c450479e5d264473482d31e8d3bf29c7c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91764735"
---
# <a name="part-5-main-app-dependencies-import-statements-and-environment-variables"></a>5부: 기본 앱 종속성, import 문, 환경 변수

[이전 파트: 기본 앱 구현 예](walkthrough-tutorial-authentication-04.md)

이 파트에서는 기본 앱으로 가져온 Python 라이브러리와 코드에 필요한 환경 변수를 검사합니다. Azure에 배포하는 경우 Azure App Service의 애플리케이션 설정을 사용하여 환경 변수를 제공합니다.

## <a name="dependencies-and-import-statements"></a>종속성 및 import 문

앱 코드는 다음과 같은 여러 라이브러리에서 필요합니다. Flask, 표준 HTTP 요청 라이브러리, Active Directory([azure.identity](/python/api/overview/azure/identity-readme)), Key Vault([azure.keyvault.secrets](/python/api/overview/azure/keyvault-secrets-readme)) 및 Queue Storage([azure.storage.queue](/python/api/overview/azure/storage-queue-readme))용 Azure 라이브러리. 이러한 라이브러리는 앱의 *requirements.txt* 파일에 포함되어 있습니다.

```txt
flask
requests
azure.identity
azure.keyvault.secrets
azure.storage.queue
```

Azure App Service에 앱을 배포할 때 Azure는 호스트 서버에 이러한 요구 사항을 자동으로 설치합니다. 로컬에서 실행하는 경우에는 `pip install -r requirements.txt`를 사용하여 환경에 설치합니다.

코드 맨 위에는 라이브러리에서 사용하는 파트에 대한 필수 import 문이 있습니다.

```python
from flask import Flask, request, jsonify
import requests, random, string, os
from datetime import datetime
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient
from azure.storage.queue import QueueClient
```

## <a name="environment-variables"></a>환경 변수

앱 코드는 네 가지 환경 변수에 따라 다릅니다.

| 변수 | 값 |
| --- | --- |
| THIRD_PARTY_API_ENDPOINT | 타사 API의 URL입니다(예: [3부](walkthrough-tutorial-authentication-03.md)에 설명된 `https://msdocs-api-example.azurewebsites.net/api/RandomNumber`). |
| KEY_VAULT_URL | 타사 API에 대한 액세스 키를 저장한 Azure Key Vault의 URL입니다. |
| THIRD_PARTY_API_SECRET_NAME | 타사 API에 대한 액세스 키가 포함된 Key Vault의 비밀 이름입니다. |
| STORAGE_QUEUE_URL | Azure에서 구성된 Azure Storage Queue의 URL(예: [4부](walkthrough-tutorial-authentication-04.md)의 `https://msdocsmainappexample.queue.core.windows.net/code-requests`)입니다. 큐 이름은 URL 끝에 포함되기 때문에 코드의 어디에도 이름이 보이지 않습니다. |

로컬에서 실행하는 경우 사용 중인 명령 셸 내에서 이러한 변수를 만듭니다. 가상 머신에 앱을 배포하는 경우 유사한 서버 쪽 변수를 만듭니다.

하지만 Azure App Service에 배포할 때는 서버 자체에 액세스할 수 없습니다. 이런 경우 동일한 이름으로 애플리케이션 설정을 만듭니다. 그러면 앱에 환경 변수로 표시됩니다. 

프로비저닝 스크립트는 Azure CLI 명령, [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set)를 사용하여 이러한 설정을 만듭니다. 4개 변수는 모두 단일 명령으로 설정됩니다.

Azure Portal을 통해 설정을 만들려면 [Azure Portal에서 App Service 앱 구성](/azure/app-service/configure-common)을 참조하세요.

> [!div class="nextstepaction"]
> [6부 - 기본 앱 시작 코드 >>>](walkthrough-tutorial-authentication-06.md)
