---
title: IntelliJ를 사용하여 Azure App Service용 Hello World 웹앱 만들기
description: 이 자습서에서는 IntelliJ용 Azure 도구 키트를 사용하여 Azure용 Hello World 웹앱을 만드는 방법을 보여 줍니다.
services: app-service
keywords: Java, Intellij, 웹앱, Azure App Service, Hello World, 빠른 시작
documentationcenter: java
author: selvasingh
ms.assetid: 75ce7b36-e3ae-491d-8305-4b42ce37db4e
ms.reviewer: asirveda
ms.date: 09/09/2020
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: devx-track-java
ms.openlocfilehash: 3a096ac0cb533fda11f2b2f3a652dfae628298d4
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90682122"
---
# <a name="create-a-hello-world-web-app-for-azure-app-service-using-intellij"></a>IntelliJ를 사용하여 Azure App Service용 Hello World 웹앱 만들기

이 문서에서는 [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053)를 사용하여 기본 Hello World 웹앱을 만들고 이를 Azure App Service에 게시하는 데 필요한 단계를 보여 줍니다.

> [!NOTE]
>
> Eclipse 사용을 선호하는 경우 [유사한 Eclipse 자습서][eclipse-hello-world]를 참조하세요.
>
>[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]
>
> 이 자습서를 완료한 후 반드시 리소스를 정리해야 합니다. 이 경우 이 가이드 실행은 체험 계정 할당량을 초과하지 않습니다.
>

[!INCLUDE [basic-prerequisites](includes/basic-prerequisites.md)]

## <a name="installation-and-sign-in"></a>설치 및 로그인

다음 단계에서는 IntelliJ 개발 환경에서 Azure 로그인 프로세스를 안내합니다.

1. 플러그 인을 설치하지 않은 경우 [Azure Toolkit for IntelliJ 설치](installation.md)를 참조하세요.

1. Azure 계정에 로그인하려면 왼쪽 **Azure Explorer** 사이드바로 이동한 다음, **Azure 로그인** 아이콘을 클릭합니다. 또는 **도구**로 이동하여 **Azure**를 펼치고, **Azure 로그인**을 클릭합니다.

   :::image type="content" source="media/sign-in-instructions/I01.png" alt-text="IntelliJ에서 Azure에 로그인"::: 

1. **Azure 로그인** 창에서 **디바이스 로그인**을 선택하고 **로그인**([다른 로그인 옵션](sign-in-instructions.md))을 클릭합니다.

1. **Azure 디바이스 로그인** 대화 상자에서 **복사 및 열기**를 클릭합니다.

1. 브라우저에서, 마지막 단계에서 **복사 및 열기**를 클릭할 때 복사한 디바이스 코드를 붙여넣은 후 **다음**을 클릭합니다.

1. Azure 계정을 선택하고, 로그인하는 데 필요한 모든 인증 절차를 완료합니다.

1. 로그인하면 브라우저를 닫고 IntelliJ IDE로 다시 전환합니다. **구독 선택** 대화 상자에서 사용하려는 구독을 선택한 다음, **확인**을 클릭합니다.

## <a name="creating-a-new-web-app-project"></a>새 웹앱 프로젝트 만들기

1. **파일**을 클릭하고, **새로 만들기**를 펼친 다음, **프로젝트**를 클릭합니다.

1. **새 프로젝트** 대화 상자에서 **Maven**을 선택하고, **Archetype에서 만들기** 옵션이 선택되어 있는지 확인합니다. 목록에서 **maven-archetype-webapp**을 선택하고, **다음**을 클릭합니다.

   :::image type="content" source="media/create-hello-world-web-app/maven-archetype-webapp.png" alt-text="maven-archetype-webapp 옵션 선택"::: 

1. **아티팩트 좌표** 드롭다운을 펼쳐 모든 입력 필드를 표시하고, 새 웹앱에 대한 다음 정보를 지정하고, **다음**을 클릭합니다.

   * **Name**: 웹앱의 이름입니다. 그러면 웹앱의 **ArtifactId** 필드가 자동으로 채워집니다.
   * **GroupId**: 아티팩트 그룹의 이름, 일반적으로 회사 도메인 (예: *com.microsoft.azure*)입니다.
   * **버전**: *1.0-SNAPSHOT* 기본 버전을 유지합니다.

1. Maven 설정을 사용자 지정하거나 기본값을 적용하고, **다음**을 클릭합니다.

1. 왼쪽 **프로젝트** 탭에서 프로젝트로 이동하여 **src/main/webapp/WEB-INF/index.jsp** 파일을 엽니다. 코드를 다음으로 바꾸고, **변경 내용을 저장**합니다.

   ```html
   <html>
    <body>
      <b><% out.println("Hello World!"); %></b>
    </body>
   </html>
   ```
   :::image type="content" source="media/create-hello-world-web-app/open-index-page.png" alt-text="index.jsp 파일 열기":::

## <a name="deploying-web-app-to-azure"></a>Azure에 웹앱 배포

1. 프로젝트 탐색기 보기 아래에서 마우스 오른쪽 단추로 프로젝트를 클릭하고, **Azure**를 펼친 다음, **Azure Web Apps에 배포**를 클릭합니다.

