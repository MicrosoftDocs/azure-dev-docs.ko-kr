---
title: Azure Database for MySQL에서 Spring Data JPA 사용
description: Azure Database for MySQL 데이터베이스에서 Spring Data JPA를 사용하는 방법을 알아봅니다.
documentationcenter: java
ms.date: 06/16/2020
ms.service: mysql
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 6e4371e4d375c663f61f59707c553ac0e2d6e237
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831229"
---
# <a name="use-spring-data-jpa-with-azure-database-for-mysql"></a>Azure Database for MySQL에서 Spring Data JPA 사용

이 항목에서는 [Spring Data JPA](https://spring.io/projects/spring-data-jpa)를 사용하여 [Azure Database for MySQL](/azure/mysql/)에서 정보를 저장하고 검색하는 애플리케이션 샘플을 만드는 방법을 보여줍니다.

[JPA(Java Persistence API)](https://en.wikipedia.org/wiki/Java_Persistence_API)는 개체 관계형 매핑을 위한 표준 Java API입니다.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>샘플 애플리케이션

이 문서에서는 샘플 애플리케이션을 코딩합니다. 더 빠르게 진행하려는 경우 이 애플리케이션은 이미 코딩되어 [https://github.com/Azure-Samples/quickstart-spring-data-jpa-mysql](https://github.com/Azure-Samples/quickstart-spring-data-jpa-mysql)에서 사용할 수 있습니다.

[!INCLUDE [spring-data-mysql-setup.md](includes/spring-data-mysql-setup.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Spring Initializr를 사용하여 애플리케이션 생성

다음을 입력하여 명령줄에서 애플리케이션을 생성합니다.

```bash
curl https://start.spring.io/starter.tgz -d dependencies=web,data-jpa,mysql -d baseDir=azure-database-workshop -d bootVersion=2.3.1.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="configure-spring-boot-to-use-azure-database-for-mysql"></a>Azure Database for MySQL을 사용하도록 Spring Boot 구성

*src/main/resources/application.properties* 파일을 열고 다음을 추가합니다. 두 개의 `$AZ_DATABASE_NAME` 변수와 `$AZ_MYSQL_PASSWORD` 변수를 이 문서의 시작 부분에서 구성한 값으로 바꾸어야 합니다.

```properties
logging.level.org.hibernate.SQL=DEBUG

spring.datasource.url=jdbc:mysql://$AZ_DATABASE_NAME.mysql.database.azure.com:3306/demo?serverTimezone=UTC
spring.datasource.username=spring@$AZ_DATABASE_NAME
spring.datasource.password=$AZ_MYSQL_PASSWORD

spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=create-drop
```

> [!WARNING]
> 구성 속성 `spring.jpa.hibernate.ddl-auto=create-drop`은 Spring Boot가 애플리케이션 시작 시 데이터베이스 스키마를 자동으로 만들고, 종료 시 이를 삭제함을 의미합니다. 이는 테스트용으로는 유용하지만 프로덕션 환경에서는 사용하면 안 됩니다.

> [!NOTE]
> `?serverTimezone=UTC`를 `spring.datasource.url` 구성 속성에 추가하여 데이터베이스에 연결할 때 UTC(협정 세계시) 날짜 형식을 사용하도록 JDBC 드라이버에 지시합니다. 그렇지 않으면 Java 서버에서 데이터베이스와 동일한 날짜 형식을 사용하지 않으므로 오류가 발생합니다.

이제 제공된 Maven 래퍼를 사용하여 애플리케이션을 시작할 수 있습니다.

```bash
./mvnw spring-boot:run
```

처음으로 실행 중인 이 애플리케이션의 스크린샷은 다음과 같습니다.

[![실행 중인 애플리케이션](media/configure-spring-data-jpa-with-azure-mysql/create-mysql-01.png)](media/configure-spring-data-jpa-with-azure-mysql/create-mysql-01.png#lightbox)

## <a name="code-the-application"></a>애플리케이션 코딩

다음으로, JPA를 사용하여 MySQL 서버에서 데이터를 저장하고 검색하는 Java 코드를 추가합니다.

[!INCLUDE [spring-data-jpa-create-application.md](includes/spring-data-jpa-create-application.md)]

이러한 cURL 요청의 스크린샷은 다음과 같습니다.

[![cURL을 사용한 테스트](media/configure-spring-data-jpa-with-azure-mysql/create-mysql-02.png)](media/configure-spring-data-jpa-with-azure-mysql/create-mysql-02.png#lightbox)

축하합니다! JPA를 사용하여 Azure Database for MySQL에서 데이터를 저장하고 검색하는 Spring Boot 애플리케이션을 만들었습니다.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>추가 리소스

Spring Data JPA에 대한 자세한 내용은 Spring의 [참조 설명서](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#reference)를 참조하세요.

Java와 함께 Azure를 사용하는 방법에 관한 자세한 정보는 [Java 개발자를 위한 Azure](../index.yml)와 [Azure DevOps 및 Java 사용하기](/azure/devops/)를 참조하세요.