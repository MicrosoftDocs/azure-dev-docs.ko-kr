---
title: Azure 개발을 위한 Java JDK 및 중장기 지원
description: 이 문서에서는 Java 애플리케이션을 개발하고 실행하기 위한 Azure 지원에 대한 다운로드 및 설명을 제공합니다.
ms.date: 04/09/2019
ms.topic: conceptual
ms.custom: seo-java-september2019, devx-track-java
ms.openlocfilehash: 5bffb4e4d2f68ef61ea96ededdf51ea98bb72d2a
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379837"
---
# <a name="java-long-term-support-and-medium-term-support-on-azure-and-azure-stack"></a>Azure 및 Azure Stack에 대한 Java 장기 지원 및 중기 지원

Azure 및 Azure Stack의 Java 개발자는 추가 지원 비용을 들이지 않고 [Azul Zulu for Azure - Enterprise Edition](https://www.azul.com/downloads/azure-only/zulu/) JDK 빌드를 사용하여 프로덕션 Java 애플리케이션을 빌드하고 실행할 수 있습니다. Azure에서 원하는 모든 Java 런타임을 사용할 수 있습니다. 그러나 Zulu를 사용하면 추가 비용 없이 유지 관리 업데이트가 제공되며 Microsoft에서 지원 티켓을 만들 수 있습니다.

LTS(장기 지원)로 지정된 릴리스는 Oracle 및 OpenJDK 커뮤니티에서 지정한 LTS 릴리스와 동일합니다. LTS 릴리스의 경우 필요에 따라 버그 수정, 보안 업데이트 및 기타(프로덕션 지원) 수정에 8년 이상 액세스할 수 있습니다. 또한 최신 JDK 버전(확장 지원)으로 마이그레이션하는 사용자에게 2년간의 추가 지원을 제공합니다.

MTS(중기 지원)로 지정된 릴리스의 경우 다음 LTS 릴리스가 일반 공급된 후 1.5년 이상의 프로덕션 지원을 제공합니다. 또한 1년간의 연장 지원을 추가로 제공합니다.

> [!div class="nextstepaction"]
> [Java 다운로드 및 설치](java-jdk-install.md)

## <a name="long-term-support-lts"></a>LTS(장기 지원)

* [Java 11](https://www.azul.com/downloads/azure-only/zulu/?&version=java-11-lts)
* [Java 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts)
* [Java 7](https://www.azul.com/downloads/azure-only/zulu/?&version=java-7-lts)

## <a name="medium-term-support-mts"></a>MTS(중기 지원)

* [Java 13](https://www.azul.com/downloads/azure-only/zulu/?&version=java-13)

## <a name="technical-preview"></a>기술 미리 보기

* [Java 14](https://www.azul.com/downloads/azure-only/zulu/?version=java-14)

## <a name="what-is-the-zulu-openjdk-for-azure"></a>Azure용 Zulu OpenJDK란 무엇인가요?

OpenJDK의 Azul Zulu for Azure - Enterprise Edition 빌드는 Azure 및 Azure Stack용 OpenJDK의 무료 다중 플랫폼 프로덕션 준비 배포입니다. 이는 Microsoft 및 Azul Systems에서 지원합니다. 해당 배포는 다음과 같습니다.

* JDK(Java Development Kit), JRE(Java Runtime Environment) 및 헤드리스 JRE로 패키지된 OpenJDK의 100% 오픈 소스 빌드. 이 이진 파일은 Java 애플리케이션 또는 Azure 및 Azure Stack의 구성 요소와 함께 사용할 수 있는 Java SE(Standard Edition)의 완전히 호환되고 상용으로 적합한 빌드입니다.
* 버그 수정, 향상된 성능 및 보안 패치를 포함하여 장기 지원이 제공됩니다.
* Windows, Linux 및 MacOS에서 Java 애플리케이션을 개발하고 실행하는 데 사용할 수 있습니다.
* Docker Hub의 컨테이너 이미지 및 Azure Marketplace의 가상 머신(Windows 및 Linux)으로 사용할 수 있습니다.
* Microsoft Azure에서 다음과 같은 많은 Azure 서비스를 지원하기 위해 사용합니다.
  * Windows의 App Service
  * Linux의 App Service
  * Azure 기능
  * Azure Service Fabric
  * Azure HDInsight
  * Azure Cognitive Search
  * Azure DevOps
  * Azure Cloud Shell  

## <a name="supported-java-versions-and-update-schedule"></a>지원되는 Java 버전 및 업데이트 일정

Azul Systems는 Java SE 7, 8, 11 및 13을 포함하여 Java의 모든 LTS(장기 지원) 및 MTS(중기 지원) 버전에 대해 완벽하게 지원되는 [Azul Zulu for Azure - Enterprise Edition](https://www.azul.com/downloads/azure-only/zulu/) 빌드를 제공합니다. 자세한 정보는 [Azul 보도 자료](https://www.azul.com/press_release/free-java-production-support-for-microsoft-azure-azure-stack) 및 [Azul 상품 지원 주기](https://www.azul.com/products/azul_support_roadmap/) 로드맵을 참조하세요.

|Java SE 버전  |다음 날짜까지 지원  |
|---------|----------|
|[![Java 7 로고](media/supported-java-versions-java-7.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-7-lts) |2023년 7월(LTS)|
|[![Java 8 로고](media/supported-java-versions-java-8.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts) |2030년 12월(LTS)|
|[![Java 11 로고](media/supported-java-versions-java-11.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-11-lts) |2027년 9월(LTS)|
|[![Java 13 로고](media/supported-java-versions-java-13.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-13) |2023년 3월(MTS)|
|[![Java 14 로고](media/supported-java-versions-java-14.png)](https://www.azul.com/downloads/azure-only/zulu/?version=java-14) |**미리 보기**|

LTS 및 MTS JDK 릴리스에는 필요에 따라 분기별 보안 업데이트, 버그 수정 및 중요한 대역 외 업데이트 및 패치가 포함됩니다. 이 지원에는 보안 업데이트의 Java 7 및 8에 대한 백포트 및 Java 11과 같은 최신 버전의 Java에서 보고된 버그 수정이 포함됩니다. 이 백포팅은 Java 이전 버전의 지속적인 안정성과 보안을 보장합니다. Azure 고객은 계획되지 않은 모든 Java SE 구독 요금이 발생하지 않고 이러한 보안 업데이트 및 플랫폼 버그 수정을 가져올 수 있습니다.

현재 Azure Functions에는 Java 8이 필요하며 Java 11에 대한 지원은 아직 개발 중입니다.

## <a name="benefits-for-developers"></a>개발자에 대한 혜택

Azul Zulu for Azure - Enterprise Edition JDK 릴리스는 다음과 같습니다.

- Microsoft 및 Azul Systems 모두에서 지원

   * Zulu 이진 파일은 프로덕션 준비 상태이며 Microsoft 및 Azul Systems에서 지원합니다.
   * Zulu에는 추가 비용 없이 Java 7, 8 및 11에 대한 LTS(장기 지원) 및 Java 13에 대한 MTS(중기 지원)가 제공됩니다. (LTS는 Java 17에도 제공됩니다.) Java 버전은 필요한 경우에만 업그레이드할 수 있습니다.
   * Java 7은 2023년 7월까지 지원됩니다. Java 8은 2030년 12월까지 지원됩니다. Java 11은 2027년 9월까지 지원됩니다. Java 13은 2023년 3월까지 지원됩니다.
   * Microsoft는 많은 Azure 서비스 기반 머신에서 내부적으로 Zulu 실행을 약속했습니다.

- 프로덕션 준비 상태

   * OpenJDK의 해당 빌드에 대한 100% 오픈 소스
   * 많은 Java SE 배포에 대한 드롭인 교체.
   * JDK, JRE 및 헤드리스 JRE
   * Java 7, 8, 11 및 13.
   * OpenJDK Community TCK(Technology Compatibility Kit)를 통해 Java SE 사양을 준수하는 것으로 확인되었습니다.
   * Java SE 7, 8, 11 및 13에 대한 버그 수정, 향상된 성능 및 보안 패치를 포함하여 Java SE에 대한 프로덕션 업데이트가 포함됩니다.

- 다중 플랫폼 지원. Zulu는 여러 플랫폼 및 버전에 대한 이진 파일을 지원합니다.

   * Windows
     * 10
     * 8.1
     * 8
     * 7
   * Windows Server
     * 2016 R2
     * 2016
     * 2012 R2
     * 2012
     * 2008 R2
   * Linux
     * RHEL
     * CentOS
     * Ubuntu
     * SLES
     * Debian
     * Oracle Linux
   * Mac OS X
   * 여러 패키지 형식으로 제공됩니다.
     * MSI, ZIP, tar, deb, RPM 및 DMG

     여러 기본 OS 이미지의 Zulu JDK, JRE 및 헤드리스 JRE에 대해 인증된 Docker 컨테이너 이미지를 Docker Hub에서 사용할 수 있습니다.

     * [JDK](https://hub.docker.com/_/microsoft-java-jdk)
     * [JRE](https://hub.docker.com/_/microsoft-java-jre)
     * [헤드리스 JRE](https://hub.docker.com/_/microsoft-java-jre-headless)

- 추가 비용 없음

   * Microsoft는 Azure에서 Java 앱을 빌드하고 크기를 조정하는 데 필요한 모든 기능을 추가 비용 없이 제공합니다. 추가 비용을 들이지 않고 Zulu를 통해 Java 앱에 대한 보안 업데이트 및 플랫폼 버그 수정이 제공됩니다.
   * [Java Flight Recorder 및 Zulu Mission Control](java-jdk-flight-recorder-and-mission-control.md)은 Zulu Java 8, 11 이상에서 사용할 수 있습니다.

- 비 LTS/MTS 버전의 기술 미리 보기 포함

   * 결국에는 Java 17 LTS로 사용 중지될 단기 버전에 제공되는 새로운 기능을 기술 미리 보기를 통해 점진적으로 테스트할 수 있습니다.

- OpenJDK에 업스트림된 변경 내용 포함
   * Azul Systems 대리인은 Zulu 변경 내용을 OpenJDK로 푸시합니다. 이러한 커밋은 업스트림 리포지토리를 광범위하고 포괄적으로 만듭니다.

항상 그렇듯이 Java 개발자는 Oracle JDK 및 Red Hat JDK를 포함한 고유의 Java 런타임을 Azure로 가져와서 보안 인프라 및 기능이 풍부한 서비스를 사용할 수 있습니다. 또한 Java 개발자는 Oracle Java SE의 프로덕션 버전을 사용하여 Azure의 Windows 또는 Linux 가상 머신에서 Java 워크로드를 실행할 수도 있습니다.

## <a name="use-for-local-development"></a>로컬 개발을 위해 사용

개발자는 로컬 개발 환경에서 사용하기 위해 [Azure 및 Azure Stack용 Java JDK를 다운로드](https://www.azul.com/downloads/azure-only/zulu/)할 수 있습니다. Windows, Linux 및 macOS에서 다운로드가 지원됩니다. Linux에서 작업하는 개발자는 [yum](https://www.azul.com/downloads/azure-only/zulu/#yum-repo) 및 [apt](https://www.azul.com/downloads/azure-only/zulu/#apt-repo) 패키지 관리자를 통해 패키지를 가져올 수도 있습니다.

자세한 내용은 [Azure용 Docker 이미지](java-jdk-docker-images.md)를 참조하세요.
