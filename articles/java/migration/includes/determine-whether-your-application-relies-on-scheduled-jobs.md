---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: e4370e6db3589e6050395fe806541505fa3eb8bb
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738155"
---
### <a name="determine-whether-your-application-relies-on-scheduled-jobs"></a>애플리케이션에서 예약된 작업을 사용하는지 확인

Quartz Scheduler 작업 또는 Unix cron 작업과 같은 예약된 작업을 Azure Kubernetes Service에서 사용해서는 안 됩니다. Azure Kubernetes Service는 예약된 작업이 포함된 애플리케이션을 내부적으로 배포하는 것을 방지하지 않습니다. 그러나 애플리케이션이 확장되는 경우 동일한 예약된 작업이 예약된 기간마다 두 번 이상 실행될 수 있습니다. 이 경우 의도하지 않은 결과가 발생할 수 있습니다.

AKS 클러스터에서 예약된 작업을 실행하려면 필요에 따라 Kubernetes CronJob을 정의합니다. 자세한 내용은 [CronJob을 사용하여 자동화된 작업 실행](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/)을 참조하세요.
