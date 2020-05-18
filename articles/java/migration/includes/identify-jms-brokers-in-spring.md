---
author: yevster
ms.author: yebronsh
ms.date: 2/12/2020
ms.openlocfilehash: d17b69ea5299c24406a63fc6239c350b6cbcbdd4
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990145"
---
#### <a name="jms-message-brokers"></a>JMS 메시지 broker

관련 종속성에 대한 빌드 매니페스트(일반적으로 *pom.xml* 또는 *build.gradle* 파일)를 검토하여 사용 중인 broker를 확인합니다.

예를 들어 ActiveMQ를 사용하는 Spring Boot 애플리케이션은 일반적으로 이 종속성을 *pom.xml* 파일에 포함합니다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-activemq</artifactId>
</dependency>
```

독점적 브로커를 사용하는 Spring Boot 애플리케이션은 일반적으로 브로커의 JMS 드라이버 라이브러리에 직접 종속성을 포함하고 있습니다. 다음은 *build.gradle* 파일의 예제입니다.

```json
    dependencies {
      ...
      compile("com.ibm.mq:com.ibm.mq.allclient:9.0.4.0")
      ...
    }
```
