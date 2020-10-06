---
title: Visual Studio Code에서 정적 Node.js 앱 만들기
description: '정적 웹앱 자습서 2부: 샘플 앱 만들기'
ms.topic: tutorial
ms.date: 09/24/2019
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: a46fd8c35cf76de65e60a175fb3ccd70767a5dc4
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91364636"
---
# <a name="create-the-app"></a>앱 만들기

[이전 단계: 소개 및 필수 구성 요소](tutorial-vscode-static-website-node-01.md)

이 단계에서는 [Angular](https://cli.angular.io/), [React](https://github.com/facebook/create-react-app), [Vue](https://cli.vuejs.org/) 또는 [Svelte](https://github.com/sveltejs/template)에 대한 CLI(명령줄 인터페이스)를 사용하여 Azure에 배포할 수 있는 간단한 앱을 만듭니다. 정적 파일 세트를 생성하는 다른 JavaScript 프레임워크 또는 HTML, CSS 또는 JavaScript 파일을 포함하는 폴더를 번갈아 사용할 수 있습니다. 배포 준비가 완료된 앱이 이미 있으면 [Azure Storage 계정 만들기](tutorial-vscode-static-website-node-03.md)로 건너뛸 수 있습니다.

# <a name="angular"></a>[Angular](#tab/angular)

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

1. 브라우저에서 `http://localhost:4200`을 열어 앱이 실행되고 있는지 확인합니다.

    ![실행 중인 샘플 Angular 앱](media/static-website/local-app-angular.png)

1. 터미널 또는 명령 프롬프트에서 **Ctrl**+**C**를 눌러서 서버를 중지합니다.

# <a name="react"></a>[React](#tab/react)

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

1. 브라우저에서 `http://localhost:3000`을 열어 앱이 실행되고 있는지 확인합니다.

    ![실행 중인 샘플 React 앱](media/static-website/local-app-react.png)

1. 터미널 또는 명령 프롬프트에서 **Ctrl**+**C**를 눌러서 서버를 중지합니다.

# <a name="vue"></a>[Vue](#tab/vue)

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

1. 브라우저에서 `http://localhost:8080`을 열어 앱이 실행되고 있는지 확인합니다.

    ![실행 중인 샘플 Vue 앱](media/static-website/local-app-vue.png)

1. 터미널 또는 명령 프롬프트에서 **Ctrl**+**C**를 눌러서 서버를 중지합니다.

# <a name="svelte"></a>[Svelte](#tab/svelte)

1. CLI를 사용하고 다음 명령을 실행하여 "my-static-app"이라는 새 앱을 스캐폴드 아웃합니다.

    ```bash
    npx degit sveltejs/template my-static-app
    ```

1. 그런 다음, 새 폴더로 이동하여 다음과 같이 `npm install` 명령을 실행합니다.

    ```bash
    cd my-static-app
    npm install
    ```

1. `npm run build` 명령을 실행하는 애플리케이션을 빌드해 보겠습니다.

    ```bash
    npm run build
    ```

1. 이제 _public_ 폴더 안에 _build_ 폴더가 생겼습니다. _build_ 폴더에는 Azure Storage에 배포하는 HTML, CSS 및 JavaScript 파일이 포함되어 있습니다.

1. 다음 명령을 사용하여 앱을 실행합니다.

     ```bash
     npm run dev
     ```

1. 브라우저에서 `http://localhost:5000`을 열어 앱이 실행되고 있는지 확인합니다.

    ![실행 중인 샘플 svelte 앱](media/static-website/local-app-svelte.png)

1. 터미널 또는 명령 프롬프트에서 **Ctrl**+**C**를 눌러서 서버를 중지합니다.

---

> [!div class="nextstepaction"]
> [앱을 만들었습니다.](tutorial-vscode-static-website-node-03.md) [이슈가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-app)
