---
author: yevster
ms.author: yebronsh
ms.date: 5/28/2020
ms.openlocfilehash: 70f54f911d97febf30b78888e6922e2d4ac3eb54
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512622"
---
#### <a name="identify-local-state"></a>로컬 상태 식별

PaaS 환경에서는 어떤 애플리케이션도 지정된 시간에 정확히 한 번만 실행된다고 보장할 수 없습니다. 단일 인스턴스에서 실행되도록 애플리케이션을 구성하더라도 다음과 같은 경우에는 중복 인스턴스를 만들 수 있습니다.

* 오류 또는 시스템 업데이트 때문에 애플리케이션을 물리적 호스트에 재배치해야 합니다.
* 애플리케이션을 업데이트하는 중입니다.

이러한 경우에는 새 인스턴스가 완전히 시작될 때까지 원래 인스턴스가 계속 실행됩니다. 이로 인해 애플리케이션에 다음과 같은 중대한 영향을 미칠 수 있습니다.

* [싱글톤](https://en.wikipedia.org/wiki/Singleton_pattern)이 진짜 단일 항목이라고 보장할 수 없습니다.
* 외부 스토리지에 보관되지 않은 데이터는 단일 물리적 서버 또는 VM에 보관될 때보다 훨씬 빠르게 손실될 가능성이 높습니다.

Azure Spring Cloud로 마이그레이션하기 전에, 손실 또는 중복되면 안 되는 로컬 상태가 코드에 포함되지 않도록 확인해야 합니다. 로컬 상태가 있는 경우 해당 상태를 애플리케이션 외부에 저장하도록 코드를 변경하세요. 클라우드 지원 애플리케이션은 일반적으로 다음과 같은 위치에 애플리케이션 상태를 저장합니다.

* [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-java-get-started)
* [Azure CosmosDB](/azure/cosmos-db/create-sql-api-java)
* [Azure SQL](/azure/azure-sql/azure-sql-iaas-vs-paas-what-is-overview), [Azure DB for MySQL](/azure/mysql/overview) 또는 [Azure DB for PostgreSQL](/azure/postgresql/overview) 같은 외부 데이터 베이스
