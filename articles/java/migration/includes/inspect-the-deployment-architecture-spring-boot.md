---
author: mriem
ms.author: manriem
ms.date: 5/8/2020
ms.openlocfilehash: f99653f3ce30c629c2c11127089445a4c3072a69
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990245"
---
### <a name="inspect-the-deployment-architecture"></a>배포 아키텍처 검사

#### <a name="document-hardware-requirements-for-each-service"></a>각 서비스에 대한 하드웨어 요구 사항 문서화

Spring Boot 애플리케이션에 대한 다음 정보를 문서화합니다.

* 실행되는 인스턴스 수
* 각 인스턴스에 할당되는 CPU 수
* 각 인스턴스에 할당되는 RAM 양

#### <a name="document-geo-replicationdistribution"></a>지역 복제/배포 문서화

Spring Boot 애플리케이션 인스턴스가 현재 여러 지역 또는 데이터 센터 간에 분산되어 있는지 확인합니다. 마이그레이션하는 애플리케이션의 가동 시간 요구 사항/SLA를 문서화합니다.
