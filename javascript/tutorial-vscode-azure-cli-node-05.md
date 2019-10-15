---
title: Azure App Service에서 로그 스트리밍
description: 자습서 5부, 로그 보기
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: kraigb
ms.openlocfilehash: ce112d535d1f89a7e153f80b9fb80032a977b8d8
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686144"
---
# <a name="stream-logs-from-app-service"></a>App Service에서 로그 스트리밍

[이전 단계: 앱 배포](tutorial-vscode-azure-cli-node-04.md)

이 단계에서는 실행중인 App Service의 로그를 보거나 "추적"합니다. 사이트 코드에서 `console.log`에 대한 모든 호출은 터미널에 표시됩니다.

1. 다음 명령을 실행하여 `<your_app_name>`을 App Service 이름으로 대체하여 로깅을 시작합니다.

    ```bash
    az webapp log tail --name <your_app_name>
    ```

1. 몇 초 후에 로그 스트리밍 서비스에 연결되었다는 메시지가 나타납니다.

    ```bash
    2019-09-25T13:39:23  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours. Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    ```

1. 브라우저에서 페이지를 몇 번 새로 고치면 출력이 추가로 생성됩니다.

    ```bash
    GET / 304 2.327 ms - -
    GET / 304 0.957 ms - -
    GET / 304 2.435 ms - -
    ```

1. **Ctrl**+**C**를 누르면, 로깅 세션이 종료됩니다.

> [!div class="nextstepaction"]
> [로그가 보입니다.](tutorial-vscode-azure-cli-node-06.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=tailing-logs)