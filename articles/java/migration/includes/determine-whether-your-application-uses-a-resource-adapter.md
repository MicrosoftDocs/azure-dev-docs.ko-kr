---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 9fa87bf3d0de64271a9ffb0e7e8fbff3cd1afd12
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738499"
---
### <a name="determine-whether-your-application-uses-a-resource-adapter"></a>애플리케이션에서 리소스 어댑터를 사용하는지 확인

RA(리소스 어댑터)가 필요한 애플리케이션은 WildFly와 호환되어야 합니다. RA를 서버에 배포하고 적절하게 구성하여 WildFly의 독립 실행형 인스턴스에서 제대로 작동하는지 확인합니다. RA가 제대로 작동하면 JAR을 Docker 이미지의 서버 클래스 경로에 추가하고 필요한 구성 파일을 WildFly 서버 디렉터리의 올바른 위치에 배치해야 합니다.
