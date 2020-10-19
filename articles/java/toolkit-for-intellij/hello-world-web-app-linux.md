---
title: Linux 컨테이너에 Hello World 웹앱 배포
titleSuffix: Azure Toolkit for IntelliJ
description: Linux 컨테이너에서 기본 Hello World 웹앱을 실행하여 IntelliJ용 Azure 도구 키트를 사용하는 클라우드에 배포합니다.
services: app-service\web
documentationcenter: java
ms.date: 09/09/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 2b6e4e6e40d5fe7904e43b36edacd14494e92b0f
ms.sourcegitcommit: f460914ac5843eb7392869a08e3a80af68ab227b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92010102"
---
# <a name="deploy-java-app-to-azure-web-apps-for-containers-using-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ를 사용하여 Azure Web Apps for Containers에 Java 앱 배포

[Docker] 컨테이너는 웹 애플리케이션을 배포하는 데 널리 사용되는 방법입니다. 개발자는 Docker 컨테이너를 통해 모든 프로젝트 파일과 종속성을 서버에 배포할 단일 패키지로 통합할 수 있습니다. Azure Toolkit for IntelliJ는 Java 개발자를 위해 컨테이너를 Microsoft Azure에 배포하는 기능을 추가하여 이 프로세스를 간소화합니다.

이 문서에서는 기본 Hello World 웹앱을 만들고 IntelliJ용 Azure 도구 키트를 사용하여 Linux 컨테이너의 웹앱을 Azure에 게시하는 데 필요한 단계를 보여 줍니다.

[!INCLUDE [prerequisites](includes/prerequisites.md)]
* [Docker] 클라이언트

> [!NOTE]
>
> 이 자습서의 단계를 완료하려면 TLS 없이 포트 2375에서 디먼을 노출하도록 [Docker]를 구성해야 합니다. Docker를 설치할 때 또는 Docker 설정 메뉴를 통해 이 설정을 구성할 수 있습니다.
>
> ![Docker 설정 메뉴][docker-settings-menu]
>

## <a name="installation-and-sign-in"></a>설치 및 로그인

다음 단계에서는 IntelliJ 개발 환경에서 Azure 로그인 프로세스를 안내합니다.

1. 플러그 인을 설치하지 않은 경우 [Azure Toolkit for IntelliJ 설치](installation.md)를 참조하세요.

1. Azure 계정에 로그인하려면 왼쪽 **Azure Explorer** 사이드바로 이동한 다음, **Azure 로그인** 아이콘을 클릭합니다. 또는 **도구**로 이동하여 **Azure**를 펼치고, **Azure 로그인**을 클릭합니다.

   :::image type="content" source="media/sign-in-instructions/I01.png" alt-text="IntelliJ에서 Azure에 로그인"::: 

1. **Azure 로그인** 창에서 **디바이스 로그인**을 선택하고 **로그인**([다른 로그인 옵션](sign-in-instructions.md))을 클릭합니다.

1. **Azure 디바이스 로그인** 대화 상자에서 **복사 및 열기**를 클릭합니다.

1. 브라우저에서, 마지막 단계에서 **복사 및 열기**를 클릭할 때 복사한 디바이스 코드를 붙여넣은 후 **다음**을 클릭합니다.

1. Azure 계정을 선택하고, 로그인하는 데 필요한 모든 인증 절차를 완료합니다.

1. 로그인하면 브라우저를 닫고 IntelliJ IDE로 다시 전환합니다. **구독 선택** 대화 상자에서 사용하려는 구독을 선택한 다음, **선택**을 클릭합니다.

## <a name="creating-a-new-web-app-project"></a>새 웹앱 프로젝트 만들기

1. **파일**을 클릭하고, **새로 만들기**를 펼친 다음, **프로젝트**를 클릭합니다.

