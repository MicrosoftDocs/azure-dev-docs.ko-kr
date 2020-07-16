---
title: Eclipse용 Azure 도구 키트 설치
description: Azure Toolkit for Eclipse 플러그 인을 설치하여 클라우드 애플리케이션을 만들어 Azure에 배포하는 방법에 대해 알아봅니다.
documentationcenter: java
ms.assetid: 9e93ff6a-f42b-4d99-b55b-624136b4a730
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 867fd12dbcf5963de36f500b96a577be141e967a
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86378277"
---
# <a name="installing-the-azure-toolkit-for-eclipse"></a>Eclipse용 Azure 도구 키트 설치

Azure Toolkit for Eclipse를 두 가지 방법으로 설치할 수 있습니다.

  - [Eclipse 마켓플레이스](#eclipse-marketplace)
  - [새 소프트웨어 설치](#install-new-software)

> [!NOTE] 
> 
> Eclipse용 Azure 도구 키트는 다음 URL에 있는 GitHub의 프로젝트 사이트를 통해 MIT 라이선스에 따라 소스 코드 사용이 허가된 오픈 소스 프로젝트입니다. 
> 
> <https://github.com/microsoft/azure-tools-for-java> 
> 

[!INCLUDE [basic-prerequisites](includes/basic-prerequisites.md)]

## <a name="eclipse-marketplace"></a>Eclipse 마켓플레이스

1. 다음 단추를 실행 중인 Eclipse 작업 영역으로 끌어옵니다.

    [![실행 중인 Eclipse* 작업 영역으로 끌어옵니다. *Eclipse 마켓플레이스 클라이언트 필요](https://marketplace.eclipse.org/sites/all/themes/solstice/public/images/marketplace/btn-install.png)](http://marketplace.eclipse.org/marketplace-client-intro?mpc_install=1919278 "실행 중인 Eclipse* 작업 영역으로 끌어옵니다. *Eclipse 마켓플레이스 클라이언트 필요")

2. 또는 **도움말/Eclipse Marketplace**에서 **Azure Toolkit for Eclipse 플러그인**을 검색하고 설치할 수도 있습니다.

    ![Marketplace](media/installation/marketplace.png)

## <a name="install-new-software"></a>새 소프트웨어 설치

1. Eclipse를 시작합니다.

1. 다음 그림에 표시된 대로 **Help** 메뉴를 클릭하고 **Install New Software**를 클릭합니다.

   ![Eclipse용 Azure 도구 키트 설치][01]

1. **Available Software** 대화 상자의 **Work with** 텍스트 상자에 `http://dl.microsoft.com/eclipse/`를 입력하고 **Enter** 키를 누릅니다.

1. **이름** 창에서 **Java용 Azure 도구 키트**를 선택하고 **Contact all update sites during install to find required software**를 선택하지 않습니다. 화면은 다음과 유사한 모양입니다.

   ![Eclipse용 Azure 도구 키트 설치][02]

1. **Eclipse용 Azure 도구 키트**를 확장하면 설치되는 구성 요소 목록이 표시되며, 다음은 그 예입니다.

   | 기능 | Description | 
   |---|---| 
   | **Java용 Application Insights 플러그 인** | 애플리케이션 및 서버 인스턴스에 Azure 원격 분석 로깅 및 분석 서비스를 사용할 수 있게 해줍니다. | 
   | **Azure 공통 플러그 인** | 다른 도구 키트 구성 요소에 필요한 공통 기능을 제공합니다. | 
   | **Eclipse용 Azure Container 도구** | .WAR을 docker 컴퓨터의 Docker 컨테이너로 빌드 및 배포할 수 있게 해줍니다. | 
   | **Eclipse용 Azure 컨테이너** | .WAR 또는 .JAR 아티팩트를 Azure 가상 머신에 Docker 컨테이너로 배포할 수 있게 해줍니다. | 
   | **Eclipse용 Azure 탐색기** | Azure 리소스를 관리할 수 있는 탐색기 스타일의 인터페이스를 제공합니다. | 
   | **SQL Server용 Microsoft JDBC Driver 6.1** | SQL Server용 JDBC API와 Java Platform Enterprise Edition 8용 Microsoft Azure SQL Database를 제공합니다. | 
   | **Java용 Microsoft Azure 라이브러리 패키지** | 스토리지, service bus, 서비스 런타임 등의 Microsoft Azure 서비스에 액세스하기 위한 API를 제공합니다. | 

1. **다음**을 클릭합니다. (도구 키트를 설치하는 동안 비정상적인 지연이 발생하는 경우에는 **Contact all update sites during install to find required software** 가 선택되어 있지 않은지 확인합니다.)

1. **설치 세부 정보** 대화 상자에서 **다음**을 클릭합니다.

   ![설치 세부 정보 검토][03]

1. **Review Licenses** 대화 상자에서 사용권 계약 조건을 검토합니다. 사용권 계약 조건에 동의하면 **동의함**을 클릭한 후 **마침**을 클릭합니다. (나머지 단계에서는 사용권 계약 조건에 동의한다고 가정합니다. 사용권 계약 조건에 동의하지 않으면 설치 프로세스를 종료합니다.)

   ![Review Licenses][04]

   Eclipse는 필수 패키지를 다운로드하고 설치합니다.

   ![설치 진행률][05]

1. 설치를 완료하기 위해 Eclipse를 다시 시작한다는 메시지가 표시되면 **Yes**를 클릭합니다.

   ![다시 시작 프롬프트][06]

## <a name="next-steps"></a>다음 단계

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

<!-- IMG List -->
[01]: media/installation/eclipse-installation-01.png
[02]: media/installation/eclipse-installation-02.png
[03]: media/installation/eclipse-installation-03.png
[04]: media/installation/eclipse-installation-04.png
[05]: media/installation/eclipse-installation-05.png
[06]: media/installation/eclipse-installation-06.png
