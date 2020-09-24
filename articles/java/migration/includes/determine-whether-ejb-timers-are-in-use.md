---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: c77a5ae60807cf25415ab86dab9d9891abab13f9
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738404"
---
### <a name="determine-whether-ejb-timers-are-in-use"></a>EJB 타이머가 사용 중인지 확인

애플리케이션에서 EJB 타이머를 사용하는 경우 각 WildFly 인스턴스에서 독립적으로 EJB 타이머 코드를 트리거할 수 있는지 확인해야 합니다. 이 유효성 검사는 Azure Kubernetes 서비스 배포 시나리오에서 각 EJB 타이머가 자체 WildFly 인스턴스에서 트리거되기 때문에 필요합니다.
