---
title: Spring Boot 애플리케이션을 Azure App Service로 마이그레이션
description: 이 가이드에서는 기존 Spring Boot를 Azure App Service로 마이그레이션하려는 경우 알아야 할 사항에 대해 설명합니다.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: e4a12380521828ac69aead376ae7ff5797e300ab
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990305"
---
# <a name="migrate-spring-boot-applications-to-azure-app-service"></a>Spring Boot 애플리케이션을 Azure App Service로 마이그레이션

이 가이드에서는 기존 Spring Boot 애플리케이션을 Azure App Service로 마이그레이션하려는 경우 알아야 할 사항에 대해 설명합니다.

## <a name="pre-migration"></a>사전 마이그레이션

마이그레이션을 성공적으로 수행하려면 시작하기 전에 다음 섹션에서 설명하는 평가 및 인벤토리 단계를 완료합니다.

이러한 마이그레이션 전 요구 사항 중에서 충족할 수 없는 요구 사항이 있는 경우 다음 마이그레이션 관련 가이드를 참조하세요.

* 실행 가능한 JAR 애플리케이션을 Azure Kubernetes Service의 컨테이너로 마이그레이션(계획된 지침)
* 실행 가능한 JAR 애플리케이션을 Azure Virtual Machines로 마이그레이션(계획된 지침)

### <a name="switch-to-a-supported-platform"></a>지원되는 플랫폼으로 전환

App Service는 특정 버전의 Java SE를 제공합니다. 호환성을 보장하기 위해, 애플리케이션을 현재 환경에서 지원되는 버전 중 하나로 마이그레이션한 후 나머지 단계를 진행하세요. 결과 구성을 완전히 테스트해야 합니다. 테스트에서 Linux 배포판의 안정적인 최신 릴리스를 사용하세요.

[!INCLUDE [note-obtain-your-current-java-version-app-service](includes/note-obtain-your-current-java-version-app-service.md)]

### <a name="inventory-external-resources"></a>인벤토리 외부 리소스

데이터 원본, JMS 메시지 브로커, 다른 서비스의 URL 등과 같은 외부 리소스를 확인합니다. Spring Boot 애플리케이션에서는 일반적으로 *application.properties* 또는 *application.yml* 파일의 *src/main/directory*에서 이러한 리소스의 구성을 찾을 수 있습니다. 또한 프로덕션 배포의 환경 변수에서 관련 구성 설정을 확인합니다.

[!INCLUDE [inventory-databases-spring-boot](includes/inventory-databases-spring-boot.md)]

[!INCLUDE [identify-jms-brokers-in-spring](includes/identify-jms-brokers-in-spring.md)]

사용되는 broker가 확인되면 해당 설정을 찾습니다. 일반적으로 Spring Boot에 대한 *application.properties* 및 *application.yml* 파일에 있습니다.

[!INCLUDE [jms-broker-settings-examples-in-spring](includes/jms-broker-settings-examples-in-spring.md)]

#### <a name="all-other-external-resources"></a>기타 모든 외부 리소스

이 가이드에서 가능한 모든 외부 종속성을 문서화하는 것은 불가능합니다. App Service 마이그레이션 후 애플리케이션의 모든 외부 종속성을 충족할 수 있는지 확인하는 것은 팀의 책임입니다.

### <a name="inventory-secrets"></a>인벤토리 비밀

#### <a name="passwords-and-secure-strings"></a>암호 및 보안 문자열

프로덕션 배포의 모든 속성 및 구성 파일과 모든 환경 변수에 비밀 문자열과 암호가 있는지 확인합니다. Spring Boot 애플리케이션에서 이러한 문자열은 *application.properties* 또는 *application.yml*에서 찾을 수 있습니다.

#### <a name="inventory-certificates"></a>인증서 인벤토리화

[!INCLUDE [inventory-certificates](includes/inventory-certificates.md)]

[!INCLUDE [determine-whether-and-how-the-file-system-is-used](includes/determine-whether-and-how-the-file-system-is-used.md)]

### <a name="special-cases"></a>특수 사례

특정 프로덕션 시나리오에는 추가 변경이 필요하거나 추가 제한 사항이 있을 수 있습니다. 이러한 시나리오는 자주 발생하지 않을 수 있지만, 애플리케이션에 적용할 수 없거나 올바르게 해결되었는지 확인해야 합니다.

#### <a name="determine-whether-application-relies-on-scheduled-jobs"></a>애플리케이션에서 예약된 작업을 사용하는지 확인

