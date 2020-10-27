---
title: tutorial-azure-web-app-mongodb-02.md 포함 파일
description: tutorial-azure-web-app-mongodb-02.md 포함 파일
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: fafb2b452e9e1bfab34d9c2d9d46adaabf4fd022
ms.sourcegitcommit: 8a2a7df568c69fff2080ffab248409040efda1ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183917"
---
자습서의 이 섹션에서는 샘플 애플리케이션을 로컬 컴퓨터에 다운로드하고 Visual Studio Code 터미널에서 실행합니다. 그런 다음, 브라우저에서 로컬로 실행되는 앱을 볼 수 있습니다. 

## <a name="download-and-run-the-initial-expressjs-app"></a>초기 Express.js 앱 다운로드 및 실행

초기 Express.js 웹앱이 시작점으로 제공됩니다. 이 절차에서는 앱을 다운로드하고, 종속성을 설치하고, 앱을 실행합니다. 사용할 수 있는 경우 초기 앱에서 데이터베이스에 대한 연결을 시도합니다. 사용할 수 없는 경우에도 웹 사이트에서 요청에 성공적으로 응답합니다. 

1. [압축된 GitHub 리포지토리를 로컬 컴퓨터에 다운로드](https://github.com/Azure-Samples/js-e2e-express-mongo.git)한 다음, 폴더를 펼칩니다. 
1. Visual Studio Code에서 폴더를 엽니다. 마우스 오른쪽 단추로 폴더를 클릭하여 **Code로 열기** 를 선택하거나 폴더 내에서 해당하는 CLI를 사용할 수 있습니다.

    ```console
    code .
    ```

1. Visual Studio Code에서 터미널 창을 열고, 다음 명령을 실행하여 샘플의 종속성을 설치합니다.

    ```javascript
    npm install
    ```

1. 동일한 터미널 창에서 명령을 실행하여 웹앱을 실행합니다.

    ```javascript
    npm start
    ```

1. 웹 브라우저를 열고, 다음 URL을 사용하여 로컬 컴퓨터에서 웹앱을 확인합니다.

    ```url
    http://localhost:8080/
    ```

    데이터베이스가 없다는 텍스트가 포함된 간단한 웹앱이 브라우저에 표시되면 자습서의 이 섹션을 성공적으로 수행한 것입니다.

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/nodejs-app-connected-mongodb-form.png" alt-text="MongoDB 데이터베이스에 연결된 간단한 Node.js 앱":::