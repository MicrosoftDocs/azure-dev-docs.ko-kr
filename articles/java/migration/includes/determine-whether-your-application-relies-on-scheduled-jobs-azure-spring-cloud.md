---
author: yevster
ms.author: yebronsh
ms.date: 7/21/2020
ms.openlocfilehash: b1acff280bddea700b0382609c1003129b05dad2
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89062651"
---
#### <a name="determine-whether-your-application-relies-on-scheduled-jobs"></a>애플리케이션에서 예약된 작업을 사용하는지 확인

Quartz Scheduler 작업 또는 Unix cron 작업과 같은 예약된 작업을 Azure Spring Cloud에서 사용해서는 안 됩니다. Azure Spring Cloud는 예약된 작업이 포함된 애플리케이션을 내부적으로 배포하는 것을 막지 않습니다. 그러나 애플리케이션이 확장되는 경우 동일한 예약된 작업이 예약된 기간마다 두 번 이상 실행될 수 있습니다. 이 경우 의도하지 않은 결과가 발생할 수 있습니다.

프로덕션 서버에서 실행되는 예약된 작업을 애플리케이션 코드 내부 또는 외부에서 인벤토리에 추가합니다.
