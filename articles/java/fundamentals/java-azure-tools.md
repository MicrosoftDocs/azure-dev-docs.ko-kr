---
title: Java 개발자용 Azure 도구
description: Azure에서 작업하는 Java 개발자를 위한 IDE 통합, 에뮬레이터, 리소스 탐색기 및 명령줄 인터페이스입니다.
author: rloutlaw
ms.assetid: b55923b7-d60a-460d-b77c-af5fac67f1cc
ms.topic: article
ms.date: 11/13/2018
ms.custom: seo-java-july2019, devx-track-java
ms.openlocfilehash: 32f3b53ed58f9436aca82f08a3a4ce8600060632
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379877"
---
# <a name="azure-tools-for-java-developers"></a>Java 개발자용 Azure 도구

## <a name="supported-jdk-runtimes"></a>지원되는 JDK 런타임

Azure 및 Azure Stack에서 Java 개발자는 추가 지원 비용 없이 [OpenJDK의 Azul Systems Zulu Enterprise 빌드](https://www.azul.com/downloads/azure-only/zulu/)를 사용하여 프로덕션 Java 7, 8 및 11 애플리케이션을 빌드하고 실행할 수 있습니다. 현재 다른 JDK를 사용하여 Java 앱을 실행하는 경우 무료 지원 및 유지 관리를 위해 Azure에서 Zulu로 마이그레이션하는 것이 좋습니다.

Azure에서 개발하는 경우 사용 가능한 지원되는 JDK에 대한 자세한 내용은 <https://aka.ms/azure-jdks>를 참조하세요.

## <a name="eclipse-and-intellij-plugins"></a>Eclipse 및 IntelliJ 플러그 인

[Eclipse](/azure/developer/java/toolkit-for-eclipse) 및 [IntelliJ](/azure/developer/java/toolkit-for-intellij)용 Azure 도구 키트를 사용하여 Azure 리소스를 관리하고 IDE에서 앱을 배포합니다.

![Azure 탐색기를 보여 주는 IntelliJ 도구 키트](media/intelliJ-azure-explorer.png)

[Eclipse용 Azure 도구 키트 시작](/azure/app-service-web/app-service-web-eclipse-create-hello-world-web-app) | [IntelliJ용 Azure 도구 키트 시작](/azure/app-service-web/app-service-web-intellij-create-hello-world-web-app)

## <a name="visual-studio-code"></a>Visual Studio Code

[VS Code](https://code.visualstudio.com/)는 가벼우면서 강력한 코드 편집기로, MacOS, Windows, Linux에 사용할 수 있습니다. VS Code는 프로젝트 지원, 코드 완성, 디버깅, 린팅, 탐색을 제공하는 여러 확장을 통해 간단한 최신 Java 개발 워크플로를 지원합니다.

[VS Code 및 Java 시작](https://code.visualstudio.com/docs/java)
[VS Code용 Java 확장 팩](https://code.visualstudio.com/docs/java/extensions)

## <a name="azure-cli-20"></a>Azure CLI 2.0

Azure 2.0 CLI는 Azure 리소스를 관리하는 명령줄 환경을 제공합니다. [Azure Cloud Shell](/azure/cloud-shell/overview)을 실행하는 브라우저에서 사용하거나 macOS, Linux 및 Windows에서 [설치](/cli/azure/install-azure-cli)하고 명령줄에서 실행할 수 있습니다.

[Azure CLI 2.0 시작](/cli/azure/get-started-with-azure-cli)

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

바탕 화면에서 Azure Storage 계정, 컨테이너 및 Blob/파일을 관리합니다. Azure Storage Explorer는 현재 미리 보기로 있으며 Windows, macOS 및 Linux에서 작동합니다.

[Azure Storage Explorer 시작](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
