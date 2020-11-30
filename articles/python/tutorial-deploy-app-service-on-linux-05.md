---
title: '5단계: VS Code를 사용하여 Linux에서 Azure App Service에 Python 웹앱 배포'
description: 자습서 5단계, 웹앱 코드 배포
ms.topic: conceptual
ms.date: 11/20/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 7b3743d417ed3455c59f5b9887ee54728fb7318a
ms.sourcegitcommit: 29930f1593563c5e968b86117945c3452bdefac1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95485716"
---
# <a name="5-deploy-your-python-web-app-to-azure-app-service-on-linux"></a>5: Azure App Service on Linux에 Python 웹앱 배포

[이전 단계: 사용자 지정 시작 파일 구성](tutorial-deploy-app-service-on-linux-04.md)

Azure App Service에 Python 앱을 배포하려면 다음 절차를 따르세요.

1. Visual Studio Code에서 **Azure: App Service** 탐색기를 열고 파란색 위쪽 화살표를 선택합니다.

   ![App Service 탐색기에서 App Service에 웹 앱 배포](media/deploy-azure/deploy-web-app-to-app-service-in-app-service-explorer.png)

    또는 App Service 이름을 마우스 오른쪽 단추로 클릭하고 **웹앱에 배포** 명령을 선택할 수 있습니다.

1. 이어지는 프롬프트에서 다음과 같은 세부 정보를 제공합니다.

    - "배포할 폴더 선택"에 대해 현재 앱 폴더를 선택합니다.
    - "웹앱 선택"에 대해 이전 단계에서 만든 App Service를 선택합니다.
    - 빌드 구성을 업데이트하여 빌드 명령을 실행하라는 메시지가 표시되면 **예** 로 대답합니다.
    - 기존 배포를 덮어쓸지 묻는 메시지가 표시되면 **배포** 로 대답합니다.
    - "항상 작업 영역 배포"라는 메시지가 표시되면 **예** 로 대답합니다.

1. 배포 프로세스가 진행되는 동안 VS Code **출력** 창에서 진행률을 볼 수 있습니다.

    ![VS Code 출력 창의 배포 진행률](media/deploy-azure/view-deployment-progress-in-visual-studio-code-output.png)

1. 몇 분 후에 배포가 완료되면(설치해야 하는 종속성의 수에 따라) 아래 메시지가 표시됩니다. **웹 사이트 찾아보기** 단추를 선택하여 실행 중인 사이트를 표시합니다.

    ![웹 사이트 찾아보기 단추를 사용하여 배포 완료](media/deploy-azure/web-app-deployment-complete-with-browse-website-button.png)

    ![앱이 App Service에서 성공적으로 실행되고 있습니다.](media/deploy-azure/web-app-running-successfully-on-app-service.png)

1. 기본 앱이 계속 표시되는 경우 배포 후 컨테이너가 다시 시작될 때까지 1-2분 정도 기다렸다가 다시 시도합니다. 사용자 지정 시작 명령을 사용 중이며 명령이 올바른 것으로 확인되었으면 6단계를 계속 진행하여 로그를 확인합니다.

1. 파일이 배포되었는지 확인하려면 **Azure: App Service** 탐색기에서 App Service를 확장한 다음, **파일** 을 확장합니다.

    ![App Service 탐색기를 통해 배포 파일 확인](media/deploy-azure/expand-files-node-to-check-deployment-of-web-app-files.png)

    참고로 *.deployment*, *antenv.tar.gz* 및 *oryx-manifest.toml* 파일은 App Service 빌드 시스템에서 사용합니다. *hostingstart.html* 은 기본 앱 페이지입니다.

> [!div class="nextstepaction"]
> [앱을 배포했습니다. - 6단계 진행 >>>](tutorial-deploy-app-service-on-linux-06.md)

[문제가 있나요? 알려주세요.](https://aka.ms/FlaskVSCQuickstartHelp)
