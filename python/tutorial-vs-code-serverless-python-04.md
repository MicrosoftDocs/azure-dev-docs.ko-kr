---
title: '4단계: VS Code를 사용하여 Azure Functions Python 코드를 로컬에서 디버그'
description: 자습서 4 단계, Python 코드를 확인하도록 VS Code 디버거를 로컬에서 실행
ms.topic: conceptual
ms.date: 09/02/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: ddb6cd0b1c1cac308e7e7e8da5b658cda277586a
ms.sourcegitcommit: 44d1abfb836f90b8731d7ea5d5a5af09245b2b89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2020
ms.locfileid: "77422140"
---
# <a name="4-debug-the-azure-functions-python-code-locally"></a>4: Azure Functions Python 코드를 로컬에서 디버그

[이전 단계: 코드 파일 검사](tutorial-vs-code-serverless-python-03.md)

Visual Studio Code에서 Azure Functions Python 코드를 로컬에서 디버그할 수 있습니다.

1. 함수 프로젝트를 만들 때 Visual Studio Code 확장은 `.vscode/launch.json`에 **Python 함수에 연결**이라는 단일 구성을 포함하는 시작 구성도 만듭니다. 이 구성은 F5 키를 누르거나 디버그 탐색기를 사용하여 프로젝트를 시작할 수 있다는 것을 의미합니다.

    ![Python 프로젝트를 시작하도록 디버그 탐색기 구성](media/tutorial-vs-code-serverless-python/configuration-to-start-a-python-project-for-debugging.png)

1. 디버거를 시작하면 사용 가능한 엔드포인트의 요약을 포함하여 Azure Functions의 출력을 표시하는 터미널이 열립니다. "HttpExample" 이외의 이름을 사용한 경우에는 URL이 다를 수 있습니다.

    ```output
    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. Visual Studio Code **출력** 창의 URL에서 **Ctrl+클릭** 또는 **Cmd+c클릭**을 사용하여 해당 주소로 브라우저를 열거나 브라우저를 시작하고 동일한 URL에 붙여 넣습니다. 두 경우 모두 엔드포인트는 `api/<function_name>`이며, 이 경우 `api/HttpExample`입니다. 그러나 해당 URL에 이름 매개 변수가 포함되어 있지 않기 때문에 브라우저 창에는 코드의 해당 경로에 대한 적절한 "쿼리 문자열 또는 요청 본문에 이름을 전달하세요."가 표시되어야 합니다.

1. 이제 `http://localhost:7071/api/HttpExample?name=VS%20Code`와 같이 사용할 이름 매개 변수를 추가해 봅니다. 그러면 브라우저 창은 해당 코드 경로를 실행했다는 것을 보여 주는 "Hello Visual Studio Code!"라는 메시지를 표시합니다.

1. JSON 요청 본문에 이름 값을 전달하기 위해 JSON 인라인과 함께 curl과 같은 도구를 사용할 수 있습니다.

    ```bash
    # Mac OS/Linux: modify the URL if you're using a different function name
    curl --header "Content-Type: application/json" --request POST \
        --data '{"name":"Visual Studio Code"}' http://localhost:7071/api/HttpExample
    ```

    ```ps
    # Windows (escaping on the quotes is necessary; also modify the URL
    # if you're using a different function name)
    curl --header "Content-Type: application/json" --request POST \
        --data {"""name""":"""Visual Studio Code"""} http://localhost:7071/api/HttpExample
    ```

    PowerShell에서 [Invoke-WebRequest cmdlet](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-6)을 사용할 수도 있습니다.

    ---

    또는 `{"name":"Visual Studio Code"}`를 포함하는 *data.json*과 같은 파일을 만들고 `curl --header "Content-Type: application/json" --request POST --data @data.json http://localhost:7071/api/HttpExample` 명령을 사용합니다.

1. 함수 디버깅을 테스트하려면 `name = req.params.get('name')`을 읽는 줄에 중단점을 설정하고 URL을 다시 요청합니다. Visual Studio Code 디버거는 해당 줄에서 중지되어 변수를 검사하고 코드를 단계별로 실행할 수 있습니다. (기본 디버깅에 대한 간단한 연습은 [Visual Studio Code 자습서 - 디버거 구성 및 실행](https://code.visualstudio.com/docs/python/python-tutorial#configure-and-run-the-debugger)을 참조하세요.)

1. 함수를 로컬에서 철저하게 테스트한 것이 만족스러우면 디버거를 중지합니다(디버깅 도구 모음에서 **디버깅** > **디버깅 중지** 메뉴 명령 또는 **연결 끊기** 명령 사용).

> [!div class="nextstepaction"]
> [디버거를 로컬로 실행했습니다. - 5단계 진행 >>>](tutorial-vs-code-serverless-python-05.md)

[문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=04-test-debug)
