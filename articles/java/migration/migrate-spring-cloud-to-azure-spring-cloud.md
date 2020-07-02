---
title: Spring Cloud 애플리케이션을 Azure Spring Cloud로 마이그레이션
description: 이 가이드에서는 Azure Spring Cloud에서 실행할 기존 Spring Cloud 애플리케이션을 마이그레이션하려는 경우 알아야 할 사항에 대해 설명합니다.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 2/12/2020
ms.openlocfilehash: c9b05ea7f7e7d181150e11afb7e145740fc5ab5a
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507611"
---
# <a name="migrate-spring-cloud-applications-to-azure-spring-cloud"></a>Spring Cloud 애플리케이션을 Azure Spring Cloud로 마이그레이션

이 가이드에서는 Azure Spring Cloud에서 실행할 기존 Spring Cloud 애플리케이션을 마이그레이션하려는 경우 알아야 할 사항에 대해 설명합니다.

## <a name="pre-migration"></a>사전 마이그레이션

마이그레이션을 성공적으로 수행하려면 시작하기 전에 다음 섹션에서 설명하는 평가 및 인벤토리 단계를 완료합니다.

이러한 마이그레이션 전 요구 사항 중에서 충족할 수 없는 요구 사항이 있는 경우 다음 마이그레이션 관련 가이드를 참조하세요.

* 실행 가능한 JAR 애플리케이션을 Azure Kubernetes Service의 컨테이너로 마이그레이션(계획된 지침)
* 실행 가능한 JAR 애플리케이션을 Azure Virtual Machines로 마이그레이션(계획된 지침)

### <a name="inspect-application-components"></a>애플리케이션 구성 요소 검사

[!INCLUDE [determine-whether-and-how-the-file-system-is-used-azure-spring-cloud](includes/determine-whether-and-how-the-file-system-is-used-azure-spring-cloud.md)]

#### <a name="determine-whether-any-of-the-services-contain-os-specific-code"></a>서비스 중에서 OS 관련 코드가 포함된 서비스가 있는지 확인

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code-no-title.md)]

[!INCLUDE [switch-to-a-supported-platform-azure-spring-cloud](includes/switch-to-a-supported-platform-azure-spring-cloud.md)]

[!INCLUDE [identify-spring-boot-versions](includes/identify-spring-boot-versions.md)]

