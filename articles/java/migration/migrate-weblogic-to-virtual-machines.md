---
title: WebLogic 애플리케이션을 Azure Virtual Machines로 마이그레이션
description: 이 가이드에서는 Azure Virtual Machines에서 실행되도록 기존 WebLogic 애플리케이션을 마이그레이션하려는 경우 알아야 할 사항을 설명합니다.
author: edburns
ms.author: edburns
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: b9d9a4ea8ef81af5c67b91281e892b4626f42f62
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82988912"
---
# <a name="migrate-weblogic-applications-to-azure-virtual-machines"></a>WebLogic 애플리케이션을 Azure Virtual Machines로 마이그레이션

이 가이드에서는 Azure Virtual Machines에서 실행되도록 기존 WebLogic 애플리케이션을 마이그레이션하려는 경우 알아야 할 사항을 설명합니다.

## <a name="pre-migration"></a>사전 마이그레이션

마이그레이션을 성공적으로 수행하려면 시작하기 전에 다음 섹션에서 설명하는 평가 및 인벤토리 단계를 완료합니다.

### <a name="define-what-you-mean-by-migration-complete"></a>"마이그레이션 완료"의 의미를 정의

이 가이드 및 해당 Azure Marketplace 제품은 Azure로 WebLogic Server 워크로드를 신속하게 마이그레이션할 수 있는 시작점입니다. 마이그레이션 작업의 범위를 정의하는 것이 중요합니다. 예를 들어 기존 인프라에서 Azure Virtual Machines로 엄격한 "리프트 앤 시프트"를 수행하려 하시나요? 만약 그렇다면 마이그레이션할 때 "리프트 및 향상"을 수행하고 싶다는 유혹에 빠질 수도 있습니다.

하지만 이 가이드에 설명된 대로 필요한 변경 내용을 고려하여 가능하다면 순수 "리프트 앤 시프트"에 최대한 가깝게 유지하는 것이 좋습니다. 마이그레이션 완료라는 마일스톤에 도달했을 때 그 의미를 알 수 있도록 "마이그레이션 완료"의 의미를 정의합니다. "마이그레이션 완료"에 도달하면 [스냅샷 만들기](/azure/virtual-machines/windows/snapshot-copy-managed-disk)에 설명된 대로 Virtual Machines의 스냅샷을 만들 수 있습니다. 스냅샷에서 성공적으로 복원할 수 있는 것을 확인한 후에는 지금까지 수행한 마이그레이션 진행 상황을 잃지 않고 보다 안전하게 기능을 개선할 수 있습니다.

### <a name="determine-whether-the-pre-built-marketplace-offers-are-a-good-starting-point"></a>미리 빌드된 Marketplace 제품이 좋은 시작점인지 확인

