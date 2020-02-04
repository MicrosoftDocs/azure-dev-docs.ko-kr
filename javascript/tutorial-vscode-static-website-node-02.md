---
title: Visual Studio Code에서 정적 Node.js 앱 만들기
description: 자습서 2부, 샘플 앱 만들기
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: buhollan
ms.openlocfilehash: b236f64f04be0c0b8faa74eb67ce22f96ec595aa
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76825847"
---
# <a name="create-the-app"></a>앱 만들기

[이전 단계: 소개 및 필수 구성 요소](tutorial-vscode-static-website-node-01.md)

이 단계에서는 [Angular](https://cli.angular.io/), [React](https://github.com/facebook/create-react-app) 또는 [Vue](https://cli.vuejs.org/)에 대한 CLI(명령줄 인터페이스)를 사용하여 Azure에 배포할 수 있는 간단한 앱을 만듭니다. 정적 파일 세트를 생성하는 다른 JavaScript 프레임워크 또는 HTML, CSS 또는 JavaScript 파일을 포함하는 폴더를 번갈아 사용할 수 있습니다. 배포 준비가 완료된 앱이 이미 있으면 [Azure Storage 계정 만들기](tutorial-vscode-static-website-node-03.md)로 건너뛸 수 있습니다.

# <a name="angulartabangular"></a>[Angular](#tab/angular)

1. CLI를 사용하고 다음 명령을 실행하여 "my-static-app"이라는 새 앱을 스캐폴드 아웃합니다.

    ```bash
    npx @angular/cli new my-static-app
    ```

    CLI가 구성에 대해 질문하면 Enter를 눌러 기본 옵션을 선택합니다.

1. 새 폴더로 전환하고 `npm run build`를 실행하여 애플리케이션을 빌드합니다.

    ```bash
    cd my-static-app
    npm run build
    ```

1. 이제 _my-static-app_ 폴더에 _dist_ 폴더가 있어야 합니다. _dist_ 폴더 안에는 프로젝트와 이름이 같은 폴더인 _my-static-app_이 있습니다. _build/my-static-app_ 폴더에는 Azure Storage에 배포하는 HTML, CSS 및 JavaScript 파일이 포함되어 있습니다.

1. 다음 명령을 사용하여 앱을 실행합니다.

    ```bash
    npm start
    ```

1. 브라우저로 [http://localhost:4200](http://localhost:4200)을 열어서 앱이 실행 중인지 확인합니다.

    ![실행 중인 샘플 Angular 앱](media/static-website/local-app-angular.png)

1. 터미널 또는 명령 프롬프트에서 **Ctrl**+**C**를 눌러서 서버를 중지합니다.

# <a name="reacttabreact"></a>[React](#tab/react)

1. CLI를 사용하고 다음 명령을 실행하여 "my-static-app"이라는 새 앱을 스캐폴드 아웃합니다.

    ```bash
    npx create-react-app my-static-app
    ```

1. 새 폴더로 전환하고 `npm run build`를 실행하여 애플리케이션을 빌드합니다.

    ```bash
    cd my-static-app
    npm run build
    ```

1. 이제 _my-static-app_ 폴더에 _build_ 폴더가 있어야 합니다. _build_ 폴더에는 Azure Storage에 배포하는 HTML, CSS 및 JavaScript 파일이 포함되어 있습니다.

1. 다음 명령을 사용하여 앱을 실행합니다.

    ```bash
    npm start
    ```

1. 브라우저로 [http://localhost:3000](http://localhost:3000)을 열어서 앱이 실행 중인지 확인합니다.

    ![실행 중인 샘플 React 앱](media/static-website/local-app-react.png)

1. 터미널 또는 명령 프롬프트에서 **Ctrl**+**C**를 눌러서 서버를 중지합니다.

# <a name="vuetabvue"></a>[Vue](#tab/vue)

1. CLI를 사용하고 다음 명령을 실행하여 "my-static-app"이라는 새 앱을 스캐폴드 아웃합니다.

    ```bash
    npx @vue/cli create my-static-app
    ```

CLI가 구성에 대해 질문하면 Enter를 눌러 기본 옵션을 선택합니다.

1. 새 폴더로 전환하고 `npm run build`를 실행하여 애플리케이션을 빌드합니다.

    ```bash
    cd my-static-app
    npm run build
    ```

1. 이제 _my-static-app_ 폴더에 _dist_ 폴더가 있어야 합니다. _dist_ 폴더에는 Azure Storage에 배포하는 HTML, CSS 및 JavaScript 파일이 포함되어 있습니다.

1. 다음 명령을 사용하여 앱을 실행합니다.

     ```bash
     npm run serve
     ```

1. 브라우저로 [http://localhost:8080](http://localhost:8080)을 열어서 앱이 실행 중인지 확인합니다.

    ![실행 중인 샘플 Vue 앱](media/static-website/local-app-vue.png)

1. 터미널 또는 명령 프롬프트에서 **Ctrl**+**C**를 눌러서 서버를 중지합니다.

---

> [!div class="nextstepaction"]
> [앱을 만들었습니다.](tutorial-vscode-static-website-node-03.md) [이슈가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-app)
