---
title: '자습서: VS Code를 사용하여 Linux에서 Azure App Service에 Python 웹앱 배포'
description: 자습서 5단계, 웹앱 코드 배포
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.custom: seo-python-october2019
ms.openlocfilehash: f7db7b93c3d8b2a130844ff91e1a4e294a0668f4
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172490"
---
# <a name="tutorial-deploy-your-python-web-app-to-azure-app-service-on-linux"></a>자습서: Azure App Service on Linux에 Python 웹앱 배포

[이전 단계: 사용자 지정 시작 파일 구성](tutorial-deploy-app-service-on-linux-04.md)

1. Visual Studio Code에서 **Azure: App Service** 탐색기를 열고 파란색 위쪽 화살표를 선택합니다.

   ![웹앱에 배포 명령](media/deploy-azure/deploy-to-web-app-command.png)

    또는 App Service 이름을 마우스 오른쪽 단추로 클릭하고 **웹앱에 배포** 명령을 선택할 수 있습니다.

1. 이어지는 프롬프트에서 다음과 같은 세부 정보를 제공합니다.

    - "배포할 폴더 선택"에 대해 현재 앱 폴더를 선택합니다.
    - "웹앱 선택"에 대해 이전 단계에서 만든 App Service를 선택합니다.

1. 배포 프로세스가 진행되는 동안 VS Code **출력** 창에서 진행률을 볼 수 있습니다.

    ![VS Code 출력 창의 배포 진행률](media/deploy-azure/deployment-progress.png)

1. 몇 분 후에 배포가 완료되면(설치해야 하는 종속성의 수에 따라) 아래 메시지가 표시됩니다. **웹 사이트 찾아보기** 단추를 선택하여 실행 중인 사이트를 표시합니다.

    ![배포 완료 메시지](media/deploy-azure/deployment-complete.png)

    ![앱이 App Service에서 성공적으로 실행되고 있습니다.](media/deploy-azure/running-app.png)

1. 파일이 배포되었는지 확인하려면 **Azure: App Service** 탐색기에서 App Service를 확장한 다음, **파일**을 확장합니다.

    ![App Service 탐색기를 통해 배포 파일 확인](media/deploy-azure/expand-files-node.png)

    *antenv* 폴더는 App Service에서 종속성이 있는 가상 환경을 만드는 위치입니다. 이 노드를 확장하면 *requirements.txt*에서 이름을 지정한 패키지가 *antenv/lib/python3.7/site-packages*에 설치되었는지 확인할 수 있습니다.

> [!div class="nextstepaction"]
> [내 앱을 배포했습니다.](tutorial-deploy-app-service-on-linux-06.md)

[문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=05-deploy-app)
