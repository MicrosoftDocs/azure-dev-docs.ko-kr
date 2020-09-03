---
title: Spring Boot 애플리케이션을 Azure Spring Cloud로 마이그레이션
description: 이 가이드에서는 Azure Spring Cloud에서 실행할 기존 Spring Boot 애플리케이션을 마이그레이션하려는 경우에 알아야 할 사항을 설명합니다.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 5/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 4d2f84c6c77294c9a2d25028608e2feb712599f8
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89062063"
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

[!INCLUDE [migrate-steps-spring-boot-azure-spring-cloud](includes/migrate-steps-spring-boot-azure-spring-cloud.md)]

## <a name="post-migration"></a>마이그레이션 후 작업

[!INCLUDE [post-migration-spring-boot-azure-spring-cloud](includes/post-migration-spring-boot-azure-spring-cloud.md)]
