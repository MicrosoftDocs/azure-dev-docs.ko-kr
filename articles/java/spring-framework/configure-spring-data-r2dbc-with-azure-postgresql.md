---
title: Azure Database for PostgreSQL에서 Spring Data R2DBC 사용
description: Azure Database for PostgreSQL 데이터베이스에서 Spring Data R2DBC를 사용하는 방법을 알아보세요.
documentationcenter: java
ms.date: 10/10/2020
ms.service: postgresql
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: b22b008c54536a2cf708b3574d77d95dba995027
ms.sourcegitcommit: f460914ac5843eb7392869a08e3a80af68ab227b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92010073"
---
# <a name="use-spring-data-r2dbc-with-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에서 Spring Data R2DBC 사용

이 항목에서는 [Spring Data R2DBC](https://spring.io/projects/spring-data-r2dbc)를 사용하여 [Azure Database for PostgreSQL](/azure/postgresql/) 데이터베이스에서 정보를 저장하고 검색하는 애플리케이션 샘플을 만드는 방법을 보여줍니다. 이 샘플은 GitHub의 [r2dbc-postgresql](https://github.com/pgjdbc/r2dbc-postgresql) 리포지토리에서 PostgreSQL에 대한 R2DBC 구현을 사용합니다.

[R2DBC](https://r2dbc.io/)는 기존 관계형 데이터베이스에 반응형 API를 제공합니다. Spring WebFlux와 함께 사용하여 비 블로킹 API를 사용하는 완전 반응형 Spring Boot 애플리케이션을 만들 수 있습니다. 기본적인 "연결당 하나의 스레드" 접근보다 향상된 확장성을 제공합니다.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>샘플 애플리케이션

이 문서에서는 샘플 애플리케이션을 코딩합니다. 더 빠르게 진행하려는 경우 이 애플리케이션은 이미 코딩되어 [https://github.com/Azure-Samples/quickstart-spring-data-r2dbc-postgresql](https://github.com/Azure-Samples/quickstart-spring-data-r2dbc-postgresql)에서 사용할 수 있습니다.

[!INCLUDE [spring-data-postgresql-setup.md](includes/spring-data-postgresql-setup.md)]

[!INCLUDE [spring-data-create-reactive.md](includes/spring-data-create-reactive.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Spring Initializr를 사용하여 애플리케이션 생성

다음 명령을 사용하여 명령 줄에서애플리케이션을 생성합니다.

```bash
curl https://start.spring.io/starter.tgz -d dependencies=webflux,data-r2dbc -d baseDir=azure-database-workshop -d bootVersion=2.3.4.RELEASE -d javaVersion=8 | tar -xzvf -
```

> [!NOTE]
> Spring Initializr는 Java 11을 기본 버전으로 사용합니다. 이 항목에 설명된 Spring Boot Starters를 사용하려면 대신 Java 8을 선택해야 합니다.

### <a name="add-the-reactive-postgresql-driver-implementation"></a>반응형 PostgreSQL 드라이버 구현 추가

생성된 프로젝트의 *pom .xml* 파일을 연 다음, [GitHub의 r2dbc-postgresql 리포지토리](https://github.com/pgjdbc/r2dbc-postgresql)에서 반응형 PostgreSQL 드라이버를 추가합니다. `spring-boot-starter-webflux` 종속성 후에 다음 텍스트를 추가합니다.

```xml
<dependency>
    <groupId>io.r2dbc</groupId>
    <artifactId>r2dbc-postgresql</artifactId>
    <scope>runtime</scope>
</dependency>
```

### <a name="configure-spring-boot-to-use-azure-database-for-postgresql"></a>Azure Database for PostgreSQL을 사용하도록 Spring Boot 구성

*src/main/resources/application.properties* 파일을 열고 다음 텍스트를 추가합니다.

```properties
logging.level.org.springframework.data.r2dbc=DEBUG

spring.r2dbc.url=r2dbc:pool:postgres://$AZ_DATABASE_NAME.postgres.database.azure.com:5432/demo
spring.r2dbc.username=spring@$AZ_DATABASE_NAME
spring.r2dbc.password=$AZ_POSTGRESQL_PASSWORD
spring.r2dbc.properties.sslMode=REQUIRE
```

> [!WARNING]
> 보안상의 이유로 Azure Database for PostgreSQL은 SSL 연결을 사용해야 합니다. 이것이 `spring.r2dbc.properties.sslMode=REQUIRE` 구성 속성을 추가해야 하는 이유입니다. 그렇지 않으면 R2DBC PostgreSQL 드라이버는 안전하지 않은 연결을 시도하며, 이는 곧 실패합니다.

두 개의 `$AZ_DATABASE_NAME` 변수와 `$AZ_POSTGRESQL_PASSWORD` 변수를 이 문서의 시작 부분에서 구성한 값으로 바꿉니다.

> [!NOTE]
> 성능 향상을 위해 `spring.r2dbc.url` 속성은 [r2dbc-pool](https://github.com/r2dbc/r2dbc-pool)을 사용하여 연결 풀을 사용하도록 구성됩니다.

이제 제공된 Maven 래퍼를 사용하여 다음과 같이 애플리케이션을 시작할 수 있습니다.

```bash
./mvnw spring-boot:run
```

처음으로 실행 중인 이 애플리케이션의 스크린샷은 다음과 같습니다.

[![실행 중인 애플리케이션](media/configure-spring-data-r2dbc-with-azure-postgresql/create-postgresql-01.png)](media/configure-spring-data-r2dbc-with-azure-postgresql/create-postgresql-01.png#lightbox)

### <a name="create-the-database-schema"></a>데이터베이스 스키마 만들기

[!INCLUDE [spring-data-r2dbc-create-schema.md](includes/spring-data-r2dbc-create-schema.md)]

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

다음 명령을 사용하여 실행 중인 애플리케이션을 중지하고 다시 시작합니다. 이제 애플리케이션이 이전에 생성된 `demo` 데이터베이스를 사용하고 내부에 `todo` 테이블을 만듭니다.

```bash
./mvnw spring-boot:run
```

생성되는 데이터베이스 테이블의 스크린샷은 다음과 같습니다.

[![데이터베이스 테이블 생성](media/configure-spring-data-r2dbc-with-azure-postgresql/create-postgresql-02.png)](media/configure-spring-data-r2dbc-with-azure-postgresql/create-postgresql-02.png#lightbox)

## <a name="code-the-application"></a>애플리케이션 코딩

다음으로, R2DBC를 사용하여 PostgreSQL 서버에서 데이터를 저장하고 검색하는 Java 코드를 추가합니다.

[!INCLUDE [spring-data-r2dbc-create-application.md](includes/spring-data-r2dbc-create-application.md)]

이러한 cURL 요청의 스크린샷은 다음과 같습니다.

[![cURL을 사용한 테스트](media/configure-spring-data-r2dbc-with-azure-postgresql/create-postgresql-03.png)](media/configure-spring-data-r2dbc-with-azure-postgresql/create-postgresql-03.png#lightbox)

축하합니다! R2DBC를 사용하여 Azure Database for PostgreSQL에서 데이터를 저장하고 검색하는 완전한 반응형 Spring Boot 애플리케이션을 만들었습니다.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>추가 리소스

Spring Data R2DBC에 대한 자세한 내용은 Spring의 [참조 설명서](https://docs.spring.io/spring-data/r2dbc/docs/current/reference/html/#reference)를 참조하세요.

Java와 함께 Azure를 사용하는 방법에 관한 자세한 정보는 [Java 개발자를 위한 Azure](../index.yml)와 [Azure DevOps 및 Java 사용하기](/azure/devops/)를 참조하세요.