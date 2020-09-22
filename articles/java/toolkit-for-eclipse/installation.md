---
title: Eclipse용 Azure 도구 키트 설치
description: Azure Toolkit for Eclipse 플러그 인을 설치하여 클라우드 애플리케이션을 만들어 Azure에 배포하는 방법에 대해 알아봅니다.
documentationcenter: java
ms.assetid: 9e93ff6a-f42b-4d99-b55b-624136b4a730
ms.date: 08/25/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 27793d827b60a5977968529377b20c7033170ffe
ms.sourcegitcommit: a139e25190960ba89c9e31f861f0996a6067cd6c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534672"
---
# <a name="installing-the-azure-toolkit-for-eclipse"></a>Eclipse용 Azure 도구 키트 설치

Azure Toolkit for Eclipse는 Eclipse 개발 환경을 사용하여 간단하고 가용성이 높고 확장성 있는 Java 웹앱 및 HDInsight Spark 작업을 쉽게 생성, 개발, 구성, 테스트 및 Azure에 배포할 수 있는 기능을 제공합니다.

> [!NOTE] 
> 
> Eclipse용 Azure 도구 키트는 다음 URL에 있는 GitHub의 프로젝트 사이트를 통해 MIT 라이선스에 따라 소스 코드 사용이 허가된 오픈 소스 프로젝트입니다. 
> 
> <https://github.com/microsoft/azure-tools-for-java> 
> 

[!INCLUDE [basic-prerequisites](includes/basic-prerequisites.md)]

Azure Toolkit for Eclipse를 설치하는 방법에는 두 가지 방법, 즉 **Eclipse 마켓플레이스**에 액세스하는 방법 및 도움말 메뉴에서 **새 소프트웨어 설치** 옵션을 사용하는 방법이 있습니다. 다음 섹션에서는 두 가지 설치 방법을 모두 보여 줍니다.

## <a name="eclipse-marketplace"></a>Eclipse 마켓플레이스

