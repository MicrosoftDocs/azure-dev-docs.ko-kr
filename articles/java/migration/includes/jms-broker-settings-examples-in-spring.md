---
author: yevster
ms.author: yebronsh
ms.date: 2/12/2020
ms.openlocfilehash: e9d6993c96fc90c065cda5f8b3177147c35d0242
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990265"
---
*application.properties* 파일의 ActiveMQ 예제는 다음과 같습니다.

```properties
spring.activemq.brokerurl=broker:(tcp://localhost:61616,network:static:tcp://remotehost:61616)?persistent=false&useJmx=true
spring.activemq.user=admin
spring.activemq.password=tryandguess
```

ActiveMQ 구성에 대한 자세한 내용은 [Spring Boot 메시징 설명서](https://docs.spring.io/spring-boot/docs/2.0.x/reference/html/boot-features-messaging.html)를 참조하세요.

*application.yaml* 파일의 IBM MQ 예제는 다음과 같습니다.

```yaml
ibm:
  mq:
    queueManager: qm1
    channel: dev.ORDERS
    connName: localhost(14)
    user: admin
    password: big$ecr3t
```

IBM MQ 구성에 대한 자세한 내용은 [IBM MQ Spring 구성 요소 설명서](https://github.com/ibm-messaging/mq-jms-spring#ibm-mq-jms-spring-components)를 참조하세요.
