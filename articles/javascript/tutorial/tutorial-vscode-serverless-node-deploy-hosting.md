---
title: Azure 클라우드에 함수 앱 배포
description: Azure Functions용 Visual Studio Code 확장을 사용하여 Azure 클라우드에 함수 앱을 배포합니다. 브라우저에서 함수 앱을 공개적으로 사용할 수 있는지 확인합니다.
ms.topic: tutorial
ms.date: 09/23/2019
ms.custom: devx-track-js, contperf-fy21q2
ms.openlocfilehash: bbe5dbf30da67c66e3cb150aa590b657ccc4ea10
ms.sourcegitcommit: c8330128d5d6a71859933a890ecdf047cb950996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97522223"
---
# <a name="4-deploy-the-functions-app-to-azure-cloud"></a>4. Azure 클라우드에 함수 앱 배포

[이전 단계: 로컬에서 함수 테스트](tutorial-vscode-serverless-node-test-local.md)

이 단계에서는 Azure Functions용 Visual Studio Code 확장을 사용하여 Azure 클라우드에 함수 앱을 배포합니다. 브라우저에서 함수 앱을 공개적으로 사용할 수 있는지 확인합니다. 

## <a name="use-visual-studio-code-extension-to-deploy-to-hosting-environment"></a>Visual Studio Code 확장을 사용하여 호스팅 환경에 배포

1. VS Code에서 Azure 로고를 선택하여 **Azure Explorer** 를 연 다음, **Functions** 에서 파란색 위쪽 화살표를 선택하여 앱을 배포합니다.

    ![Azure Functions에 배포 명령](../media/functions-extension/deploy-app.png)

    또는 **명령 팔레트**(**F1**)를 열고 'deploy to function app'을 입력하고 **Azure Functions: 함수 앱에 배포** 명령을 선택합니다.

1. **Select Function App in Azure**(Azure에서 함수 앱 선택) 프롬프트에서 **Create new Function app in Azure**(Azure에서 새 함수 앱 만들기)를 선택합니다.

1. 다음 프롬프트에서 함수 앱에 대해 전역적으로 고유한 이름을 입력하고 **Enter** 키를 누릅니다. 함수 앱 이름으로 유효한 문자는 'a-z', '0-9' 및 '-'입니다.

1. Node.js 버전/런타임을 선택합니다.

    ![Node.js 버전/런타임을 보여주는 VS Code 출력 패널](../media/functions-extension/nodejs-runtime-version.png)

1. 다음 프롬프트에서 가까운 Azure [지역](https://azure.microsoft.com/regions/)을 선택합니다.

1. **Azure Functions** 의 VS Code **출력** 패널에 진행 상황이 표시됩니다.

    ![배포 진행 상황을 보여주는 VS Code 출력 패널](../media/functions-extension/deploy-progress.png)

## <a name="verify-functions-app-is-publicly-available-with-browser"></a>브라우저에서 함수 앱을 공개적으로 사용할 수 있는지 확인

1. 배포가 완료되면 **Azure Functions** 탐색기로 이동하여 Azure 구독의 노드를 확장하고 함수 앱의 노드를 확장한 다음, **함수(읽기 전용)** 를 확장합니다. 함수 이름을 마우스 오른쪽 단추로 클릭하고 **Copy Function Url**(함수 URL 복사)을 선택합니다.

    ![함수 URL 복사 명령](../media/functions-extension/copy-function-url-command.png)

1. URL을 브라우저에 붙여넣고 `?name=<yourname>` 인수를 추가합니다. 그러면 클라우드에서 실행되는 함수가 브라우저에 표시됩니다.

    ![클라우드에서 실행되는 함수](../media/functions-extension/remote-test-browser.png)

1. 원하는 경우 *index.js* 에서 함수 코드를 변경하거나 다른 트리거와 함께 함수를 더 추가합니다. 로컬에서 테스트한 후 이전 단계처럼 코드를 다시 배포하여 클라우드에서 변경 사항을 테스트합니다.

    > [!TIP]
    > 배포할 때 전체 Functions 애플리케이션이 배포되기 때문에 모든 개별 Functions에 대한 변경 사항이 한 번에 배포됩니다.

> [!div class="nextstepaction"]
> [함수 앱을 배포했습니다.](tutorial-vscode-serverless-node-remove-resource.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=deploy-app)
