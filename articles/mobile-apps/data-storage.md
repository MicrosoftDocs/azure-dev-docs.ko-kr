---
title: Visual Studio App Center 및 Azure 서비스를 사용하여 클라우드에서 애플리케이션 데이터 저장, 관리 및 유지
description: 클라우드에서 모바일 애플리케이션 데이터를 저장, 관리 및 유지할 수 있는 Visual Studio App Center와 같은 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 1fc4fa3118368bd459024d894094ac2eac4b1b71
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85790660"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>클라우드의 모바일 애플리케이션 데이터 저장, 동기화 및 쿼리

빌드하는 애플리케이션의 종류에 관계없이 데이터를 생성하고 처리할 가능성이 높습니다. 애플리케이션의 사용자는 기대 수준이 높습니다. 애플리케이션이 모든 상황에서 신속하고 원활하게 작동하기를 원합니다. 또한 대부분의 애플리케이션은 여러 디바이스에서 작동합니다. 데스크톱 또는 모바일 디바이스에서 애플리케이션에 액세스할 수 있습니다. 여러 사용자가 데이터에 대한 즉각적인 실시간 액세스를 기대하며 애플리케이션을 동시에 사용하고 데이터를 공유할 수 있습니다.

애플리케이션 사용자가 항상 인터넷에 연결되어 있는 것은 아닙니다. 애플리케이션은 인터넷 연결을 사용하거나 사용하지 않고 작동하도록 설계되었습니다. 개발자는 자신의 오프라인 데이터 저장소 개발을 포함하여 애플리케이션에 뛰어난 고객 환경을 제공하기 위해 데이터를 저장하고 클라우드에 동기화하는 데 적합한 솔루션을 선택해야 합니다.

Microsoft는 서버를 실행하거나, 데이터베이스를 선택하거나, 규모 또는 보안을 걱정할 필요없이 최대한 풍부한 환경을 제공하는 다양한 서비스를 지원합니다. 이러한 서비스는 SQL 또는 NoSQL API를 사용하여 클라우드에 애플리케이션 데이터를 저장할 수 있게 해주는 뛰어난 개발자 환경을 제공합니다. 모든 디바이스의 데이터를 동기화하고 애플리케이션이 네트워크 연결을 사용하거나 사용하지 않고 작동하도록 하여 확장 가능하고 강력한 애플리케이션을 빌드할 수도 있습니다.

다음 서비스를 사용하여 클라우드에서 모바일 애플리케이션 데이터를 관리하고 저장하세요.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)는 전 세계에 배포된 다중 모델 데이터베이스 서비스입니다. 전 세계적인 애플리케이션을 빌드할 때 사용할 수 있습니다. Azure Cosmos DB를 사용하면 전 세계 여러 Azure 지역에서 처리량 및 스토리지의 규모를 탄력적이고 독립적으로 조정할 수 있습니다. 선호하는 API 화면을 사용하여 몇 밀리초 내에 데이터에 빠르게 액세스할 수 있습니다. 이러한 화면에는 SQL, MongoDB, Cassandra, Tables 또는 Gremlin이 포함됩니다. Azure Cosmos DB는 처리량, 대기 시간, 가용성 및 일관성에 대한 포괄적인 SLA(서비스 수준 계약)를 고유하게 제공합니다.

### <a name="azure-cosmos-db-features"></a>Azure DB Cosmos 기능

