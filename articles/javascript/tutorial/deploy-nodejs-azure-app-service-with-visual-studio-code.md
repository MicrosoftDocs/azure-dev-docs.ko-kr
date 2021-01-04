---
title: Visual Studio Code에서 Azure App Service에 Node.js 앱 배포
description: Visual Studio Code App Service 확장을 사용하여 Express.js Node.js 애플리케이션을 Azure App Service에 배포합니다.
ms.topic: tutorial
ms.date: 12/09/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9b6b91cffa50c3c1a2beb3dc2c800db20877b9af
ms.sourcegitcommit: f723980ade4cbc13548a5d8ac3f3fa681b8a2dbd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97601034"
---
# <a name="deploy-nodejs-to-azure-app-service-using-visual-studio-code"></a>Visual Studio Code를 사용하여 Azure App Services에 Node.js 배포

Visual Studio Code [App Service 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)을 사용하여 Node.js 애플리케이션을 Linux 또는 Windows의 Azure App Service에 배포합니다.

Git와 Azure App Service 확장을 사용하여 Node.js 앱을 Azure에 배포합니다. 이 목표를 달성하려면 다음을 수행합니다.

* Express.js 앱 만들기
* 로컬 Git 리포지토리 초기화
* 앱을 호스트하는 웹앱 리소스 만들기
* 리소스에 앱 배포
* 로컬에서 원격 로그 보기

## <a name="walkthrough-video"></a>연습 비디오

이 문서의 내용에 대한 전체 연습을 보려면 이 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Deploy-to-Azure-App-Service-using-Visual-Studio-Code/player]

