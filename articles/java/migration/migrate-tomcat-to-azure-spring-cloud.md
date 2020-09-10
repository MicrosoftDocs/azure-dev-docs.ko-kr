---
title: Tomcat 애플리케이션을 Azure Spring Cloud로 마이그레이션
description: 이 가이드에서는 기존 Tomcat 애플리케이션을 Azure Spring Cloud로 마이그레이션하려는 경우 알아야 할 사항에 대해 설명합니다.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 6/16/2020
ms.openlocfilehash: 7b8a29c2769b3c4b04a40053d0470bfc6b1a0cca
ms.sourcegitcommit: 2f98cf2a394d4fd82ddc917ac1041c1dc08473b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89275187"
---
# <a name="migrate-a-tomcat-application-to-azure-spring-cloud"></a>Tomcat 애플리케이션을 Azure Spring Cloud로 마이그레이션

이 가이드에서는 기존 Tomcat 애플리케이션을 Azure Spring Cloud에서 실행되도록 마이그레이션하려는 경우 알아야 할 사항에 대해 설명합니다.

## <a name="pre-migration"></a>사전 마이그레이션

마이그레이션을 성공적으로 수행하려면 시작하기 전에 다음 섹션에서 설명하는 평가 및 인벤토리 단계를 완료합니다.

### <a name="switch-to-a-supported-platform"></a>지원되는 플랫폼으로 전환

Azure Spring Cloud는 특정 버전의 Java SE를 제공합니다. 호환성을 보장하기 위해, 애플리케이션을 현재 환경에서 지원되는 버전 중 하나로 마이그레이션한 후 나머지 단계를 진행하세요. 결과 구성을 완전히 테스트해야 합니다. 테스트에서 Linux 배포판의 안정적인 최신 릴리스를 사용하세요.

[!INCLUDE [note-obtain-your-current-java-version](includes/note-obtain-your-current-java-version.md)]

[!INCLUDE [determine-whether-and-how-the-file-system-is-used](includes/determine-whether-and-how-the-file-system-is-used.md)]

### <a name="identify-the-application-builddependency-system"></a>애플리케이션 빌드/종속성 시스템 식별

모든 종속성 다운로드를 포함하여 애플리케이션을 빌드하거나 패키징하는 데 사용되는 도구를 식별합니다.

[!INCLUDE [inventory-external-resources](includes/inventory-external-resources.md)]

### <a name="inventory-secrets"></a>인벤토리 비밀

#### <a name="passwords-and-secure-strings"></a>암호 및 보안 문자열

프로덕션 서버의 모든 속성 및 구성 파일에 비밀 문자열과 암호가 있는지 확인합니다. *$CATALINA_BASE/conf*에서 *server.xml* 및 *context.xml*을 확인해야 합니다. *META-INF/context.xml*에서 애플리케이션 내 암호 또는 자격 증명을 포함하는 구성 파일을 찾을 수도 있습니다.

[!INCLUDE [inventory-certificates](includes/inventory-certificates.md)]

### <a name="determine-whether-your-application-contains-os-specific-code"></a>OS 관련 코드가 애플리케이션에 포함되어 있는지 확인

[!INCLUDE [determine-whether-your-application-contains-os-specific-code-no-title](includes/determine-whether-your-application-contains-os-specific-code-no-title.md)]

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

### <a name="determine-whether-tomcat-is-connected-to-a-web-server"></a>Tomcat이 웹 서버에 연결되어 있는지 확인

Tomcat은 Tomcat 커넥터(예: `mod_jk`)를 통해 정적 웹 서버(예: Apache)에 연결할 수 있습니다. `conf` 디렉터리의 `workers.properties` 파일을 검사하여 이러한 연결이 있는지 확인합니다.

### <a name="special-cases"></a>특수 사례

특정 프로덕션 시나리오에는 추가 변경이 필요하거나 추가 제한 사항이 있을 수 있습니다. 이러한 시나리오는 자주 발생하지 않을 수 있지만, 애플리케이션에 적용할 수 없거나 올바르게 해결되었는지 확인해야 합니다.

