---
title: Azure App Service에서 Visual Studio Code로 로그 스트리밍
description: 'Node.js 자습서 4부: 로그 보기 또는 추적'
ms.topic: tutorial
ms.date: 03/04/2020
ms.custom: devx-track-js
ms.openlocfilehash: eed2663f32571dc40fb402553424c1303d43f7b9
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91365236"
---
# <a name="stream-logs-from-azure-app-service"></a>Azure App Service에서 로그 스트리밍

[이전 단계: 웹 사이트 배포](tutorial-vscode-azure-app-service-node-03.md)

이 단계에서는 실행 중인 앱에서 `console.log`에 대한 호출을 통해 생성한 출력을 보거나 "추적"하는 방법을 알아봅니다. 이러한 출력은 Visual Studio Code의 **출력** 창에 나타납니다.

1. **Azure App Service** 탐색기에서 앱 노드를 마우스 오른쪽 단추로 클릭하고, **Start Streaming Logs**(로그 스트리밍 시작)를 선택합니다.

    ![스트리밍 로그 보기](media/deploy-azure/start-streaming-logs.png)

1. 메시지가 표시되면 로깅을 사용하도록 설정하고 애플리케이션을 다시 시작합니다.

    ![로깅을 사용하도록 설정하고 다시 시작하라는 메시지](media/deploy-azure/enable-restart.png)

1. 앱이 다시 시작되면 VS Code **출력** 창이 열리고 출력을 표시하는 로그 스트림에 연결됩니다.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

1. 브라우저에서 페이지를 몇 번 새로 고치면 로그 출력이 추가로 표시됩니다.

> [!div class="nextstepaction"]
> [로그가 보입니다.](tutorial-vscode-azure-app-service-node-05.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=tailing-logs)
