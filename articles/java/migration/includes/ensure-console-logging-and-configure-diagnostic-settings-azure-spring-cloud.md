---
author: yevster
ms.author: yebronsh
ms.date: 4/15/2020
ms.openlocfilehash: 3ff76d977c231b4b238f9dbec7f3bfaddfe5e484
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507609"
---
### <a name="ensure-console-logging-and-configure-diagnostic-settings"></a>콘솔 로깅 확인 및 진단 설정 구성

모든 출력이 파일이 아닌 콘솔로 라우팅되도록 로깅을 구성합니다.

애플리케이션이 Azure Spring Cloud에 배포되면 [진단 설정을 추가](/azure/spring-cloud/diagnostic-services)하여 기록된(예: Azure Monitor Log Analytics를 통해) 이벤트를 사용할 수 있도록 합니다.

#### <a name="logstashelk-stack"></a>LogStash/ELK Stack

LogStash/ELK Stack을 로그 집계에 사용하는 경우 콘솔 출력을 [Azure Event Hub](/azure/event-hubs/)로 스트림하도록 진단 설정을 구성합니다. 그런 다음, [LogStash EventHub 플러그 인](https://github.com/logstash-plugins/logstash-input-azure_event_hubs)을 사용하여 기록된 이벤트를 LogStash에 수집합니다.

#### <a name="splunk"></a>Splunk

Splunk를 로그 집계에 사용하는 경우 콘솔 출력을 [Azure Blob Storage](/azure/storage/blobs/)로 스트림하도록 진단 설정을 구성합니다. 그런 다음, [Microsoft Cloud Services용 Splunk 추가 기능](https://splunkbase.splunk.com/app/3757/)을 사용하여 기록된 이벤트를 Splunk에 수집합니다.
