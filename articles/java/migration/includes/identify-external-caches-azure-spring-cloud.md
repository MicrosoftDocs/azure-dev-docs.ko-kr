---
author: yevster
ms.author: yebronsh
ms.date: 4/15/2020
ms.openlocfilehash: f2bc0442aafcfffc963d4e5a6da18085d9205a22
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990165"
---
#### <a name="identify-external-caches"></a>외부 캐시 확인

사용 중인 외부 캐시를 확인합니다. Redis는 Spring Data Redis를 통해 자주 사용됩니다. 구성 정보는 [Spring Data Redis](https://spring.io/projects/spring-data-redis) 설명서를 참조하세요.

[Java](https://docs.spring.io/spring-session/docs/current/reference/html5/#httpsession-redis-jc) 또는 [XML](https://docs.spring.io/spring-session/docs/current/reference/html5/#httpsession-redis-xml)에서 각 구성을 검색하여 [Spring Session](https://spring.io/projects/spring-session)을 통해 세션 데이터가 캐시되는지 여부를 확인합니다.
