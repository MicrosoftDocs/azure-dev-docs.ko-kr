---
title: Tomcat 애플리케이션을 Azure App Service의 Tomcat으로 마이그레이션
description: 이 가이드에서는 Tomcat을 사용하여 Azure App Service에서 실행되도록 기존 Tomcat 애플리케이션을 마이그레이션하려는 경우 알아야 할 사항에 대해 설명합니다.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 1/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: e109d8f32e6ffbf42f155f0292a292bff237d6bf
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561769"
---
# <a name="migrate-tomcat-applications-to-tomcat-on-azure-app-service"></a>Tomcat 애플리케이션을 Azure App Service의 Tomcat으로 마이그레이션

이 가이드에서는 Tomcat 9.0을 사용하여 Azure App Service에서 실행되도록 기존 Tomcat 애플리케이션을 마이그레이션하려는 경우 알아야 할 사항에 대해 설명합니다.

## <a name="pre-migration"></a>사전 마이그레이션

마이그레이션을 성공적으로 수행하려면 시작하기 전에 다음 섹션에서 설명하는 평가 및 인벤토리 단계를 완료합니다.

이러한 마이그레이션 전 요구 사항 중에서 충족할 수 없는 요구 사항이 있는 경우 다음 마이그레이션 관련 가이드를 참조하세요.

* [Tomcat 애플리케이션을 Azure Kubernetes Service의 컨테이너로 마이그레이션](migrate-tomcat-to-containers-on-azure-kubernetes-service.md)
* Tomcat 애플리케이션을 Azure Virtual Machines로 마이그레이션(계획된 지침)

### <a name="switch-to-a-supported-platform"></a>지원되는 플랫폼으로 전환

App Service는 특정 버전의 Java에서 특정 버전의 Tomcat을 제공합니다. 호환성을 보장하려면 먼저 애플리케이션을 현재 환경에서 지원되는 Tomcat 및 Java 버전 중 하나로 마이그레이션한 후에 나머지 단계를 계속합니다. 결과 구성을 완전히 테스트해야 합니다. 테스트에서 Linux 배포판의 안정적인 최신 릴리스를 사용하세요.

[!INCLUDE [note-obtain-your-current-java-version-app-service](includes/note-obtain-your-current-java-version-app-service.md)]

현재 Tomcat 버전을 가져오려면 프로덕션 서버에 로그인하고 다음 명령을 실행합니다.

```bash
${CATALINA_HOME}/bin/version.sh
```

