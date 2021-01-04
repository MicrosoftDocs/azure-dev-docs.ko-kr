---
title: Azure CLI를 사용하여 Azure App Service에 앱 코드 배포
description: '자습서 4부: Azure CLI - 웹 사이트 배포'
ms.topic: tutorial
ms.date: 12/14/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 7dc0369615f58e8677b479b28c2223d3fa865b19
ms.sourcegitcommit: 1dfcc022a3098b1a1505e9458eada35f527ef070
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97658412"
---
# <a name="deploy-the-app-to-app-service"></a>App Service에 앱 배포

[이전 단계: App Service 만들기](tutorial-vscode-azure-cli-node-03.md)

이 단계에서는 로컬 Git 리포지토리를 Azure로 푸시하는 기본 프로세스를 사용하여 Node.js 앱 코드를 Azure App Service에 배포합니다.

1. 터미널 또는 명령 프롬프트에서 다음 명령을 실행하여 로컬 Git 리포지토리를 초기화하고 초기 커밋을 수행합니다. (*node_modules* 폴더는 이전에 Express Generator를 실행할 때 생성된 *.gitignore* 파일에 지정되어 있으므로 무시됩니다.)

    ```bash
    git init
    git add -A
    git commit -m "Initial Commit"
    ```

1. 다음 명령을 실행하여 [Azure CLI에서 사용자 수준 배포 자격 증명을 설정](/azure/app-service/deploy-configure-credentials)하고 `username` 및 `password`를 배포 전용의 새 자격 증명으로 바꿉니다. 이러한 자격 증명은 Azure 구독 자격 증명과 동일하지 않습니다. 

    ```azurecli
    az webapp deployment user set --user-name <username> --password <password>
    ```

1. 다음 명령을 실행하여 앱 코드를 푸시할 [Git 엔드포인트를 Azure CLI에서 검색](/cli/azure/webapp/deployment/source?view=azure-cli-latest&preserve-view=false)하고 `<your_app_name>`을 이전 단계에서 App Service를 만들 때 사용한 이름으로 바꿉니다.

    ```azurecli
    az webapp deployment source config-local-git --name <your_app_name>
    ```

    명령의 출력은 다음과 유사합니다.

    <pre>
    {
      "url": "https://username@msdocs-node-cli.scm.azurewebsites.net/msdocs-node-cli.git"
    }
    </pre>

1. 다음 명령을 실행하여 이전 단계 *omitting the username*(사용자 이름 생략)의 URL을 사용하여 Git에서 `azure`라는 새 원격 항목을 설정합니다. 이전 단계의 예를 사용하면, 명령은 다음과 같습니다.

    ```bash
    git remote add azure https://msdocs-node-cli.scm.azurewebsites.net/msdocs-node-cli.git
    ```

1. 다음 명령을 실행하여, Git 리포지토리에서 App Service로 앱 코드를 배포합니다. 이 명령은 로그인 정보를 입력하라는 메시지를 표시합니다.

    ```bash
    git push azure <DEFAULT-BRANCH-NAME>
    ```

1. 명령이 실행되는 동안, 원격 호스트에서 일련의 메시지가 표시됩니다. 프로세스가 완료된 후 앱의 URL이 열려 있는 브라우저를 새로 고치면 실행 중인 코드가 보입니다.

    ![Azure에서 실행 중인 앱 코드](../../media/azure-cli/remote-app.png)

> [!div class="nextstepaction"]
> [앱을 배포했습니다.](tutorial-vscode-azure-cli-node-05.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=deploy-website)
