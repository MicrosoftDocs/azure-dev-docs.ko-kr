---
title: '7단계: VS Code를 사용하여 Python에서 서버리스 Azure Functions에 대한 스토리지 바인딩 추가'
description: 자습서 7단계로, Azure Storage에 메시지를 기록하기 위해 서버리스 Python 코드에서 바인딩을 추가합니다.
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: aa0913f35915a4d4a97b1d0183976614509b704d
ms.sourcegitcommit: 709fa38a137b30184a7397e0bfa348822f3ea0a7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96442181"
---
# <a name="7-add-a-storage-binding-for-azure-functions-in-python"></a>7: Python에서 Azure Functions에 대한 스토리지 바인딩 추가

[이전 단계: 두 번째 함수 배포](tutorial-vs-code-serverless-python-06.md)

_바인딩_ 을 사용하면 데이터 액세스 코드를 작성하지 않고도 Azure Storage와 같은 리소스에 함수 코드를 연결할 수 있습니다.

바인딩은 *function.json* 파일에 정의되며 입력과 출력을 모두 나타낼 수 있습니다. 하나의 함수는 입력 및 출력 바인딩을 여러 개 가질 수 있지만, 트리거는 하나만 가질 수 있습니다. 자세한 내용은 [Azure Functions 트리거 및 바인딩 개념](/azure/azure-functions/functions-triggers-bindings)을 참조하세요.

이 섹션에서는 이 자습서의 앞부분에서 만든 HttpExample 함수에 스토리지 바인딩을 추가합니다. 함수는 이 바인딩을 사용하여 각 요청과 함께 스토리지에 메시지를 씁니다. (해당 스토리지는 함수 앱에서 사용하는 것과 동일한 기본 스토리지 계정을 사용합니다. 그러나 스토리지를 많이 사용하려는 경우에는 별도의 계정을 만드는 것을 고려해보세요.)

1. Azure Functions 프로젝트에 대한 원격 설정을 *local.settings.json* 파일에 동기화합니다. 명령 팔레트를 열고 **Azure Functions: 원격 설정 다운로드** 를 선택하면 됩니다.

    *local.settings.json* 을 열고 `AzureWebJobsStorage`에 대한 값이 포함되어 있는지 확인합니다. 해당 값은 스토리지 계정에 대한 연결 문자열입니다.

1. Azure Functions 탐색기의 **Local Project** 아래에서 `HttpExample` 함수를 마우스 오른쪽 단추로 클릭하고 **바인딩 추가** 를 선택합니다.

    ![Visual Studio Code 탐색기에서 바인딩 추가 명령](media/tutorial-vs-code-serverless-python/add-binding-command-to-azure-functions-in-visual-studio-code.png)

1. Visual Studio Code에서 이어지는 프롬프트에서 다음 값을 선택하거나 입력합니다.

    | prompt | 제공할 값 |
    | --- | --- |
    | 바인딩 방향 선택 | out |
    | "out" 방향의 바인딩 선택 | Azure Queue Storage |
    | 코드에서 이 바인딩을 식별하는 데 사용하는 이름 | msg |
    | 메시지가 전송될 큐 | outqueue |
    | *local.settings.json* 에서 설정 선택(스토리지 연결 요청) | AzureWebJobsStorage |

1. 이러한 항목을 선택한 후 *function.json* 파일에 다음 바인딩이 추가되었는지 확인합니다.

    ```json
        {
          "type": "queue",
          "direction": "out",
          "name": "msg",
          "queueName": "outqueue",
          "connection": "AzureWebJobsStorage"
        }
    ```

1. 이제 바인딩을 구성했으므로 함수 코드에서 사용할 수 있습니다. 다시, 새로 정의된 바인딩은 코드에서 *\_\_init\_\_.py* 의 `main` 함수에 대한 인수로 나타납니다.

    예를 들어 다음과 일치하도록 HttpExample의 *\_\_init\_\_.py* 를 수정할 수 있습니다. 이는 `msg` 인수를 사용하여 요청에 사용된 이름을 가진 타임스탬프 메시지를 작성하는 방법을 보여줍니다. 설명은 특정 변경 내용을 설명합니다.

    ```python
    import logging
    import datetime  # MODIFICATION: added import
    import azure.functions as func

    # MODIFICATION: the added binding appears as an argument; func.Out[func.QueueMessage]
    # is the appropriate type for an output binding with "type": "queue" (in function.json).
    def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> func.HttpResponse:
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
            # MODIFICATION: write the a message to the message queue, using msg.set
            msg.set(f"Request made for {name} at {datetime.datetime.now()}")

            return func.HttpResponse(f"Hello, {name}. This HTTP triggered function executed successfully.")
        else:
            return func.HttpResponse(
                 "Please pass a name on the query string or in the request body",
                 status_code=400
            )
    ```

1. 이러한 변경 내용을 로컬에서 테스트하려면 F5 키를 누르거나 **실행** > **디버깅 시작** 메뉴 명령을 선택하여 Visual Studio Code에서 디버거를 다시 시작합니다.

    전과 같이 **출력** 창에 프로젝트의 엔드포인트가 표시됩니다.

1. 브라우저에서 URL `http://localhost:7071/api/HttpExample?name=VS%20Code`를 방문하여 HttpExample 엔드포인트에 대한 요청을 만듭니다. 또한 큐에 메시지를 써야 합니다.

1. 메시지가 바인딩에 지정된 대로 “outqueue” 큐에 기록되었는지 확인하려면 다음 세 가지 방법 중 하나를 사용할 수 있습니다.

    1. [Azure Portal](https://portal.azure.com)에 로그인하고 함수 프로젝트를 포함하는 리소스 그룹으로 이동합니다. 해당 리소스 그룹 내에서 프로젝트의 스토리지 계정을 찾아 이동한 다음, **큐** 로 이동합니다. 해당 페이지에서 로그된 모든 메시지를 표시하는 “outqueue”로 이동합니다.

    1. [Visual Studio Code를 사용하여 Azure 스토리지에 Functions 연결](/azure/azure-functions/functions-add-output-binding-storage-queue-vs-code), 특히 [출력 큐 검사](/azure/azure-functions/functions-add-output-binding-storage-queue-vs-code#examine-the-output-queue) 섹션에 설명된 대로 Visual Studio와 통합된 Azure Storage Explorer를 사용하여 큐를 탐색하고 검사합니다.

    1. [스토리지 큐 쿼리](/azure/azure-functions/functions-add-output-binding-storage-queue-cli?pivots=programming-language-python)에 설명된 대로 Azure CLI를 사용하여 스토리지 큐를 쿼리합니다.

1. 클라우드에서 테스트하려면 **함수 앱에 배포** 를 사용하여(**Azure: Functions** 탐색기에서) 코드를 다시 배포합니다. 메시지가 표시되면 이전에 만든 함수 앱을 선택합니다. 배포가 완료되면(몇 분이 소요됩니다.) **출력** 창에는 테스트를 반복할 수 있는 공용 엔드포인트가 표시됩니다.

> [!div class="nextstepaction"]
> [스토리지 바인딩을 추가했습니다. - 8단계 진행 >>>](tutorial-vs-code-serverless-python-08.md)

[문제가 있나요? 알려주세요.](https://aka.ms/python-functions-qs-ms-survey)
