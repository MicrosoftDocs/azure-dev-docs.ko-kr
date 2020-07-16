---
title: Eclipse를 사용하여 Azure App Service용 Hello World 웹앱 만들기
description: 이 자습서에서는Eclipse용 Azure 도구 키트를 사용하여 Azure용 Hello World 웹앱을 만드는 방법을 보여 줍니다.
services: app-service
keywords: Java, Eclipse, 웹앱, Azure App Service, Hello World, 빠른 시작
documentationcenter: java
author: selvasingh
ms.assetid: 20d41e88-9eab-462e-8ee3-89da71e7a33f
ms.reviewer: asirveda
ms.date: 02/01/2018
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: devx-track-java
ms.openlocfilehash: ea650fdf86f0a614303f94f67792f2c05827576e
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86378397"
---
# <a name="create-a-hello-world-web-app-for-azure-app-service-using-eclipse"></a>Eclipse를 사용하여 Azure App Service용 Hello World 웹앱 만들기

오픈 소스형 [Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) 플러그인을 사용하여 몇 분 만에 기본 Hello World 애플리케이션을 만들고 Azure App Service에 웹앱으로 배포할 수 있습니다.

> [!NOTE]
>
> IntelliJ IDEA 사용을 선호하는 경우 [유사한 IntelliJ 자습서][intellij-hello-world]를 참조하세요.
>
>[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]
>
> 이 자습서를 완료한 후 반드시 리소스를 정리해야 합니다. 이 경우 이 가이드 실행은 체험 계정 할당량을 초과하지 않습니다.
>

[!INCLUDE [basic-prerequisites](includes/basic-prerequisites.md)]

## <a name="installation-and-sign-in"></a>설치 및 로그인

