---
author: burkeholland
ms.service: app-service
ms.topic: include
ms.date: 03/31/2020
ms.author: buhollan
ms.custom: devx-track-javascript
ms.openlocfilehash: 9db8df165bd4d0f42694ff1e752878c8f055bce8
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88218416"
---
1. 터미널 명령 프롬프트에서 앱 폴더를 만들 위치로 이동합니다.

1. 다음 명령을 실행하여 Express 생성기를 사용하여 `myexpressapp`이라는 새 Express 앱을 만듭니다. `--git --view pug` 매개 변수는 .gitignore 파일을 만들고 [Pug](https://pugjs.org/api/getting-started.html) 템플릿 엔진(이전 이름: Jade)을 사용하도록 생성기에 지시합니다.

    ```bash
    npx express-generator myexpressapp --git --view pug
    ```

1. 앱 폴더로 이동합니다.

    ```bash
    cd myexpressapp
    ```

1. 애플리케이션의 종속성을 설치합니다.

    ```bash
    npm install
    ```

1. 서버를 시작합니다.

    ```bash
    npm start
    ```

1. 브라우저를 열고 `http://localhost:3000`으로 이동하여 앱을 테스트합니다. 사이트가 다음과 같은 모양으로 표시됩니다.

    ![Express 애플리케이션 실행](../media/deploy-azure/express.png)

1. 터미널에서 **Ctrl**+**C**를 선택하여 서버를 중지합니다.
 
