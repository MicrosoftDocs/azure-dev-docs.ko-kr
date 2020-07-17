---
title: Java 애플리케이션을 Azure로 마이그레이션
description: 이 항목에서는 Java 애플리케이션을 Azure로 마이그레이션하는 데 추천되는 전략에 대해 간략하게 설명합니다.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 1/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 3487cc326222a013c7f0acec396ed343c8bd9621
ms.sourcegitcommit: c6642cae6fdb5e3025ed66fcd4ef89792c3b436a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2020
ms.locfileid: "86405714"
---
# <a name="migrate-java-applications-to-azure"></a>Java 애플리케이션을 Azure로 마이그레이션

이 항목에서는 Java 애플리케이션을 Azure로 마이그레이션하는 데 추천되는 전략에 대해 간략하게 설명합니다.

이 마이그레이션 지침은 일반 Java on Azure 시나리오를 다루고 개략적인 계획 제안 및 고려 사항을 제공하도록 설계되었습니다. Microsoft Java on Azure 팀과 특정 Java 앱 마이그레이션 시나리오를 논의하기 위해 다음 질문서를 작성하면 담당자가 연락할 것입니다.

> [!div class="nextstepaction"]
> [Java 마이그레이션 질문서](https://aka.ms/migrate-my-Java-app-requested-thru-docs)

## <a name="identifying-application-type"></a>애플리케이션 유형 식별

Java 애플리케이션의 클라우드 대상을 선택하려면 먼저 해당 애플리케이션 유형을 식별해야 합니다. 대부분의 Java 애플리케이션은 다음 유형 중 하나입니다.

* [Spring Boot/JAR 애플리케이션](#spring-boot--jar-applications)
* [Spring Cloud/마이크로서비스](#spring-cloud--microservices)
* [웹 애플리케이션](#web-applications)
* [Java EE 애플리케이션](#java-ee-applications)
* [Batch/예약된 작업](#batch--scheduled-jobs)

이러한 유형에 대해서는 다음 섹션에서 설명합니다.

### <a name="spring-boot--jar-applications"></a>Spring Boot/JAR 애플리케이션

많은 최신 애플리케이션은 명령줄에서 직접 호출됩니다. 이러한 애플리케이션은 여전히 웹 요청을 처리하지만, HTTP 요청 처리를 제공하기 위해 애플리케이션 서버에 의존하는 대신 HTTP 통신 및 기타 모든 종속성을 애플리케이션 패키지에 직접 통합합니다. 이러한 애플리케이션은 Spring Boot, Dropwizard, Microaut, MicroProfile, Vert.x 등과 같은 프레임워크를 사용하여 빌드되는 경우가 많습니다.

이러한 애플리케이션은 *.jar* 확장명(JAR 파일)의 보관 파일로 패키지됩니다.

### <a name="spring-cloud--microservices"></a>Spring Cloud/마이크로서비스

마이크로서비스 아키텍처 스타일은 단일 애플리케이션을 작은 서비스 모음으로 개발하는 방법입니다. 즉 각 서비스가 자체 프로세스에서 실행되고 경량 메커니즘, 종종 HTTP 리소스 API와 통신합니다. 이러한 서비스는 비즈니스 기능을 중심으로 빌드되고 완전히 자동화된 배포 머신을 통해 독립적으로 배포할 수 있습니다. 이러한 서비스는 기본적으로 중앙 집중식으로 관리되고, 다양한 프로그래밍 언어로 작성되며, 다른 데이터 스토리지 기술을 사용할 수 있습니다. 이러한 서비스는 Spring Cloud와 같은 프레임워크를 사용하여 빌드되는 경우가 많습니다.

이러한 서비스는 *.jar* 확장명(JAR 파일)의 여러 애플리케이션으로 패키지됩니다.

### <a name="web-applications"></a>웹 애플리케이션

웹 애플리케이션은 [서블릿](https://en.wikipedia.org/wiki/Java_servlet) 컨테이너 내에서 실행됩니다. 일부는 서블릿 API를 직접 사용하는 반면, 대부분은 Apache Struts, Spring MVC, JSF(JavaServer Faces) 등과 같은 서블릿 API를 캡슐화하는 추가 프레임워크를 사용합니다.

웹 애플리케이션은 *.war* 확장명(WAR 파일)의 보관 파일로 패키지됩니다.

### <a name="java-ee-applications"></a>Java EE 애플리케이션

Java EE 애플리케이션(J2EE 애플리케이션 또는 더 최근에는 Jakarta EE 애플리케이션이라고도 함)은 웹 애플리케이션의 요소 중 일부 또는 전부를 포함하거나 전혀 포함하지 않을 수 있습니다. 또한 [Java EE 사양](https://en.wikipedia.org/wiki/Java_Platform,_Enterprise_Edition)에서 정의한 대로 많은 추가 구성 요소를 포함하고 사용할 수 있습니다.

Java EE 애플리케이션은 *.ear* 확장명(EAR 파일) 또는 *.war* 확장명(WAR 파일)의 보관 파일로 패키지될 수 있습니다.

Java EE 애플리케이션은 Java EE 호환 애플리케이션 서버(예: WebLogic, WebSphere, WildFly, GlassFish, Payara 등)에 배포해야 합니다.

Java EE 사양에서 제공하는 기능만 사용하는 애플리케이션(즉, 앱 서버 독립 애플리케이션)은 호환되는 애플리케이션 서버에서 다른 애플리케이션 서버로 마이그레이션할 수 있습니다. 애플리케이션이 특정 애플리케이션 서버에 종속된 경우(애플리케이션 서버 종속), 해당 애플리케이션 서버를 호스팅할 수 있는 Azure 서비스 대상을 선택해야 할 수 있습니다.

### <a name="batch--scheduled-jobs"></a>Batch/예약된 작업

일부 애플리케이션은 요청 또는 사용자 입력을 기다리지 않고 잠시 실행되어 특정 워크로드를 실행한 후 종료합니다. 경우에 따라 이러한 작업은 한 번 또는 정기적으로 예약된 간격으로 실행되어야 합니다. 온-프레미스에서 이러한 작업은 서버의 crontab에서 호출되는 경우가 많습니다.

이러한 애플리케이션은 *.jar* 확장명(JAR 파일)의 보관 파일로 패키지됩니다.

> [!NOTE]
> 애플리케이션에서 스케줄러(예: Spring Batch 또는 Quartz)를 사용하여 예약된 작업을 실행하는 경우 이러한 작업은 애플리케이션 외부에서 실행하는 것이 좋습니다. 애플리케이션이 클라우드에서 여러 인스턴스로 확장되는 경우 동일한 작업이 두 번 이상 실행됩니다. 또한 예약 메커니즘이 호스트의 현지 표준 시간대를 사용하는 경우 애플리케이션을 여러 지역에 걸쳐 확장할 때 원하지 않은 동작이 발생할 수 있습니다.

## <a name="selecting-the-target-azure-service-destination"></a>대상 Azure 서비스 대상 선택

다음 섹션에서는 애플리케이션 요구 사항을 충족하는 서비스 대상과 이와 관련된 책임을 보여 줍니다.

### <a name="hosting-options-grid"></a>호스팅 옵션 그리드

다음 그리드를 사용하여 애플리케이션 유형의 잠재적 대상을 식별합니다. 보시다시피 AKS 및 Virtual Machines는 모든 애플리케이션 유형을 지원하지만, 다음 섹션에 표시된 것처럼 팀이 더 많은 책임을 져야 합니다.

|대상&nbsp;→<br><br>애플리케이션&nbsp;유형&nbsp;↓|앱<br>서비스<br>Java SE|앱<br>서비스<br>Tomcat|Azure<br>Spring<br>클라우드|AKS|가상<br>머신|
|---|---|---|---|---|---|---|
| Spring Boot/JAR 애플리케이션                                    |&#x2714;|        |&#x2714;|&#x2714;|&#x2714;|
| Spring Cloud/마이크로서비스                                      |        |        |&#x2714;|&#x2714;|&#x2714;|
| 웹 애플리케이션                                                  |        |&#x2714;|        |&#x2714;|&#x2714;|
| Java EE 애플리케이션                                              |        |        |        |&#x2714;|&#x2714;|
| 상용 애플리케이션 서버<br>(예: WebLogic 또는 WebSphere) |        |        |        |&#x2714;|&#x2714;|
| 로컬 파일 시스템의 장기 지속성                         |&#x2714;|&#x2714;|        |&#x2714;|&#x2714;|
| 애플리케이션 서버 수준 클러스터링                               |        |        |        |&#x2714;|&#x2714;|
| Batch/예약된 작업                                            |        |        |&#x2714;|&#x2714;|&#x2714;|
| VNet 통합/하이브리드 연결                              |미리 보기 |미리 보기 |        |&#x2714;|&#x2714;|
| Azure 지역 가용성                | [세부 정보][10] | [세부 정보][10] | [세부 정보][11] |[세부 정보][12]|[세부 정보][13]|

### <a name="ongoing-responsibility-grid"></a>진행 중인 책임 그리드

다음 그리드를 사용하여 마이그레이션 후 팀의 각 대상에 대한 책임을 파악합니다.

팀은 "&#x1F449;"로 표시된 작업에 대해 지속적으로 책임을 집니다. 이러한 모든 책임을 이행하기 위해 강력하고 자동화된 프로세스를 구현하는 것이 좋습니다.

> [!NOTE]
> 이는 완전한 책임 목록이 아닙니다.

|대상&nbsp;→<br><br>작업&nbsp;↓                            | 앱<br>서비스 | Azure<br>Spring<br>클라우드 | AKS | 가상<br>머신 |
|---|---|---|---|---|
| 라이브러리 업데이트<br>(취약성 수정 포함)                 | &#x1F449;   | &#x1F449;   | &#x1F449;   | &#x1F449; |
| 애플리케이션 서버 업데이트<br>(취약성 수정 포함)    | ![Azure][1] | ![Azure][1] | &#x1F449;   | &#x1F449; |
| Java 런타임 업데이트<br>(취약성 수정 포함)          | ![Azure][1] | ![Azure][1] | &#x1F449;   | &#x1F449; |
| Kubernetes 업데이트 트리거<br>(Azure에서 수동 트리거를 사용하여 수행) | 해당 없음         | ![Azure][1] | &#x1F449;   | 해당 없음       |
| 이전 버전과 호환되지 않는 Kubernetes API 변경 내용 조정                  | 해당 없음         | ![Azure][1] | &#x1F449;   | 해당 없음       |
| 컨테이너 기본 이미지 업데이트<br>(취약성 수정 포함)      | 해당 없음         | ![Azure][1] | &#x1F449;   | 해당 없음       |
| 운영 체제 업데이트<br>(취약성 수정 포함)      | ![Azure][1] | ![Azure][1] | ![Azure][1] | &#x1F449; |
| 실패한 인스턴스 검색 및 다시 시작                                   | ![Azure][1] | ![Azure][1] | ![Azure][1] | &#x1F449; |
| 업데이트에 대한 드레이닝 및 롤링 다시 시작 구현                       | ![Azure][1] | ![Azure][1] | ![Azure][1] | &#x1F449; |
| 인프라 관리                                                   | ![Azure][1] | ![Azure][1] | &#x1F449;   | &#x1F449; |
| 모니터링 및 경고 관리                                             | &#x1F449;   | &#x1F449;   | &#x1F449;   | &#x1F449; |

서블릿 컨테이너(예: Spring Boot)를 애플리케이션의 일부로 배포하는 경우 이를 라이브러리로 간주해야 하므로 항상 사용자의 책임입니다.

## <a name="ensuring-on-premises-connectivity"></a>온-프레미스 연결 보장

애플리케이션에서 온-프레미스 서비스에 액세스해야 하는 경우 Azure의 연결 서비스 중 하나를 프로비저닝해야 합니다. 자세한 내용은 [온-프레미스 네트워크를 Azure에 연결하기 위한 솔루션 선택](/azure/architecture/reference-architectures/hybrid-networking/)을 참조하세요. 또는 온-프레미스 리소스에서 노출하는 공개적으로 사용 가능한 API를 사용하도록 애플리케이션을 리팩터링해야 합니다.

마이그레이션을 시작하려면 이 작업을 완료해야 합니다.

## <a name="inventory-current-capacity-and-resource-usage"></a>인벤토리 현재 용량 및 리소스 사용량

현재 프로덕션 서버의 하드웨어와 평균 및 최대 요청 수 및 리소스 사용량을 문서화합니다. 이 정보는 서비스 대상에서 리소스를 프로비저닝하는 데 필요합니다.

## <a name="migration-guidance"></a>마이그레이션 지침

다음 그리드를 사용하여 애플리케이션 유형 및 대상 Azure 서비스 대상별 마이그레이션 지침을 확인합니다.

**Java 애플리케이션**

아래 행을 사용하여 Java 애플리케이션 유형을 찾고, 열을 사용하여 애플리케이션을 호스팅할 Azure 서비스 대상을 찾습니다.

JBoss EAP 앱을 App Service의 Tomcat으로 마이그레이션하려면 먼저 Java EE 앱을 Tomcat에서 실행되는 Java Web Apps(서블릿)로 변환한 다음, 아래에 나와 있는 지침을 따릅니다.

Tomcat의 웹앱을 Azure Spring Cloud로 마이그레이션하려면 먼저 앱을 Spring Cloud 마이크로서비스로 변환한 다음, 아래에 나와 있는 지침을 따릅니다.

|대상&nbsp;→<br><br>애플리케이션&nbsp;유형&nbsp;↓|앱<br>서비스<br>Java SE|앱<br>서비스<br>Tomcat|Azure<br>Spring<br>클라우드|AKS|Virtual Machines|
|---|---|---|---|---|---|---|
| Spring Boot /<br>JAR 애플리케이션 | [지침][5] | 지침<br>예정 | [지침][16] | [지침][14]      | 지침<br>예정 |
| Spring Cloud /<br>마이크로서비스   | 해당 없음           | 해당 없음                 | [지침][15] | 지침<br>예정 | 지침<br>예정 |
| 웹 애플리케이션<br>(Tomcat에 있음)     | 해당 없음           | [지침][2]       | 해당 없음            | [지침][3]       | 지침<br>예정 |

**Java EE 애플리케이션**

아래 행을 사용하여 특정 앱 서버에서 실행되는 Java EE 애플리케이션 유형을 찾습니다. 열을 사용하여 애플리케이션을 호스팅할 Azure 서비스 대상을 찾습니다.

|대상&nbsp;→<br><br>앱 서버&nbsp;↓|앱<br>서비스<br>Java SE|앱<br>서비스<br>Tomcat|Azure<br>Spring<br>클라우드|AKS|Virtual Machines|
|---|---|---|---|---|---|---|
| WildFly /<br>JBoss AS | 해당 없음 | 해당 없음 | 해당 없음 | [지침][9] | 지침<br>예정 |
| WebLogic              | 해당 없음 | 해당 없음 | 해당 없음 | [지침][6] | [지침][4]       |
| WebSphere             | 해당 없음 | 해당 없음 | 해당 없음 | [지침][7] | 지침<br>예정 |
| JBoss EAP             | 해당 없음 | 해당 없음 | 해당 없음 | [지침][8] | 지침<br>예정 |

<!-- reference links, for use with tables -->
[1]: media/migration-overview/logo_azure.svg
[2]: migrate-tomcat-to-tomcat-app-service.md
[3]: migrate-tomcat-to-containers-on-azure-kubernetes-service.md
[4]: migrate-weblogic-to-virtual-machines.md
[5]: migrate-spring-boot-to-app-service.md
[6]: migrate-weblogic-to-wildfly-on-azure-kubernetes-service.md
[7]: migrate-websphere-to-wildfly-on-azure-kubernetes-service.md
[8]: migrate-jboss-eap-to-wildfly-on-azure-kubernetes-service.md
[9]: migrate-wildfly-to-wildfly-on-azure-kubernetes-service.md
[10]: https://azure.microsoft.com/global-infrastructure/services/?products=app-service-linux
[11]: https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud
[12]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[13]: https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines
[14]: migrate-spring-boot-to-azure-kubernetes-service.md
[15]: migrate-spring-cloud-to-azure-spring-cloud.md
[16]: migrate-spring-boot-to-azure-spring-cloud.md