1. **새 프로젝트** 대화 상자에서 **Maven**을 선택하고, **Archetype에서 만들기** 옵션이 선택되어 있는지 확인합니다. 목록에서 **maven-archetype-webapp**을 선택하고, **다음**을 클릭합니다.

   :::image type="content" source="media/create-hello-world-web-app/maven-archetype-webapp.png" alt-text="IntelliJ에서 Azure에 로그인"::: 

1. **아티팩트 좌표** 드롭다운을 펼쳐 모든 입력 필드를 표시하고, 새 웹앱에 대한 다음 정보를 지정하고, **다음**을 클릭합니다.

   * **Name**: 웹앱의 이름입니다. 그러면 웹앱의 **ArtifactId** 필드가 자동으로 채워집니다.
   * **GroupId**: 아티팩트 그룹의 이름, 일반적으로 회사 도메인 (예: *com.microsoft.azure*)입니다.
   * **버전**: *1.0-SNAPSHOT* 기본 버전을 유지합니다.

1. Maven 설정을 사용자 지정하거나 기본값을 적용하고, **다음**을 클릭합니다.

1. 왼쪽 **프로젝트** 탭에서 프로젝트로 이동하여 **src/main/webapp/index.jsp** 파일을 엽니다. 코드를 다음으로 바꾸고, **변경 내용을 저장**합니다.

   ```html
   <html>
    <body>
      <b><% out.println("Hello World!"); %></b>
    </body>
   </html>
   ```
   :::image type="content" source="media/create-hello-world-web-app/open-index-page.png" alt-text="IntelliJ에서 Azure에 로그인":::

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Azure Container Registry를 만들어서 프라이빗 Docker 레지스트리로 사용

다음 단계에서는 Azure Portal을 사용하여 Azure Container Registry를 만드는 방법을 설명합니다.

> [!NOTE]
>
> Azure Portal 대신 Azure CLI를 사용하려는 경우 [Azure CLI 2.0을 사용하여 프라이빗 Docker 컨테이너 레지스트리 만들기][Create Docker Registry using Azure CLI]의 단계에 따르세요.
>

1. [Azure Portal]을 찾아 로그인합니다.

   Azure Portal에서 사용자의 계정에 로그인하면 [Azure Portal을 사용하여 프라이빗 Docker 컨테이너 레지스트리 만들기] 문서의 단계를 수행할 수 있습니다. 편의상 다음 단계에서 다시 설명합니다.

1. **+ 리소스 만들기**에 대한 메뉴 아이콘, **컨테이너** 범주, **컨테이너 레지스트리**를 차례로 클릭합니다.

1. **컨테이너 레지스트리 만들기** 페이지가 표시되면 다음 정보를 지정합니다.

   * **구독**: 새 컨테이너 레지스트리에 사용하려는 Azure 구독을 지정합니다.

   * **리소스 그룹**: 컨테이너 레지스트리에 대한 리소스 그룹을 지정합니다. 다음 옵션 중 하나를 선택합니다.
      * **새로 만들기**: 새 리소스 그룹을 만들도록 지정합니다.
      * **기존 리소스 사용**: Azure 계정에 연결된 리소스 그룹 목록에서 선택하도록 지정합니다.

   * **레지스트리 이름**: 새 컨테이너 레지스트리의 이름을 지정합니다.

   * **위치**: 컨테이너 레지스트리를 만들 지역(예: "미국 서부")을 지정합니다.

   * **SKU**: 컨테이너 레지스트리에 대한 서비스 계층을 지정합니다. 이 자습서에서는 *기본*을 선택합니다. 자세한 내용은 [Azure Container Registry 서비스 계층](/azure/container-registry/container-registry-skus)을 참조하세요.

1. **검토 + 만들기**를 클릭하고, 정보가 올바른지 확인합니다. **만들기**를 클릭하여 완료합니다.

## <a name="deploy-your-web-app-in-a-docker-container"></a>Docker 컨테이너에서 웹앱 배포

다음 단계에서는 웹앱에 대한 Docker 지원을 구성하고 웹앱을 Docker 컨테이너에 배포하는 과정을 안내합니다.

