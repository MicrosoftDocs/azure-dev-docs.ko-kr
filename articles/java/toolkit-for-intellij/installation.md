---
title: IntelliJ용 Azure 도구 키트 설치
description: Azure Toolkit for IntelliJ 플러그 인을 설치하여 클라우드 애플리케이션을 만들어 Azure에 배포하는 방법에 대해 알아봅니다.
documentationcenter: java
ms.assetid: c6817c7b-f28c-4c06-8216-41c7a8117de3
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 15e3bdc3f001b3fe93a96b27bf7fc44c7aaa8f3a
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379927"
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


## <a name="to-install-the-azure-toolkit-for-intellij-from-the-settings-dialog-box"></a>설정 대화 상자에서 IntelliJ용 Azure 도구 키트를 설치하려면

1. IntelliJ IDEA를 시작합니다.

1. IntelliJ IDEA가 열리면 **File**을 클릭한 다음 **Settings**를 클릭합니다.
   
   ![IntelliJ IDEA Settings 대화 상자 열기][01a]

1. 설정 대화 상자에서 **Plugins**를 클릭하고 **Browse repositories**를 클릭합니다.
   
   ![IntelliJ IDEA Settings 대화 상자][02a]

1. **Browse repositories** 대화 상자에서 검색 상자에 "Azure"를 입력합니다. **IntelliJ용 Azure 도구 키트**를 강조 표시하고 **Install**을 클릭합니다.
   
   ![IntelliJ용 Azure 도구 키트 검색][03]
   
   IntelliJ IDEA 대화 상자에서 설치 진행 상태가 표시됩니다.
   
   ![설치 진행률][04]

1. 설치가 완료되면 **Restart IntelliJ IDEA**를 클릭합니다.
   
   ![Restart IntelliJ IDEA][05]

1. **OK** 를 클릭하여 Settings 대화 상자를 닫습니다.
   
   ![IntelliJ IDEA Settings 대화 상자 닫기][06]

1. IntelliJ IDEA를 다시 시작할지 또는 연기할지 묻는 메시지가 나타나면 **Restart**를 클릭합니다.
   
1   ![Restart IntelliJ IDEA][07]

## <a name="to-install-the-azure-toolkit-for-intellij-from-the-start-screen"></a>시작 화면에서 IntelliJ용 Azure 도구 키트를 설치하려면

1. IntelliJ IDEA를 시작합니다.

1. IntelliJ IDEA 시작 화면이 나타나면 **Configure**를 클릭하고 **Plugins**를 클릭합니다.
   
   ![IntelliJ IDEA 플러그 인 설치][01b]

1. **Plugins** 대화 상자에서 **Browse repositories**를 클릭합니다.
   
   ![IntelliJ 아이디어 플러그 인 저장소 찾아보기][02b]

1. **Browse repositories** 대화 상자에서 검색 상자에 "Azure"를 입력합니다. **IntelliJ용 Azure 도구 키트**를 강조 표시하고 **Install**을 클릭합니다.
   
   ![IntelliJ용 Azure 도구 키트 검색][03]
   
   IntelliJ IDEA 대화 상자에서 설치 진행 상태가 표시됩니다.
   
   ![설치 진행률][04]

1. 설치가 완료되면 **Restart IntelliJ IDEA**를 클릭합니다.
   
   ![Restart IntelliJ IDEA][05]

1. IntelliJ IDEA를 다시 시작할지 또는 연기할지 묻는 메시지가 나타나면 **Restart**를 클릭합니다.
   
   ![Restart IntelliJ IDEA][07]

## <a name="next-steps"></a>다음 단계

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

<!-- IMG List -->

[01a]: media/installation/01-intellij-file-settings.png
[01b]: media/installation/01-intellij-configure-dropdown.png
[02a]: media/installation/02-intellij-settings-dialog.png
[02b]: media/installation/02-intellij-plugins-dialog.png
[03]: media/installation/03-intellij-browse-repositories.png
[04]: media/installation/04-install-progress.png
[05]: media/installation/05-restart-intellij.png
[06]: media/installation/06-intellij-settings-dialog.png
[07]: media/installation/07-restart-intellij.png