Quartz 스케줄러 작업 또는 cron 작업과 같은 예약된 작업은 App Service에서 사용할 수 없습니다. App Service는 예약된 작업이 포함된 애플리케이션을 내부적으로 배포하지 못하도록 방지하지 않습니다. 그러나 애플리케이션이 확장되는 경우 동일한 예약된 작업이 예약된 기간마다 두 번 이상 실행될 수 있습니다. 이 경우 의도하지 않은 결과가 발생할 수 있습니다.

예약된 작업을 애플리케이션 프로세스 내부 또는 외부에 인벤토리화합니다.

#### <a name="determine-whether-your-application-contains-os-specific-code"></a>OS 관련 코드가 애플리케이션에 포함되어 있는지 확인

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code-no-title.md)]

#### <a name="identify-all-outside-processesdaemons-running-on-the-production-servers"></a>프로덕션 서버에서 실행되는 모든 외부 프로세스/디먼 확인

디먼 모니터링과 같이 애플리케이션 서버 외부에서 실행되는 모든 프로세스를 다른 곳으로 마이그레이션하거나 제거해야 합니다.

#### <a name="identify-handling-of-non-http-requests-or-multiple-ports"></a>비 HTTP 요청 또는 여러 포트의 처리를 확인해야 합니다.

App Service는 단일 포트에서 단일 HTTP 엔드포인트만 지원합니다. 애플리케이션에서 HTTP 이외의 다른 프로토콜을 사용하여 여러 포트에서 수신 대기하거나 요청을 수락하는 경우 Azure App Service를 사용하지 마세요.

## <a name="migration"></a>마이그레이션

### <a name="parameterize-the-configuration"></a>구성 매개 변수화

모든 외부 리소스 좌표(예: 데이터베이스 연결 문자열) 및 기타 사용자 지정 가능 설정을 환경 변수에서 읽을 수 있는지 확인합니다. Spring Boot 애플리케이션을 마이그레이션하는 경우 모든 구성 설정을 이미 외부화할 수 있어야 합니다. 자세한 내용은 Spring Boot 설명서의 [외부화된 구성](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-external-config)을 참조하세요.

다음은 *application.properties* 파일의 `SERVICEBUS_CONNECTION_STRING` 환경 변수를 참조하는 예제입니다.

```properties
spring.jms.servicebus.connection-string=${SERVICEBUS_CONNECTION_STRING}
spring.jms.servicebus.topic-client-id=contoso1
spring.jms.servicebus.idle-timeout=10000
```

### <a name="provision-an-app-service-plan"></a>App Service 계획 프로비저닝

