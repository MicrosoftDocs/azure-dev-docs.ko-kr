---
title: Visual Studio Code에서 Azure Functions 애플리케이션을 로컬에서 실행
description: '서버리스 자습서 3부: 앱을 로컬에서 실행하여 테스트'
ms.topic: tutorial
ms.date: 09/23/2019
ms.custom: devx-track-js
ms.openlocfilehash: 94b646d29b7459b2a74a1dbdaafde4a98a348f6b
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91364586"
---
# <a name="test-the-function-locally"></a>로컬에서 함수 테스트

[이전 단계: Functions 앱 만들기](tutorial-vscode-serverless-node-02.md)

이 단계에서는 Azure Functions 프로젝트를 Azure에 배포하기 전에 로컬에서 실행하여 테스트합니다.

Functions 앱을 만들 때, Azure Functions 확장 프로그램이 VS Code 시작 구성을 프로젝트에 자동으로 추가하며, 이 구성은 *.vscode/launch.json* 파일에 있습니다. 이 구성은 Azure에서 실행되는 것과 동일한 런타임을 사용하기 때문에 클라우드에 배포하기 전에 소스 코드가 작동하는지 확인할 수 있습니다.

1. Visual Studio Code에서 **F5**를 눌러서(또는 **디버그** > **디버깅 시작** 메뉴 명령을 사용하여) 디버거를 시작하고 Azure Functions 호스트에 연결합니다. (이 명령은 Azure Functions에서 생성된 단일 디버그 구성을 자동으로 사용합니다.)

1. Functions Core 도구의 출력이 VS Code **터미널** 패널에 나타납니다. 호스트가 시작되면 출력에 표시된 로컬 URL을 **Alt**키를 누른 채로 클릭하여 브라우저를 열고 함수를 실행합니다.

    ![로컬에서 디버깅할 때 VS Code 터미널 패널에 표시되는 출력](media/functions-extension/local-test-output.png)

1. 기본 HTTP 트리거 템플릿으로 생성된 코드는 `name` 쿼리 매개 변수를 구문 분석하여 응답을 사용자 지정합니다. 브라우저에서 응답 출력이 제대로 표시되도록 브라우저의 URL에 `?name=<yourname>`을 추가합니다.

    ![URL 매개 변수를 구문 분석하는 HTTP 트리거 함수](media/functions-extension/local-test-browser.png)

1. 함수를 로컬에서 실행하면 코드의 다양한 부분에 중단점을 설정할 수 있습니다. (VS Code의 중단점 및 디버깅에 대해 자세히 알아보려면, [디버깅](https://code.visualstudio.com/docs/editor/debugging)을 참조하세요.) *index.js*를 연 다음, 편집기 창에서 11번 줄 왼쪽의 여백을 클릭합니다. 작은 빨간점이 중단점을 나타냅니다. 이제 브라우저의 URL에서 `?name=` 인수를 제거합니다. 브라우저가 해당 요청을 수행하면 VS Code는 해당 중단점에서 함수 코드를 중지합니다.

    ![중단점에서 중지한 VS Code](media/functions-extension/debugging-breakpoint.png)

> [!Note]
>
> 이 프로세스에서 실행 정책 오류가 발생하면 npm을 사용하여 `azure-functions-core-tools@3`을 제거한 다음, 관리자 권한 터미널에서 Chocolatey를 사용하여 패키지를 다시 설치해보십시오.

> [!div class="nextstepaction"]
> [함수 앱을 로컬에서 실행했습니다.](tutorial-vscode-serverless-node-04.md)[문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=run-app)
