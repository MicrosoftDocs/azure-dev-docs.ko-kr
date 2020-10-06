---
title: Visual Studio Code에서 Azure App Service에 앱 배포
description: 'Node.js 자습서 3부: 웹 사이트 배포'
ms.topic: tutorial
ms.date: 03/04/2020
ms.custom: devx-track-js
ms.openlocfilehash: 4907a8466828ccaaef0891394ac4f33694264ccf
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91365256"
---
# <a name="deploy-the-app-to-azure"></a>Azure에 앱 배포

[이전 단계: 앱 만들기](tutorial-vscode-azure-app-service-node-02.md)

이 단계에서는 Visual Studio Code 및 Azure App Service 확장을 통해 Git 배포를 사용하여 Node.js 앱을 Azure에 배포합니다. 이 목표를 달성하려면 먼저 로컬 Git 리포지토리를 초기화하고, Azure에서 웹앱을 만든 다음, Git 배포를 사용하도록 VS Code를 구성합니다.

1. 터미널에서 *expressApp1* 폴더에 있는지 확인한 다음, 다음 명령을 사용하여 Visual Studio Code를 시작합니다.

    ```bash
    code .
    ```

1. VS Code에서 소스 제어 아이콘을 선택하여 **소스 제어** 탐색기를 연 다음, **+** 를 선택하여 로컬 Git 리포지토리를 초기화합니다.

    ![git 리포지토리 초기화](media/deploy-azure/git-init.png)

1. 프롬프트에서 작업 영역 폴더에 대해 *expressApp1*을 선택합니다.

1. 리포지토리가 초기화되면 "초기 커밋" 메시지를 입력하고, 확인 표시를 선택하여 원본 파일의 초기 커밋을 만듭니다.

    ![리포지토리에 대한 초기 커밋 완료](media/deploy-azure/initial-commit.png)

1. 명령 팔레트(**Ctrl**+**Shift**+**P**)에서 "웹 만들기"를 입력하고, **Azure App Service: 새 웹앱 만들기...고급**을 선택합니다. 고급 명령을 사용하면 Linux 기본값을 사용하는 대신 리소스 그룹, App Service 계획 및 운영 체제를 포함한 배포를 완전히 제어할 수 있습니다.

1. 프롬프트에 다음과 같이 응답합니다.

    - **구독** 계정을 선택합니다.
    - **전역적으로 고유한 이름을 입력하세요.** 에 대해 모든 Azure에서 고유한 이름을 입력합니다. 영숫자 문자('A-Z', 'a-z' 및 '0-9') 및 하이픈('-')만 사용합니다.
    - **새 리소스 그룹 만들기**를 선택하고, `AppServiceTutorial-rg`와 같은 이름을 지정합니다.
    - 운영 체제(Windows 또는 Linux)를 선택합니다.
    - Linux만: Node.js 버전을 선택합니다. (Windows의 경우 나중에 앱 설정을 사용하여 버전을 설정합니다.)
    - **새 App Service 계획 만들기**를 선택하고, `AppServiceTutorial-plan`과 같은 이름을 제공하고, **F1 체험** 가격 책정 계층을 선택합니다.
    - Application Insights 리소스에 대해 **지금 건너뛰기**를 선택합니다.
    - 가까운 위치를 선택합니다.

1. 잠시 후에 VS Code에서 만들기가 완료되었음을 알려줍니다. **X** 단추를 사용하여 알림을 닫습니다.

    ![웹앱 만들기 완료 시 알림](media/deploy-azure/creation-complete.png)

1. 웹앱이 준비되면 다음으로 VS Code에서 로컬 Git 리포지토리의 코드를 배포하도록 지시합니다. Azure 아이콘을 선택하여 **Azure App Service** 탐색기를 열고, 구독 노드를 펼치고, 마우스 오른쪽 단추로 방금 만든 웹앱 이름을 클릭하고, **배포 원본 구성**을 선택합니다.

    ![웹앱에서 배포 원본 명령 구성](media/deploy-azure/configure-deployment-source.png)

1. 메시지가 표시되면 **LocalGit**을 선택합니다.

1. Windows에서 App Service에 배포하는 경우 먼저 두 가지 설정을 만든 후에 배포해야 합니다.

    1. VS Code에서 새 앱 서비스의 노드를 펼치고, 마우스 오른쪽 단추로 **애플리케이션 설정**을 클릭하고, **새 설정 추가**를 선택합니다.

        ![앱 설정 추가 명령](media/deploy-azure/add-setting.png)

    1. 설정 키로 `WEBSITE_NODE_DEFAULT_VERSION`을 입력하고, 설정 값으로 `10.15.2`를 입력합니다. 이러한 설정은 Node.js 버전을 설정합니다.
    1. 프로세스를 반복하여 값이 `1`인 `SCM_DO_BUILD_DURING_DEPLOYMENT`에 대한 키를 만듭니다. 이러한 설정은 배포 시 서버에서 `npm install`을 강제로 실행하도록 합니다.
    1. **애플리케이션 설정** 노드를 펼쳐서 설정이 올바른지 확인합니다.

1. 파란색 위쪽 화살표 아이콘을 선택하여 코드를 Azure에 배포합니다.

    ![웹앱에 배포 아이콘](media/deploy-azure/deploy.png)

1. 프롬프트에서 *expressApp1* 폴더를 선택하고, **구독** 계정을 다시 선택한 다음, 이전에 만든 웹앱의 이름을 선택합니다.

1. Linux에 배포하는 경우 대상 서버에서 `npm install`을 실행하도록 구성을 업데이트하라는 메시지가 표시되면 **예**를 선택합니다.

    ![대상 Linux 서버의 구성을 업데이트하도록 요구하는 메시지](media/deploy-azure/server-build.png)

1. Linux 및 Windows의 경우 **항상 "nodejs-docs-hello-world" 작업 영역을 (앱 이름)에 배포합니다.** 라는 메시지가 표시되면 **예**를 선택합니다. **예**를 선택하면 VS Code에서 자동으로 후속 배포에서 동일한 App Service 웹앱을 대상으로 지정하도록 지시합니다.

1. 배포가 완료되면 프롬프트에서 **웹 사이트 찾아보기**를 선택하여 새로 배포된 웹앱을 확인합니다. 브라우저에서 "Hello World!"가 표시됩니다.

1. (선택 사항): 코드 파일을 변경한 다음, 배포 단추를 다시 사용하여 웹앱을 업데이트할 수 있습니다.

> [!div class="nextstepaction"]
> [내 사이트가 Azure에 있습니다.](tutorial-vscode-azure-app-service-node-04.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=deploy-app)