Azure로 마이그레이션하기 위한 견고한 시작점을 제공하기 위해, Oracle과 Microsoft는 파트너 관계를 맺고 Azure 솔루션 템플릿 세트를 Azure Marketplace에 제공합니다. [Oracle 퓨전 미들웨어](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlazu/) 설명서를 참조하여 기존 배포와 가장 일치하는 제품을 선택하세요. [Oracle 설명서](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlazu/select-required-oracle-weblogic-server-offer-azure-marketplace.html#GUID-187739C5-EE7A-47C6-B3BA-C0A0333DC398)에서 제품 목록을 볼 수 있습니다.

기존 제품 중에 좋은 시작점이 없는 경우 Azure Virtual Machine 리소스를 사용하여 배포를 직접 재현해야 합니다. 자세한 내용은 [IaaS란?](https://azure.microsoft.com/overview/what-is-iaas/)을 참조하세요.

### <a name="determine-whether-the-weblogic-version-is-compatible"></a>WebLogic 버전이 호환되는지 확인

기존 WebLogic 버전이 IaaS 제품의 버전과 호환되어야 합니다. 이 쿼리는 [WebLogic 버전 12.2.1.3](https://azuremarketplace.microsoft.com/marketplace/apps?search=oracle%20weblogic%2012.2.1.3&page=1)에 대한 제품을 보여줍니다. 기존 WebLogic 버전이 해당 버전과 호환되지 않는 경우 Azure IaaS 리소스를 사용하여 직접 배포를 재현해야 합니다. 자세한 내용은 [Azure 설명서](https://azure.microsoft.com/overview/what-is-iaas/)를 참조하세요.

[!INCLUDE [inventory-server-capacity-virtual-machines](includes/inventory-server-capacity-virtual-machines.md)]

[!INCLUDE [inventory-all-secrets](includes/inventory-all-secrets.md)]

[!INCLUDE [inventory-all-certificates](includes/inventory-all-certificates.md)]

[!INCLUDE [validate-that-the-supported-java-version-works-correctly-weblogic](includes/validate-that-the-supported-java-version-works-correctly-weblogic.md)]

[!INCLUDE [inventory-jndi-resources](includes/inventory-jndi-resources.md)]

[!INCLUDE [inspect-your-domain-configuration](includes/inspect-your-domain-configuration.md)]

[!INCLUDE [determine-whether-session-replication-is-used](includes/determine-whether-session-replication-is-used.md)]

[!INCLUDE [document-datasources](includes/document-datasources.md)]

[!INCLUDE [determine-whether-weblogic-has-been-customized](includes/determine-whether-weblogic-has-been-customized.md)]

[!INCLUDE [determine-whether-management-over-rest-is-used](includes/determine-whether-management-over-rest-is-used.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-jms-queues-or-topics-are-in-use-virtual-machines](includes/determine-whether-jms-queues-or-topics-are-in-use-virtual-machines.md)]

[!INCLUDE [determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries](includes/determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries.md)]

[!INCLUDE [determine-whether-osgi-bundles-are-used](includes/determine-whether-osgi-bundles-are-used.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [determine-whether-oracle-service-bus-is-in-use](includes/determine-whether-oracle-service-bus-is-in-use.md)]

[!INCLUDE [determine-whether-your-application-is-composed-of-multiple-wars](includes/determine-whether-your-application-is-composed-of-multiple-wars.md)]

[!INCLUDE [determine-whether-your-application-is-packaged-as-an-ear](includes/determine-whether-your-application-is-packaged-as-an-ear.md)]

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

[!INCLUDE [determine-whether-wlst-is-used](includes/determine-whether-wlst-is-used.md)]

### <a name="determine-whether-and-how-the-file-system-is-used"></a>파일 시스템의 사용 여부 및 방법 확인

VM 파일 시스템은 지속성, 시작 및 종료의 측면에서 온-프레미스 파일 시스템과 동일한 방식으로 작동합니다. 그렇다 하더라도, 파일 시스템 요구 사항을 파악하고 VM의 스토리지 크기와 성능이 적절한지 확인해야 합니다.

[!INCLUDE [static-content](includes/static-content.md)]

[!INCLUDE [determine-the-network-topology](includes/determine-the-network-topology.md)]

[!INCLUDE [account-for-the-use-of-jca-adapters-and-resource-adapters](includes/account-for-the-use-of-jca-adapters-and-resource-adapters.md)]

[!INCLUDE [account-for-the-use-of-custom-security-providers-and-jaas](includes/account-for-the-use-of-custom-security-providers-and-jaas.md)]

[!INCLUDE [determine-whether-weblogic-clustering-is-used](includes/determine-whether-weblogic-clustering-is-used.md)]

[!INCLUDE [determine-whether-the-java-ee-application-client-feature-is-used](includes/determine-whether-the-java-ee-application-client-feature-is-used.md)]

## <a name="migration"></a>마이그레이션

### <a name="select-a-weblogic-on-azure-virtual-machines-offer"></a>Azure Virtual Machines 제품에서 WebLogic 선택

다음은 Azure Virtual Machines에서 WebLogic에 사용할 수 있는 제품입니다.

제품을 배포하는 동안 WebLogic Server 노드에 사용할 Virtual Machine 크기를 선택하라는 메시지가 표시됩니다. VM 크기를 선택할 때 모든 관점(메모리, 프로세서, 디스크)을 고려해야 합니다. 자세한 내용은 [제품 설명서](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlazu/deploy-oracle-weblogic-server-administration-server-single-node.html) 및 [가상 머신 크기 조정에 대한 Azure 설명서](/azure/cloud-services/cloud-services-sizes-specs)를 참조하세요.

#### <a name="weblogic-server-single-node-with-no-admin-server"></a>관리 서버가 없는 WebLogic Server 단일 노드

이 제품은 단일 VM을 만들고 그 VM에 WebLogic을 설치하지만, 도메인을 구성하지는 않습니다. 따라서 고도로 사용자 지정된 도메인 구성이 있는 시나리오에 유용합니다.

#### <a name="weblogic-server-single-node-with-admin-server"></a>관리 서버가 있는 WebLogic Server 단일 노드

이 제품은 단일 VM을 프로비저닝하고 거기에 WebLogic Server 12.1.2.3을 설치합니다. 도메인을 만들고 관리 서버를 시작합니다.

#### <a name="weblogic-server-n-node-cluster"></a>WebLogic Server N-노드 클러스터

이 제품은 고가용성 WebLogic Server VM 클러스터를 만듭니다.

#### <a name="weblogic-server-n-node-dynamic-cluster"></a>WebLogic Server N-노드 동적 클러스터

이 제품은 확장 가능한 고가용성 WebLogic Server VM 동적 클러스터를 만듭니다.

### <a name="provision-the-offer"></a>제품 프로비저닝

시작할 제품을 선택한 후에는 [제품 설명서](https://wls-eng.github.io/arm-oraclelinux-wls/)의 지침에 따라 해당 제품을 프로비저닝합니다. 기존 도메인 이름과 일치하는 도메인 이름을 선택해야 합니다. 도메인 암호를 기존 도메인 암호와 매칭할 수도 있습니다.

### <a name="migrate-the-domains"></a>도메인 마이그레이션

제품을 프로비저닝한 후에는 도메인 구성을 검사하고, 도메인 마이그레이션 방법에 대해 자세히 설명하는 [이 지침](https://support.oracle.com/knowledge/Middleware/2336356_1.html)을 따릅니다.

### <a name="connect-the-databases"></a>데이터베이스 연결

도메인을 마이그레이션한 후에는 [제품 설명서](https://wls-eng.github.io/arm-oraclelinux-wls/#connecting-a-database-to-a-cluster)의 지침에 따라 데이터베이스를 연결할 수 있습니다. 이러한 지침은 관련된 데이터베이스 비밀과 액세스 문자열을 고려하는 데 도움이 됩니다.

### <a name="account-for-keystores"></a>KeyStores 계정

애플리케이션에서 사용하는 SSL KeyStores의 마이그레이션을 고려해야 합니다. 자세한 내용은 [Keystores 구성](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/secmg/identity_trust.html#GUID-7F03EB9C-9755-430B-8B86-17199E0C01DC)을 참조하세요.

### <a name="connect-the-jms-sources"></a>JMS 원본 연결

데이터베이스를 연결한 후에는 WebLogic 설명서의 [Oracle WebLogic Server용 JMS 리소스를 관리하는 퓨전 미들웨어](https://docs.oracle.com/middleware/12213/wls/JMSAD/toc.htm)에 설명된 지침에 따라 JMS를 구성할 수 있습니다.

### <a name="account-for-logging"></a>로깅 계정

도메인을 마이그레이션할 때 기존 로깅 구성을 수행해야 합니다. 자세한 내용은 [java.util.logging 로거 수준 구성](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlach/taskhelp/logging/ConfigureJavaLoggingLevels.html) 및 [Oracle WebLogic Server의 로그 파일 구성 및 로그 메시지 필터링](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wllog/index.html)을 참조하세요.

### <a name="migrating-your-applications"></a>애플리케이션 마이그레이션

개발 팀에서 테스트, 준비 및 프로덕션 서버에 애플리케이션을 배포하는 데 사용되는 기술은 상황에 따라 크게 다릅니다. 애플리케이션이 WebLogic Server에 배포되는 매우 발전된 CI/CD 플랫폼이 사용되는 경우가 있습니다. 그 외의 경우에는 프로세스를 보다 수동적으로 수행할 수 있습니다. Azure Virtual Machines를 사용하여 WebLogic 애플리케이션을 클라우드로 마이그레이션할 때 얻을 수 있는 이점 중 하나는 기존 프로세스가 계속 작동한다는 것입니다.

CI/CD 파이프라인 또는 수동 배포 시스템에서 액세스할 수 있도록 제품에서 프로비저닝되는 네트워크 보안 그룹을 구성해야 합니다. 자세한 내용은 Azure 설명서의 [보안 그룹](/azure/virtual-network/security-overview)을 참조하세요.

### <a name="testing"></a>테스트

애플리케이션에 대한 모든 컨테이너 내 테스트는 Azure 내에서 실행되는 새 서버에 액세스하도록 구성해야 합니다. CI/CD와 마찬가지로, 테스트 시 Azure에 배포된 애플리케이션에 액세스할 수 있도록 필요한 네트워크 보안 규칙을 구성해야 합니다. 자세한 내용은 Azure 설명서의 [보안 그룹](/azure/virtual-network/security-overview)을 참조하세요.

## <a name="post-migration"></a>마이그레이션 후 작업

[사전 마이그레이션](#pre-migration) 단계에서 정의한 마이그레이션 목표에 도달한 후에는 엔드투엔드 수용 테스트를 수행하여 모든 것이 예상대로 작동하는지 확인해야 합니다. 다음은 마이그레이션 후 기능 향상에 대한 토픽 중 일부이며, 이 외에도 다른 토픽이 더 있습니다.

* Azure Storage를 사용하여 가상 머신에 탑재된 정적 콘텐츠 제공. 자세한 내용은 [가상 머신에 데이터 디스크 연결 또는 분리](/azure/lab-services/devtest-lab-attach-detach-data-disk)를 참조하세요.

* Azure DevOps를 사용하여 마이그레이션된 WebLogic 클러스터에 애플리케이션 배포. 자세한 내용은 [Azure DevOps 시작 설명서](/azure/devops/get-started/?view=azure-devops)를 참조하세요.

* 고급 부하 분산 서비스를 사용하여 네트워크 토폴로지 향상. 자세한 내용은 [Azure에서 부하 분산 서비스 사용](/azure/traffic-manager/traffic-manager-load-balancing-azure)을 참조하세요.

* Azure 관리 ID를 관리형 비밀에 활용하고 Azure 리소스에 대한 역할 기반 액세스 할당. 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](/azure/active-directory/managed-identities-azure-resources/overview)을 참조하세요.

* WebLogic Java EE 인증 및 권한 부여를 Azure Active Directory와 통합. 자세한 내용은 [Azure Active Directory 시작 가이드](/azure/active-directory/manage-apps/plan-an-application-integration)를 참조하세요.

* Azure Key Vault를 사용하여 "비밀"로 작동하는 모든 정보 저장. 자세한 내용은 [Azure Key Vault 기본 개념](/azure/key-vault/basic-concepts)을 참조하세요.