## <a name="1-set-up-your-development-environment"></a>1. 개발 환경 설정

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-extension&mktingSource=vscode-tutorial-appservice-extension)
- [Visual Studio Code](https://code.visualstudio.com/)
- VS Code용 [Azure App Service 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)(VS Code 내에서 설치됨)
- [Node.js 12+ 및 npm](https://nodejs.org/en/download).

## <a name="2-sign-in-to-azure"></a>2. Azure에 로그인

[!INCLUDE [azure-sign-in](../includes/azure-sign-in.md)]

## <a name="3-create-a-local-expressjs-app"></a>3. 로컬 Express .js 앱 만들기

Express.js 애플리케이션 생성기를 사용하여 Node.js 앱을 만듭니다. 

1. 터미널 또는 명령 프롬프트에서 앱 폴더를 만들려는 위치로 이동합니다.

1. 다음 명령을 실행하여 Express Generator를 통해 *expressApp1* 이라는 새 Express 앱을 만듭니다. (`--view pug --git` 매개 변수는 [pug](https://pugjs.org/api/getting-started.html) 템플릿 엔진(이전 이름: Jade)을 사용하고 *.gitignore* 파일을 만들도록 생성기에 지시합니다.)

    ```bash
    npx express-generator expressApp1 -–git --view pug 
    ```

1. 앱 폴더로 이동합니다.

    ```bash
    cd expressApp1
    ```

1. 애플리케이션의 종속성을 설치합니다.

    ```bash
    npm install
    ```

## <a name="4-run-your-local-expressjs-app"></a>4. 로컬 Express .js 앱 실행

1. 서버를 시작합니다.

    ```bash
    npm start
    ```

1. 브라우저에서 `http://localhost:3000`을 열어 앱을 테스트합니다. 사이트가 다음과 같이 표시됩니다.

    ![Express 애플리케이션 실행](../media/deploy-azure/express.png)

1. 터미널에서 **Ctrl**+**C** 를 눌러 서버를 중지합니다.

## <a name="5-initialize-git-in-visual-studio-code-for-current-app"></a>5. Visual Studio Code에서 현재 앱의 Git 초기화

1. 터미널에서 *expressApp1* 폴더에 있는지 확인한 다음, 다음 명령을 사용하여 Visual Studio Code를 시작합니다.

    ```bash
    code .
    ```

1. Visual Studio Code에서 소스 제어 아이콘을 선택하여 **소스 제어** 탐색기를 연 다음, **리포지토리 초기화** 를 선택하여 로컬 Git 리포지토리를 초기화합니다.

    ![git 리포지토리 초기화](../media/deploy-azure/git-init.png)

1. 프롬프트에서 작업 영역 폴더에 대해 *expressApp1* 을 선택합니다.

1. 리포지토리가 초기화되면 "초기 커밋" 메시지를 입력하고, 확인 표시를 선택하여 원본 파일의 초기 커밋을 만듭니다.

    ![리포지토리에 대한 초기 커밋 완료](../media/deploy-azure/initial-commit.png)

## <a name="6-create-app-service-resource-in-visual-studio-code"></a>6. Visual Studio Code에서 App Service 리소스 만들기

1. 명령 팔레트(**Ctrl**+**Shift**+**P**)에서 "웹 만들기"를 입력하고, **Azure App Service: 새 웹앱 만들기...고급** 을 선택합니다. 고급 명령을 사용하면 Linux 기본값을 사용하는 대신 리소스 그룹, App Service 계획 및 운영 체제를 포함한 배포를 완전히 제어할 수 있습니다.

1. 프롬프트에 다음과 같이 응답합니다.

    - **구독** 계정을 선택합니다.
    - **전역적으로 고유한 이름을 입력하세요.** 에 대해 모든 Azure에서 고유한 이름을 입력합니다. 영숫자 문자('A-Z', 'a-z' 및 '0-9') 및 하이픈('-')만 사용합니다.
    - **새 리소스 그룹 만들기** 를 선택하고, `AppServiceTutorial-rg`와 같은 이름을 지정합니다.
    - 운영 체제(Windows 또는 Linux)를 선택합니다.
    - Linux만: Node.js 버전을 선택합니다. (Windows의 경우 나중에 앱 설정을 사용하여 버전을 설정합니다.)
    - **새 App Service 계획 만들기** 를 선택하고, `AppServiceTutorial-plan`과 같은 이름을 제공하고, **F1 체험** [가격 책정 계층](../core/what-is-azure-for-javascript-development.md#free-tier-resources)을 선택합니다.
    - Application Insights 리소스에 대해 **지금 건너뛰기** 를 선택합니다.
    - 가까운 위치를 선택합니다.

1. 잠시 후에 VS Code에서 만들기가 완료되었음을 알려줍니다. **X** 단추를 사용하여 알림을 닫습니다.

    ![웹앱 만들기 완료 시 알림](../media/deploy-azure/creation-complete.png)

1. 웹앱이 준비되면 다음으로 VS Code에서 로컬 Git 리포지토리의 코드를 배포하도록 지시합니다. Azure 아이콘을 선택하여 **Azure App Service** 탐색기를 열고, 구독 노드를 펼치고, 마우스 오른쪽 단추로 방금 만든 웹앱 이름을 클릭하고, **배포 원본 구성** 을 선택합니다.

    ![웹앱에서 배포 원본 명령 구성](../media/deploy-azure/configure-deployment-source.png)

1. 메시지가 표시되면 **LocalGit** 을 선택합니다.

1. Windows에서 App Service에 배포하는 경우 먼저 두 가지 설정을 만든 후에 배포해야 합니다.

    1. VS Code에서 새 앱 서비스의 노드를 펼치고, 마우스 오른쪽 단추로 **애플리케이션 설정** 을 클릭하고, **새 설정 추가** 를 선택합니다.

        ![앱 설정 추가 명령](../media/deploy-azure/add-setting.png)

    1. 설정 키로 `WEBSITE_NODE_DEFAULT_VERSION`을 입력하고, 설정 값으로 `10.15.2`를 입력합니다. 이러한 설정은 Node.js 버전을 설정합니다.
    1. 프로세스를 반복하여 값이 `1`인 `SCM_DO_BUILD_DURING_DEPLOYMENT`에 대한 키를 만듭니다. 이러한 설정은 배포 시 서버에서 `npm install`을 강제로 실행하도록 합니다.
    1. **애플리케이션 설정** 노드를 펼쳐서 설정이 올바른지 확인합니다.

1. 파란색 위쪽 화살표 아이콘을 선택하여 코드를 Azure에 배포합니다.

    ![웹앱에 배포 아이콘](../media/deploy-azure/deploy.png)

1. 프롬프트에서 *expressApp1* 폴더를 선택하고, **구독** 계정을 다시 선택한 다음, 이전에 만든 웹앱의 이름을 선택합니다.

1. Linux에 배포하는 경우 대상 서버에서 `npm install`을 실행하도록 구성을 업데이트하라는 메시지가 표시되면 **예** 를 선택합니다.

    ![대상 Linux 서버의 구성을 업데이트하도록 요구하는 메시지](../media/deploy-azure/server-build.png)

1. Linux 및 Windows의 경우 **항상 "nodejs-docs-hello-world" 작업 영역을 (앱 이름)에 배포합니다.** 라는 메시지가 표시되면 **예** 를 선택합니다. **예** 를 선택하면 VS Code에서 자동으로 후속 배포에서 동일한 App Service 웹앱을 대상으로 지정하도록 지시합니다.

1. 배포가 완료되면 프롬프트에서 **웹 사이트 찾아보기** 를 선택하여 새로 배포된 웹앱을 확인합니다. 브라우저에서 "Hello World!"가 표시됩니다.

1. (선택 사항): 코드 파일을 변경한 다음, 배포 단추를 다시 사용하여 웹앱을 업데이트할 수 있습니다.

## <a name="7-stream-remote-service-logs-in-visual-studio-code"></a>7. Visual Studio Code에서 원격 서비스 로그 스트림

실행 중인 앱에서 `console.log`에 대한 호출을 통해 생성한 출력을 보거나 추적합니다. 이러한 출력은 Visual Studio Code의 **출력** 창에 나타납니다.

1. **Azure App Service** 탐색기에서 앱 노드를 마우스 오른쪽 단추로 클릭하고, **Start Streaming Logs**(로그 스트리밍 시작)를 선택합니다.

    ![스트리밍 로그 보기](../media/deploy-azure/start-streaming-logs.png)

1. 메시지가 표시되면 로깅을 사용하도록 설정하고 애플리케이션을 다시 시작합니다.

    ![로깅을 사용하도록 설정하고 다시 시작하라는 메시지](../media/deploy-azure/enable-restart.png)

1. 앱이 다시 시작되면 VS Code **출력** 창이 열리고 출력을 표시하는 로그 스트림에 연결됩니다.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

1. 브라우저에서 페이지를 몇 번 새로 고치면 로그 출력이 추가로 표시됩니다.

## <a name="8-clean-up-resources"></a>8. 리소스 정리

리소스를 정리하려면 Visual Studio Code의 App Service 확장에서 App Service를 마우스 오른쪽 단추로 클릭한 다음, **삭제** 를 선택합니다.

:::image type="content" source="../media/deploy-azure/delete-azure-app-service-with-visual-studio-code-extension.png" alt-text="리소스를 정리하려면 Visual Studio Code의 App Service 확장에서 App Service를 마우스 오른쪽 단추로 클릭한 다음, **삭제**를 선택합니다.":::

## <a name="next-steps"></a>다음 단계

* [앱 설정 구성 방법 알아보기](../how-to/configure-web-app-settings.md)

[!INCLUDE [tutorial-next-steps](../includes/tutorial-next-steps.md)]
