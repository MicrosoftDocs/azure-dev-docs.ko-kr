---
author: mnriem
ms.author: manriem
ms.date: 5/8/2020
ms.openlocfilehash: 82cf9553654e1efc884542ecdd52d294688291df
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738159"
---
### <a name="inspect-the-deployment-architecture"></a>배포 아키텍처 검사

#### <a name="document-hardware-requirements-for-each-service"></a>각 서비스에 대한 하드웨어 요구 사항 문서화

Spring Boot 애플리케이션에 대한 다음 정보를 문서화합니다.

* 실행되는 인스턴스 수
* 각 인스턴스에 할당되는 CPU 수
* 각 인스턴스에 할당되는 RAM 양

#### <a name="document-geo-replicationdistribution"></a>지역 복제/배포 문서화

Spring Boot 애플리케이션 인스턴스가 현재 여러 지역 또는 데이터 센터 간에 분산되어 있는지 확인합니다. 마이그레이션하는 애플리케이션의 가동 시간 요구 사항/SLA를 문서화합니다.
