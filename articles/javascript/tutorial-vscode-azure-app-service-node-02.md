---
title: Visual Studio Code에서 Node.js Azure App Service 만들기
description: 'Node.js 자습서 2부: Node.js 앱을 만들고 로컬에서 실행'
ms.topic: tutorial
ms.date: 03/04/2020
ms.custom: devx-track-js
ms.openlocfilehash: 7d627d97987ed41f90972362ab38d436a6157d33
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91365246"
---
# <a name="create-and-run-a-local-nodejs-app"></a>로컬 Node.js 앱 만들기 및 실행

[이전 단계: 소개 및 필수 구성 요소](tutorial-vscode-azure-app-service-node-01.md)

이 단계에서는 Express 애플리케이션 생성기를 사용하여 간단한 Node.js 앱을 만듭니다. 그런 다음, 애플리케이션을 로컬로 실행합니다.

1. 터미널 또는 명령 프롬프트에서 앱 폴더를 만들려는 위치로 이동합니다.

1. 다음 명령을 실행하여 Express Generator를 통해 *expressApp1*이라는 새 Express 앱을 만듭니다. (`--view pug --git` 매개 변수는 [pug](https://pugjs.org/api/getting-started.html) 템플릿 엔진(이전 이름: Jade)을 사용하고 *.gitignore* 파일을 만들도록 생성기에 지시합니다.)

    ```bash
    npx express-generator expressApp1 --view pug -–git
    ```

1. 앱 폴더로 이동합니다.

    ```bash
    cd expressApp1
    ```

1. 애플리케이션의 종속성을 설치합니다.

    ```bash
    npm install
    ```

1. 서버를 시작합니다.

    ```bash
    npm start
    ```

1. 브라우저에서 `http://localhost:3000`을 열어 앱을 테스트합니다. 사이트가 다음과 같이 표시됩니다.

    ![Express 애플리케이션 실행](media/deploy-azure/express.png)

1. 터미널에서 **Ctrl**+**C**를 눌러 서버를 중지합니다.

> [!div class="nextstepaction"]
> [Node.js 앱을 만들었습니다.](tutorial-vscode-azure-app-service-node-03.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=create-app)
