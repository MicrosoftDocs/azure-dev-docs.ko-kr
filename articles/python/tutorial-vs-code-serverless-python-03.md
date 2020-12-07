---
title: '3단계: VS Code에서 서버리스 Azure Functions에 대한 Python 코드 파일 검사'
description: 자습서 3단계, Azure Functions에서 제공하는 템플릿 서버리스 Python 코드 이해
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: a57d7bee924082a187b08c4326ae4de1d7cca998
ms.sourcegitcommit: 709fa38a137b30184a7397e0bfa348822f3ea0a7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96442192"
---
# <a name="3-examine-the-python-code-files-in-visual-studio-code"></a>3: Visual Studio Code에서 Python 코드 파일 검사

[이전 단계: 함수 만들기](tutorial-vs-code-serverless-python-02.md)

새로 만든 **HttpExample** 함수에는 세 개의 파일이 있습니다. *\_\_init\_\_.py* 는 함수의 코드를 포함하고, *function.json* 은 Azure Functions에 대한 함수를 설명하고 *sample.dat* 는 샘플 데이터 파일입니다. 다른 파일을 하위 폴더에 추가할 수 있음을 보여 주기 위해서만 존재하므로 원하는 경우 *sample.dat* 를 삭제할 수 있습니다.

## <a name="functionjson"></a>function.json

*function.json* 파일은 Azure Functions 엔드포인트에 필요한 구성 정보를 설명합니다.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

`scriptFile` 속성은 코드의 시작 파일을 식별하며, 이 코드에는 `main`이라는 Python 함수가 포함되어 있어야 합니다. 여기에 지정된 파일에 `main` 함수가 포함되어 있으면 코드를 여러 파일로 나눌 수 있습니다.

`bindings` 요소는 두 개체, 즉 들어오는 요청을 설명하는 개체와 HTTP 응답을 설명하는 개체를 포함합니다. 들어오는 요청의 경우(`"direction": "in"`) 함수는 HTTP GET 또는 POST 요청에 응답하며 인증이 필요하지 않습니다. 응답(`"direction": "out"`)은 `main` Python 함수에서 반환되는 값을 반환하는 HTTP 응답입니다.

*functions.json* 파일에 대한 자세한 내용은 [Azure Functions 개발자 가이드](/azure/azure-functions/functions-reference) 및 [Azure Functions 트리거 및 바인딩 참조](/azure/azure-functions/functions-triggers-bindings?tabs=python)를 참조하세요.

## <a name="__init__py"></a>\_\_init\_\_.py

새 함수를 만들 때 Azure Functions는 *\_\_init\_\_.py* 에서 기본 Python 코드를 제공합니다.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello, {name}. This HTTP triggered function executed successfully.")
    else:
        return func.HttpResponse(
             "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.",
             status_code=200
        )
```

코드의 중요한 부분은 다음과 같습니다.

- `azure.functions` 모듈을 가져와야 합니다. 로깅 모듈 가져오기는 선택 사항이지만 권장됩니다.
- 필요한 `main` Python 함수는 `req`라는 `func.HttpRequest` 개체를 수신하고, `func.HttpResponse` 형식의 값을 반환합니다. [func.HttpRequest](/python/api/azure-functions/azure.functions.httprequest) 및 [func.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse) 참조에서 이러한 개체의 기능에 대해 자세히 알아볼 수 있습니다.
- 그러면 `main`의 본문에서 요청을 처리하고 응답을 생성합니다. 이 경우 코드는 URL에서 `name` 매개 변수를 찾습니다. 실패한 경우 요청 본문에 JSON이 포함되어 있는지(`func.HttpRequest.get_json` 사용), JSON에 `name` 값이 포함되어 있는지 확인합니다(`get_json`에서 반환된 JSON 개체의 `get` 메서드 사용).
- 이름이 발견되면 코드는 이름이 추가된 "Hello" 문자열을 반환하고 함수 성공을 알립니다. 그렇지 않으면 코드가 일반 메시지를 반환합니다.

> [!div class="nextstepaction"]
> [코드 파일을 검사했습니다. - 4단계 진행 >>>](tutorial-vs-code-serverless-python-04.md)

[문제가 있나요? 알려주세요.](https://aka.ms/python-functions-qs-ms-survey)