Azure App Service에서 사용하는 현재 버전을 가져오려면 Azure App Service에서 사용하려는 버전에 따라 [Tomcat 9](https://tomcat.apache.org/download-90.cgi)를 다운로드합니다.

[!INCLUDE [inventory-external-resources](includes/inventory-external-resources.md)]

[!INCLUDE [inventory-secrets](includes/inventory-secrets.md)]

[!INCLUDE [inventory-certificates](includes/inventory-certificates.md)]

[!INCLUDE [determine-whether-and-how-the-file-system-is-used](includes/determine-whether-and-how-the-file-system-is-used.md)]

<!-- App-Service-specific addendum to inventory-persistence-usage -->
#### <a name="dynamic-or-internal-content"></a>동적 또는 내부 콘텐츠

애플리케이션에서 자주 쓰고 읽는 파일(예: 임시 데이터 파일) 또는 애플리케이션에만 표시되는 정적 파일의 경우 Azure Storage를 App Service 파일 시스템에 탑재할 수 있습니다. 자세한 내용은 [App Service on Linux에 있는 Azure Storage의 콘텐츠 제공](/azure/app-service/configure-connect-to-azure-storage)을 참조하세요.

### <a name="identify-session-persistence-mechanism"></a>세션 지속성 메커니즘 식별

사용 중인 세션 지속성 관리자를 확인하려면 애플리케이션 및 Tomcat 구성의 *context.xml* 파일을 검사합니다. `<Manager>` 요소를 찾은 다음, `className` 특성의 값을 확인합니다.

[StandardManager](https://tomcat.apache.org/tomcat-9.0-doc/config/manager.html#Standard_Implementation) 또는 [FileStore](https://tomcat.apache.org/tomcat-9.0-doc/config/manager.html#Nested_Components)와 같은 Tomcat의 기본 제공 [PersistentManager](https://tomcat.apache.org/tomcat-9.0-doc/config/manager.html) 구현은 App Service와 같은 분산된 확장 플랫폼에서 사용하도록 설계되지 않았습니다. App Service는 여러 인스턴스 간에 부하를 분산하고 언제든지 인스턴스를 투명하게 다시 시작할 수 있으므로 변경 가능한 상태를 파일 시스템에 유지하지 않는 것이 좋습니다.

세션 지속성이 필요한 경우 외부 데이터 저장소에 쓰는 대체 `PersistentManager` 구현(예: Redis Cache를 사용하는 VMware Tanzu 세션 관리자)을 사용해야 합니다. 자세한 내용은 [Tomcat을 사용하여 Redis를 세션 캐시로 사용](/azure/app-service/containers/configure-language-java#use-redis-as-a-session-cache-with-tomcat)을 참조하세요.

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

### <a name="special-cases"></a>특수 사례

특정 프로덕션 시나리오에는 추가 변경이 필요하거나 추가 제한 사항이 있을 수 있습니다. 이러한 시나리오는 자주 발생하지 않을 수 있지만, 애플리케이션에 적용할 수 없거나 올바르게 해결되었는지 확인해야 합니다.

#### <a name="determine-whether-application-relies-on-scheduled-jobs"></a>애플리케이션에서 예약된 작업을 사용하는지 확인

Quartz 스케줄러 작업 또는 cron 작업과 같은 예약된 작업은 App Service에서 사용할 수 없습니다. App Service는 예약된 작업이 포함된 애플리케이션을 내부적으로 배포하지 못하도록 방지하지 않습니다. 그러나 애플리케이션이 확장되는 경우 동일한 예약된 작업이 예약된 기간마다 두 번 이상 실행될 수 있습니다. 이 경우 의도하지 않은 결과가 발생할 수 있습니다.

모든 예약된 작업을 애플리케이션 서버 내부 또는 외부에 인벤토리합니다.

#### <a name="determine-whether-your-application-contains-os-specific-code"></a>OS 관련 코드가 애플리케이션에 포함되어 있는지 확인

[!INCLUDE [determine-whether-your-application-contains-os-specific-code-no-title](includes/determine-whether-your-application-contains-os-specific-code-no-title.md)]

#### <a name="determine-whether-tomcat-clustering-is-used"></a>Tomcat 클러스터링이 사용되는지 확인

[Tomcat 클러스터링](https://tomcat.apache.org/tomcat-9.0-doc/cluster-howto.html)은 Azure App Service에서 지원되지 않습니다. 대신 Tomcat 특정 기능 없이 Azure App Service를 통해 크기 조정 및 부하 분산을 구성하고 관리할 수 있습니다. 복제본에서 사용할 수 있도록 세션 상태를 대체 위치로 유지할 수 있습니다. 자세한 내용은 [세션 지속성 메커니즘 식별](#identify-session-persistence-mechanism)을 참조하세요.

애플리케이션에서 클러스터링을 사용하는지 확인하려면 *server.xml* 파일에서 `<Host>` 요소 또는 `<Engine>` 요소 내의 `<Cluster>` 요소를 찾습니다.

#### <a name="determine-whether-non-http-connectors-are-used"></a>HTTP가 아닌 커넥터가 사용되는지 확인

App Service는 단일 HTTP 커넥터만 지원합니다. 애플리케이션에 AJP 커넥터와 같은 추가 커넥터가 필요한 경우 App Service를 사용하지 않습니다.

애플리케이션에서 사용하는 HTTP 커넥터를 확인하려면 Tomcat 구성의 *server.xml* 파일 내에서 `<Connector>` 요소를 찾습니다.

#### <a name="determine-whether-memoryrealm-is-used"></a>MemoryRealm이 사용되는지 확인

[MemoryRealm](https://tomcat.apache.org/tomcat-9.0-doc/api/org/apache/catalina/realm/MemoryRealm.html)에는 지속형 XML 파일이 필요합니다. Azure AppService에서 이 파일을 */home* 디렉터리, 해당 하위 디렉터리 중 하나 또는 탑재된 스토리지에 업로드해야 합니다. 이에 따라 `pathName` 매개 변수도 적절히 수정해야 합니다.

`MemoryRealm`이 현재 사용되고 있는지 확인하려면 *server.xml* 및 *context.xml* 파일을 검사하고 `className` 특성이 `org.apache.catalina.realm.MemoryRealm`으로 설정된 `<Realm>` 요소를 찾습니다.

#### <a name="determine-whether-ssl-session-tracking-is-used"></a>SSL 세션 추적이 사용되는지 확인

App Service는 Tomcat 런타임 외부에서 세션 오프로드를 수행하므로 [SSL 세션 추적](https://tomcat.apache.org/tomcat-9.0-doc/servletapi/javax/servlet/SessionTrackingMode.html#SSL)을 사용할 수 없습니다. 대신 다른 세션 추적 모드(`COOKIE` 또는 `URL`)를 사용합니다. SSL 세션 추적이 필요한 경우 App Service를 사용하지 않습니다.

#### <a name="determine-whether-accesslogvalve-is-used"></a>AccessLogValve가 사용되는지 확인

[AccessLogValve](https://tomcat.apache.org/tomcat-9.0-doc/api/org/apache/catalina/valves/AccessLogValve.html)를 사용하는 경우 `directory` 매개 변수를 `/home/LogFiles` 또는 해당 하위 디렉터리 중 하나로 설정해야 합니다.

## <a name="migration"></a>마이그레이션

### <a name="parameterize-the-configuration"></a>구성 매개 변수화

마이그레이션 전 단계의 *server.xml* 및 *context.xml* 파일에서 데이터 원본과 같은 일부 비밀 및 외부 종속성을 확인했을 가능성이 높습니다. 확인된 각 항목에 대해 사용자 이름, 암호, 연결 문자열 또는 URL을 환경 변수로 바꿉니다.

예를 들어 *context.xml* 파일에 다음 요소가 포함되어 있다고 가정합니다.

```xml
<Resource
    name="jdbc/dbconnection"
    type="javax.sql.DataSource"
    url="jdbc:postgresql://postgresdb.contoso.com/wickedsecret?ssl=true"
    driverClassName="org.postgresql.Driver"
    username="postgres"
    password="t00secure2gue$$"
/>
```

이 경우 다음 예제와 같이 변경할 수 있습니다.

```xml
<Resource
    name="jdbc/dbconnection"
    type="javax.sql.DataSource"
    url="${postgresdb.connectionString}"
    driverClassName="org.postgresql.Driver"
    username="${postgresdb.username}"
    password="${postgresdb.password}"
/>
```

### <a name="provision-an-app-service-plan"></a>App Service 계획 프로비저닝

[App Service 가격](https://azure.microsoft.com/pricing/details/app-service/linux/)의 사용 가능한 서비스 계획 목록에서 사양이 현재 프로덕션 하드웨어의 사양과 일치하거나 초과하는 계획을 선택합니다.

> [!NOTE]
> 스테이징/카나리아 배포를 실행하거나 배포 슬롯을 사용하려면 추가 용량이 App Service 계획에 포함되어야 합니다. Java 애플리케이션에는 프리미엄 이상의 계획을 사용하는 것이 좋습니다. 자세한 내용은 [Azure App Service에서 스테이징 환경 설정](/azure/app-service/deploy-staging-slots)을 참조하세요.

그런 다음, App Service 계획을 만듭니다. 자세한 내용은 [Azure에서 App Service 계획 관리](/azure/app-service/app-service-plan-manage)를 참조하세요.

### <a name="create-and-deploy-web-apps"></a>Web App(s) 만들기 및 배포

App Service 계획에서 Tomcat 서버에 배포된 모든 WAR 파일에 대한 웹앱을 만들어야 합니다(런타임 스택으로 Tomcat 서버 선택).

> [!NOTE]
> 여러 개의 WAR 파일을 단일 웹앱에 배포할 수도 있지만 이는 매우 바람직하지 않습니다. 여러 개의 WAR 파일이 단일 웹앱에 배포되면 각 애플리케이션이 자체의 사용량 요구 사항에 따라 확장되지 않습니다. 또한 복잡성을 후속 배포 파이프라인에 추가합니다. 단일 URL에서 여러 애플리케이션을 사용해야 하는 경우 [Azure Application Gateway](/azure/application-gateway/)와 같은 라우팅 솔루션을 사용하는 것이 좋습니다.

#### <a name="maven-applications"></a>Maven 애플리케이션

애플리케이션이 Maven POM 파일에서 빌드되는 경우 [Maven용 Webapp 플러그 인을 사용](/azure/app-service/containers/quickstart-java#configure-the-maven-plugin)하여 Web App을 만들고 애플리케이션을 배포합니다.

#### <a name="non-maven-applications"></a>비 Maven 애플리케이션

Maven 플러그 인을 사용할 수 없는 경우 다음과 같은 다른 메커니즘을 통해 Web App을 프로비저닝해야 합니다.

* [Azure Portal](https://portal.azure.com/#create/Microsoft.WebSite)
* [Azure CLI](/cli/azure/webapp#az-webapp-create)
* [Azure PowerShell](/powershell/module/az.websites/new-azwebapp)

Web App이 만들어지면 [사용 가능한 배포 메커니즘](/azure/app-service/deploy-zip) 중 하나를 사용하여 애플리케이션을 배포합니다.

### <a name="migrate-jvm-runtime-options"></a>JVM 런타임 옵션 마이그레이션

애플리케이션에 특정 런타임 옵션이 필요한 경우 [가장 적합한 메커니즘을 사용하여 해당 옵션을 지정](/azure/app-service/containers/configure-language-java#set-java-runtime-options)합니다.

### <a name="populate-secrets"></a>비밀 채우기

[애플리케이션 설정]을 사용하여 애플리케이션과 관련된 모든 비밀을 저장합니다. 여러 애플리케이션에서 동일한 비밀을 사용하거나 세분화된 액세스 정책 및 감사 기능이 필요한 경우 [Azure Key Vault](/azure/app-service/containers/configure-language-java#use-keyvault-references)를 대신 사용합니다.

[!INCLUDE [configure-custom-domain-and-ssl](includes/configure-custom-domain-and-ssl.md)]

[!INCLUDE [import-backend-certificates](includes/import-backend-certificates.md)]

### <a name="migrate-data-sources-libraries-and-jndi-resources"></a>데이터 원본, 라이브러리 및 JNDI 리소스 마이그레이션

데이터 원본 구성 단계는 [Azure App Service에 대한 Linux Java 앱 구성](/azure/app-service/containers/configure-language-java)의 [데이터 원본](/azure/app-service/containers/configure-language-java#data-sources) 섹션을 참조하세요.

[!INCLUDE[Tomcat datasource additional instructions](includes/tomcat-datasource-additional-instructions.md)]

[데이터 원본 JAR 파일과 동일한 단계](/azure/app-service/containers/configure-language-java#finalize-configuration)를 수행하여 추가 서버 수준 클래스 경로 종속성을 마이그레이션합니다.

추가 [공유 서버 수준 JDNI 리소스](/azure/app-service/containers/configure-language-java#shared-server-level-resources)를 마이그레이션합니다.

> [!NOTE]
> 웹앱당 추천되는 하나의 WAR 아키텍처를 따르는 경우 서버 수준 클래스 경로 라이브러리 및 JNDI 리소스를 애플리케이션으로 마이그레이션하는 것이 좋습니다. 이렇게 하면 구성 요소 거버넌스 및 변경 관리가 크게 간소화됩니다.

### <a name="migrate-remaining-configuration"></a>나머지 구성 마이그레이션

이전 섹션이 완료되면 사용자 지정 가능한 서버 구성이 */home/tomcat/conf* 에 있어야 합니다.

추가 구성(예: [영역(realm)](https://tomcat.apache.org/tomcat-9.0-doc/config/realm.html) 및 [JASPIC](https://tomcat.apache.org/tomcat-9.0-doc/config/jaspic.html))을 복사하여 마이그레이션을 완료합니다.

[!INCLUDE [migrate-scheduled-jobs](includes/migrate-scheduled-jobs.md)]

### <a name="restart-and-smoke-test"></a>다시 시작 및 스모크 테스트

마지막으로, Web App을 다시 시작하여 모든 구성 변경 내용을 적용해야 합니다. 다시 시작이 완료되면 애플리케이션이 올바르게 실행되고 있는지 확인합니다.

## <a name="post-migration"></a>마이그레이션 후 작업

이제 애플리케이션이 Azure App Service로 마이그레이션되었으므로 예상대로 작동하는지 확인해야 합니다. 이 작업이 완료되면 애플리케이션을 클라우드 네이티브로 만들 수 있는 몇 가지 추천 사항이 있습니다.

### <a name="recommendations"></a>권장 사항

* */home* 디렉터리를 파일 스토리지에 사용하도록 선택한 경우 [이를 Azure Storage로 바꾸는 것](/azure/app-service/configure-connect-to-azure-storage)이 좋습니다.

* 연결 문자열, SSL 키 및 기타 비밀 정보가 포함된 구성이 */home* 디렉터리에 있는 경우 [Azure Key Vault](/azure/app-service/app-service-key-vault-references) 및/또는 [애플리케이션 설정을 통한 매개 변수 주입](/azure/app-service/configure-common#configure-app-settings)의 조합을 사용하는 것이 좋습니다(가능한 경우).

* 가동 중지 시간이 없는 안정적인 배포를 위해 [배포 슬롯을 사용](/azure/app-service/deploy-staging-slots)하는 것이 좋습니다.

* DevOps 전략을 설계하고 구현합니다. 개발 속도를 높이는 동시에 안정성을 유지하기 위해 [Azure Pipelines를 사용하여 배포를 자동화하고 테스트](/azure/devops/pipelines/ecosystems/java-webapp)하는 것이 좋습니다. 배포 슬롯을 사용하는 경우 [슬롯에 대한 배포를 자동화](/azure/devops/pipelines/targets/webapp?tabs=yaml#deploy-to-a-slot)한 다음, 슬롯 교환을 수행할 수 있습니다.

* 비즈니스 연속성 및 재해 복구 전략을 설계하고 구현합니다. 중요 업무용 애플리케이션의 경우 [다중 지역 배포 아키텍처](/azure/architecture/reference-architectures/app-service-web-app/multi-region)를 고려하세요.