Spring Boot 1.x를 사용하는 애플리케이션의 경우 [Spring Boot 2.0 마이그레이션 가이드](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide)에 따라 지원되는 Spring Boot 버전으로 업데이트합니다. 지원되는 버전은 [배포용 Java Spring 앱 준비](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment#spring-boot-and-spring-cloud-versions)를 참조하세요.

#### <a name="identify-spring-cloud-versions"></a>Spring Cloud 버전 확인

마이그레이션하는 각 애플리케이션의 종속성을 검사하여 사용하는 Spring Cloud 구성 요소의 버전을 확인합니다.

##### <a name="maven"></a>Maven

Maven 프로젝트에서 Spring Cloud 버전은 일반적으로 `spring-cloud.version` 속성에 설정됩니다.

```xml
  <properties>
    <java.version>1.8</java.version>
    <spring-cloud.version>Hoxton.SR3</spring-cloud.version>
  </properties>
```

##### <a name="gradle"></a>Gradle

Gradle 프로젝트에서 Spring Cloud 버전은 일반적으로 "추가 속성" 블록에 설정됩니다.

```gradle
ext {
  set('springCloudVersion', "Hoxton.SR3")
}
```

지원되는 버전의 Spring Cloud를 사용하도록 모든 애플리케이션을 업데이트해야 합니다. 지원되는 버전의 목록은 [배포용 Java Spring 앱 준비](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment#spring-boot-and-spring-cloud-versions)를 참조하세요.

[!INCLUDE [identify-logs-metrics-apm-azure-spring-cloud.md](includes/identify-logs-metrics-apm-azure-spring-cloud.md)]

#### <a name="identify-zipkin-dependencies"></a>Zipkin 종속성 확인

애플리케이션에 Zipkin에 대한 명시적 종속성이 있는지 여부를 확인합니다. Maven 또는 Gradle 종속성에서 `io.zipkin.java` 그룹에 대한 종속성을 찾습니다.

### <a name="inventory-external-resources"></a>인벤토리 외부 리소스

데이터 원본, JMS 메시지 브로커, 다른 서비스의 URL 등과 같은 외부 리소스를 확인합니다. Spring Cloud 애플리케이션에서는 일반적으로 다음 위치 중 하나에서 이러한 리소스에 대한 구성을 찾을 수 있습니다.

* *src/main/directory* 폴더에서 일반적으로 *application.properties* 또는 *application.yml*이라는 파일
* 이전 단계에서 확인된 Spring Cloud Config 리포지토리

[!INCLUDE [inventory-databases-spring-boot](includes/inventory-databases-spring-boot.md)]

[!INCLUDE [identify-jms-brokers-in-spring](includes/identify-jms-brokers-in-spring.md)]

사용 중인 broker가 확인되면 해당 설정을 찾습니다. Spring Cloud 애플리케이션에서 이러한 설정은 일반적으로 애플리케이션 디렉터리의 *application.properties* 및 *application.yml* 파일 또는 Spring Cloud Config 서버 리포지토리에서 찾을 수 있습니다.

[!INCLUDE [jms-broker-settings-examples-in-spring](includes/jms-broker-settings-examples-in-spring.md)]

[!INCLUDE [identify-external-caches-azure-spring-cloud](includes/identify-external-caches-azure-spring-cloud.md)]

#### <a name="identity-providers"></a>ID 공급자

인증 및/또는 권한 부여가 필요한 모든 ID 공급자 및 모든 Spring Cloud 애플리케이션을 확인합니다. ID 공급자를 구성하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* OAuth2 구성은 [Spring Cloud Security 빠른 시작](https://cloud.spring.io/spring-cloud-static/spring-cloud-security/current/reference/html/#_quickstart)을 참조하세요.
* Auth0 Spring Security 구성은 [Auth0 Spring Security 설명서](https://auth0.com/docs/quickstart/backend/java-spring-security5/01-authorization)를 참조하세요.
* PingFederate Spring Security 구성은 [Auth0 PingFederate 지침](https://auth0.com/authenticate/java-spring-security/ping-federate/)을 참조하세요.

#### <a name="resources-configured-through-pivotal-cloud-foundry-pcf"></a>PCF(Pivotal Cloud Foundry)를 통해 구성된 리소스

Pivotal Cloud Foundry를 사용하여 관리되는 애플리케이션의 경우 앞에서 설명한 리소스를 포함한 외부 리소스는 PCF 서비스 바인딩을 통해 구성되는 경우가 많습니다. 이러한 리소스에 대한 구성을 검사하려면 [Cloud Foundry CLI](https://docs.cloudfoundry.org/cf-cli/)를 사용하여 애플리케이션에 대한 `VCAP_SERVICES` 변수를 확인합니다.

```bash
# Log into PCF, if needed (enter credentials when prompted)
cf login -a <API endpoint>

# Set the organization and space containing the application, if not already selected during login.
cf target org <organization name>
cf target space <space name>

# Display variables for the application
cf env <Application Name>
```

`VCAP_SERVICES`를 검사하여 애플리케이션에 바인딩된 외부 서비스의 구성 설정을 확인합니다. 자세한 내용은 [PCF 설명서](https://docs.cloudfoundry.org/devguide/deploy-apps/environment-variable.html#VCAP-SERVICES)를 참조하세요.

#### <a name="all-other-external-resources"></a>기타 모든 외부 리소스

이 가이드에서 가능한 모든 외부 종속성을 문서화할 수는 없습니다. 마이그레이션 후에 사용자가 애플리케이션의 모든 외부 종속성이 충족될 수 있는지 확인해야 합니다.

[!INCLUDE [inventory-configuration-sources-and-secrets-spring-cloud](includes/inventory-configuration-sources-and-secrets-spring-cloud.md)]

[!INCLUDE [inspect-the-deployment-architecture-spring-cloud](includes/inspect-the-deployment-architecture-spring-cloud.md)]

## <a name="migration"></a>마이그레이션

### <a name="remove-explicit-configuration-server-settings"></a>명시적 구성 서버 설정 제거

마이그레이션하는 서비스에서 Eureka 설정의 명시적 할당을 찾아 제거합니다. 이러한 설정은 일반적으로 *application.properties* 또는 *application.yml* 파일에 표시되어 있습니다.

**application.yml**

```yaml
eureka:
  client:
    serviceUrl:
      defaultZone: http://myusername:mysecretpassword@localhost:8761/eureka/
```

이러한 설정이 애플리케이션 구성에 표시되면 제거합니다. Azure Spring Cloud는 구성 서버의 연결 정보를 자동으로 주입합니다.

### <a name="create-an-azure-spring-cloud-instance-and-apps"></a>Azure Spring Cloud 인스턴스 및 앱 만들기

Azure 구독에서 Azure Spring Cloud 인스턴스를 프로비저닝합니다. 그런 다음, 마이그레이션하는 모든 서비스에 대해 앱을 프로비저닝합니다. Spring Cloud 레지스트리 및 구성 서버는 포함하지 않습니다. Spring Cloud Gateway 서비스는 포함해야 합니다. 지침은 [빠른 시작: Azure Portal을 사용하여 기존 Azure Spring Cloud 애플리케이션 시작](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal)을 참조하세요.

### <a name="prepare-the-spring-cloud-config-server"></a>Azure Spring Cloud Config 서버 준비

Azure Spring Cloud 인스턴스에서 구성 서버를 구성합니다. 자세한 내용은 [자습서: 서비스용 Spring Cloud Config 서버 인스턴스 설정](/azure/spring-cloud/spring-cloud-tutorial-config-server)을 참조하세요.

> [!NOTE]
> 현재 Spring Cloud Config 리포지토리가 로컬 파일 시스템 또는 온-프레미스에 있는 경우 먼저 프라이빗 클라우드 기반 리포지토리(예: GitHub, Azure Repos 또는 BitBucket)로 구성 파일을 마이그레이션하거나 복제해야 합니다.

[!INCLUDE [ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud](includes/ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud.md)]

[!INCLUDE [configure-persistent-storage-azure-spring-cloud](includes/configure-persistent-storage-azure-spring-cloud.md)]

### <a name="migrate-spring-cloud-vault-secrets-to-azure-keyvault"></a>Spring Cloud Vault 비밀을 Azure KeyVault로 마이그레이션

Azure KeyVault Spring Boot Starter를 사용하여 Spring을 통해 비밀을 애플리케이션에 직접 주입할 수 있습니다. 자세한 내용은 [Azure Key Vault에 Spring Boot Starter를 사용하는 방법](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-key-vault)을 참조하세요.

> [!NOTE]
> 마이그레이션을 수행하려면 일부 비밀의 이름을 바꿔야 할 수 있습니다. 이에 따라 애플리케이션 코드를 적절히 업데이트하세요.

### <a name="migrate-all-certificates-to-keyvault"></a>모든 인증서를 KeyVault로 마이그레이션

Azure Spring Cloud는 JRE 키 저장소에 대한 액세스를 제공하지 않으므로 인증서를 Azure KeyVault로 마이그레이션하고 KeyVault의 인증서에 액세스하도록 애플리케이션 코드를 변경해야 합니다. 자세한 내용은 [Key Vault 인증서 시작](/azure/key-vault/certificates/certificate-scenarios) 및 [Java용 Azure Key Vault 인증서 클라이언트 라이브러리](/java/api/overview/azure/security-keyvault-certificates-readme)를 참조하세요.

### <a name="remove-application-performance-management-apm-integrations"></a>APM(애플리케이션 성능 관리) 통합 제거

APM 도구/에이전트와의 통합을 제거합니다. Azure Monitor를 사용하여 성능 관리를 구성하는 방법에 대한 자세한 내용은 [마이그레이션 후](#post-migration) 섹션을 참조하세요.

### <a name="replace-explicit-zipkin-dependencies-with-spring-cloud-starters"></a>명시적 Zipkin 종속성을 Spring Cloud Starter로 바꾸기

마이그레이션된 애플리케이션 중에서 명시적 Zipkin 종속성이 있는 애플리케이션이 있는 경우 [Azure Spring Cloud에서 배포용 Java Spring 애플리케이션 준비](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment)의 [분산 추적 종속성](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment#distributed-tracing-dependency) 섹션에서 설명한 대로 해당 애플리케이션을 제거하고 Spring Cloud Starter로 바꿉니다. Azure App Insights를 사용한 분산 추적에 대한 자세한 내용은 [마이그레이션 후](#post-migration) 섹션을 참조하세요.

### <a name="disable-metrics-clients-and-endpoints-in-your-applications"></a>애플리케이션에서 메트릭 클라이언트 및 엔드포인트 사용 안 함

사용된 메트릭 클라이언트 또는 애플리케이션에 공개된 메트릭 엔드포인트를 모두 제거합니다.

### <a name="deploy-the-services"></a>서비스 배포

[빠른 시작: Azure Portal을 사용하여 기존 Azure Spring Cloud 애플리케이션 시작](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal)에서 설명한 대로 마이그레이션된 각 마이크로서비스(Spring Cloud Config 및 Registry 서버 제외)를 배포합니다.

### <a name="configure-per-service-secrets-and-externalized-settings"></a>서비스별 비밀 및 외부화된 설정 구성

서비스별 구성 설정을 환경 변수로 각 서비스에 주입할 수 있습니다. Azure Portal에서 다음 단계를 사용합니다.

1. Azure Spring Cloud 인스턴스로 이동하여 **Apps(앱)** 를 선택합니다.
1. 구성할 서비스를 선택합니다.
1. **Configuration(구성)** 을 선택합니다.
1. 구성할 변수를 입력합니다.
1. **저장**을 선택합니다.

![Spring Cloud 앱 구성 설정](media/migrate-spring-cloud-to-azure-spring-cloud/spring-cloud-app-configuration-settings.png)

### <a name="migrate-and-enable-the-identity-provider"></a>ID 공급자 마이그레이션 및 사용

Spring Cloud 애플리케이션 중에서 인증 또는 권한 부여가 필요한 애플리케이션이 있는 경우 ID 공급자에 액세스하도록 구성되어 있는지 확인합니다.

* ID 공급자가 Azure Active Directory인 경우 변경할 필요가 없습니다.
* ID 공급자가 온-프레미스 Active Directory 포리스트인 경우 Azure Active Directory를 사용하여 하이브리드 ID 솔루션을 구현하는 것이 좋습니다. 지침은 [하이브리드 ID 설명서](/azure/active-directory/hybrid/)를 참조하세요.
* ID 공급자가 다른 온-프레미스 솔루션(예: PingFederate)인 경우 [Azure AD Connect 설치 사용자 지정](/azure/active-directory/hybrid/how-to-connect-install-custom) 항목을 참조하여 Azure Active Directory와 페더레이션하도록 구성합니다. 또는 Spring Security를 사용하여 [OAuth2/OpenID Connect](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#oauth2) 또는 [SAML](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-saml2)을 통해 ID 공급자를 사용하는 것이 좋습니다.

### <a name="update-client-applications"></a>클라이언트 애플리케이션 업데이트

마이그레이션된 애플리케이션에 대해 게시된 Azure Spring Cloud 엔드포인트를 사용하도록 모든 클라이언트 애플리케이션의 구성을 업데이트합니다.

## <a name="post-migration"></a>마이그레이션 후 작업

* 일관된 자동 배포를 위해 배포 파이프라인을 추가하는 것이 좋습니다. [Azure Pipelines](/azure/spring-cloud/spring-cloud-howto-cicd), [GitHub Actions](/azure/spring-cloud/spring-cloud-howto-github-actions) 및 [Jenkins](/azure/jenkins/tutorial-jenkins-deploy-cli-spring-cloud-service)에 대한 지침을 사용할 수 있습니다.

* 일부 또는 모든 최종 사용자가 사용할 수 있기 전에 스테이징 배포를 사용하여 프로덕션 환경에서 코드 변경을 테스트하는 것이 좋습니다. 자세한 내용은 [Azure Spring Cloud에서 스테이징 환경 설정](/azure/spring-cloud/spring-cloud-howto-staging-environment)을 참조하세요.

* 서비스 바인딩을 추가하여 애플리케이션을 지원되는 Azure 데이터베이스에 연결하는 것이 좋습니다. 이러한 서비스 바인딩을 사용하면 자격 증명을 포함한 연결 정보를 Spring Cloud 애플리케이션에 제공할 필요가 없습니다.

* [분산 추적 및 Azure App Insights를 사용](/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)하여 애플리케이션의 성능과 상호 작용을 모니터링하는 것이 좋습니다.

* Azure Monitor 경고 규칙 및 작업 그룹을 추가하여 이상 상태를 빠르게 검색하여 처리하는 것이 좋습니다. 자세한 내용은 [자습서: 경고 및 작업 그룹을 사용하여 Spring Cloud 리소스 모니터링](/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)을 참조하세요.

* Azure Spring Cloud 배포를 다른 지역에 복제하여 대기 시간을 줄이고 안정성과 내결함성을 높이는 것이 좋습니다. [Azure Traffic Manager](/azure/traffic-manager)를 사용하여 배포 간의 부하를 분산하거나 [Azure Front Door](/azure/frontdoor)를 사용하여 DDoS 보호가 있는 SSL 오프로딩 및 웹 애플리케이션 방화벽을 추가합니다.

* 지역 복제가 필요하지 않은 경우 [Azure Application Gateway](/azure/application-gateway)를 추가하여 DDoS 보호가 있는 SSL 오프로딩 및 웹 애플리케이션 방화벽을 추가합니다.

* 애플리케이션에서 레거시 Spring Cloud Netflix 구성 요소를 사용하는 경우 이 구성 요소를 현재 대체 구성 요소로 바꾸는 것이 좋습니다.

   | 레거시                        | 현재                                                |
   |-------------------------------|--------------------------------------------------------|
   | Spring Cloud Eureka           | Spring Cloud Service Registry                          |
   | Spring Cloud Netflix Zuul     | Spring Cloud Gateway                                   |
   | Spring Cloud Netflix Archaius | Spring Cloud 구성 서버                             |
   | Spring Cloud Netflix Ribbon   | Spring Cloud Load Balancer(클라이언트 쪽 부하 분산 장치) |
   | Spring Cloud Hystrix          | Spring Cloud Circuit Breaker + Resilience4J            |
   | Spring Cloud Netflix Turbine  | Micrometer + Prometheus                                |
