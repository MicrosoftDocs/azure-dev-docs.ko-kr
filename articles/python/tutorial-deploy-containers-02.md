---
title: '2단계: Visual Studio Code를 사용하여 Azure App Service에 컨테이너 이미지 배포'
description: 자습서 2단계로, 컨테이너 레지스트리에서 Azure App Service로 실제 Docker 이미지를 배포합니다.
ms.topic: conceptual
ms.date: 09/17/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 5255e0d65fda839fbbe86c1743d424ab5801774f
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90830899"
---
# <a name="2-deploy-a-container-image-to-azure-app-service"></a>2: Azure App Service에 컨테이너 이미지 배포

[이전 단계: 환경 구성](tutorial-deploy-containers-01.md)

레지스트리에 있는 컨테이너 이미지를 사용하면 VS Code의 Docker 확장을 사용하여 컨테이너를 실행하는 Azure App Service를 쉽게 설정할 수 있습니다.

1. **Docker** 탐색기에서 **레지스트리**를 확장하고 자신의 레지스트리에 대한 노드(예: **Azure**)를 확장한 다음, `:latest` 태그가 포함된 이미지가 표시될 때까지 이미지 이름에 대한 노드를 확장합니다.

    ![Docker 탐색기의 이미지 위치 찾기](media/deploy-containers/find-image-to-deploy-in-docker-explorer.png)

1. 이미지를 마우스 오른쪽 단추로 클릭하고 **Azure App Service에 이미지 배포**를 선택합니다.

    ![Azure App Service에 이미지 배포 메뉴 항목 선택](media/deploy-containers/deploy-image-to-azure-app-service-with-docker-explorer.png)

1. Azure 구독을 선택하라는 메시지에 따라 리소스 그룹을 선택하거나 지정하고, 지역을 지정하고, App Service 계획(B1이 최소 비용)을 구성하고, 사이트에 대한 이름을 지정합니다. 아래 애니메이션에 과정이 설명되어 있습니다.

    ![이미지를 생성하고 Azure App Service에 배포](media/deploy-containers/deploy-image-to-azure-app-service.gif)

    **리소스 그룹**은 앱을 구성하는 여러 리소스의 컬렉션을 말합니다. 앱의 리소스를 단일 그룹에 모두 할당하면 해당하는 리소스들을 하나의 단위로 손쉽게 관리할 수 있습니다. (자세한 내용은 Azure 설명서의 [Azure Resource Manager 개요](/azure/azure-resource-manager/resource-group-overview)를 참조하세요.)

    **App Service 계획**은 실행 중인 컨테이너를 호스트하는 물리적 리소스(기본 가상 머신)를 정의합니다. 이 자습서의 경우 B1이 Docker 컨테이너를 지원하는 최소 비용의 계획입니다. (자세한 내용은 Azure 설명서의 [App Service 계획 개요](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)를 참조하세요.)

    App Service의 이름은 모든 Azure에서 고유해야 하므로 일반적으로 회사나 사람 이름을 사용합니다. 프로덕션 사이트의 경우 개별적으로 등록된 도메인 이름을 사용하여 App Service를 구성하는 것이 일반적입니다.

1. 앱 서비스를 만드는 데는 몇 분이 걸릴 수 있으며 VS Code의 출력 창에서 진행 상황을 볼 수 있습니다.

1. 완료되면 App Service에 `WEBSITES_PORT`(복수형으로 “WEBSITES”임)라는 설정을 **반드시** 추가하여 컨테이너가 수신 대기하는 포트를 지정해야 합니다. (예를 들어 [VS Code에서 Python 컨테이너 만들기](https://code.visualstudio.com/docs/python/tutorial-create-containers) 자습서의 이미지를 사용하는 경우 포트는 Flask에 대해서는 5000, Django에 대해서는 8000입니다.) `WEBSITES_PORT`로 설정하려면 **Azure: App Service** 탐색창으로 전환하고, 새 App Service에 대한 노드를 확장한 다음(필요한 경우 새로 고침), **애플리케이션 설정**을 마우스 오른쪽 단추로 클릭하여 **새 설정 추가**를 선택합니다. 프롬프트에서 키로 `WEBSITES_PORT`를, 값에 포트 번호를 입력합니다.

    ![App Service에 포트를 지정하는 새 설정 추가](media/deploy-containers/add-new-setting-in-app-service-settings-explorer.png)

1. 설정을 변경하면 App Service가 자동으로 다시 시작됩니다. 또한 언제든지 App Service를 마우스 오른쪽 단추로 클릭하여 **다시 시작**을 선택할 수 있습니다.

1. 서비스가 다시 시작된 후 `http://<name>.azurewebsites.net`에서 사이트를 찾아봅니다. 출력 패널의 URL에서 **Ctrl**+ 클릭(macOS의 경우 **Cmd** + 클릭)을 사용하거나 **Azure: App Service** 탐색기에서 App Service를 마우스 오른쪽 단추로 클릭하여 **웹 사이트 찾아보기**를 선택할 수 있습니다.

> [!div class="nextstepaction"]
> [이미지를 배포했습니다. - 3단계 진행 >>>](tutorial-deploy-containers-03.md)