1. 다음 단추를 실행 중인 Eclipse 작업 영역으로 끌어와서 Azure Toolkit for Eclipse 플러그인([다른 설치 옵션](installation.md))을 설치합니다.

    [![실행 중인 Eclipse* 작업 영역으로 끌어옵니다. *Eclipse 마켓플레이스 클라이언트 필요](https://marketplace.eclipse.org/sites/all/themes/solstice/public/images/marketplace/btn-install.png)](http://marketplace.eclipse.org/marketplace-client-intro?mpc_install=1919278 "실행 중인 Eclipse* 작업 영역으로 끌어옵니다. *Eclipse 마켓플레이스 클라이언트 필요")

1. Azure 계정에 로그인하려면 **도구**, **Azure**, **로그인**을 차례로 클릭합니다.
   ![Azure 로그인을 위한 Eclipse 메뉴][I01]

1. **Azure 로그인** 창에서 **디바이스 로그인**을 선택하고 **로그인**([다른 로그인 옵션](sign-in-instructions.md))을 클릭합니다.

   ![디바이스 로그인을 선택한 Azure 로그인 창][I02]

1. **Azure 디바이스 로그인** 대화 상자에서 **복사 및 열기**를 클릭합니다.

   ![Azure 로그인 대화 상자 창][I03]

1. 브라우저에서, 마지막 단계에서 **복사 및 열기**를 클릭할 때 복사한 디바이스 코드를 붙여넣은 후 **다음**을 클릭합니다.

   ![디바이스 로그인 브라우저][I04]

1. 끝으로 **구독 선택** 대화 상자에서 사용하려는 구독을 선택한 다음, **확인**을 클릭합니다.

   ![구독 선택 대화 상자][I05]

## <a name="creating-web-app-project"></a>웹앱 프로젝트 만들기

1. **파일**, **새로 만들기**, **동적 웹 프로젝트**를 차례로 클릭합니다. (**File**, **New**를 차례로 클릭한 후 **Dynamic Web Project**가 사용 가능한 프로젝트로 표시되지 않는 경우 **File**, **New**, **Project...** 를 차례로 클릭한 후 **Web**을 확장하고 **Dynamic Web Project**를 클릭한 후 **Next**를 클릭합니다.)

   ![새로운 동적 웹 프로젝트 만들기][file-new-dynamic-web-project]

2. 이 자습서에서는 프로젝트의 이름을 **MyWebApp**으로 지정합니다. 화면이 다음과 유사하게 나타납니다.
   
   ![새 동적 웹 프로젝트 속성][dynamic-web-project-properties]

3. **Finish**를 클릭합니다.

4. Eclipse의 Project Explorer 뷰 내에서 **MyWebApp**을 확장합니다. **WebContent**를 마우스 오른쪽 단추로 클릭하고 **New**를 클릭한 후 **JSP File**을 클릭합니다.

   ![새 JSP 파일 만들기][create-new-jsp-file]

5. **새 JSP 파일** 대화 상자에서 **index.jsp** 파일의 이름을 지정하고 부모 폴더를 **MyWebApp/WebContent**로 유지한 후 **다음**을 클릭합니다.

   ![새 JSP 파일 대화 상자][new-jsp-file-dialog]

6. **JSP 템플릿 선택** 대화 상자에서 이 자습서의 목적에 따라, **새 JSP 파일(html)** 을 선택한 후 **마침**을 클릭합니다.

   ![JSP 템플릿 선택][select-jsp-template]

7. Eclipse에서 index.jsp 파일이 열리면 **Hello World!** 를 동적으로 표시하도록 텍스트를 추가합니다. 기존 `<body>` 요소 내. 업데이트된 `<body>` 콘텐츠는 다음 예제와 유사하게 표시됩니다.
   
   ```jsp
   <body><b><% out.println("Hello World!"); %></b></body>
   ```

8. index.jsp를 저장합니다.

## <a name="deploying-web-app-to-azure"></a>Azure에 웹앱 배포

1. Eclipse의 Project Explorer 보기 내에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Azure**, **Azure Web App으로 게시**를 차례로 선택합니다.
   
   ![Azure 웹앱으로 게시][publish-as-azure-web-app]

1. **웹앱 배포** 대화 상자가 나타나면 다음 옵션 중 하나를 선택할 수 있습니다.

   * 기존 웹앱이 있으면 기존 웹앱을 선택합니다.

      ![앱 서비스 선택][select-app-service]

   * **새 웹앱 만들기**를 클릭합니다.

      ![App Service 만들기][create-app-service]

      **App Service 만들기** 대화 상자에서 웹앱에 대한 필수 정보를 지정한 후 **만들기**를 클릭합니다.

      여기서 런타임 환경, 앱 설정, 서비스 계획 및 리소스 그룹을 구성할 수 있습니다.

      ![App Service 만들기 대화 상자][create-app-service-dialog]

1. 웹앱을 선택한 다음 **배포**를 클릭합니다.

   ![앱 서비스 배포][deploy-app-service]

1. 웹앱이 성공적으로 배포되면 도구 키트가 **Azure 활동 로그** 탭 아래에 **게시됨** 상태를 표시하며, 이것은 배포된 웹앱의 URL에 대한 하이퍼링크입니다.

   ![게시 상태][publish-status]

1. 상태 메시지에 제공된 링크를 사용하여 웹앱으로 이동할 수 있습니다.

   ![웹앱 찾아보기][browse-web-app]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="cleaning-up-resources"></a>리소스 정리

1. Azure에 웹앱을 게시한 후에는 Azure Explorer에서 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 옵션 중 하나를 선택하여 웹앱을 관리할 수 있습니다. 예를 들어 여기서 웹앱을 **삭제**하여 이 자습서의 리소스를 정리할 수 있습니다.

   ![앱 서비스 관리][manage-app-service]

## <a name="next-steps"></a>다음 단계

[!INCLUDE [additional-resources](includes/additional-resources.md)]

Azure Web Apps 만들기에 대한 자세한 내용은 [Web Apps 개요]를 참조하세요.

<!-- URL List -->

[Azure Toolkit for Eclipse]: azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ../toolkit-for-intellij
[intellij-hello-world]: ../toolkit-for-intellij/create-hello-world-web-app.md
[Web Apps 개요]: /azure/app-service/app-service-web-overview
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/
[Legacy Version]: create-hello-world-web-app-legacy-version.md

<!-- IMG List -->
[I01]: media/sign-in-instructions/I01.png
[I02]: media/sign-in-instructions/I02.png
[I03]: media/sign-in-instructions/I03.png
[I04]: media/sign-in-instructions/I04.png
[I05]: media/sign-in-instructions/I05.png

[browse-web-app]: media/create-hello-world-web-app/browse-web-app.png
[file-new-dynamic-web-project]: media/create-hello-world-web-app/file-new-dynamic-web-project.png
[dynamic-web-project-properties]: media/create-hello-world-web-app/dynamic-web-project-properties.png
[create-new-jsp-file]: media/create-hello-world-web-app/create-new-jsp-file.png
[new-jsp-file-dialog]: media/create-hello-world-web-app/new-jsp-file-dialog.png
[select-jsp-template]: media/create-hello-world-web-app/select-jsp-template.png
[publish-as-azure-web-app]: media/create-hello-world-web-app/publish-as-azure-web-app.png
[deploy-web-app-dialog]: media/create-hello-world-web-app/deploy-web-app-dialog.png
[select-app-service]: media/create-hello-world-web-app/select-app-service.png
[create-app-service-dialog]: media/create-hello-world-web-app/create-app-service-dialog.png
[publish-status]: media/create-hello-world-web-app/publish-status.png
[create-app-service]: media/create-hello-world-web-app/create-app-service.png
[deploy-app-service]: media/create-hello-world-web-app/deploy-app-service.png
[manage-app-service]: media/create-hello-world-web-app/manage-app-service.png
