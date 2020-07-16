---
title: Spring Boot 애플리케이션을 Azure Spring Cloud로 마이그레이션
description: 이 가이드에서는 Azure Spring Cloud에서 실행할 기존 Spring Boot 애플리케이션을 마이그레이션하려는 경우에 알아야 할 사항을 설명합니다.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 5/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: c779a85f49ccd7507882fbd123a329addca60a7c
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379747"
---
# <a name="migrate-spring-boot-applications-to-azure-spring-cloud"></a>Spring Boot 애플리케이션을 Azure Spring Cloud로 마이그레이션

이 가이드에서는 Azure Spring Cloud에서 실행할 기존 Spring Boot 애플리케이션을 마이그레이션하려는 경우에 알아야 할 사항을 설명합니다.

## <a name="pre-migration"></a>사전 마이그레이션

마이그레이션을 성공적으로 수행하려면 시작하기 전에 다음 섹션에서 설명하는 평가 및 인벤토리 단계를 완료합니다.

이러한 마이그레이션 전 요구 사항 중에서 충족할 수 없는 요구 사항이 있는 경우 다음 마이그레이션 관련 가이드를 참조하세요.

* 실행 가능한 JAR 애플리케이션을 Azure Kubernetes Service의 컨테이너로 마이그레이션(계획된 지침)
* 실행 가능한 JAR 애플리케이션을 Azure Virtual Machines로 마이그레이션(계획된 지침)

### <a name="inspect-application-components"></a>애플리케이션 구성 요소 검사

[!INCLUDE [identify-local-state](includes/identify-local-state-azure-spring-cloud.md)]

[!INCLUDE [static-content-azure-spring-cloud](includes/determine-whether-and-how-the-file-system-is-used-azure-spring-cloud.md)]

#### <a name="determine-whether-any-of-the-services-contain-os-specific-code"></a>서비스 중에서 OS 관련 코드가 포함된 서비스가 있는지 확인

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code-no-title.md)]

[!INCLUDE [switch-to-a-supported-platform-azure-spring-cloud](includes/switch-to-a-supported-platform-azure-spring-cloud.md)]

[!INCLUDE [identify-spring-boot-versions](includes/identify-spring-boot-versions.md)]

