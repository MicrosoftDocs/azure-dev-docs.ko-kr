---
author: yevster
ms.author: yebronsh
ms.date: 2/12/2020
ms.openlocfilehash: 6bf4c54c8f428e3ffce79a7ba9ac49aae028e2fd
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990235"
---
#### <a name="databases"></a>데이터베이스

SQL 데이터베이스의 연결 문자열을 확인합니다.

Spring Boot 애플리케이션의 경우 연결 문자열은 일반적으로 구성 파일에 나타납니다. 

다음은 *application.properties* 파일의 예제입니다.

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/mysql_db
spring.datasource.username=dbuser
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

다음은 *application.yaml* 파일의 예제입니다.

```yaml
spring:
  data:
    mongodb:
      uri: mongodb://mongouser:deepsecret@mongoserver.contoso.com:27017
```

가능한 구성 시나리오는 다음 Spring Data 설명서를 참조하세요.

* [JPA 리포지토리](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.repositories)
* [JDBC 리포지토리](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#jdbc.repositories)
* [Cassandra 리포지토리](https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/#cassandra.repositories)
* [MongoDB 리포지토리](https://docs.spring.io/spring-data/mongodb/docs/current/reference/html/#mongodb.repositories)