- SQL(Core) API, Cassandra API, MongoDB API, Gremlin API 및 Table API를 포함하는 광범위한 API를 지원합니다.
- 턴키 글로벌 배포는 사용자가 어디에 있든 데이터를 복제합니다. 사용자는 가장 가까운 데이터의 복제본과 상호 작용할 수 있습니다.
- 데이터베이스 엔진이 스키마에 전혀 구애받지 않기 때문에 스키마 또는 인덱스 관리가 필요 없습니다.
- Azure Cosmos DB는 퍼블릭 클라우드의 54개 이상 지역을 포함한 전 세계 모든 Azure 지역에서 사용할 수 있기 때문에 어느 지역에나 존재합니다.
- Azure Cosmos DB의 다중 마스터 복제 프로토콜은 잘 정의된 5가지 일관성 선택 항목을 제공하도록 신중하게 설계되었기 때문에 여러 일관성 선택 항목이 정확하게 정의되어 있습니다. 이러한 5개 선택 항목은 강력, 제한된 부실, 세션, 일관적인 접두사 및 최종입니다.
- 읽기 및 쓰기 작업 모두에 99.999%의 가용성을 제공합니다.
- 프로그래밍 방식으로(또는 Azure Portal을 통해) Azure Cosmos DB 계정의 지역 장애 조치(failover)를 호출하여 지역 재해를 견딜 수 있도록 애플리케이션을 설계해야 합니다.
- 전 세계적으로 99번째 백분위수의 짧은 대기 시간 보장

### <a name="azure-cosmos-db-references"></a>Azure DB Cosmos 참조

- [Azure Portal](https://portal.azure.com) 
- [Azure Cosmos DB 설명서](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Azure SQL Database

 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)는 범용 관계형 데이터베이스 관리형 서비스입니다. Azure SQL Database를 사용하면 Azure 클라우드에서 애플리케이션 및 솔루션에 대한 고가용성 및 고성능 데이터 스토리지 계층을 만들 수 있습니다.

### <a name="azure-sql-database-features"></a>Azure SQL Database 기능

- **Elastic Database 모델 및 도구:** 개발자는 Elastic Database를 사용하여 스케일링할 데이터베이스 그룹 간에 리소스를 풀링할 수 있습니다. 이러한 리소스를 관리하려면 스크립트를 작업으로 제출합니다. 그러면 SQL 데이터베이스에서 데이터베이스에 대해 스크립트를 수행합니다.
- **고성능:** 처리량이 높은 애플리케이션은 최신 버전을 활용할 수 있습니다. 25% 더 향상된 데이터베이스 성능을 제공합니다.
- **백업, 복제 및 고가용성:** 데이터베이스 수준에서 기본 제공되는 복제 및 Microsoft 지원 SLA는 애플리케이션 연속성 및 치명적 이벤트 방지를 지원합니다. 활성 지역 복제를 사용하면 장애 조치(failover) 및 셀프 서비스 복원을 구성하여 "오류 복구"를 완벽하게 제어할 수 있습니다. 최대 35일 동안의 데이터 백업으로 데이터를 복원할 수 있습니다.
- **유지 관리가 거의 필요 없음:** 자동 소프트웨어가 서비스의 일부로 제공됩니다. 기본 제공 시스템 복제본은 기본적인 데이터 보호, 데이터베이스 작동 시간 및 시스템 안정성을 지원하는 데 도움이 됩니다. 시스템 복제본은 새 컴퓨터로 자동 이전됩니다. 이전에 실패하는 즉시 프로비저닝됩니다.
- **보안:** Azure SQL Database는 조직 또는 업계의 필수 규정 준수 정책을 충족하는 보안 기능 포트폴리오를 제공합니다.

- 개발자는 감사 기능을 통해 규정 준수 관련 작업을 수행하고 작업에 대한 정보를 얻을 수 있습니다.
- 개발자와 IT는 데이터베이스 수준에서 정책을 구현하여 행 수준 보안, 동적 데이터 마스킹 및 Azure SQL Database에 대한 투명한 데이터 암호화를 통해 중요한 데이터에 대한 액세스를 제한할 수 있습니다.
- Azure SQL Database는 주요 클라우드 감사 기관으로부터 HIPAA BAA, ISO/IEC 27001:2005, FedRAMP 및 EU Model Clauses와 같은 주요 Azure 규정 준수 인증 및 승인 범위 내에 있다는 확인을 받았습니다.

### <a name="azure-sql-database-references"></a>Azure SQL Database 참조

- [Azure Portal](https://portal.azure.com) 
- [Azure SQL Database 설명서](/azure/sql-database/)
