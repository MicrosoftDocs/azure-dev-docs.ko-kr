---
author: yevster
ms.author: yebronsh
ms.date: 5/4/2020
ms.openlocfilehash: 7b2ad87dfe2e2c7358737ff02ec52b97b1332ae7
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990135"
---
### <a name="inspect-the-deployment-architecture"></a>배포 아키텍처 검사

#### <a name="document-hardware-requirements-for-each-service"></a>각 서비스에 대한 하드웨어 요구 사항 문서화

각 Spring Cloud 서비스(구성 서버, 레지스트리 또는 게이트웨이 제외)에 대해 다음 정보를 문서화합니다.

* 실행되는 인스턴스 수
* 각 인스턴스에 할당되는 CPU 수
* 각 인스턴스에 할당되는 RAM 양

#### <a name="document-geo-replicationdistribution"></a>지역 복제/배포 문서화

Spring Cloud 애플리케이션이 현재 여러 지역 또는 데이터 센터 간에 분산되어 있는지 확인합니다. 마이그레이션하는 애플리케이션의 가동 시간 요구 사항/SLA를 문서화합니다.

#### <a name="identify-clients-that-bypass-the-service-registry"></a>서비스 레지스트리를 무시하는 클라이언트 확인

Spring Cloud Service Registry를 사용하지 않고 마이그레이션할 서비스를 호출하는 클라이언트 애플리케이션을 확인합니다. 마이그레이션 후에는 이러한 호출을 더 이상 수행할 수 없습니다. 마이그레이션하기 전에 [Spring Cloud OpenFeign](https://spring.io/projects/spring-cloud-openfeign)을 사용하도록 이러한 클라이언트를 업데이트합니다.
