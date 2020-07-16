---
title: Linux 클라우드 컨테이너에 Hello World 앱 배포
titleSuffix: Azure Toolkit for Eclipse
description: Linux 컨테이너에서 기본 Hello World 웹앱을 실행하여 Eclipse용 Azure 도구 키트를 사용하는 클라우드에 배포합니다.
services: app-service\web
documentationcenter: java
ms.date: 12/20/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: f5606cb8b1e61358e610c8ff1bcadc837e44a539
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86378287"
---
# <a name="deploy-a-hello-world-web-app-to-a-linux-container-in-the-cloud-using-the-azure-toolkit-for-eclipse"></a>Eclipse용 Azure 도구 키트를 사용하는 클라우드 내의 Linux 컨테이너에 Hello World 웹앱 배포

[Docker] 컨테이너는 웹 애플리케이션을 배포하는 데 널리 사용되는 방법입니다. 개발자는 Docker 컨테이너를 통해 모든 프로젝트 파일과 종속성을 서버에 배포할 단일 패키지로 통합할 수 있습니다. Eclipse용 Azure 도구 키트는 Microsoft Azure에 컨테이너 배포를 위한 기능을 추가함으로써 Java 개발자들을 위해 이 프로세스를 간소화합니다.

이 문서에서는 기본 Hello World 웹앱을 만들고 Eclipse용 Azure 도구 키트를 사용하여 Linux 컨테이너의 웹앱을 Azure에 게시하는 데 필요한 단계를 보여 줍니다.

[!INCLUDE [prerequisites](includes/prerequisites.md)]
* [Docker] 클라이언트

> [!NOTE]
>
> 이 자습서의 단계를 완료하려면 TLS 없이 포트 2375에서 디먼을 노출하도록 [Docker]를 구성해야 합니다. Docker를 설치할 때 또는 Docker 설정 메뉴를 통해 이 설정을 구성할 수 있습니다.
>
> ![Docker 설정 메뉴][docker-settings-menu]
>

## <a name="create-a-new-web-app-project"></a>새 웹앱 프로젝트 만들기

1. [Eclipse용 Azure 도구 키트에 대한 로그인 지침](/azure/developer/java/toolkit-for-eclipse/sign-in-instructions) 문서의 단계를 사용하여 Eclipse를 시작하고 Azure 계정에 로그인합니다.

1. **파일** 메뉴, **새로 만들기**, **동적 웹 프로젝트**를 차례로 클릭합니다.
   
   ![새 프로젝트 만들기][file-new-project]

1. **새 동적 웹 프로젝트** 대화 상자에서 프로젝트 이름 및 위치를 지정한 다음 **마침**을 클릭합니다.
   
   ![프로젝트 이름 지정][project-name]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Azure Container Registry를 만들어서 프라이빗 Docker 레지스트리로 사용

다음 단계에서는 Azure Portal을 사용하여 Azure Container Registry를 만드는 방법을 설명합니다.

> [!NOTE]
>
> Azure Portal 대신 Azure CLI를 사용하려는 경우 [Azure CLI 2.0을 사용하여 프라이빗 Docker 컨테이너 레지스트리 만들기][Create Docker Registry using Azure CLI]의 단계에 따르세요.
>

1. [Azure Portal]을 찾아 로그인합니다.

   Azure Portal에서 사용자의 계정에 로그인하면 [Azure Portal을 사용하여 프라이빗 Docker 컨테이너 레지스트리 만들기] 문서의 단계를 수행할 수 있습니다. 편의상 다음 단계에서 다시 설명합니다.

1. **+리소스 만들기**의 메뉴 아이콘을 클릭하고 **컨테이너**를 클릭한 다음, **Container Registry**를 클릭합니다.
   
   ![새로운 Azure Container Registry 만들기][create-container-registry-01]

1. **컨테이너 레지스트리 만들기** 페이지가 표시되면 **레지스트리 이름** 및 **리소스 그룹**을 입력하고 **관리 사용자**에 대해 **사용**을 선택한 다음, **만들기**를 클릭합니다.

   ![Azure Container Registry 설정 구성][create-container-registry-02]

