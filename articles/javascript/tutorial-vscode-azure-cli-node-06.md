---
title: 앱 코드를 변경하고 Azure에 다시 배포
description: 자습서 6부, 변경 및 재배포
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: e6747551ec80fc7b04873445557e2d2153e3b358
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88217923"
---
# <a name="part-6-make-changes-and-redeploy"></a>6부 변경 및 재배포

[이전 단계: 로그 스트리밍](tutorial-vscode-azure-cli-node-05.md)

이 단계에서는 앱 코드를 변경하고 로컬 Git 리포지토리에 커밋한 다음, Azure로 푸시하여 사이트를 다시 배포합니다.

1. `myExpressApp` 폴더에서 *views/index.pug* 파일을 열고 5번 줄의 메시지를 `p Welcome to Azure!`로 변경합니다.

    ![index.pug 파일 편집](media/azure-cli/editpugfile.png)

1. 파일을 저장합니다.

1. 터미널 또는 명령 프롬프트에서 다음 명령을 실행하여 변경 사항을 Git에 커밋합니다.

    ```bash
    git commit -a -m "Edited message"
    ```

1. 앞에서 만든 Azure라는 Git 원격으로 변경 사항을 푸시합니다.

    ```bash
    git push azure master
    ```

1. App Service가 Git 리포지토리에 이미 연결되어 있기 때문에 명령의 출력에 변경 내용이 Azure에 자동으로 게시된 것으로 표시됩니다. 

    ```output
    Enumerating objects: 7, done.
    Counting objects: 100% (7/7), done.
    Delta compression using up to 4 threads
    Compressing objects: 100% (4/4), done.
    Writing objects: 100% (4/4), 405 bytes | 202.00 KiB/s, done.
    Total 4 (delta 2), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id '9fd89a25b6'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling node.js deployment.
    remote: Creating app_offline.htm
    remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
    remote: Copying file: 'views\index.pug'
    remote: Deleting app_offline.htm
    remote: Using start-up script bin/www from package.json.
    remote: Generated web.config.
    remote: The package.json file does not specify node.js engine version constraints.
    remote: The node.js application will run with the default node.js version 6.9.5.
    remote: Selected npm version 3.10.10
    remote: ..
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://msdocs-node-cli.scm.azurewebsites.net/msdocs-node-cli.git
       a98bad8..9fd89a2  master -> master
    ```

1. 브라우저에서 앱을 새로 고치면 변경 내용이 보입니다.

    ![브라우저에 보이는 게시된 변경 내용](media/azure-cli/remote-app-changes.png)

> [!div class="nextstepaction"]
> [변경 내용이 보입니다.](tutorial-vscode-azure-cli-node-07.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=publishing-changes)
