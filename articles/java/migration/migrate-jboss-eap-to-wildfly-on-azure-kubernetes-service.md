---
title: JBoss EAP 애플리케이션을 Azure Kubernetes Service의 WildFly로 마이그레이션
description: 이 가이드에서는 Azure Kubernetes Service 컨테이너의 WildFly에서 실행되도록 기존 JBoss EAP 애플리케이션을 마이그레이션하려는 경우 알아야 할 사항에 대해 설명합니다.
author: mriem
ms.author: manriem
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: 4ab902e61703d5abc093dc508a370777b69632ff
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82988950"
---
# <a name="migrate-jboss-eap-applications-to-wildfly-on-azure-kubernetes-service"></a>JBoss EAP 애플리케이션을 Azure Kubernetes Service의 WildFly로 마이그레이션

이 가이드에서는 Azure Kubernetes Service 컨테이너의 WildFly에서 실행되도록 기존 JBoss EAP 애플리케이션을 마이그레이션하려는 경우 알아야 할 사항에 대해 설명합니다.

## <a name="pre-migration"></a>사전 마이그레이션

마이그레이션을 성공적으로 수행하려면 시작하기 전에 다음 섹션에서 설명하는 평가 및 인벤토리 단계를 완료합니다.

[!INCLUDE [inventory-server-capacity-aks](includes/inventory-server-capacity-aks.md)]

### <a name="inventory-all-secrets"></a>모든 비밀 인벤토리화

프로덕션 서버의 모든 속성 및 구성 파일에 비밀과 암호가 있는지 확인합니다. WAR에서 *jboss-web.xml* 파일을 확인합니다. 암호 또는 자격 증명이 포함된 구성 파일은 애플리케이션 내에서도 찾을 수 있습니다.

이러한 비밀은 Azure Key Vault에 저장하는 것이 좋습니다. 자세한 내용은 [Azure Key Vault 기본 개념](/azure/key-vault/basic-concepts)을 참조하세요.

[!INCLUDE [inventory-all-certificates](includes/inventory-all-certificates.md)]

[!INCLUDE [validate-that-the-supported-java-version-works-correctly-wildfly](includes/validate-that-the-supported-java-version-works-correctly-wildfly.md)]

### <a name="inventory-jndi-resources"></a>JNDI 리소스 인벤토리화

모든 JNDI 리소스를 인벤토리화합니다. JMS 메시지 브로커와 같은 일부 리소스에는 마이그레이션 또는 재구성이 필요할 수 있습니다.

### <a name="determine-whether-session-replication-is-used"></a>세션 복제가 사용되는지 확인

애플리케이션에서 세션 복제를 사용하는 경우 이 종속성을 제거하도록 애플리케이션을 변경해야 합니다.

#### <a name="inside-your-application"></a>애플리케이션 내부

*WEB-INF/jboss-web.xml* 및/또는 *WEB-INF/web.xml* 파일을 검사합니다.

### <a name="document-datasources"></a>데이터 원본 문서화

애플리케이션에서 데이터베이스를 사용하는 경우 다음 정보를 캡처해야 합니다.

* 데이터 원본 이름은 무엇인가요?
* 연결 풀 구성이란?
* JDBC 드라이버 JAR 파일은 어디에서 찾을 수 있나요?

자세한 내용은 JBoss EAP 설명서의 [JBoss EAP 데이터 원본 정보](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/datasource_management)를 참조하세요.

### <a name="determine-whether-and-how-the-file-system-is-used"></a>파일 시스템의 사용 여부 및 방법 확인

애플리케이션 서버에서 파일 시스템을 사용하려면 재구성하거나 드물게 아키텍처 변경이 필요합니다. 파일 시스템은 JBoss EAP 모듈 또는 애플리케이션 코드에서 사용할 수 있습니다. 다음 섹션에서 설명하는 시나리오의 일부 또는 전부를 식별할 수 있습니다.

[!INCLUDE [static-content](includes/static-content.md)]

[!INCLUDE [dynamic-or-internal-content-aks](includes/dynamic-or-internal-content-aks.md)]

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs](includes/determine-whether-your-application-relies-on-scheduled-jobs.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-jms-queues-or-topics-are-in-use](includes/determine-whether-jms-queues-or-topics-are-in-use.md)]

