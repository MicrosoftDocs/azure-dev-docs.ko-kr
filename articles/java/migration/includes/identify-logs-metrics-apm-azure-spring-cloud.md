---
author: yevster
ms.author: yebronsh
ms.date: 4/15/2020
ms.openlocfilehash: 201cffb10758410abc74c330c127e18c1c5355dc
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990175"
---
#### <a name="identify-log-aggregation-solutions"></a>로그 집계 솔루션 확인

마이그레이션하는 애플리케이션에서 사용 중인 로그 집계 솔루션을 확인합니다.

#### <a name="identify-application-performance-management-apm-agents"></a>APM(애플리케이션 성능 관리) 에이전트 확인

애플리케이션에서 사용 중인 애플리케이션 성능 모니터링 에이전트(예: Dynatrace 및 Datadog)를 확인합니다. 이러한 에이전트를 대신하여 Azure Spring Cloud는 Azure Monitor와 긴밀하게 통합하여 성능 관리와 이상 상황에 대한 실시간 응답을 제공합니다. 자세한 내용은 [마이그레이션 후](#post-migration)를 참조하세요.
