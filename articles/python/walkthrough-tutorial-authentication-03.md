---
title: '연습, 3부: Azure 서비스를 사용하여 Python 앱 인증'
description: Azure Functions를 사용하여 타사 API를 구현하는 예와 액세스 키를 사용하여 엔드포인트를 보호하는 방법을 살펴봅니다.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: ce6fbefc52c36bcd5e002cf1e6c8ca7ca4814062
ms.sourcegitcommit: 075f39972e390e79ed09a3fcfdbfc776727e08fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97952445"
---
# <a name="part-3-example-third-party-api-implementation"></a>3부: 타사 API 구현 예

[이전 파트: 인증 과제](walkthrough-tutorial-authentication-02.md)

예제 시나리오에서 기본 앱의 퍼블릭 엔드포인트는 액세스 키로 보호되는 타사 API를 사용합니다. 이 섹션에서는 Azure Functions를 사용하여 타사 API를 구현하는 방법을 보여주지만 API를 다른 방식으로 구현하고 다른 클라우드 서버 또는 웹 호스트에 배포할 수 있습니다. 유일하게 중요한 측면은 모든 클라이언트 요청에 포함되어야 하는 특정 액세스 키로 보호되는 엔드포인트입니다. 이 API를 호출하는 모든 앱은 해당 키를 안전하게 관리해야 합니다.

데모용으로, 이 API는 `https://msdocs-example-api.azurewebsites.net/api/RandomNumber` 엔드포인트에 배포됩니다. 단, API를 호출하려면 `?code=` URL 매개 변수 또는 HTTP 헤더의 `'x-functions-key'` 속성에 액세스 키 `d0c5atM1cr0s0ft`를 제공해야 합니다. 예를 들어, 브라우저 또는 curl에서 다음 URL을 시도해보세요. [https://msdocs-example-api.azurewebsites.net/api/RandomNumber?code=d0c5atM1cr0s0ft](https://msdocs-example-api.azurewebsites.net/api/RandomNumber?code=d0c5atM1cr0s0ft).

액세스 키가 유효하면 엔드포인트는 단일 속성 "value"가 포함된 JSON 응답을 반환합니다. 이 값은 1에서 999 사이의 숫자(예: `{"value": 959}`)입니다.

엔드포인트는 Python으로 구현되고 Azure Functions에 배포됩니다. 코드는 다음과 같습니다.

```python
import logging
import random
import json

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('RandomNumber invoked via HTTP trigger.')

    random_value = random.randint(1, 1000)
    dict = { "value" : random_value }
    return func.HttpResponse(json.dumps(dict))
```

샘플 리포지토리에서 이 코드는 *third_party_api/RandomNumber/\_\_init\_\_.py* 아래에 있습니다. *RandomNumber* 폴더는 함수의 이름을 제공하고 *\_\_init\_\_.py* 에는 코드가 포함됩니다. 이 폴더에 있는 또 다른 파일 *function.json* 은 함수가 트리거되는 시점을 설명합니다. *third_party_api* 부모 폴더의 다른 파일은 함수 자체를 호스트하는 Azure Function "앱"에 대한 세부 정보를 제공합니다.

코드를 배포하기 위해 샘플의 프로비저닝 스크립트는 다음 단계를 수행합니다.

1. Azure CLI 명령 [`az storage account create`](/cli/azure/storage/account#az-storage-account-create)를 사용하여 Azure Functions에 대한 백업 스토리지 계정을 만듭니다.

1. Azure CLI 명령 [`az function app create`](/cli/azure/functionapp#az-functionapp-create)를 사용하여 Azure Functions "앱"을 만듭니다.

1. 호스트가 완전히 프로비저닝될 때까지 60초를 기다린 후 [Azure Functions Core Tools](/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash) 명령, [`func azure functionapp publish`](/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash#project-file-deployment)를 사용하여 코드를 배포합니다.

1. 액세스 키 `d0c5atM1cr0s0ft`를 함수에 할당합니다. (함수 키에 대한 배경 정보는 [Azure Functions 보안 설정](/azure/azure-functions/security-concepts)을 참조하세요.)

    프로비저닝 스크립트에서는 현재 Azure CLI가 이 특정 기능을 지원하지 않기 때문에 [함수 키 관리 API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)에 대한 REST API 호출을 통해 이 단계가 수행됩니다. REST API를 호출하기 위해, 프로비저닝 스크립트가 먼저 다른 REST API 호출을 사용하여 함수 앱의 마스터 키를 검색해야 합니다.

    [Azure Portal](https://portal.azure.com)을 통해 액세스 키를 할당할 수도 있습니다. 함수 앱의 페이지에서 **함수** 를 선택한 다음, 보호할 특정 함수(이 예에서는 `RandomNumber`)를 선택합니다. 함수의 페이지에서 **함수 키** 를 선택하여 해당 키를 만들고 관리할 수 있는 페이지를 엽니다.

> [!div class="nextstepaction"]
> [4부 - 기본 앱 구현 예 >>>](walkthrough-tutorial-authentication-04.md)
