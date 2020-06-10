---
title: Azure 개발에 대한 Java JDK 및 장기 지원
description: Java 애플리케이션을 개발하고 실행하는 Azure 지원의 다운로드 및 설명입니다.
ms.date: 04/09/2019
ms.topic: conceptual
ms.custom: seo-java-september2019
ms.openlocfilehash: 4722c64e3b328cb5b63b6b976bd25e5b91341117
ms.sourcegitcommit: 367217792f3b16c769e2c39372358bc6b9c9c044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84292797"
---
# <a name="java-long-term-support-and-medium-term-support-for-azure-and-azure-stack"></a>Azure 및 Azure Stack에 대한 Java 장기 및 중기 지원

Azure 및 Azure Stack의 Java 개발자는 추가 지원 비용 없이 [Azure용 Azul Zulu - Enterprise Edition](https://www.azul.com/downloads/azure-only/zulu/)을 사용하여 프로덕션 Java 애플리케이션을 빌드하고 실행할 수 있습니다. Azure에서 원하는 모든 Java 런타임을 사용할 수 있지만 Zulu를 사용하는 경우 무료 유지 관리 업데이트를 가져오면 Microsoft와의 지원 문제가 발생할 수 있습니다.

LTS(장기 지원)로 지정된 릴리스는 Oracle 및 OpenJDK 커뮤니티에서 지정한 것과 동일한 LTS 릴리스입니다. LTS 릴리스의 경우 필요에 따라 최소 8년 동안 버그 수정, 보안 업데이트 및 기타("운영 지원") 수정 기능에 액세스할 수 있으며, 사용자에게 최신 JDK 버전("확장 지원")으로 마이그레이션하도록 조언하고 지원하기 위한 추가 지원도 2년 동안 제공합니다.

MTS(중기 지원)용으로 디자인된 릴리스의 경우, 다음 LTS 릴리스가 일반 공급된 후 최소 1.5년 동안 프로덕션 지원이 제공되고 1년의 추가 지원이 제공됩니다.

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

Azure용 Azul Zulu - Enterprise Edition OpenJDK 빌드는 Microsoft와 Azul Systems가 후원하는 Azure 및 Azure Stack에 대한 OpenJDK의 무료 다중 플랫폼 프로덕션 준비 배포입니다. 해당 배포는 다음과 같습니다.

* JDK(Java Development Kit), JRE(Java Runtime Environment) 및 Headless JRE로 패키지된 OpenJDK의 100% 오픈 소스 빌드. 이 이진 파일은 Java 애플리케이션 또는 Azure 및 Azure Stack의 구성 요소와 함께 사용할 수 있는 Java SE(Standard Edition)의 완전히 호환되고 상용으로 적합한 빌드입니다.
* 버그 수정, 성능 향상 및 보안 패치를 포함한 장기 지원과 함께 제공됩니다.
* Windows, Linux 및 macOS에서 Java 애플리케이션을 개발하고 실행하는 데 사용할 수 있습니다.
* Docker Hub의 컨테이너 이미지 및 Azure Marketplace의 가상 머신(Windows 및 Linux)으로 사용할 수 있습니다.
* Microsoft Azure에서 다음과 같은 많은 Azure 서비스를 지원하기 위해 사용합니다.
  * App Service Windows
  * App Service Linux
  * Functions
  * Service Fabric
  * HDInsight
  * 검색
  * Azure DevOps
  * Cloud Shell  

## <a name="supported-java-versions-and-update-schedule"></a>지원되는 Java 버전 및 업데이트 일정

Azul Systems는 Java SE 7, 8, 11 및 13을 포함하여 Java의 모든 LTS(장기 지원) 및 MTS(중기 지원) 버전에 대해 완벽하게 지원되는 [Azure용 Azul Zulu - Enterprise Edition](https://www.azul.com/downloads/azure-only/zulu/) 빌드를 제공합니다. 자세한 정보는 [Azul 보도 자료](https://www.azul.com/press_release/free-java-production-support-for-microsoft-azure-azure-stack) 및 [Azul 상품 지원 주기](https://www.azul.com/products/azul_support_roadmap/) 로드맵을 참조하세요.

|Java SE 버전  |지원 기한  |
|---------|----------|
|[![지원되는 Java 버전 - LTS - Java 7](media/supported-java-versions-java-7.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-7-lts) |2023년 7월 - LTS|
|[![지원되는 Java 버전 - LTS - Java 8](media/supported-java-versions-java-8.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts) |2030년 12월 - LTS|
|[![지원되는 Java 버전 - LTS - Java 11](media/supported-java-versions-java-11.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-11-lts) |2027년 9월 - LTS|
|[![지원되는 Java 버전 - MTS - Java 13](media/supported-java-versions-java-13.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-13) |2023년 3월 - MTS|
|[![지원되는 Java 버전 - 미리 보기 - Java 14](media/supported-java-versions-java-14.png)](https://www.azul.com/downloads/azure-only/zulu/?version=java-14) |**미리 보기**|

LTS 및 MTS JDK 릴리스에는 분기별 보안 업데이트, 버그 수정 그리고 필요한 경우 중요 대역 외 업데이트 및 패치가 포함됩니다.  이 지원에는 Java 11과 같은 Java의 최신 버전에서 보고된 Java 7 및 8에 대한 보안 업데이트 및 버그 수정 이식이 포함되며, 이전 버전의 Java에 대한 지속적인 안정성 및 보안을 보장합니다.  Azure 고객은 계획되지 않은 모든 Java SE 구독 요금이 발생하지 않고 이러한 보안 업데이트 및 플랫폼 버그 수정을 가져올 수 있습니다.

## <a name="benefits-for-developers"></a>개발자에 대한 혜택

Azure용 Azul Zulu - Enterprise Edition JDK 릴리스는 다음과 같습니다.

1. Microsoft와 Azul Systems에서 모두 후원 및 지원

   * Zulu 이진 파일은 프로덕션 준비 상태이며 Microsoft 및 Azul Systems에서 후원
   * Zulu에는 Java 7, 8 및 11에 대해 무료 LTS(장기 지원), Java 13에 대해 MTS(중기 지원)가 제공됩니다. (LTS는 Java 17에 대해서도 제공됩니다.) Java 버전은 필요한 경우에만 업그레이드할 수 있습니다.
   * Java 7은 2023년 7월까지 지원됩니다. Java 8 및 11은 2027년 9월까지 지원됩니다. Java 13은 2023년 3월까지 지원됩니다.
   * Microsoft는 많은 Azure 서비스 기반 머신에서 내부적으로 Zulu 실행을 약속했습니다.

2. 프로덕션 준비

   * OpenJDK의 해당 빌드에 대한 100% 오픈 소스.
   * 많은 Java SE 배포에 대한 드롭인 교체.
   * JDK, JRE 및 JRE 헤드리스
   * Java 7, 8, 11 및 13.
   * OpenJDK Community TCK(Technology Compatibility Kit)를 사용하여 Java SE 사양을 준수하는 것으로 확인되었습니다.
   * 개발자는 Java SE 7, 8, 11 및 13에 대한 버그 수정, 성능 향상 및 보안 패치를 포함하여 Java SE에 대한 프로덕션 업데이트를 계속 받습니다.

3. 다중 플랫폼 지원. Zulu는 다음을 포함한 여러 플랫폼 및 버전에 대한 이진 파일을 지원합니다.

   * Windows 클라이언트
     * 10
     * 8.1
     * 8, 7
   * Windows Server
     * 2016R2
     * 2016
     * 2012 R2
     * 2012
     * 2008 R2
   * 다음을 포함한 Linux
     * RHEL
     * CentOS
     * Ubuntu
     * SLES
     * Debian
     * Oracle Linux
   * Mac OS X
   * 여러 패키지 형식으로 제공됩니다.
     * MSI, ZIP, TAR, DEB, RPM 및 DMG

    여러 기본 OS 이미지의 Zulu JDK, JRE 및 JRE 헤드리스에 대해 인증된 Docker 컨테이너 이미지를 Docker에서 사용할 수 있습니다.

    허브:

    * [JDK](https://hub.docker.com/_/microsoft-java-jdk)
    * [JRE](https://hub.docker.com/_/microsoft-java-jre)
    * [JRE 헤드리스](https://hub.docker.com/_/microsoft-java-jre-headless)

4. 무료

   * Microsoft는 Azure에서 Java 앱을 빌드하고 확장하는 데 필요한 모든 것을 무료로 제공합니다. 즉, Zulu를 통해 Java 앱에 대한 무료 보안 업데이트 및 플랫폼 버그 수정을 수수료 없이 받게 됩니다.
   * [Java Flight Recorder 및 Mission Control](java-jdk-flight-recorder-and-mission-control.md)은 Zulu Java 8, 11 이상에서 사용할 수 있습니다.

5. LTS/MTS 이외 버전의 기술 미리 보기

   * 기술 미리 보기는 결국 Java 17 LTS로 종료될 단기 버전에 제공될 때 새 기능을 점진적으로 테스트할 기회를 제공합니다.

6. OpenJDK 변경이 업스트림됨

   * Azul Systems 커미터는 Zulu 변경 내용을 OpenJDK에 푸시하여 업스트림 리포지토리를 포괄적이고 모든 것이 포함되도록 만듭니다.

늘 그렇듯이 Java 개발자는 Oracle JDK 및 Red Hat JDK를 포함한 자신의 고유한 Java 런타임을 Azure로 가져오고 보안 인프라 및 기능이 풍부한 서비스를 사용할 수 있습니다. 또한 Java 개발자는 Oracle Java SE의 프로덕션 버전을 사용하여 Azure의 Windows 또는 Linux 가상 머신에서 Java 워크로드를 실행할 수도 있습니다.

## <a name="use-for-local-development"></a>로컬 개발을 위해 사용

개발자는 로컬 개발 환경에서 사용하기 위해 Azure 및 Azure Stack용 Java JDK를 [다운로드](https://www.azul.com/downloads/azure-only/zulu/)할 수 있습니다. Windows, Linux 및 macOS에서 다운로드가 지원됩니다. Linux에서 작업하는 개발자는 [yum](https://www.azul.com/downloads/azure-only/zulu/#yum-repo) 및 [apt](https://www.azul.com/downloads/azure-only/zulu/#apt-repo) 패키지 관리자를 통해 패키지를 가져올 수도 있습니다.

추가 지침은 [Azure용 Docker 이미지](java-jdk-docker-images.md)를 참조하세요.
