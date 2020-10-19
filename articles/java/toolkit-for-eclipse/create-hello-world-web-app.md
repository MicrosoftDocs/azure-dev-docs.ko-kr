---
title: Eclipse를 사용하여 Azure App Service용 Hello World 웹앱 만들기
description: 이 자습서에서는Eclipse용 Azure 도구 키트를 사용하여 Azure용 Hello World 웹앱을 만드는 방법을 보여 줍니다.
services: app-service
keywords: Java, Eclipse, 웹앱, Azure App Service, Hello World, 빠른 시작
documentationcenter: java
author: selvasingh
ms.assetid: 20d41e88-9eab-462e-8ee3-89da71e7a33f
ms.reviewer: asirveda
ms.date: 08/25/2020
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: devx-track-java
ms.openlocfilehash: a318d5aa7f411add20ee201d48d3640983efefee
ms.sourcegitcommit: f460914ac5843eb7392869a08e3a80af68ab227b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92010259"
---
# <a name="create-a-hello-world-web-app-for-azure-app-service-using-eclipse"></a>Eclipse를 사용하여 Azure App Service용 Hello World 웹앱 만들기

이 문서에서는 [Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse)를 사용하여 기본 Hello World 웹앱을 만들고 이를 Azure App Service에 게시하는 데 필요한 단계를 보여 줍니다.

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

다음 단계에서는 Eclipse 개발 환경에서 Azure 로그인 프로세스를 안내합니다.

1. 플러그 인을 설치하지 않은 경우 [Azure Toolkit for Eclipse 설치](installation.md)를 참조하세요.

1. Azure 계정에 로그인하려면 **도구**, **Azure**, **로그인**을 차례로 클릭합니다.

   :::image type="content" source="media/sign-in-instructions/eclipse-azure-signin.png" alt-text="Eclipse IDE에서 Azure에 로그인":::

1. **Azure 로그인** 창에서 **디바이스 로그인**을 선택하고 **로그인**([다른 로그인 옵션](sign-in-instructions.md))을 클릭합니다.

1. **Azure 디바이스 로그인** 대화 상자에서 **복사 및 열기**를 클릭합니다.

1. 브라우저에서, 마지막 단계에서 **복사 및 열기**를 클릭할 때 복사한 디바이스 코드를 붙여넣은 후 **다음**을 클릭합니다.

1. Azure 계정을 선택하고, 로그인하는 데 필요한 모든 인증 절차를 완료합니다.

1. 로그인하면 브라우저를 닫고 Eclipse IDE로 다시 전환합니다. **구독 선택** 대화 상자에서 사용하려는 구독을 선택한 다음, **선택**을 클릭합니다.

### <a name="install-required-software-optional"></a>필수 소프트웨어 설치 *(선택 사항)*

웹앱 프로젝트를 사용하는 데 필요한 구성 요소가 있는지 확인하려면 다음 단계를 수행합니다.

1. **도움말** 메뉴를 클릭한 다음, **새 소프트웨어 설치**를 클릭합니다.

1. **사용 가능한 소프트웨어** 대화 상자에서 **관리**를 클릭하고, 최신 Eclipse 버전(예: *2020-06*)이 선택되어 있는지 확인합니다.

1. **적용 및 닫기**를 클릭합니다. *작업:* 드롭다운 메뉴를 펼쳐 제안된 사이트를 표시합니다. 사용 가능한 소프트웨어를 쿼리하려면 최신 Eclipse 버전 사이트를 선택합니다.

1. 목록을 아래로 스크롤하여 **웹, XML, Java EE 및 OSGi Enterprise Development** 항목을 선택합니다. **다음**을 클릭합니다.

1. [설치 세부 정보] 창에서 **다음**을 클릭합니다.

1. Review Licenses(라이선스 검토) 대화 상자에서 사용권 계약의 약관을 검토합니다. 사용권 계약 조건에 동의하면 **동의함**을 클릭한 후 **마침**을 클릭합니다. 

   > [!NOTE]
   > Eclipse 작업 영역의 오른쪽 아래 모서리에서 설치 진행률을 확인할 수 있습니다.

1. Eclipse를 다시 시작하여 설치를 완료 하라는 메시지가 나타나면 **Restart Now(지금 다시 시작)** 를 클릭합니다.

## <a name="creating-a-web-app-project"></a>웹앱 프로젝트 만들기

