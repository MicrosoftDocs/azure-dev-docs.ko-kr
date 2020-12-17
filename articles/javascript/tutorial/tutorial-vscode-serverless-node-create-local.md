---
title: Visual Studio Code에서 Azure Functions 애플리케이션 만들기
description: HTTP 트리거를 사용하는 함수가 포함된 로컬 Azure Functions(서버리스) 애플리케이션을 만듭니다. Azure Functions 앱은 다양한 트리거로 Functions를 많이 포함할 수 있습니다. HTTP 트리거는 들어오는 HTTP 트래픽을 처리합니다.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: devx-track-js, contperf-fy21q2
ms.openlocfilehash: b1e357bc3329c2dc19fdf4988b22182576575cf5
ms.sourcegitcommit: c8330128d5d6a71859933a890ecdf047cb950996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97522079"
---
# <a name="2-create-the-local-functions-app-with-the-visual-studio-code-_functions_-extension"></a>2. Visual Studio Code _Functions_ 확장을 사용하여 로컬 함수 앱 만들기

[이전 단계: 소개 및 필수 구성 요소](tutorial-vscode-serverless-node-install.md)

이 단계에서는 [HTTP 트리거](/azure/azure-functions/functions-reference-node#http-triggers-and-bindings)를 사용하는 함수가 포함된 로컬 Azure Functions(서버리스) 애플리케이션을 만듭니다. Azure Functions 앱에는 [서로 다른 트리거](/azure/azure-functions/functions-triggers-bindings)를 가진 함수가 많이 포함될 수 있습니다. HTTP 트리거는 들어오는 HTTP 트래픽을 처리합니다.

1. 터미널 또는 명령 프롬프트의 프로젝트에 적합한 폴더 내에서 Visual Studio Code를 실행합니다.

    ```bash
    # Create and navigate to a project folder

    # Run VS Code in that folder
    code .
    ```

1. Visual Studio Code에서 Azure 로고를 선택하여 **Azure Functions** 탐색기를 연 다음, **프로젝트 만들기** 명령을 선택합니다.

    ![VS Code에서 로컬 함수 앱 만들기](../media/functions-extension/create-function-app-project.png)

1. 처음 두 프롬프트에서 현재 폴더를 선택한 다음, **JavaScript** 를 언어로 선택합니다.

1. **Select a template for your project's first function**(프로젝트의 첫 번째 함수에 대한 템플릿 선택) 프롬프트에서 **HTTP 트리거** 를 선택합니다.

    ![함수에 대한 트리거 선택](../media/functions-extension/create-function-choose-template.png)

1. **Provide a function name**(함수 이름 제공) 프롬프트에서 **HttpExample** 을 입력합니다. (기본 'HttpTrigger' 이름은 트리거와 동일하므로 사용하지 마십시오. 혼동될 수 있습니다.)

    ![함수 이름 입력](../media/functions-extension/create-function-name.png)

1. **권한 수준** 프롬프트에서 **익명** 을 선택합니다.

    ![ `권한 수준` 프롬프트에서 `익명` 선택](../media/functions-extension/create-function-anonymous-auth.png)

1. 몇 분 후 VS Code가 프로젝트 생성을 완료합니다. 함수의 이름이 *HttpExample* 인 폴더가 있고, 그 안에는 세 개의 파일이 있습니다.

    | 파일 이름 | Description |
    | --- | --- |
    | *index.js* |  HTTP 요청에 응답하는 소스 코드입니다. |
    | *function.json* | HTTP 트리거의 [바인딩 구성](/azure/azure-functions/functions-triggers-bindings)입니다. |
    | *sample.dat* | 폴더에 다른 파일이 있을 수 있다는 것을 보여주는 자리 표시자 데이터 파일입니다. 원하는 경우 이 자습서에서 사용되지 않으므로 이 파일을 삭제해도 됩니다. |

    ![함수 앱 만들기 결과](../media/functions-extension/create-function-app-results.png)

## <a name="http-function-javascript-template-code"></a>HTTP 함수 JavaScript 템플릿 코드

HTTP 요청에 응답하는 기본 코드가 자동으로 제공됩니다. HTTP 요청(_req_ 매개 변수) 및 응답 개체에 대해 잘 알고 있는 분들에게는 이 함수가 익숙할 것입니다. `res` 속성에 대한 **컨텍스트** 개체를 사용하여 응답 정보를 반환합니다.  

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

개발자가 애플리케이션의 코드에 즉시 집중할 수 있도록 각 함수 [트리거](/azure/azure-functions/functions-triggers-bindings?tabs=csharp) 형식에서 자동으로 템플릿 함수를 제공합니다. Express.js에서 Azure Functions로 이동하는 경우 해당 애플리케이션에 [필요한 변경 내용을 검토](/azure/azure-functions/shift-expressjs?tabs=javascript)하세요. 

> [!div class="nextstepaction"]
> [함수 앱을 만들었습니다.](tutorial-vscode-serverless-node-test-local.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=create-app)