1. 왼쪽 **프로젝트** 탭에서 프로젝트로 이동하고, 마우스 오른쪽 단추로 프로젝트를 클릭합니다. **Azure**를 펼치고, **Docker 지원 추가**를 클릭합니다.

   기본 구성을 사용하여 Docker 파일이 자동으로 만들어집니다.

   :::image type="content" source="media/hello-world-web-app-linux/docker-support-file.png" alt-text="IntelliJ에서 Azure에 로그인":::

1. Docker 지원이 추가되면 프로젝트 탐색기에서 마우스 오른쪽 단추로 프로젝트를 클릭하고, **Azure**를 펼친 다음, **Web App for Containers에서 실행**을 클릭합니다.

1. **Web App for Containers에서 실행** 대화 상자에서 다음 정보를 입력합니다.

   * **Name**: Azure 도구 키트에 표시되는 친숙한 이름을 지정합니다. 

   * **Container Registry**: 이 문서의 이전 섹션에서 만든 드롭다운 메뉴에서 컨테이너 레지스트리를 선택합니다. **서버 URL**, **사용자 이름**, **암호** 필드는 자동으로 채워집니다.

   * **이미지 및 태그**: 컨테이너 이미지 이름을 지정합니다. 일반적으로 다음 구문: "*registry*.azurecr.io/*appname*:latest"를 사용합니다. 여기에서 
      * *registry*는 이 문서의 이전 섹션에서 컨테이너 레지스트리이며 
      * *appname*은 웹앱의 이름입니다. 

   * **기존 웹앱 사용** 또는 **새 웹앱 만들기**: 컨테이너를 기존 웹앱에 배포할지 새 웹앱을 만들지 여부를 지정합니다. **앱 이름**을 지정하면 웹앱에 대한 URL이 만들어집니다. 예: *wingtiptoys.azurewebsites.net*

   * **리소스 그룹**: 기존 리소스 그룹을 사용할지 새 리소스 그룹을 만들지 여부를 지정합니다. 

   * **App Service 계획**: 기존 앱 서비스 플랜을 사용할지 새로 만들지 여부를 지정합니다. 

1. 위에 나열된 설정을 구성했으면 **실행**을 클릭합니다. 웹앱이 성공적으로 배포된 경우 상태가 **실행** 창에 표시됩니다.

1. 웹앱이 게시되면 웹앱에 대해 이전에 지정한 URL(예: *wingtiptoys.azurewebsites.net*)로 이동할 수 있습니다.

   ![웹앱 검색][browsing-to-web-app]

## <a name="optional-modify-your-web-app-publish-settings"></a>선택 사항: 웹 앱 게시 설정 수정

1. 웹앱을 게시한 후 설정은 기본값으로 저장되고 도구 모음에서 녹색 화살표 아이콘을 클릭하여 Azure에서 애플리케이션을 실행할 수 있습니다. 이러한 설정은 웹앱에 대한 드롭다운 메뉴, **구성 편집**을 차례로 클릭하여 수정할 수 있습니다.

    :::image type="content" source="media/create-hello-world-web-app/edit-configuration-menu.png" alt-text="IntelliJ에서 Azure에 로그인":::

1. **실행/디버그 구성** 대화 상자가 표시되면 기본 설정을 수정한 다음 **확인**을 클릭할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Docker의 추가 리소스는 공식 [Docker 웹 사이트][Docker]를 참조하세요.

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

[Azure Portal]: https://portal.azure.com/
[Azure Portal을 사용하여 프라이빗 Docker 컨테이너 레지스트리 만들기]: /azure/container-registry/container-registry-get-started-portal
[Azure for Java Developers]: ../index.yml
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Create Docker Registry using Azure CLI]: /azure/container-registry/container-registry-get-started-azure-cli

[Docker]: https://www.docker.com/
[Configuring artifacts]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html

<!-- IMG List -->

[browsing-to-web-app]:  media/hello-world-web-app-linux/browsing-to-web-app.png
[docker-settings-menu]: media/hello-world-web-app-linux/docker-settings-menu.png
