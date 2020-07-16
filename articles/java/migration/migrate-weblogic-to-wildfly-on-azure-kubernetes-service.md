---
title: WebLogic 애플리케이션을 Azure Kubernetes Service의 WildFly로 마이그레이션
description: 이 가이드에서는 Azure Kubernetes Service 컨테이너의 WildFly에서 실행되도록 기존 WebLogic 애플리케이션을 마이그레이션하려는 경우 알고 있어야 할 사항에 대해 설명합니다.
author: mriem
ms.author: manriem
ms.topic: conceptual
ms.date: 2/28/2020
ms.custom: devx-track-java
ms.openlocfilehash: aa496125a5a692f13e90441eb7be85f049481151
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379777"
---
# <a name="migrate-weblogic-applications-to-wildfly-on-azure-kubernetes-service"></a>WebLogic 애플리케이션을 Azure Kubernetes Service의 WildFly로 마이그레이션

이 가이드에서는 Azure Kubernetes Service 컨테이너의 WildFly에서 실행되도록 기존 WebLogic 애플리케이션을 마이그레이션하려는 경우 알고 있어야 할 사항에 대해 설명합니다.

## <a name="pre-migration"></a>사전 마이그레이션

마이그레이션을 성공적으로 수행하려면 시작하기 전에 다음 섹션에서 설명하는 평가 및 인벤토리 단계를 완료합니다.

사전 마이그레이션 요구 사항이 충족될 수 없는 경우 다음 마이그레이션 가이드를 참조하세요.

* [WebLogic 애플리케이션을 Azure Virtual Machines로 마이그레이션](migrate-weblogic-to-virtual-machines.md)

[!INCLUDE [inventory-server-capacity-aks](includes/inventory-server-capacity-aks.md)]

[!INCLUDE [inventory-all-secrets](includes/inventory-all-secrets.md)]

[!INCLUDE [inventory-all-certificates](includes/inventory-all-certificates.md)]

[!INCLUDE [inventory-jndi-resources](includes/inventory-jndi-resources.md)]

### <a name="determine-whether-session-replication-is-used"></a>세션 복제가 사용되는지 확인

Oracle Coherence*Web의 사용 여부에 관계없이 애플리케이션에서 세션 복제를 사용하는 경우 다음 두 가지 옵션이 있습니다.

* 세션 관리에 데이터베이스를 사용하도록 애플리케이션을 리팩터링합니다.
* 세션을 Azure Redis Service로 외부화하도록 애플리케이션을 리팩터링합니다. 자세한 내용은 [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview)를 참조하세요.

[!INCLUDE [document-datasources](includes/document-datasources.md)]

[!INCLUDE [determine-whether-weblogic-has-been-customized](includes/determine-whether-weblogic-has-been-customized.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-jms-queues-or-topics-are-in-use](includes/determine-whether-jms-queues-or-topics-are-in-use.md)]

[!INCLUDE [determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries](includes/determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries.md)]

[!INCLUDE [determine-whether-osgi-bundles-are-used](includes/determine-whether-osgi-bundles-are-used.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [determine-whether-oracle-service-bus-is-in-use](includes/determine-whether-oracle-service-bus-is-in-use.md)]

[!INCLUDE [determine-whether-your-application-is-composed-of-multiple-wars](includes/determine-whether-your-application-is-composed-of-multiple-wars.md)]

[!INCLUDE [determine-whether-your-application-is-packaged-as-an-ear](includes/determine-whether-your-application-is-packaged-as-an-ear.md)]

<!-- AKS-specific extension of the last INCLUDE. -->
> [!NOTE]
> AKS 리소스를 더 효율적으로 사용하기 위해 각 웹 애플리케이션의 크기를 독립적으로 조정하려면 EAR을 별도의 웹 애플리케이션으로 분리해야 합니다.
<!-- end extension -->

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

[!INCLUDE [validate-that-the-supported-java-version-works-correctly-wildfly](includes/validate-that-the-supported-java-version-works-correctly-wildfly.md)]

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs](includes/determine-whether-your-application-relies-on-scheduled-jobs.md)]

### <a name="determine-whether-weblogic-scripting-tool-wlst-is-used"></a>WLST(WebLogic Scripting Tool)가 사용되는지 확인

현재 WLST를 사용하여 배포하는 경우 이 배포 작업을 평가해야 합니다. WLST에서 배포의 일환으로 애플리케이션의 런타임 매개 변수를 변경하는 경우 해당 매개 변수에서 다음 옵션 중 하나를 준수하는지 확인해야 합니다.

