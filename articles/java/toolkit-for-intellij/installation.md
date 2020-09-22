---
title: IntelliJ용 Azure 도구 키트 설치
description: Azure Toolkit for IntelliJ 플러그 인을 설치하여 클라우드 애플리케이션을 만들어 Azure에 배포하는 방법에 대해 알아봅니다.
documentationcenter: java
ms.assetid: c6817c7b-f28c-4c06-8216-41c7a8117de3
ms.date: 09/09/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: fe8b07257ff3a9fc5523d13dd13e19982103ab05
ms.sourcegitcommit: a139e25190960ba89c9e31f861f0996a6067cd6c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534480"
---
# <a name="installing-the-azure-toolkit-for-intellij"></a>IntelliJ용 Azure 도구 키트 설치

Azure Toolkit for IntelliJ는 IntelliJ IDEA 개발 환경을 사용하여 클라우드 애플리케이션을 쉽게 작성, 개발, 테스트 및 Azure에 배포할 수 있는 템플릿과 기능을 제공합니다.

> [!NOTE] 
> 
> IntelliJ용 Azure 도구 키트는 다음 URL에 있는 GitHub의 프로젝트 사이트를 통해 MIT 라이선스에 따라 소스 코드 사용이 허가된 오픈 소스 프로젝트입니다. 
> 
> <https://github.com/microsoft/azure-tools-for-java> 
> 

IntelliJ용 Azure 도구 키트를 설치하는 두 가지 방법이 있는데, 하나는 **설정** 대화 상자를 사용하는 것이고 다른 하나는 시작 화면의 **구성** 메뉴를 사용하는 것입니다. 두 설치 방법 모두 다음 단계에서 소개합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure Toolkit for IntelliJ에는 다음 소프트웨어 구성 요소가 필요합니다.

* JDK(Java Development Kit) 8 이상 설치, 예: [OpenJDK](https://openjdk.java.net/) 또는 [Oracle JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition 또는 Community Edition 설치

> [!NOTE]
> 
> JetBrains 플러그인 리포지토리에 있는 [IntelliJ용 Azure Toolkit](https://plugins.jetbrains.com/plugin/8053) 페이지는 툴킷과 호환되는 빌드를 나열합니다.
> 

<!--
> [!IMPORTANT]
> 
> If you are using the Azure Toolkit for IntelliJ on Windows, the toolkit requires installing the Azure SDK 2.9.6 or later in order to use the Azure emulator. You have two options for installing the Azure SDK:
> 
> * You can download and install the Azure SDK by using the [Web Platform Installer (WebPI)](https://go.microsoft.com/fwlink/?LinkID=252838).
> * If you do not have the Azure SDK installed when you create your first Azure deployment project, you will be prompted to automatically download install the requisite version of the Azure SDK.
> 
> Note that the Azure SDK is only required on Windows.
> 
-->


## <a name="from-the-settings-dialog-box"></a>설정 대화 상자에서

1. IntelliJ 도구 모음에서 **파일**을 클릭한 다음, **설정**을 클릭합니다.

1. 설정 대화 상자의 왼쪽 탐색 메뉴에서 **플러그 인**을 클릭합니다.

1. **Marketplace** 검색 창에 "Azure"를 입력하여 플러그 인 목록을 필터링합니다. **Azure Toolkit for IntelliJ**를 선택한 다음, **설치**를 클릭합니다. IntelliJ의 *타사 플러그 인 개인 정보 노트*를 읽고 **수락**을 클릭합니다.

   :::image type="content" source="media/installation/03-intellij-search-plugin.png" alt-text="Azure Toolkit for IntelliJ 플러그 인을 검색합니다."::: 

1. 설치가 완료되면 **IDE 다시 시작**을 클릭합니다.

1. IntelliJ IDEA를 다시 시작하라는 메시지가 표시되면 **다시 시작**을 클릭합니다.
   
   :::image type="content" source="media/installation/07-restart-intellij.png" alt-text="IntelliJ IDEA를 다시 시작합니다."::: 

## <a name="from-the-start-screen"></a>시작 화면에서

1. IntelliJ IDEA 시작 화면에서 **구성**을 클릭한 다음, **플러그 인**을 클릭합니다.

   :::image type="content" source="media/installation/01-intellij-configure-dropdown.png" alt-text="시작 화면에서 플러그 인."::: 

1. **Marketplace** 검색 창에 "Azure"를 입력하여 플러그 인 목록을 필터링합니다. **Azure Toolkit for IntelliJ**를 선택한 다음, **설치**를 클릭합니다. IntelliJ의 *타사 플러그 인 개인 정보 노트*를 읽고 **수락**을 클릭합니다.

   :::image type="content" source="media/installation/01-intellij-start-screen-marketplace.png" alt-text="시작 화면에서 플러그 인 마켓플레이스.":::

1. 설치가 완료되면 **IDE 다시 시작**을 클릭합니다.

1. IntelliJ IDEA를 다시 시작하라는 메시지가 표시되면 **다시 시작**을 클릭합니다.
   
   :::image type="content" source="media/installation/01-intellij-start-screen-marketplace-restart.png" alt-text="시작 화면에서 설치하려면 다시 시작합니다.":::

## <a name="next-steps"></a>다음 단계

[!INCLUDE [additional-resources](includes/additional-resources.md)]

