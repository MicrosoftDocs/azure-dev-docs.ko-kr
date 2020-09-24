---
title: Azure Database for MySQL에서 Spring Data R2DBC 사용
description: Azure Database for MySQL 데이터베이스에서 Spring Data R2DBC를 사용하는 방법을 알아보세요.
documentationcenter: java
ms.date: 03/18/2020
ms.service: mysql
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 84dffb21916498e9e80f458512d83251815306c0
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831239"
---
# <a name="use-spring-data-r2dbc-with-azure-database-for-mysql"></a>Azure Database for MySQL에서 Spring Data R2DBC 사용

이 항목에서는 [Spring Data R2DBC](https://spring.io/projects/spring-data-r2dbc)를 사용하는 샘플 애플리케이션을 만들어 [r2dbc-mysql GitHub 리포지토리](https://github.com/mirromutth/r2dbc-mysql)의 MySQL용 R2DBC 구현을 사용하여 [Azure Database for MySQL](/azure/mysql/)에서 정보를 저장 및 검색하는 것을 보여줍니다.

[R2DBC](https://r2dbc.io/)는 기존 관계형 데이터베이스에 반응형 API를 제공합니다. Spring WebFlux와 함께 사용하여 비 블로킹 API를 사용하는 완전 반응형 Spring Boot 애플리케이션을 만들 수 있습니다. 기본적인 "연결당 하나의 스레드" 접근보다 향상된 확장성을 제공합니다.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>샘플 애플리케이션

이 문서에서는 샘플 애플리케이션을 코딩합니다. 더 빠르게 진행하려는 경우 이 애플리케이션은 이미 코딩되어 [https://github.com/Azure-Samples/quickstart-spring-data-r2dbc-mysql](https://github.com/Azure-Samples/quickstart-spring-data-r2dbc-mysql)에서 사용할 수 있습니다.

[!INCLUDE [spring-data-mysql-setup.md](includes/spring-data-mysql-setup.md)]

[!INCLUDE [spring-data-create-reactive.md](includes/spring-data-create-reactive.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Spring Initializr를 사용하여 애플리케이션 생성

다음을 입력하여 명령줄에서 애플리케이션을 생성합니다.

```bash
curl https://start.spring.io/starter.tgz -d dependencies=webflux,data-r2dbc -d baseDir=azure-database-workshop -d bootVersion=2.3.1.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="add-the-reactive-mysql-driver-implementation"></a>반응형 MySQL 드라이버 구현 추가

생성된 프로젝트의 *pom .xml* 파일을 열어 [GitHub의 r2dbc-mysql 리포지토리](https://github.com/mirromutth/r2dbc-mysql)의 반응형 MySQL 드라이버를 추가합니다.

`spring-boot-starter-webflux` 종속성 다음에 아래와 같은 코드 조각을 추가합니다.

```xml
<dependency>
   <groupId>dev.miku</groupId>
   <artifactId>r2dbc-mysql</artifactId>
   <version>0.8.1.RELEASE</version>
   <scope>runtime</scope>
</dependency>
```

### <a name="configure-spring-boot-to-use-azure-database-for-mysql"></a>Azure Database for MySQL을 사용하도록 Spring Boot 구성

*src/main/resources/application.properties* 파일을 열고 다음을 추가합니다.

```properties
logging.level.org.springframework.data.r2dbc=DEBUG

spring.r2dbc.url=r2dbc:pool:mysql://$AZ_DATABASE_NAME.mysql.database.azure.com:3306/demo
spring.r2dbc.username=spring@$AZ_DATABASE_NAME
spring.r2dbc.password=$AZ_MYSQL_PASSWORD
```

- 두 개의 `$AZ_DATABASE_NAME` 변수를 이 문서의 시작 부분에서 구성한 값으로 바꿉니다.
- `$AZ_MYSQL_PASSWORD` 변수를 이 문서의 시작 부분에서 구성한 값으로 바꿉니다.

> [!NOTE]
> 성능 향상을 위해 `spring.r2dbc.url` 속성은 [r2dbc-pool](https://github.com/r2dbc/r2dbc-pool)을 사용하여 연결 풀을 사용하도록 구성됩니다.

이제 제공된 Maven 래퍼를 사용하여 애플리케이션을 시작할 수 있습니다.

```bash
./mvnw spring-boot:run
```

처음으로 실행 중인 이 애플리케이션의 스크린샷은 다음과 같습니다.

[![실행 중인 애플리케이션](media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-01.png)](media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-01.png#lightbox)

### <a name="create-the-database-schema"></a>데이터베이스 스키마 만들기

[!INCLUDE [spring-data-r2dbc-create-schema.md](includes/spring-data-r2dbc-create-schema.md)]

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

실행 중인 애플리케이션을 중지하고 다시 시작합니다. 이제 애플리케이션이 이전에 생성된 `demo` 데이터베이스를 사용하고 내부에 `todo` 테이블을 만듭니다.

```bash
./mvnw spring-boot:run
```

생성되는 데이터베이스 테이블의 스크린샷은 다음과 같습니다.

[![데이터베이스 테이블 생성](media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-02.png)](media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-02.png#lightbox)

## <a name="code-the-application"></a>애플리케이션 코딩

다음으로, R2DBC를 사용하여 MySQL 서버에서 데이터를 저장하고 검색하는 Java 코드를 추가합니다.

[!INCLUDE [spring-data-r2dbc-create-application.md](includes/spring-data-r2dbc-create-application.md)]

이러한 cURL 요청의 스크린샷은 다음과 같습니다.

[![cURL을 사용한 테스트](media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-03.png)](media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-03.png#lightbox)

축하합니다! R2DBC를 사용하여 Azure Database for MySQL에서 데이터를 저장하고 검색하는 완전한 반응형 Spring Boot 애플리케이션을 만들었습니다.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>추가 리소스

Spring Data R2DBC에 대한 자세한 내용은 Spring의 [참조 설명서](https://docs.spring.io/spring-data/r2dbc/docs/current/reference/html/#reference)를 참조하세요.

Java와 함께 Azure를 사용하는 방법에 관한 자세한 정보는 [Java 개발자를 위한 Azure](../index.yml)와 [Azure DevOps 및 Java 사용하기](/azure/devops/)를 참조하세요.