* 앱 설정으로 표면화됩니다.
* 애플리케이션에 포함됩니다.
* 배포 중에 JBoss CLI를 사용합니다.

WLST를 통해 위에서 언급한 것보다 더 많은 작업을 수행하는 경우 마이그레이션 중에 몇 가지 추가 작업을 수행해야 합니다.

### <a name="determine-whether-your-application-uses-weblogic-specific-apis"></a>애플리케이션에서 WebLogic 특정 API를 사용하는지 확인

애플리케이션에서 WebLogic 특정 API를 사용하는 경우 해당 종속성을 제거하도록 애플리케이션을 리팩터링해야 합니다. 예를 들어 [Oracle WebLogic Server용 Java API 참조](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlapi/index.html?overview-summary.html)에 언급된 클래스를 사용한 경우 애플리케이션에서 WebLogic 특정 API를 사용한 것입니다.

[!INCLUDE [determine-whether-your-application-uses-entity-beans](includes/determine-whether-your-application-uses-entity-beans.md)]

[!INCLUDE [determine-whether-the-java-ee-application-client-feature-is-in-use-aks](includes/determine-whether-the-java-ee-application-client-feature-is-in-use-aks.md)]

### <a name="determine-whether-a-deployment-plan-was-used"></a>배포 계획이 사용되었는지 확인

배포 계획을 사용하여 앱을 배포한 경우 배포 계획에서 수행하는 작업을 평가해야 합니다. 배포 계획이 직접 배포인 경우 변경하지 않고 웹 애플리케이션을 배포할 수 있습니다. 배포 계획이 더 정교한 경우 JBoss CLI를 사용하여 애플리케이션을 배포의 일환으로 올바르게 구성할 수 있는지 확인해야 합니다. JBoss CLI를 사용할 수 없는 경우 배포 계획을 더 이상 요구하지 않도록 애플리케이션을 리팩터링해야 합니다.

[!INCLUDE [determine-whether-ejb-timers-are-in-use](includes/determine-whether-ejb-timers-are-in-use.md)]

### <a name="determine-whether-and-how-the-file-system-is-used"></a>파일 시스템의 사용 여부 및 방법 확인

애플리케이션 서버에서 파일 시스템을 사용하려면 재구성하거나 드물게 아키텍처 변경이 필요합니다. 파일 시스템은 WebLogic 공유 모듈 또는 애플리케이션 코드에서 사용할 수 있습니다. 다음 섹션에서 설명하는 시나리오의 일부 또는 전부를 식별할 수 있습니다.

[!INCLUDE [static-content](includes/static-content.md)]

[!INCLUDE [dynamic-or-internal-content-aks](includes/dynamic-or-internal-content-aks.md)]

### <a name="determine-whether-jca-connectors-are-used"></a>JCA 커넥터가 사용되는지 여부 확인

애플리케이션에서 JCA 커넥터를 사용하는 경우 WildFly에서 JCA 커넥터를 사용할 수 있는지 확인해야 합니다. JCA 구현이 WebLogic과 연결되는 경우 해당 종속성을 제거하도록 애플리케이션을 리팩터링해야 합니다. 사용할 수 있는 경우 JAR을 서버 클래스 경로에 추가하고, 필요한 구성 파일을 WildFly 서버 디렉터리의 올바른 위치에 배치해야 합니다.

[!INCLUDE [determine-whether-your-application-uses-a-resource-adapter](includes/determine-whether-your-application-uses-a-resource-adapter.md)]

[!INCLUDE [determine-whether-jaas-is-in-use](includes/determine-whether-jaas-is-in-use.md)]

### <a name="determine-whether-weblogic-clustering-is-used"></a>WebLogic 클러스터링이 사용되는지 확인

아마도 많은 분들이 고가용성을 얻기 위해 여러 WebLogic 서버에 애플리케이션을 배포하셨을 것입니다. Azure Kubernetes Service에서 크기를 조정할 수 있지만, WebLogic 클러스터 API를 사용한 경우 해당 API를 사용하지 않도록 코드를 리팩터링해야 합니다.

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

이제 애플리케이션을 Azure Kubernetes Service로 마이그레이션했으므로 예상대로 작동하는지 확인해야 합니다. 이 작업이 완료되면 다음 추천 사항을 참조하여 애플리케이션을 클라우드 네이티브로 만듭니다.

[!INCLUDE [recommendations-wildfly-on-aks](includes/recommendations-wildfly-on-aks.md)]