#### <a name="determine-if-the-application-uses-filters"></a>애플리케이션에서 필터를 사용하는지 확인

애플리케이션의 *web.xml* 파일에 [구성 필터](https://tomcat.apache.org/tomcat-9.0-doc/config/filter.html#Expires_Filter/Basic_configuration_sample)가 있는지 검사합니다.

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs-azure-spring-cloud](includes/determine-whether-your-application-relies-on-scheduled-jobs-azure-spring-cloud.md)]

#### <a name="determine-whether-non-http-connectors-are-used"></a>HTTP가 아닌 커넥터가 사용되는지 확인

Azure Spring Cloud는 사용자 지정할 수 없는 단일 HTTP 포트에서 HTTP 연결만 지원합니다. 애플리케이션에 추가 포트나 추가 프로토콜이 필요하면 Azure Spring Cloud를 사용하지 마세요.

애플리케이션에서 사용하는 HTTP 커넥터를 확인하려면 Tomcat 구성의 *server.xml* 파일 내에서 `<Connector>` 요소를 찾습니다.

#### <a name="determine-whether-ssl-session-tracking-is-used"></a>SSL 세션 추적이 사용되는지 확인

Azure Spring Cloud에서 SSL 세션은 애플리케이션 코드에 도달하기 전에 종료되므로 [SSL 세션 추적](https://tomcat.apache.org/tomcat-9.0-doc/servletapi/javax/servlet/SessionTrackingMode.html#SSL)을 사용할 수 없습니다. 대신 [Spring Session](https://docs.spring.io/spring-session/docs/current/reference/html5/index.html)을 사용하도록 전환해야 합니다.

#### <a name="determine-whether-tomcat-realms-are-used"></a>Tomcat 보안영역 사용 여부 확인

Azure Spring Cloud에서는 Tomcat 보안영역 대신 Spring Security를 사용해야 합니다. *server.xml* 파일을 검사하여 [구성된 보안영역](https://tomcat.apache.org/tomcat-9.0-doc/realm-howto.html#Configuring_a_Realm)이 있으면 인벤토리를 만듭니다.

#### <a name="determine-whether-servlet-filters-are-used"></a>서블릿 필터 사용 여부 확인

애플리케이션의 *web.xml* 파일에서 `<filter>` 요소가 있는지 검사합니다. 사용 가능한 필터 목록은 [Tomcat 필터 설명서](https://tomcat.apache.org/tomcat-9.0-doc/config/filter.html)를 참조하세요.

## <a name="migration"></a>마이그레이션

### <a name="remove-connection-to-web-server-if-present"></a>웹 서버에 대한 연결 제거(있는 경우)

Tomcat이 정적 웹 서버에(일반적으로 `mod_jk`를 통해 Apache에) 연결된 경우 Tomcat이 독립 실행형 서버로 실행되도록 해당 연결을 비활성화하여 필요에 따라 표준 서버에서 웹 리디렉션을 만듭니다. Azure CDN(Content Delivery Network)을 사용하여 정적 웹 콘텐츠를 Azure Storage로 마이그레이션하는 것이 좋습니다. 자세한 내용은 [Azure Storage에서 정적 웹 사이트 호스팅](/azure/storage/blobs/storage-blob-static-website) 및 [빠른 시작: Azure CDN과 Azure Storage 계정 통합](/azure/cdn/cdn-create-a-storage-account-with-cdn)을 참조하세요.

### <a name="update-to-tomcat-9"></a>Tomcat 9로 업데이트

현재 애플리케이션을 Tomcat 9 이전 버전에서 실행중인 경우 Tomcat 9로 마이그레이션하고 애플리케이션이 완벽하게 작동하는지 확인합니다. 자세한 내용은 [Tomcat 9 마이그레이션 가이드](http://tomcat.apache.org/migration-9.html)를 참조하세요.

### <a name="switch-to-maven-or-gradle"></a>Maven 또는 Gradle로 전환

Spring Boot 및 Spring Cloud에는 빌드 및 종속성 관리를 위해 Maven 또는 Gradle이 필요합니다. 애플리케이션에서 다른 빌드 또는 종속성 관리 시스템을 사용하는 경우 계속하기 전에 현재 버전의 [Maven](https://maven.apache.org/download.cgi)으로 전환합니다. Gradle도 지원되지만 이 가이드의 전체 단계에는 Maven을 사용합니다. Gradle을 사용하기로 결정한 경우 지침을 적절하게 조정하세요.

애플리케이션에 대한 [POM 파일](https://maven.apache.org/pom.html)을 만들고 계속하기 전에 애플리케이션이 전체 기능으로 빌드되고 실행되는지 확인합니다.

### <a name="migrate-to-spring-boot"></a>Spring Boot로 마이그레이션

다음 표에는 Tomcat 애플리케이션을 Spring Boot로 마이그레이션한 다음, Azure Spring Cloud로 마이그레이션하는 데 필요한 마이그레이션 및 코드 변경 사항이 요약되어 있습니다. 레거시 열의 요소가 애플리케이션에서 사용되는 경우 최소한 또는 이상적으로는 권장 열의 해당 요소로 바꿔야 합니다.

|레거시 | 확인할 위치 |최소 마이그레이션 |권장 마이그레이션|
|---|---|---|---|
|[DataSource를 통한 JDBC](https://docs.oracle.com/javase/tutorial/jdbc/basics/connecting.html)|*server.xml*|[JDBC 템플릿](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-using-jdbc-template)이 있는 [Spring Data Datasource](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-sql)|해당하는 경우 Spring Data 및 JPA를 고려합니다.|
|서블릿 |*web.xml*|[서블릿 컨텍스트 초기화](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/html/spring-boot-features.html#boot-features-embedded-container-context-initializer)를 사용하도록 설정하고 `@WebServlet`으로 주석 달기|[Spring-MVC 컨트롤러(`@RestController`](https://spring.io/guides/gs/rest-service/#_create_a_resource_controller) 사용)로 다시 작성
|필터 |*web.xml*| [서블릿 컨텍스트 초기화](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/html/spring-boot-features.html#boot-features-embedded-container-context-initializer)를 사용하도록 설정하고 [`@WebFilter`로 주석 달기](https://docs.oracle.com/javaee/7/api/javax/servlet/annotation/WebFilter.html) |최소값과 동일|
|JSP(Java Server Page)|*web.xml* 및 WAR 파일 콘텐츠|[Spring MVC용 JSP 뷰](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-view-jsp)|뷰 레이어를 별도로 호스트|
|JMS(Java Message Service)|*server.xml*|연결 팩터리를 [Spring Bean](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-spring-beans-and-dependency-injection)으로 인스턴스화|[Spring JMS](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/integration.html#jms-using) 사용
|WAR 파일 내부의 정적 콘텐츠(이미지, JavaScript 파일 등)|정적 콘텐츠 디렉터리(일반적으로 */static*, */public* 또는 */resources*)|콘텐츠를 */src/main/resources*로 이동|[사전 마이그레이션의 정적 콘텐츠 권장 사항](#read-only-static-content)을 참조하세요.
|WAR 파일 외부의 정적 콘텐츠(이미지, JavaScript 파일 등)|로컬 파일 시스템의 경로|콘텐츠를 *src/main/resources*로 이동. 소스 코드에서 하드 코드된 경로를 검색하여 [ClassPathResource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/ClassPathResource.html)로 바꾸기 |[사전 마이그레이션의 정적 콘텐츠 권장 사항](#read-only-static-content)을 참조하세요.

1. 애플리케이션이 JNDI 리소스(예: JDBC 드라이버)를 통해 삽입된 라이브러리에 의존하는 경우, 이러한 라이브러리를 POM 파일에 종속성으로 추가합니다. Tomcat 서버(일반적으로 *tomcat/lib* 디렉터리)에서 라이브러리를 제거하고 계속하기 전에 애플리케이션이 전체 기능으로 실행되는지 확인합니다.

1. Spring Boot 부모 POM을 POM 파일에 추가합니다. 자세한 내용은 Spring Boot 설명서의 [POM 만들기](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#getting-started-first-application-pom)를 참조하세요.

1. Spring Boot Tomcat 스타터를 POM 파일에 대한 종속성으로 추가합니다.

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    ```

    이전에는 Tomcat 애플리케이션이었지만 `war`를 대상 패키징으로 추가하지 마십시오.

1. Tomcat 데이터 원본을 Spring 데이터 원본으로 바꿉니다. 애플리케이션에 사용되는 모든 데이터베이스에 대해 [Spring DataSource를 구성](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto-configure-a-datasource)합니다. 코드가 직접 SQL 쿼리를 실행하는 경우 [JdbcTemplate을 사용](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-using-jdbc-template)하도록 수정합니다. 트랜잭션 관리 및 CRUD 도구와 같은 추가 데이터 액세스 기능은 [Spring Framework 설명서](https://docs.spring.io/spring/docs/current/spring-framework-reference/data-access.html#jdbc) 및 [Spring Data 설명서](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#reference)를 참조하세요.

1. [내장형 서블릿 컨테이너](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/html/spring-boot-features.html#boot-features-embedded-container) 내에 서블릿 구현을 포함할 수 있지만 그렇게 하지 않는 것이 좋습니다. 대신 [서블릿 구현](https://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpServletRequest.html)을 Spring [Rest 컨트롤러](https://spring.io/guides/gs/rest-service/#_create_a_resource_controller)로 바꿉니다. 애플리케이션이 비 Spring MVC 프레임워크를 사용하는 경우에는 Spring MVC로 바꿉니다. 자세한 내용은 [Spring MVC 주석 처리된 컨트롤러 참조](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/web.html#mvc-controller)를 참조하세요.

1. [Spring Bean](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-spring-beans-and-dependency-injection)으로 다른 모든 JNDI 종속성을 다시 만듭니다. Spring 관용구 메커니즘을 사용(예: 메시징에 [Spring JMS](https://spring.io/guides/gs/messaging-jms/)를 사용)하는 것이 좋습니다.

1. Tomcat 보안영역을 [Spring Security](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-filters-review)로 바꿉니다. [Active Directory에 대한 Spring Boot Starter](/azure/developer/java/spring-framework/spring-boot-starters-for-azure#azure-active-directory)를 통해 Azure Active Directory를 권한 부여 관리에 사용하는 것이 좋습니다.

1. [Spring bean](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto-add-a-servlet-filter-or-listener-as-spring-bean) 또는 [클래스 경로 검색](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto-add-a-servlet-filter-or-listener-using-scanning)을 사용하여 *web.xml*에 구성된 서블릿 필터를 다시 만듭니다.

1. 애플리케이션이 정적 콘텐츠(예: 이미지 또는 JavaScript 파일)를 포함하거나 참조하는 경우, 이러한 파일을 프로젝트 소스 코드의 *src/main/resources*로 이동해야 합니다. 파일을 이동한 후에는 소스 코드를 업데이트하여 로컬 파일 시스템 참조를 제거합니다. 이러한 파일에 액세스하려면 Spring의 `ClassPathResource` 클래스를 사용합니다.

`mvn spring-boot:run`을 실행하여 애플리케이션을 테스트합니다. 계속하기 전에 결과 애플리케이션이 전체 기능으로 실행되는지 확인합니다.

[!INCLUDE [migrate-steps-spring-boot-azure-spring-cloud](includes/migrate-steps-spring-boot-azure-spring-cloud.md)]

## <a name="post-migration"></a>마이그레이션 후 작업

[!INCLUDE [post-migration-spring-boot-azure-spring-cloud](includes/post-migration-spring-boot-azure-spring-cloud.md)]
