---
title: Visual Studio Code에서 Azure Functions 애플리케이션 배포
description: 자습서 4부, 클라우드에 Functions 앱 배포
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 690477392fffda4cd94d7271b061c195ccceb42b
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74467104"
---
# <a name="deploy-the-functions-app"></a>Functions 앱 배포

[이전 단계: 로컬에서 함수 테스트](tutorial-vscode-serverless-node-03.md)

1. VS Code에서 Azure 로고를 선택하여 **Azure Explorer**를 연 다음, **Functions**에서 파란색 위쪽 화살표를 선택하여 앱을 배포합니다.

    ![Azure Functions에 배포 명령](media/functions-extension/deploy-app.png)

    또는 **명령 팔레트**(**F1**)를 열고 'deploy to function app'을 입력하고 **Azure Functions: 함수 앱에 배포** 명령을 선택합니다.

1. **Select Function App in Azure**(Azure에서 함수 앱 선택) 프롬프트에서 **Create new Function app in Azure**(Azure에서 새 함수 앱 만들기)를 선택합니다.

1. 다음 프롬프트에서 함수 앱에 대해 전역적으로 고유한 이름을 입력하고 **Enter** 키를 누릅니다. 함수 앱 이름으로 유효한 문자는 'a-z', '0-9' 및 '-'입니다.

1. 다음 프롬프트에서 가까운 Azure [지역](https://azure.microsoft.com/regions/)을 선택합니다.

1. **Azure Functions**의 VS Code **출력** 패널에 진행 상황이 표시됩니다.

    ![배포 진행 상황을 보여주는 VS Code 출력 패널](media/functions-extension/deploy-progress.png)

1. 배포가 완료되면 **Azure Functions** 탐색기로 이동하여 Azure 구독의 노드를 확장하고 함수 앱의 노드를 확장한 다음, **함수(읽기 전용)** 를 확장합니다. 함수 이름을 마우스 오른쪽 단추로 클릭하고 **Copy Function Url**(함수 URL 복사)을 선택합니다.

    ![함수 URL 복사 명령](media/functions-extension/copy-function-url-command.png)

1. URL을 브라우저에 붙여넣고 `?name=<yourname>` 인수를 추가합니다. 그러면 클라우드에서 실행되는 함수가 브라우저에 표시됩니다.

    ![클라우드에서 실행되는 함수](media/functions-extension/remote-test-browser.png)

1. 원하는 경우 *index.js*에서 함수 코드를 변경하거나 다른 트리거와 함께 함수를 더 추가합니다. 로컬에서 테스트한 후 이전 단계처럼 코드를 다시 배포하여 클라우드에서 변경 사항을 테스트합니다.

    > [!TIP]
    > 배포할 때, 전체 Functions 애플리케이션이 배포되기 때문에 모든 개별 Functions에 대한 변경 사항이 한 번에 배포됩니다.

> [!div class="nextstepaction"]
> [함수 앱을 배포했습니다.](tutorial-vscode-serverless-node-05.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=deploy-app)