Eclipse IDE에서 Eclipse 마켓플레이스 마법사를 사용하면 [Eclipse 마켓플레이스](https://marketplace.eclipse.org/)를 찾아보고 솔루션을 설치할 수 있습니다. 다음 두 가지 옵션을 사용하여 Eclipse 마켓플레이스로 이동할 수 있습니다.

   * 다음 단추를 실행 중인 Eclipse 작업 영역으로 끌어옵니다. 이 단추를 사용하면 Azure Toolkit for Eclipse가 이미 선택된 Eclipse 마켓플레이스가 열립니다.

      [![실행 중인 Eclipse* 작업 영역으로 끌어옵니다. *Eclipse 마켓플레이스 클라이언트 필요](https://marketplace.eclipse.org/sites/all/themes/solstice/public/images/marketplace/btn-install.png)](http://marketplace.eclipse.org/marketplace-client-intro?mpc_install=1919278 "실행 중인 Eclipse* 작업 영역으로 끌어옵니다. *Eclipse 마켓플레이스 클라이언트 필요")

   * Eclipse IDE에서 **도움말** 메뉴를 클릭하고, **Eclipse 마켓플레이스**로 이동하여 "Azure Toolkit for Eclipse"를 검색한 다음, **설치**를 클릭합니다.

      :::image type="content" source="media/installation/eclipse-marketplace-button.png" alt-text="마켓플레이스 창, 도움말 메뉴."::: 

1. Eclipse 마켓플레이스 마법사에서 설치될 구성 요소 목록이 포함된 설치 지침을 표시합니다. 모든 기능이 선택되어 있는지 확인하고 **확인 >** 을 클릭합니다.

   | 기능 | Description | 
   |---|---| 
   | **Java용 Application Insights 플러그 인** | 애플리케이션 및 서버 인스턴스에 Azure 원격 분석 로깅 및 분석 서비스를 사용할 수 있게 해줍니다. | 
   | **Azure 공통 플러그 인** | 다른 도구 키트 구성 요소에 필요한 공통 기능을 제공합니다. | 
   | **Eclipse용 Azure Container 도구** | .WAR을 docker 컴퓨터의 Docker 컨테이너로 빌드 및 배포할 수 있게 해줍니다. | 
   | **Eclipse용 Azure 컨테이너** | .WAR 또는 .JAR 아티팩트를 Azure 가상 머신에 Docker 컨테이너로 배포할 수 있게 해줍니다. | 
   | **Eclipse용 Azure 탐색기** | Azure 리소스를 관리할 수 있는 탐색기 스타일의 인터페이스를 제공합니다. | 
   | **Java용 Azure HDInsight 플러그 인** | Scala에서 Apache Spark 애플리케이션 개발을 사용하도록 설정합니다. |
   | **SQL Server용 Microsoft JDBC Driver 6.1** | SQL Server용 JDBC API와 Java Platform Enterprise Edition 8용 Microsoft Azure SQL Database를 제공합니다. | 
   | **Java용 Microsoft Azure 라이브러리 패키지** | 스토리지, service bus, 서비스 런타임 등의 Microsoft Azure 서비스에 액세스하기 위한 API를 제공합니다. | 
   | **Eclipse용 WebApp 플러그 인** | 웹 애플리케이션을 Azure App Services로 배포하도록 설정합니다. | 

1. **Review Licenses** 대화 상자에서 사용권 계약 조건을 검토합니다. 사용권 계약 조건에 동의하면 **서비스 계약 조건에 동의합니다.** 를 클릭한 다음, **마침**을 클릭합니다. 

   > [!NOTE]
   > Eclipse 작업 영역의 오른쪽 아래 모서리에서 설치 진행률을 확인할 수 있습니다.

4. 설치가 완료되면 Eclipse IDE를 다시 시작하여 소프트웨어 업데이트를 적용하라는 메시지가 표시됩니다. **지금 다시 시작**을 클릭합니다.

## <a name="install-new-software"></a>새 소프트웨어 설치

*도움말* 메뉴에서 직접 Azure Toolkit for Eclipse를 새 소프트웨어 유형으로 설치할 수 있습니다.

1. **도움말** 메뉴를 클릭한 다음, **새 소프트웨어 설치**를 클릭합니다.

   :::image type="content" source="media/installation/eclipse-install-software-button.png" alt-text="새 소프트웨어 설치, 도움말 메뉴"::: 

1. **사용 가능한 소프트웨어** 대화 상자의 **작업** 텍스트 상자에서 `http://dl.microsoft.com/eclipse/`를 입력합니다.

1. **이름** 창에서 **Java용 Azure 도구 키트**를 선택하고 **Contact all update sites during install to find required software**를 선택하지 않습니다. 화면은 다음과 유사한 모양입니다.

   ![Eclipse용 Azure 도구 키트 설치][02]

1. **Azure Toolkit for Java**를 펼치면 설치되는 구성 요소 목록이 표시됩니다. 예를 들어 다음과 같습니다.

   | 기능 | Description | 
   |---|---| 
   | **Java용 Application Insights 플러그 인** | 애플리케이션 및 서버 인스턴스에 Azure 원격 분석 로깅 및 분석 서비스를 사용할 수 있게 해줍니다. | 
   | **Azure 공통 플러그 인** | 다른 도구 키트 구성 요소에 필요한 공통 기능을 제공합니다. | 
   | **Eclipse용 Azure Container 도구** | .WAR을 docker 컴퓨터의 Docker 컨테이너로 빌드 및 배포할 수 있게 해줍니다. | 
   | **Eclipse용 Azure 컨테이너** | .WAR 또는 .JAR 아티팩트를 Azure 가상 머신에 Docker 컨테이너로 배포할 수 있게 해줍니다. | 
   | **Eclipse용 Azure 탐색기** | Azure 리소스를 관리할 수 있는 탐색기 스타일의 인터페이스를 제공합니다. | 
   | **Java용 Azure HDInsight 플러그 인** | Scala에서 Apache Spark 애플리케이션 개발을 사용하도록 설정합니다. |
   | **SQL Server용 Microsoft JDBC Driver 6.1** | SQL Server용 JDBC API와 Java Platform Enterprise Edition 8용 Microsoft Azure SQL Database를 제공합니다. | 
   | **Java용 Microsoft Azure 라이브러리 패키지** | 스토리지, service bus, 서비스 런타임 등의 Microsoft Azure 서비스에 액세스하기 위한 API를 제공합니다. | 
   | **Eclipse용 WebApp 플러그 인** | 웹 애플리케이션을 Azure App Services로 배포하도록 설정합니다. | 

1. **다음**을 클릭합니다. (도구 키트를 설치하는 동안 비정상적인 지연이 발생하는 경우에는 **Contact all update sites during install to find required software** 가 선택되어 있지 않은지 확인합니다.)

1. **설치 세부 정보** 대화 상자에서 **다음**을 클릭합니다.

1. **Review Licenses** 대화 상자에서 사용권 계약 조건을 검토합니다. 사용권 계약 조건에 동의하면 **동의함**을 클릭한 후 **마침**을 클릭합니다. (나머지 단계에서는 사용권 계약 조건에 동의한다고 가정합니다. 사용권 계약 조건에 동의하지 않으면 설치 프로세스를 종료합니다.)

   > [!NOTE]
   > Eclipse 작업 영역의 오른쪽 아래 모서리에서 설치 진행률을 확인할 수 있습니다.

1. Eclipse를 다시 시작하여 설치를 완료 하라는 메시지가 나타나면 **Restart Now(지금 다시 시작)** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

<!-- IMG List -->
[02]: media/installation/eclipse-installation-02.png