### <a name="determine-whether-your-application-uses-jboss-eap-specific-apis"></a>애플리케이션에서 JBoss-EAP 관련 API를 사용하는지 확인

애플리케이션에서 JBoss-EAP 관련 API를 사용하는 경우 해당 종속성을 제거하도록 애플리케이션을 리팩터링해야 합니다.

[!INCLUDE [determine-whether-your-application-uses-entity-beans](includes/determine-whether-your-application-uses-entity-beans.md)]

[!INCLUDE [determine-whether-the-java-ee-application-client-feature-is-in-use-aks](includes/determine-whether-the-java-ee-application-client-feature-is-in-use-aks.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [determine-whether-ejb-timers-are-in-use](includes/determine-whether-ejb-timers-are-in-use.md)]

### <a name="determine-whether-jca-connectors-are-in-use"></a>JCA 커넥터가 사용되는지 확인

애플리케이션에서 JCA 커넥터를 사용하는 경우 WildFly에서 JCA 커넥터를 사용할 수 있는지 확인합니다. JCA 구현이 JBoss EAP에 연결되는 경우 애플리케이션을 리팩터링하여 해당 종속성을 제거해야 합니다. WildFly에서 JCA 커넥터를 사용할 수 있는 경우 JAR을 서버 클래스 경로에 추가하고 필요한 구성 파일을 WildFly 서버 디렉터리의 올바른 위치에 배치해야 합니다.

[!INCLUDE [determine-whether-jaas-is-in-use](includes/determine-whether-jaas-is-in-use.md)]

[!INCLUDE [determine-whether-your-application-uses-a-resource-adapter](includes/determine-whether-your-application-uses-a-resource-adapter.md)]

[!INCLUDE [determine-whether-your-application-is-composed-of-multiple-wars](includes/determine-whether-your-application-is-composed-of-multiple-wars.md)]

### <a name="determine-whether-your-application-is-packaged-as-an-ear"></a>애플리케이션이 EAR로 패키징되는지 확인

애플리케이션이 EAR 파일로 패키지되는 경우 *application.xml* 파일을 검사하고 해당 구성을 캡처합니다.

> [!NOTE]
> AKS 리소스를 더 효율적으로 사용하기 위해 각 웹 애플리케이션의 크기를 독립적으로 조정하려면 EAR을 별도의 웹 애플리케이션으로 분리해야 합니다.

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

[!INCLUDE [perform-in-place-testing](includes/perform-in-place-testing.md)]

## <a name="migration"></a>마이그레이션

[!INCLUDE [provision-azure-container-registry-and-azure-kubernetes-service](includes/provision-azure-container-registry-and-azure-kubernetes-service.md)]

[!INCLUDE [create-a-docker-image-for-wildfly](includes/create-a-docker-image-for-wildfly.md)]

[!INCLUDE [build-and-push-the-docker-image-to-azure-container-registry](includes/build-and-push-the-docker-image-to-azure-container-registry.md)]

[!INCLUDE [provision-a-public-ip-address](includes/provision-a-public-ip-address.md)]

[!INCLUDE [deploy-to-aks](includes/deploy-to-aks.md)]

### <a name="configure-persistent-storage"></a>영구 스토리지 구성

애플리케이션에 비휘발성 스토리지가 필요한 경우 하나 이상의 [영구 볼륨](/azure/aks/azure-disks-dynamic-pv)을 구성합니다.

[!INCLUDE [migrate-scheduled-jobs-aks](includes/migrate-scheduled-jobs-aks.md)]

## <a name="post-migration"></a>마이그레이션 후 작업

이제 애플리케이션을 Azure Kubernetes Service로 마이그레이션했으므로 예상대로 작동하는지 확인해야 합니다. 이 작업이 완료되면 애플리케이션을 클라우드 네이티브로 만들 수 있는 몇 가지 추천 사항이 있습니다.

[!INCLUDE [recommendations-wildfly-on-aks](includes/recommendations-wildfly-on-aks.md)]
