---
title: Azure CLI를 사용하여 Azure App Service에 앱 코드 배포
description: '자습서 4부: Azure CLI - 웹 사이트 배포'
ms.topic: tutorial
ms.date: 09/24/2019
ms.custom: devx-track-js
ms.openlocfilehash: 8e6075b8a1e526f916ab43c9a1f52ef1b66e8614
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91364786"
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

1. 다음 명령을 실행하여 Azure에서 배포 자격 증명을 설정하고 `username`과 `pPassword`를 로그인 정보로 바꿉니다. 이 명령은 성공하면 JSON 출력이 표시됩니다.

    ```azurecli
    az webapp deployment user set --user-name <username> --password <password>
    ```

1. 다음 명령을 실행하여 앱 코드를 푸시할 Git 엔드포인트를 검색하고 `<your_app_name>`을 이전 단계에서 App Service를 만들 때 사용한 이름으로 바꿉니다.

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
    git push azure master
    ```

1. 명령이 실행되는 동안, 원격 호스트에서 일련의 메시지가 표시됩니다. 프로세스가 완료된 후 앱의 URL이 열려 있는 브라우저를 새로 고치면 실행 중인 코드가 보입니다.

    ![Azure에서 실행 중인 앱 코드](media/azure-cli/remote-app.png)

> [!TIP]
> `Object #<eventemitter> has no method 'hrtime'` 오류가 발생하면 사이트에서 노드 런타임 버전을 설정해야 할 수도 있습니다. 아래 명령은 노드 버전 `6.9.1`을 사용하도록 사이트에 지시합니다. 사이트에 다른 버전의 노드 또는 이후 버전의 노드가 필요한 경우에는 전체 의미 체계 버전 `major.minor.patch`를 지정합니다.
>
> ```azurecli
> az webapp config appsettings set --name <your_app_name> --settings
> ```

> [!div class="nextstepaction"]
> [앱을 배포했습니다.](tutorial-vscode-azure-cli-node-05.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=deploy-website)
