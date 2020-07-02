---
title: Visual Studio Code에서 정적 Node.js 웹 사이트에 변경 내용 배포
description: 자습서 5부, 변경 및 재배포
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: buhollan
ms.openlocfilehash: d0f6be1d57c9d378a4428a5a05da0428314f76c0
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85792703"
---
# <a name="make-changes-and-redeploy"></a>변경 및 재배포

[이전 단계: Azure Storage에 배포](tutorial-vscode-static-website-node-04.md)

이 단계에서는 앱의 소스 코드를 간단히 변경하고 사이트를 다시 배포하여 엔드투엔드 배포 워크플로를 경험합니다.

# <a name="angular"></a>[Angular](#tab/angular)

1. Visual Studio Code에서 _src/app/app.component.html_ 파일을 열고 305번 줄을 다음과 일치하도록 변경합니다.

    ```html
    <span>Welcome To Azure</span>
    ```

1. 터미널 또는 명령 프롬프트에서 `npm run build`를 실행합니다.

1. VS Code에서 업데이트한 _dist/my-static-site_ 폴더를 마우스 오른쪽 단추로 클릭하고 **Deploy to Static Website**(정적 웹 사이트에 배포)를 다시 선택합니다. 스토리지 계정을 선택하고 변경 내용을 배포할 것인지 확인합니다. (Azure 확장은 캐싱 문제를 방지하기 위해 변경 내용을 배포하기 전에 이전 파일을 자동으로 삭제합니다.)

1. 배포가 완료되면 브라우저에서 사이트를 새로 고치고 변경 내용을 관찰합니다.

    ![재배포 후 앱의 변경 내용](media/static-website/updated-azure-app-angular.png)

# <a name="react"></a>[React](#tab/react)

1. Visual Studio Code에서 _src/app.js_ 파일을 열고 11번 줄을 다음과 일치하도록 변경합니다.

    ```html
    <h1 className="App-title">Welcome to Azure!</h1>
    ```

1. 터미널 또는 명령 프롬프트에서 `npm run build`를 실행합니다.

1. VS Code에서 업데이트한 _build_ 폴더를 마우스 오른쪽 단추로 클릭하고 **Deploy to Static Website**(정적 웹 사이트에 배포)를 다시 선택합니다. 스토리지 계정을 선택하고 변경 내용을 배포할 것인지 확인합니다. (Azure 확장은 캐싱 문제를 방지하기 위해 변경 내용을 배포하기 전에 이전 파일을 자동으로 삭제합니다.)

1. 배포가 완료되면 브라우저에서 사이트를 새로 고치고 변경 내용을 관찰합니다.

    ![재배포 후 앱의 변경 내용](media/static-website/updated-azure-app-react.png)

# <a name="vue"></a>[Vue](#tab/vue)

1. Visual Studio Code에서 _src/App.vue_ 파일을 열고 11번 줄을 다음과 일치하도록 변경합니다.

    ```html
    <HelloWorld msg="Welcome to Azure!" />
    ```

1. 터미널 또는 명령 프롬프트에서 `npm run build`를 실행합니다.

1. VS Code에서 업데이트한 _dist_ 폴더를 마우스 오른쪽 단추로 클릭하고 **Deploy to Static Website**(정적 웹 사이트에 배포)를 다시 선택합니다. 스토리지 계정을 선택하고 변경 내용을 배포할 것인지 확인합니다. (Azure 확장은 캐싱 문제를 방지하기 위해 변경 내용을 배포하기 전에 이전 파일을 자동으로 삭제합니다.)

1. 배포가 완료되면 브라우저에서 사이트를 새로 고치고 변경 내용을 관찰합니다.

    ![재배포 후 앱의 변경 내용](media/static-website/updated-azure-app-vue.png)

# <a name="svelte"></a>[Svelte](#tab/svelte)

1. Visual Studio Code에서 _src/main.js_ 파일을 열고 6번 줄을 다음과 일치하도록 변경합니다.

    ```js
    import App from './App.svelte';

    const app = new App({
        target: document.body,
        props: {
            name: 'Welcome to Azure!'
        }
    });

    export default app;
    ```

2. 이제 _src/App.svelte_ 파일을 열고 6번 줄을 다음과 일치하도록 변경합니다.

    ```html
    <h1>{name}</h1>
    ```

1. 터미널 또는 명령 프롬프트에서 `npm run build`를 실행합니다.

1. VS Code에서, 업데이트한 _public_ 폴더를 마우스 오른쪽 단추로 클릭하고 **Deploy to Static Website**(정적 웹 사이트에 배포)를 다시 선택합니다. 스토리지 계정을 선택하고 변경 내용을 배포할 것인지 확인합니다. (Azure 확장은 캐싱 문제를 방지하기 위해 변경 내용을 배포하기 전에 이전 파일을 자동으로 삭제합니다.)

1. 배포가 완료되면 브라우저에서 사이트를 새로 고치고 변경 내용을 관찰합니다.

    ![재배포 후 앱의 변경 내용](media/static-website/updated-azure-app-svelte.png)

---

> [!div class="nextstepaction"]
> [변경 내용을 배포했습니다.](tutorial-vscode-static-website-node-06.md)[문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=code-change)