Spring Boot 1.x를 사용하는 애플리케이션의 경우 [Spring Boot 2.0 마이그레이션 가이드](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide)에 따라 지원되는 Spring Boot 버전으로 업데이트합니다. 지원되는 버전은 [배포용 Java Spring 앱 준비](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment#spring-boot-and-spring-cloud-versions)를 참조하세요.

[!INCLUDE [identify-logs-metrics-apm-azure-spring-cloud.md](includes/identify-logs-metrics-apm-azure-spring-cloud.md)]

### <a name="inventory-external-resources"></a>인벤토리 외부 리소스

데이터 원본, JMS 메시지 브로커, 다른 서비스의 URL 등과 같은 외부 리소스를 확인합니다. Spring Boot 애플리케이션에서 이러한 리소스의 구성은 일반적으로 *src/main/directory* 폴더의 *application.properties* 또는 *application.yml*이라는 파일에서 찾을 수 있습니다.

[!INCLUDE [inventory-databases-spring-boot](includes/inventory-databases-spring-boot.md)]

[!INCLUDE [identify-jms-brokers-in-spring](includes/identify-jms-brokers-in-spring.md)]

사용 중인 broker가 확인되면 해당 설정을 찾습니다. Spring Boot 애플리케이션에서 이러한 설정은 일반적으로 애플리케이션 디렉터리의 *application.properties* 및 *application.yml* 파일에서 찾을 수 있습니다.

[!INCLUDE [jms-broker-settings-examples-in-spring](includes/jms-broker-settings-examples-in-spring.md)]

[!INCLUDE [identify-external-caches-azure-spring-cloud](includes/identify-external-caches-azure-spring-cloud.md)]

[!INCLUDE [inventory-identity-providers-spring-boot.md](includes/inventory-identity-providers-spring-boot.md)]

#### <a name="identify-any-clients-relying-on-a-non-standard-port"></a>비표준 포트를 사용하는 클라이언트 식별

Azure Spring Cloud는 배포된 애플리케이션의 `server.port` 설정을 덮어씁니다. 클라이언트가 443이 아닌 다른 포트에 제공되는 애플리케이션을 사용하는 경우 수정이 필요합니다.

#### <a name="all-other-external-resources"></a>기타 모든 외부 리소스

이 가이드에서 가능한 모든 외부 종속성을 문서화할 수는 없습니다. 마이그레이션 후에 사용자가 애플리케이션의 모든 외부 종속성이 충족될 수 있는지 확인해야 합니다.

[!INCLUDE [inventory-configuration-sources-and-secrets-spring-boot](includes/inventory-configuration-sources-and-secrets-spring-boot.md)]

[!INCLUDE [inspect-the-deployment-architecture-spring-boot](includes/inspect-the-deployment-architecture-spring-boot.md)]

## <a name="migration"></a>마이그레이션

### <a name="create-an-azure-spring-cloud-instance-and-apps"></a>Azure Spring Cloud 인스턴스 및 앱 만들기

Azure 구독에 아직 Azure Spring Cloud 인스턴스가 없다면 지금 프로비저닝합니다. 그런 다음, 애플리케이션 템플릿을 만듭니다. 자세한 내용은 [빠른 시작: Azure Portal을 사용하여 기존 Azure Spring Cloud 애플리케이션 시작](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal)의 지침을 따릅니다.

[!INCLUDE [ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud](includes/ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud.md)]

[!INCLUDE [configure-persistent-storage-azure-spring-cloud](includes/configure-persistent-storage-azure-spring-cloud.md)]

### <a name="migrate-all-certificates-to-keyvault"></a>모든 인증서를 KeyVault로 마이그레이션

Azure Spring Cloud는 JRE 키 저장소에 대한 액세스를 제공하지 않으므로 인증서를 Azure KeyVault로 마이그레이션하고 KeyVault의 인증서에 액세스하도록 애플리케이션 코드를 변경해야 합니다. 자세한 내용은 [Key Vault 인증서 시작](/azure/key-vault/certificates/certificate-scenarios) 및 [Java용 Azure Key Vault 인증서 클라이언트 라이브러리](/java/api/overview/azure/security-keyvault-certificates-readme)를 참조하세요.

### <a name="remove-application-performance-management-apm-integrations"></a>APM(애플리케이션 성능 관리) 통합 제거

APM 도구/에이전트와의 통합을 제거합니다. Azure Monitor를 사용하여 성능 관리를 구성하는 방법에 대한 자세한 내용은 [마이그레이션 후](#post-migration) 섹션을 참조하세요.

### <a name="disable-metrics-clients-and-endpoints-in-your-applications"></a>애플리케이션에서 메트릭 클라이언트 및 엔드포인트 사용 안 함

사용된 메트릭 클라이언트 또는 애플리케이션에 공개된 메트릭 엔드포인트를 모두 제거합니다.

### <a name="deploy-the-application"></a>애플리케이션 배포

마이그레이션된 각 마이크로서비스(Spring Cloud Config 및 Registry 서버는 제외)를 배포합니다. 자세한 지침은 [빠른 시작: Azure Portal을 사용하여 기존 Azure Spring Cloud 애플리케이션 시작](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal)을 참조하세요.

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
* ID 공급자가 온-프레미스 Active Directory 포리스트인 경우 Azure Active Directory를 사용하여 하이브리드 ID 솔루션을 구현하는 것이 좋습니다. 자세한 내용은 [하이브리드 ID 설명서](/azure/active-directory/hybrid/)를 참조하세요.
* ID 공급자가 다른 온-프레미스 솔루션(예: PingFederate)인 경우 [Azure AD Connect 설치 사용자 지정](/azure/active-directory/hybrid/how-to-connect-install-custom) 항목을 참조하여 Azure Active Directory와 페더레이션하도록 구성합니다. 또는 Spring Security를 사용하여 [OAuth2/OpenID Connect](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#oauth2) 또는 [SAML](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-saml2)을 통해 ID 공급자를 사용하는 것이 좋습니다.

### <a name="expose-the-application"></a>애플리케이션 공개

기본적으로 Azure Spring Cloud에 배포된 애플리케이션은 외부에서 볼 수 없습니다. 다음 명령을 사용하여 애플리케이션을 공개로 설정하면 애플리케이션을 공개할 수 있습니다.

```azurecli
az spring-cloud app update -n <application name> --is-public true
```

Spring Cloud 게이트웨이를 사용 중이거나 사용할 생각이라면 이 단계를 건너뜁니다(자세한 내용은 다음 섹션에서 설명).

## <a name="post-migration"></a>마이그레이션 후 작업

마이그레이션을 완료했으므로, 애플리케이션이 예상대로 작동하는지 확인합니다. 그 후에는 다음 권장 사항에 따라 애플리케이션을 클라우드 네이티브로 만들 수 있습니다.

* 애플리케이션이 Spring Cloud Registry에서 작동하도록 설정하는 방안을 고려해 보세요. 이렇게 하면 배포된 다른 마이크로서비스 및 클라이언트에서 애플리케이션을 동적으로 검색할 수 있습니다. 자세한 내용은 [자습서: 배포할 Java Spring 앱 준비](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment) 그런 다음, Spring 클라이언트 부하 분산 장치를 사용하도록 애플리케이션 클라이언트를 수정합니다. 이렇게 하면 클라이언트는 실행 중인 모든 애플리케이션 인스턴스의 주소를 가져오고, 다른 인스턴스가 손상되거나 응답하지 않을 때 작동하는 인스턴스를 찾을 수 있습니다. 자세한 내용은 Spring 블로그의 [Spring 팁: Spring Cloud 부하 분산 장치](https://spring.io/blog/2020/03/25/spring-tips-spring-cloud-loadbalancer)를 참조하세요.

* 애플리케이션을 공개로 설정하는 대신 [Spring Cloud Gateway](https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/current/reference/html/) 인스턴스를 추가하는 것이 좋습니다. Spring Cloud Gateway는 Azure Spring Cloud 인스턴스에 배포된 모든 애플리케이션/마이크로서비스에 대한 단일 엔드포인트를 제공합니다. Spring Cloud Gateway가 이미 배포된 경우에는 새로 배포된 애플리케이션으로 트래픽을 라우팅하도록 구성해야 합니다.

* Spring Cloud Config 서버를 추가하여 모든 Spring Cloud 마이크로서비스의 구성을 중앙에서 관리하고 버전을 제어하는 것이 좋습니다. 먼저 구성을 저장할 Git 리포지토리를 만들고 이 구성을 사용하도록 Azure Spring Cloud 인스턴스를 구성합니다. 자세한 내용은 [자습서: 서비스용 Spring Cloud Config 서버 인스턴스 설정](/azure/spring-cloud/spring-cloud-tutorial-config-server)을 참조하세요. 그 후에는 다음 단계에 따라 구성을 마이그레이션합니다.

  1. Azure Spring Cloud 인스턴스에서 정의한 애플리케이션과 동일한 이름의 디렉터리를 구성 Git 리포지토리에 만듭니다.

  1. 이 디렉터리 안에서 다음 콘텐츠가 포함된 *bootstrap.yml* 파일을 만듭니다.

     ```yml
     spring:
       application:
         name: <Your the application name used in the previous step>
     ```

  1. 위의 디렉터리 안에서 *application.yml* 파일을 만든 다음, 애플리케이션 설정을 이 파일로 이동합니다. 이전에는 설정이 *.properties* 파일에 있었던 경우에는 YAML로 변환해야 합니다.

  1. 변경 내용을 Git 리포지토리에 커밋하고 푸시합니다.

* 일관된 자동 배포를 위해 배포 파이프라인을 추가하는 것이 좋습니다. [Azure Pipelines](/azure/spring-cloud/spring-cloud-howto-cicd), [GitHub Actions](/azure/spring-cloud/spring-cloud-howto-github-actions) 및 [Jenkins](/azure/jenkins/tutorial-jenkins-deploy-cli-spring-cloud-service)에 대한 지침을 사용할 수 있습니다.

* 일부 또는 모든 최종 사용자가 사용할 수 있기 전에 스테이징 배포를 사용하여 프로덕션 환경에서 코드 변경을 테스트하는 것이 좋습니다. 자세한 내용은 [Azure Spring Cloud에서 스테이징 환경 설정](/azure/spring-cloud/spring-cloud-howto-staging-environment)을 참조하세요.

* 서비스 바인딩을 추가하여 애플리케이션을 지원되는 Azure 데이터베이스에 연결하는 것이 좋습니다. 이러한 서비스 바인딩을 사용하면 자격 증명을 포함한 연결 정보를 Spring Cloud 애플리케이션에 제공할 필요가 없습니다.

* 분산 추적 및 Azure App Insights를 사용하여 애플리케이션의 성능과 상호 작용을 모니터링하는 것이 좋습니다. 자세한 내용은 [Azure Spring Cloud에서 분산 추적 사용](/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)을 참조하세요.

* Azure Monitor 경고 규칙 및 작업 그룹을 추가하여 이상 상태를 빠르게 검색하여 처리하는 것이 좋습니다. 자세한 내용은 [자습서: 경고 및 작업 그룹을 사용하여 Spring Cloud 리소스 모니터링](/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)을 참조하세요.

* Azure Spring Cloud 배포를 다른 지역에 복제하여 대기 시간을 줄이고 안정성과 내결함성을 높이는 것이 좋습니다. [Azure Traffic Manager](/azure/traffic-manager)를 사용하여 배포 간의 부하를 분산하거나 [Azure Front Door](/azure/frontdoor)를 사용하여 DDoS 보호가 있는 SSL 오프로딩 및 웹 애플리케이션 방화벽을 추가합니다.

* 지역 복제가 필요하지 않은 경우 [Azure Application Gateway](/azure/application-gateway)를 추가하여 DDoS 보호가 있는 SSL 오프로딩 및 웹 애플리케이션 방화벽을 추가합니다.
