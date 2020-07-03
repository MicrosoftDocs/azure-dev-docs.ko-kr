---
author: judubois
ms.date: 05/06/2020
ms.author: judubois
ms.openlocfilehash: fa66c4e9db481e31853c8e67816a14b6ee753fd2
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507517"
---
주 `DemoApplication` 클래스 내에서 다음 코드를 사용하여 데이터베이스 스키마를 만들 새 Spring 빈을 구성합니다.

```java
    @Bean
    public ConnectionFactoryInitializer initializer(ConnectionFactory connectionFactory) {
        ConnectionFactoryInitializer initializer = new ConnectionFactoryInitializer();
        initializer.setConnectionFactory(connectionFactory);
        ResourceDatabasePopulator populator = new ResourceDatabasePopulator(new ClassPathResource("schema.sql"));
        initializer.setDatabasePopulator(populator);
        return initializer;
    }
```

이 Spring 빈은 *schema.sql*이라는 파일을 사용하므로 *src/main/resources* 폴더에 해당 파일을 만들고 다음 텍스트를 추가합니다.
