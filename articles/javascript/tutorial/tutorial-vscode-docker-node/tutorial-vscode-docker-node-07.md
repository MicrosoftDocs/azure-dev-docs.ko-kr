---
title: Visual Studio Code에서 컨테이너화된 Node.js 앱의 로그 스트리밍
description: 'Docker 자습서 7부: Visual Studio Code로 로그 스트리밍'
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: devx-track-js
ms.openlocfilehash: b3301a503715721fa40084c8288684f2e58152c0
ms.sourcegitcommit: f723980ade4cbc13548a5d8ac3f3fa681b8a2dbd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609294"
---
# <a name="stream-logs-into-visual-studio-code"></a>Visual Studio Code로 로그 스트리밍

[이전 단계: 변경 및 재배포](tutorial-vscode-docker-node-06.md)

이 단계에서는 `console.log`에 대한 호출을 통해 실행 중인 웹 사이트가 생성한 출력을 보거나 "추적"하는 방법을 알아봅니다. 이러한 출력은 Visual Studio Code의 **출력** 창에 나타납니다.

1. **Azure App Service** 탐색기에서 앱 노드를 마우스 오른쪽 단추로 클릭하고, **Start Streaming Logs**(로그 스트리밍 시작)를 선택합니다.

    ![스트리밍 로그 보기](../../media/deploy-containers/stream-logs-command.png)

1. 메시지가 표시되면 로깅을 사용하도록 설정하고 애플리케이션을 다시 시작합니다.

    ![로깅을 사용하도록 설정하고 다시 시작하라는 메시지](../../media/deploy-azure/enable-restart.png)

1. 앱이 다시 시작되면 Visual Studio Code의 **출력** 패널이 `Starting Live Log Stream` 메시지로 시작되는 로그 스트림에 연결되어 열립니다.

> [!div class="nextstepaction"]
> [로그가 보입니다.](tutorial-vscode-docker-node-08.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-docker-extension&step=tailing-logs)
