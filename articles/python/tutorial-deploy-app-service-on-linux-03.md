---
title: '3단계: Visual Studio Code에서 App Service 만들기'
description: 자습서 3단계로, VS Code 확장에서 App Service를 만듭니다.
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: de70e026398797cbbb406924ed2a3184f627de92
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87983628"
---
# <a name="3-create-the-app-service-from-visual-studio-code"></a>3: Visual Studio Code에서 App Service 만들기

[이전 단계: 앱 준비](tutorial-deploy-app-service-on-linux-02.md)

이 단계에서는 앱을 배포할 Azure App Service의 인스턴스를 만듭니다.

다음 단계에서 필요한 경우 사용자 지정 시작 파일을 구성할 수 있도록 코드를 배포하기 전에 이 단계를 수행합니다.

1. **Azure: App Service** 탐색기에서 새 App Service를 만드는 **+** 명령을 선택하거나 명령 팔레트(**F1**)를 열고 **Azure App Service: 새 웹앱 만들기**를 선택합니다. (App Service 용어에서 “웹앱”은 앱 코드 자체가 아니라 웹앱 코드에 대한 **호스트**입니다.)

    ![App Service 탐색기에서 새 App Service 만들기](media/deploy-azure/create-new-app-service-in-app-service-explorer.png)

1. 아래 프롬프트에서:

    - App Service에서 전역적으로 고유한 앱 이름을 입력합니다. 일반적으로 사용자의 이름이나 회사 이름, 앱 이름 순으로 사용합니다.
    - 런타임으로 **Python 3.7**을 선택합니다.

1. 새 App Service가 생성되었음을 나타내는 메시지가 표시되면 **출력 보기**를 선택하여 VS Code에서 **출력** 창으로 전환합니다. 출력에는 App Service에 대한 URL과 함께 생성된 Azure 리소스 그룹 및 App Service 계획의 이름이 표시됩니다.

    ![App Service의 URL, 리소스 그룹 및 App Service 계획](media/deploy-azure/url-for-your-new-app-service-and-resource-group-and-plan.png)

1. App Service가 제대로 실행되고 있는지 확인하려면 **Azure: App Service** 탐색기에서 구독을 확장하고 App Service 이름을 마우스 오른쪽 단추로 클릭하고 **웹 사이트 찾아보기**를 선택합니다.

    ![App Service 탐색기에서 App Service의 웹 사이트 찾아보기 명령](media/deploy-azure/select-command-to-browse-website-in-app-service.png)

1. 사용자 고유의 코드를 아직 App Service에 배포하지 않았기 때문에(다음 단계에서 수행) 기본 앱만 표시됩니다.

    ![App Service on Linux의 기본 Python 앱](media/deploy-azure/default-python-app-on-app-service-on-linux.png)

## <a name="optional-upload-an-environment-variable-definitions-file"></a>(선택 사항) 환경 변수 정의 파일 업로드

환경 변수 정의 파일이 있는 경우 해당 파일을 사용하여 App Service 환경도 구성할 수 있습니다. (일반적으로 확장명이 *.env*인 파일에 대해 자세히 알아보려면 [Visual Studio Code - Python 환경](https://code.visualstudio.com/docs/python/environments#environment-variable-definitions-file)을 참조하세요.)

1. **Azure: App Service** 탐색기에서 원하는 App Service에 대한 노드를 확장한 다음, **애플리케이션 설정** 노드를 마우스 오른쪽 단추로 클릭하고 **로컬 설정 업로드**를 선택합니다.

1. VS Code에서 *.env* 파일의 위치를 묻는 메시지가 표시되고 난 다음, App Service에 업로드됩니다.

1. 업로드가 완료되면 **애플리케이션 설정** 노드를 확장하여 개별 값을 볼 수 있습니다. App Service로 이동하고 **구성**을 선택하면 Azure Portal에서도 볼 수 있습니다.

1. Azure Portal에서 직접 설정을 만드는 경우 **애플리케이션 설정** 노드를 마우스 오른쪽 단추로 클릭하고 **원격 설정 다운로드**를 선택하여 정의 파일에 저장할 수 있습니다. 이 프로세스를 통해 포털 뿐만 아니라 리포지토리에도 이러한 설정이 있는지 확인할 수 있습니다.

> [!div class="nextstepaction"]
> [App Service를 만들었습니다. - 4단계 진행 >>>](tutorial-deploy-app-service-on-linux-04.md)

[문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=03-create-app-service)
