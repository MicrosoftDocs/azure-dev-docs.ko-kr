---
title: Azure CLI를 사용하여 Azure에 배포할 Node.js 앱 만들기
description: 자습서 2부, 앱 코드 만들기
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: dba089c58cf6413263348b7bdeb975852c2e6a2f
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74467185"
---
# <a name="create-the-app-code-using-express"></a>Express를 사용하여 앱 코드 만들기

[이전 단계: 소개 및 필수 구성 요소](tutorial-vscode-azure-cli-node-01.md)

이 단계에서는 [Express Generator](https://expressjs.com/en/starter/generator.html)를 사용하여 [Express](https://www.expressjs.com)로 간단한 Node.js 앱을 만듭니다.

1. 다음 명령을 사용하여 Express Generator를 실행하고 "myExpressApp"이라는 새 Express 앱을 스캐폴드합니다. (`--view pug --git` 매개 변수는 [pug](https://pugjs.org/api/getting-started.html) 템플릿 엔진(이전 이름: Jade)을 사용하고 *.gitignore* 파일을 만들도록 생성기에 지시합니다.)

    ```bash
    npx express-generator myExpressApp --view pug –git
    ```

1. 다음 명령을 실행하여 앱 폴더로 이동하여 앱의 종속성을 설치합니다.

    ```bash
    cd myExpressApp
    npm install
    ```

1. 다음 명령을 실행하여 앱 서버를 시작합니다.

    ```bash
    npm start
    ```

1. 브라우저에서 [http://localhost:3000](http://localhost:3000)을 열면 실행 중인 앱이 표시됩니다.

    ![로컬에서 Express 앱 실행](media/azure-cli/local-app.png)

1. 앱 테스트가 완료되면 `npm start`를 실행한 터미널에서 **Ctrl**+**C**를 눌러서 서버를 중지합니다.

> [!div class="nextstepaction"]
> [앱을 만들었습니다.](tutorial-vscode-azure-cli-node-03.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=express)