1. **파일**을 클릭하고, **새로 만들기**를 펼친 다음, **...프로젝트**를 클릭합니다. [새 프로젝트] 대화 상자 창에서 **웹**을 펼치고, **동적 웹 프로젝트**를 선택하고, **다음**을 클릭합니다.

   > [!TIP]
   > **웹**이 사용 가능한 프로젝트로 표시되지 않으면 [이 섹션](#install-required-software-optional)을 참조하여 필수 Eclipse 소프트웨어가 있는지 확인하세요.

1. 이 자습서에서는 프로젝트의 이름을 **MyWebApp**으로 지정합니다. 화면이 다음과 유사하게 나타납니다.
   
   ![새 동적 웹 프로젝트 속성][dynamic-web-project-properties]

1. **마침**을 클릭합니다.

1. 왼쪽 패키지 탐색기 창에서 **MyWebApp**을 펼칩니다. 마우스 오른쪽 단추로 **WebContent**를 클릭하고, 마우스로 **새로 만들기** 위를 가리킨 다음, **기타...** 를 클릭합니다.

1. **웹**을 펼쳐 **JSP 파일** 옵션을 찾습니다. **다음**을 클릭합니다.

1. **새 JSP 파일** 대화 상자에서 **index.jsp** 파일의 이름을 지정하고 부모 폴더를 **MyWebApp/WebContent**로 유지한 후 **다음**을 클릭합니다.

   ![새 JSP 파일 대화 상자][new-jsp-file-dialog]

1. 이 자습서의 목적을 위해 **JSP 템플릿 선택** 대화 상자에서 **새 JSP 파일(html 5)** 을 선택한 다음, **마침**을 클릭합니다.

1. Eclipse에서 index.jsp 파일이 열리면 **Hello World!** 를 동적으로 표시하도록 텍스트를 추가합니다. 기존 `<body>` 요소 내. 업데이트된 `<body>` 콘텐츠는 다음 예제와 유사하게 표시됩니다.
   
   ```jsp
   <body>
   <b><% out.println("Hello World!"); %></b>
   </body>
   ```
1. index.jsp를 저장합니다.

## <a name="deploying-the-web-app-to-azure"></a>Azure에 웹앱 배포

1. 왼쪽 패키지 탐색기 창에서 마우스 오른쪽 단추로 프로젝트를 클릭한 다음, **Azure**, **Azure Web App으로 게시**를 차례로 선택합니다.
   
   ![Azure 웹앱으로 게시][publish-as-azure-web-app]

1. **웹앱 배포** 대화 상자가 나타나면 다음 옵션 중 하나를 선택할 수 있습니다.

   * 기존 웹앱이 있으면 기존 웹앱을 선택합니다.

   * 기존 웹앱이 없는 경우 **만들기**를 클릭합니다.

      여기서는 런타임 환경, 앱 서비스 계획, 리소스 그룹 및 앱 설정을 구성할 수 있습니다. 필요한 경우 새 리소스를 만듭니다.

      **App Service 만들기** 대화 상자에서 웹앱에 대한 필수 정보를 지정한 후 **만들기**를 클릭합니다.

1. 웹앱을 선택한 다음 **배포**를 클릭합니다.

1. 웹앱이 성공적으로 배포되면 도구 키트가 **Azure 활동 로그** 탭 아래에 **게시됨** 상태를 표시하며, 이것은 배포된 웹앱의 URL에 대한 하이퍼링크입니다.

1. 상태 메시지에 제공된 링크를 사용하여 웹앱으로 이동할 수 있습니다.

   ![웹앱 찾아보기][browse-web-app]

## <a name="cleaning-up-resources"></a>리소스 정리

1. Azure에 웹앱을 게시한 후에는 Azure Explorer에서 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 옵션 중 하나를 선택하여 웹앱을 관리할 수 있습니다. 예를 들어 여기서 웹앱을 **삭제**하여 이 자습서의 리소스를 정리할 수 있습니다.

   ![앱 서비스 관리][manage-app-service]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="next-steps"></a>다음 단계

[!INCLUDE [additional-resources](includes/additional-resources.md)]

Azure Web Apps 만들기에 대한 자세한 내용은 [Web Apps 개요]를 참조하세요.

<!-- URL List -->

[Azure Toolkit for Eclipse]: /azure/developer/java/tookit-for-eclipse
[Azure Toolkit for IntelliJ]: ../toolkit-for-intellij
[intellij-hello-world]: ../toolkit-for-intellij/create-hello-world-web-app.md
[Web Apps 개요]: /azure/app-service/app-service-web-overview
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/

<!-- IMG List -->

[browse-web-app]: media/create-hello-world-web-app/browse-web-app.png
[dynamic-web-project-properties]: media/create-hello-world-web-app/dynamic-web-project-properties.png
[new-jsp-file-dialog]: media/create-hello-world-web-app/new-jsp-file-dialog.png
[publish-as-azure-web-app]: media/create-hello-world-web-app/publish-as-azure-web-app.png
[publish-status]: media/create-hello-world-web-app/publish-status.png
[manage-app-service]: media/create-hello-world-web-app/manage-app-service.png