[사용 가능한 서비스 계획 목록](https://azure.microsoft.com/pricing/details/app-service/linux/)에서 사양이 현재 프로덕션 하드웨어의 사양과 일치하거나 초과하는 계획을 선택합니다.

> [!NOTE]
> 스테이징/카나리아 배포를 실행하거나 [배포 슬롯](/azure/app-service/deploy-staging-slots)을 사용하려면 추가 용량이 App Service 계획에 포함되어야 합니다. Java 애플리케이션에는 프리미엄 이상의 계획을 사용하는 것이 좋습니다.

[App Service 계획을 만듭니다](/azure/app-service/app-service-plan-manage#create-an-app-service-plan).

### <a name="create-and-deploy-web-apps"></a>웹앱 만들기 및 배포

실행하려는 모든 실행 가능 JAR 파일의 App Service 계획에 대한 웹앱을 만들어야 합니다(런타임 스택으로 "Java SE" 선택).

#### <a name="maven-applications"></a>Maven 애플리케이션

애플리케이션이 Maven POM 파일에서 빌드되는 경우 [Maven용 Webapp 플러그 인을 사용](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-with-maven-plugin#configure-maven-plugin-for-azure-app-service)하여 Web App을 만들고 애플리케이션을 배포합니다.

#### <a name="non-maven-applications"></a>비 Maven 애플리케이션

Maven 플러그 인을 사용할 수 없는 경우 다음과 같은 다른 메커니즘을 통해 Web App을 프로비저닝해야 합니다.

* [Azure Portal](https://portal.azure.com/#create/Microsoft.WebSite)
* [Azure CLI](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)
* [Azure PowerShell](/powershell/module/az.websites/new-azwebapp)

Web App이 만들어지면 [사용 가능한 배포 메커니즘](/azure/app-service/deploy-ftp) 중 하나를 사용하여 애플리케이션을 배포합니다. 가능하다면 애플리케이션을 */home/site/wwwroot/app.jar*에 업로드해야 합니다. JAR 이름을 *app.jar*로 바꾸지 않으려면 JAR을 실행하는 명령을 사용하여 셸 스크립트를 업로드할 수 있습니다. 그런 다음, 포털의 구성 섹션에 있는 [시작 파일](/azure/app-service/containers/app-service-linux-faq#built-in-images) 텍스트 상자에 이 스크립트의 전체 경로를 붙여넣습니다. 시작 스크립트는 배치된 디렉터리에서 실행되지 않습니다. 따라서 항상 절대 경로를 사용하여 시작 스크립트의 파일을 참조해야 합니다(예: `java -jar /home/myapp/myapp.jar`).

### <a name="migrate-jvm-runtime-options"></a>JVM 런타임 옵션 마이그레이션

애플리케이션에 특정 런타임 옵션이 필요한 경우 [가장 적합한 메커니즘을 사용하여 해당 옵션을 지정](/azure/app-service/containers/configure-language-java#set-java-runtime-options)합니다.

[!INCLUDE [configure-custom-domain-and-ssl](includes/configure-custom-domain-and-ssl.md)]

[!INCLUDE [import-backend-certificates](includes/import-backend-certificates.md)]

### <a name="migrate-external-resource-coordinates-and-other-settings"></a>외부 리소스 좌표 및 기타 설정 마이그레이션

[다음 단계에 따라 연결 문자열 및 기타 설정 마이그레이션](/azure/app-service/containers/configure-language-java#spring-boot-1)을 따릅니다.

> [!NOTE]
> [구성 매개 변수화](#parameterize-the-configuration) 섹션의 변수를 사용하여 매개 변수화된 Spring Boot 애플리케이션 설정의 경우에는 애플리케이션 구성에서 이러한 환경 변수를 정의해야 합니다. 환경 변수를 사용하여 명시적으로 매개 변수화되지 않은 Spring Boot 애플리케이션 설정은 애플리케이션 구성을 통해 재정의할 수 있습니다. 다음은 그 예입니다.

  ```properties
  spring.jms.servicebus.connection-string=${CUSTOMCONNSTR_SERVICE_BUS}
  spring.jms.servicebus.topic-client-id=contoso1
  spring.jms.servicebus.idle-timeout=1800000
  ```

![App Service 애플리케이션 구성](media/migrate-spring-boot-to-app-service/app-service-parameterized-spring-boot-app-settings.png)

[!INCLUDE [migrate-scheduled-jobs](includes/migrate-scheduled-jobs.md)]

### <a name="restart-and-smoke-test"></a>다시 시작 및 스모크 테스트

마지막으로, Web App을 다시 시작하여 모든 구성 변경 내용을 적용해야 합니다. 다시 시작이 완료되면 애플리케이션이 올바르게 실행되고 있는지 확인합니다.

## <a name="post-migration"></a>마이그레이션 후 작업

이제 애플리케이션이 Azure App Service로 마이그레이션되었으므로 예상대로 작동하는지 확인해야 합니다. 이 작업이 완료되면 애플리케이션을 클라우드 네이티브로 만들 수 있는 몇 가지 추천 사항이 있습니다.

### <a name="recommendations"></a>권장 사항

* */home* 디렉터리를 파일 스토리지에 사용하도록 선택한 경우 [이를 Azure Storage로 바꾸는 것](/azure/app-service/containers/how-to-serve-content-from-azure-storage)이 좋습니다.

* 연결 문자열, SSL 키 및 기타 비밀 정보가 포함된 */home* 디렉터리에 구성이 있는 경우 가능하다면 [Azure Key Vault](/azure/app-service/app-service-key-vault-references) 및/또는 [애플리케이션 설정을 통한 매개 변수 주입](/azure/app-service/configure-common#configure-app-settings)을 사용하는 방안을 고려해 보세요.

* 가동 중지 시간이 없는 안정적인 배포를 위해 [배포 슬롯을 사용](/azure/app-service/deploy-staging-slots)하는 것이 좋습니다.

* DevOps 전략을 설계하고 구현합니다. 개발 속도를 높이는 동시에 안정성을 유지하기 위해 [Azure Pipelines를 사용하여 배포를 자동화하고 테스트](/azure/devops/pipelines/ecosystems/java-webapp)하는 것이 좋습니다. 배포 슬롯을 사용하는 경우 [슬롯에 대한 배포를 자동화](/azure/devops/pipelines/targets/webapp?view=azure-devops&tabs=yaml#deploy-to-a-slot)한 다음, 슬롯 교환을 수행할 수 있습니다.

* 비즈니스 연속성 및 재해 복구 전략을 설계하고 구현합니다. 중요 업무용 애플리케이션의 경우 [다중 지역 배포 아키텍처](/azure/architecture/reference-architectures/app-service-web-app/multi-region)를 고려하세요.