1. [Azure에 배포] 대화 상자에서 애플리케이션을 기존 Tomcat 웹앱에 배포하거나 새 애플리케이션을 만들 수 있습니다.

   a. **사용할 수 있는 웹앱이 없습니다. 새 웹앱을 만들려면 클릭하세요.** 를 클릭하여 새 웹앱을 만듭니다. 그렇지 않고 기존 웹앱이 구독에 있으면 WebApp 드롭다운에서 **새 WebApp 만들기**를 선택합니다.

      :::image type="content" source="media/create-hello-world-web-app/deploy-to-azure-webapps.png" alt-text="Azure에 배포 대화 상자 창":::

   **WebApp 만들기** 팝업 대화 상자에서 다음 정보를 지정하고 **확인**을 클릭합니다. 

      * **Name**: WebApp의 도메인 이름 문자열입니다.
      * **구독**: 새 WebApp에 사용하려는 Azure 구독을 지정합니다.
      * **Platform**: *Linux*를 선택합니다.
      * **웹 컨테이너**: *TOMCAT 9.0-jre8* 또는 적절하게 선택합니다.
      * **리소스 그룹**: WebApp에 대한 리소스 그룹을 지정합니다. Azure 계정과 연결된 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만들 수 있습니다.
      * **App Service 계획**: WebApp에 대한 App Service 계획을 지정합니다. Azure 계정과 연결된 기존 계획을 선택하거나 새 계획을 만들 수 있습니다.

   b. 기존 웹앱에 배포하려면 WebApp 드롭다운에서 웹앱을 선택한 다음, **실행**을 클릭합니다.

1. 도구 키트에는 웹앱이 성공적으로 배포되면 상태 메시지가 표시되며, 배포에 성공한 경우 배포된 웹앱의 URL도 표시됩니다.

1. 상태 메시지에 제공된 링크를 사용하여 웹앱으로 이동할 수 있습니다.

   :::image type="content" source="media/create-hello-world-web-app/browse-web-app.png" alt-text="웹앱 찾아보기":::

## <a name="managing-deploy-configurations"></a>배포 구성 관리

> [!TIP]
> 웹앱이 게시되면 도구 모음에서 녹색 화살표 아이콘을 클릭하여 배포를 실행할 수 있습니다.

1. WebApp 배포를 실행하기 전에 웹앱에 대한 드롭다운 메뉴를 클릭하고 **구성 편집**을 선택하여 기본 설정을 수정할 수 있습니다.

   :::image type="content" source="media/create-hello-world-web-app/edit-configuration-menu.png" alt-text="구성 편집 메뉴":::

1. **실행/디버그 구성** 대화 상자에서 기본 설정을 수정할 수 있습니다. **확인**을 클릭하여 설정을 저장합니다.

## <a name="cleaning-up-resources"></a>리소스 정리

1. 웹앱을 삭제하려면 왼쪽 **Azure Explorer** 사이드바로 이동하여 **Web Apps** 항목을 찾습니다. 

   > [!NOTE]
   > Web Apps 메뉴 항목이 펼쳐지지 않으면 Azure Explorer 도구 모음에서 **새로 고침** 아이콘을 클릭하거나, 마우스 오른쪽 단추로 Web Apps 메뉴 항목을 클릭하고 **새로 고침**을 선택하여 목록을 수동으로 새로 고칩니다.

1. 마우스 오른쪽 단추로 삭제하려는 웹앱을 클릭하고, **삭제**를 클릭합니다.

1. 앱 서비스 계획 또는 리소스 그룹을 삭제하려면 [Azure Portal](https://portal.azure.com)을 방문하여 구독 아래에서 리소스를 수동으로 삭제합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [additional-resources](includes/additional-resources.md)]

Azure Web Apps 만들기에 대한 자세한 내용은 [Web Apps 개요]를 참조하세요.

<!-- URL List -->

[Azure Toolkit for IntelliJ]: azure-toolkit-for-intellij.md
[Azure Toolkit for Eclipse]: ../toolkit-for-eclipse/azure-toolkit-for-eclipse.md
[eclipse-hello-world]: ../toolkit-for-eclipse/create-hello-world-web-app.md
[Web Apps 개요]: /azure/app-service/app-service-web-overview
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/
[Legacy Version]: create-hello-world-web-app-legacy-version.md
[intelliJ-sign-in-instructions]: sign-in-instructions.md

<!-- IMG List -->
[marketplace]:media/create-hello-world-web-app/marketplace.png
[file-new-project]: media/create-hello-world-web-app/file-new-project.png
[maven-archetype-webapp]: media/create-hello-world-web-app/maven-archetype-webapp.png
[groupid-and-artifactid]: media/create-hello-world-web-app/groupid-and-artifactid.png
[maven-options]: media/create-hello-world-web-app/maven-options.png
[project-name]: media/create-hello-world-web-app/project-name.png
[open-index-page]: media/create-hello-world-web-app/open-index-page.png
[edit-index-page]: media/create-hello-world-web-app/edit-index-page.png
[deploy-to-azure-menu]: media/create-hello-world-web-app/run-on-web-app-menu.png
[deploy-to-azure-dialog]: media/create-hello-world-web-app/run-on-web-app-dialog.png
[deploy-to-existing-webapp]: media/create-hello-world-web-app/deploy-to-existing-webapp.png
[create-new-web-app-dialog]: media/create-hello-world-web-app/create-new-web-app-dialog.png
[successfully-deployed]: media/create-hello-world-web-app/successfully-deployed.png
[browse-web-app]: media/create-hello-world-web-app/browse-web-app.png
[edit-configuration-menu]: media/create-hello-world-web-app/edit-configuration-menu.png
[edit-configuration-dialog]: media/create-hello-world-web-app/edit-configuration-dialog.png
[clean-resources]: media/create-hello-world-web-app/clean-resource.png