---
title: Visual Studio Code에서 Azure Functions 애플리케이션 만들기
description: 자습서 2부, Azure Functions 앱 만들기
ms.topic: conceptual
ms.date: 09/23/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: 2aca9c53e254eca7c8a070cc50a88426d3886cdd
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88218395"
---
# <a name="create-the-local-functions-app"></a>로컬 Functions 앱 만들기

[이전 단계: 소개 및 필수 구성 요소](tutorial-vscode-serverless-node-01.md)

이 단계에서는 [HTTP 트리거](https://docs.microsoft.com/azure/azure-functions/functions-reference-node#http-triggers-and-bindings)를 사용하는 함수가 포함된 로컬 Azure Functions 애플리케이션을 만듭니다. Azure Functions 앱에는 [서로 다른 트리거](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings)를 가진 함수가 많이 포함될 수 있습니다. HTTP 트리거는 들어오는 HTTP 트래픽을 처리합니다.

1. 터미널 또는 명령 프롬프트의 프로젝트에 적합한 폴더 내에서 Visual Studio Code를 실행합니다.

    ```bash
    # Create and navigate to a project folder

    # Run VS Code in that folder
    code .
    ```

1. VS Code에서 Azure 로고를 선택하여 **Azure Functions** 탐색기를 연 다음, **프로젝트 만들기** 명령을 선택합니다.

    ![VS Code에서 로컬 함수 앱 만들기](media/functions-extension/create-function-app-project.png)

1. 처음 두 프롬프트에서 현재 폴더를 선택한 다음, **JavaScript**를 언어로 선택합니다.

1. **Select a template for your project's first function**(프로젝트의 첫 번째 함수에 대한 템플릿 선택) 프롬프트에서 **HTTP 트리거**를 선택합니다.

    ![함수에 대한 트리거 선택](media/functions-extension/create-function-choose-template.png)

1. **Provide a function name**(함수 이름 제공) 프롬프트에서 **HttpExample**을 입력합니다. (기본 'HttpTrigger' 이름은 트리거와 동일하므로 사용하지 마십시오. 혼동될 수 있습니다.)

    ![함수 이름 입력](media/functions-extension/create-function-name.png)

1. **권한 수준** 프롬프트에서 **익명**을 선택합니다.

    ![함수 이름 입력](media/functions-extension/create-function-anonymous-auth.png)

1. 몇 분 후 VS Code가 프로젝트 생성을 완료합니다. 함수의 이름이 *HttpExample*인 폴더가 있고, 그 안에는 세 개의 파일이 있습니다.

    | 파일 이름 | Description |
    | --- | --- |
    | *index.js* |  HTTP 요청에 응답하는 소스 코드입니다. |
    | *function.json* | HTTP 트리거의 [바인딩 구성](/azure/azure-functions/functions-triggers-bindings)입니다. |
    | *sample.dat* | 폴더에 다른 파일이 있을 수 있다는 것을 보여주는 자리 표시자 데이터 파일입니다. 원하는 경우 이 자습서에서 사용되지 않으므로 이 파일을 삭제해도 됩니다. |

    ![함수 앱 만들기 결과](media/functions-extension/create-function-app-results.png)

> [!div class="nextstepaction"]
> [함수 앱을 만들었습니다.](tutorial-vscode-serverless-node-03.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=create-app)
