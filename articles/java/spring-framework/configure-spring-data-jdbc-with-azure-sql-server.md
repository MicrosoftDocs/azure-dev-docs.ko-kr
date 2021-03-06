---
title: Azure SQL Database에서 Spring Data JDBC 사용
description: Azure SQL Database를 통해 Spring Data JDBC를 사용하는 방법을 알아보세요.
documentationcenter: java
ms.date: 10/13/2020
ms.service: sql-database
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: d88ec684be27f06cad4c16f4f5233e98bbbac2f7
ms.sourcegitcommit: 709fa38a137b30184a7397e0bfa348822f3ea0a7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96442305"
---
# <a name="use-spring-data-jdbc-with-azure-sql-database"></a>Azure SQL Database에서 Spring Data JDBC 사용

이 항목에서는 [Spring Data JDBC](https://spring.io/projects/spring-data-jdbc)를 사용하여 [Azure SQL Database](/azure/sql-database/)에 정보를 저장하고 검색하는 애플리케이션 샘플을 만드는 방법을 보여줍니다.

[JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity)는 기존 관계형 데이터베이스에 연결하는 표준 Java API입니다.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>샘플 애플리케이션

이 문서에서는 샘플 애플리케이션을 코딩합니다. 더 빠르게 진행하려는 경우 이 애플리케이션은 이미 코딩되어 [https://github.com/Azure-Samples/quickstart-spring-data-jdbc-sql-server](https://github.com/Azure-Samples/quickstart-spring-data-jdbc-sql-server)에서 사용할 수 있습니다.

[!INCLUDE [spring-data-sql-server-setup.md](includes/spring-data-sql-server-setup.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Spring Initializr를 사용하여 애플리케이션 생성

다음 명령을 실행하여 명령줄에서애플리케이션을 생성합니다.

```bash
curl https://start.spring.io/starter.tgz -d dependencies=web,data-jdbc,sqlserver -d baseDir=azure-database-workshop -d bootVersion=2.3.1.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="configure-spring-boot-to-use-azure-sql-database"></a>Azure SQL Database를 사용하도록 Spring Boot 구성

*src/main/resources/application.properties* 파일을 열고 다음 텍스트를 추가합니다.

```properties
logging.level.org.springframework.jdbc.core=DEBUG

spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
spring.datasource.username=spring@$AZ_DATABASE_NAME
spring.datasource.password=$AZ_SQL_SERVER_PASSWORD

spring.datasource.initialization-mode=always
```

두 개의 `$AZ_DATABASE_NAME` 변수와 `$AZ_SQL_SERVER_PASSWORD` 변수를 이 문서의 시작 부분에서 구성한 값으로 바꿉니다.

> [!WARNING]
> `spring.datasource.initialization-mode=always` 구성 속성은 서버가 시작될 때마다 Spring Boot에서 나중에 만들 `schema.sql` 파일을 사용하여 데이터베이스 스키마를 자동으로 생성함을 의미합니다. 이는 테스트에 유용하지만, 다시 시작할 때마다 데이터가 삭제되므로 프로덕션 환경에서 사용하면 안 됩니다.

이제 제공된 Maven 래퍼를 사용하여 다음과 같이 애플리케이션을 시작할 수 있습니다.

```bash
./mvnw spring-boot:run
```

처음으로 실행 중인 이 애플리케이션의 스크린샷은 다음과 같습니다.

[![실행 중인 애플리케이션](media/configure-spring-data-jdbc-with-azure-sql-server/create-sql-server-01.png)](media/configure-spring-data-jdbc-with-azure-sql-server/create-sql-server-01.png#lightbox)

### <a name="create-the-database-schema"></a>데이터베이스 스키마 만들기

Spring Boot는 데이터베이스 스키마를 만들기 위해 *src/main/resources/schema.sql* 을 자동으로 실행합니다. 해당 파일을 만들고 다음 콘텐츠를 추가합니다.

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id INT IDENTITY PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BIT);
```

다음 명령을 사용하여 실행 중인 애플리케이션을 중지하고 다시 시작합니다. 이제 애플리케이션이 이전에 생성된 `demo` 데이터베이스를 사용하고 내부에 `todo` 테이블을 만듭니다.

```bash
./mvnw spring-boot:run
```

## <a name="code-the-application"></a>애플리케이션 코딩

다음으로, JDBC를 사용하여 Azure SQL Database 서버에서 데이터를 저장하고 검색하는 Java 코드를 추가합니다.

[!INCLUDE [spring-data-jdbc-create-application.md](includes/spring-data-jdbc-create-application.md)]

이러한 cURL 요청의 스크린샷은 다음과 같습니다.

[![cURL을 사용한 테스트](media/configure-spring-data-jdbc-with-azure-sql-server/create-sql-server-02.png)](media/configure-spring-data-jdbc-with-azure-sql-server/create-sql-server-02.png#lightbox)

축하합니다! JDBC를 사용하여 Azure SQL Database에서 데이터를 저장하고 검색하는 Spring Boot 애플리케이션을 만들었습니다.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>추가 리소스

Spring Data JDBC에 대한 자세한 내용은 Spring의 [참조 설명서](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#reference)를 참조하세요.

Java와 함께 Azure를 사용하는 방법에 관한 자세한 정보는 [Java 개발자를 위한 Azure](../index.yml)와 [Azure DevOps 및 Java 사용하기](/azure/devops/)를 참조하세요.

### <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 예기치 않은 요금이 청구되지 않도록 [Azure Portal](https://portal.azure.com/)을 사용하여 이 문서에서 만든 리소스를 삭제합니다.