## <a name="deploy-your-web-app-in-a-docker-container"></a>Docker 컨테이너에서 웹앱 배포

1. 프로젝트 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Azure**를 선택한 다음 **Docker 지원 추가**를 클릭합니다.

   기본 구성을 사용하여 Docker 파일이 자동으로 만들어집니다.

   ![Docker 지원 추가][add-docker-support]

1. Docker 지원을 추가한 후 프로젝트 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Azure**를 선택한 다음 **Web App for Containers에 게시**를 클릭합니다.

   ![컨테이너용 웹앱에 게시][run-on-web-app-for-containers]

1. **Web App for Containers에서 실행** 대화 상자가 표시되면 필수 정보를 입력합니다.

   * **Docker 파일**: 프로젝트에 Docker 지원을 추가할 때 생성된 Docker 파일의 경로를 지정합니다. 

   * **Container Registry**: 이 문서의 이전 섹션에서 만든 드롭다운 메뉴에서 컨테이너 레지스트리를 선택합니다. **서버 URL**, **사용자 이름**, **암호** 필드는 자동으로 채워집니다.

   * **이미지 및 태그**: 컨테이너 이미지 이름을 지정합니다. 일반적으로 다음 구문: "*registry*.azurecr.io/*appname*:latest"를 사용합니다. 여기에서 
      * *registry*는 이 문서의 이전 섹션에서 컨테이너 레지스트리이며 
      * *appname*은 웹앱의 이름입니다. 

   * **Web App for Container**: **기존 사용** 또는 **새로 만들기**를 선택하여 컨테이너를 기존 웹앱에 배포할지 새 웹앱을 만들지 여부를 지정합니다.  **앱 이름**을 지정하면 웹앱에 대한 URL이 만들어집니다. 예: *wingtiptoys.azurewebsites.net*

   * **리소스 그룹**: 기존 리소스 그룹을 사용할지 새 리소스 그룹을 만들지 여부를 지정합니다. 

   * **App Service 계획**: 기존 앱 서비스 플랜을 사용할지 새로 만들지 여부를 지정합니다. 

   ![Web App for Containers에서 실행][run-on-web-app-linux]

1. 위에 나열된 설정을 구성했으면 **확인**을 클릭하여 웹앱을 Azure에 게시합니다.

1. 웹앱이 게시된 후에는 웹앱에 대해 이전에 지정한 URL로 이동할 수 있습니다. 예: *wingtiptoys.azurewebsites.net*

   ![웹앱 검색][browsing-to-web-app]

## <a name="next-steps"></a>다음 단계

Docker의 추가 리소스는 공식 [Docker 웹 사이트][Docker]를 참조하세요.

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

[Azure Portal]: https://portal.azure.com/
[Azure Portal을 사용하여 프라이빗 Docker 컨테이너 레지스트리 만들기]: /azure/container-registry/container-registry-get-started-portal
[Azure for Java Developers]: /azure/developer/java/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Create Docker Registry using Azure CLI]: /azure/container-registry/container-registry-get-started-azure-cli

[Docker]: https://www.docker.com/
[Configuring artifacts]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html

<!-- IMG List -->

[add-docker-support]: media/hello-world-web-app-linux/add-docker-support.png
[browsing-to-web-app]:  media/hello-world-web-app-linux/browsing-to-web-app.png
[create-container-registry-01]: media/hello-world-web-app-linux/create-container-registry-01.png
[create-container-registry-02]: media/hello-world-web-app-linux/create-container-registry-02.png
[docker-settings-menu]: media/hello-world-web-app-linux/docker-settings-menu.png
[file-new-project]: media/hello-world-web-app-linux/file-new-project.png
[project-name]: media/hello-world-web-app-linux/project-name.png
[run-on-web-app-for-containers]: media/hello-world-web-app-linux/run-on-web-app-for-containers.png
[run-on-web-app-linux]: media/hello-world-web-app-linux/run-on-web-app-linux.png
