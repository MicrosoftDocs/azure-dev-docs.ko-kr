---
title: Azure Database for PostgreSQL에서 Spring Data R2DBC 사용
description: Azure Database for PostgreSQL 데이터베이스에서 Spring Data R2DBC를 사용하는 방법을 알아보세요.
documentationcenter: java
ms.date: 03/18/2020
ms.service: postgresql
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.openlocfilehash: 154997506c15b84c7e0110fa2e45645bfd1585a2
ms.sourcegitcommit: fbbc341a0b9e17da305bd877027b779f5b0694cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83631662"
---
# <a name="use-spring-data-r2dbc-with-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에서 Spring Data R2DBC 사용

이 항목에서는 [Spring Data R2DBC](https://spring.io/projects/spring-data-r2dbc)를 사용하는 샘플 애플리케이션을 만들어 [r2dbc-mysql GitHub 리포지토리](https://github.com/r2dbc/r2dbc-postgresql)의 PostgreSQL용 R2DBC 구현을 사용하여 [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/)에서 정보를 저장 및 검색하는 것을 보여줍니다.

[R2DBC](https://r2dbc.io/)는 기존 관계형 데이터베이스에 반응형 API를 제공합니다. Spring WebFlux와 함께 사용하여 비 블로킹 API를 사용하는 완전 반응형 Spring Boot 애플리케이션을 만들 수 있습니다. 기본적인 "연결당 하나의 스레드" 접근보다 향상된 확장성을 제공합니다.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="prepare-the-working-environment"></a>작업 환경 준비

먼저 다음 명령을 사용하여 몇 가지 환경 변수를 설정합니다.

```bash
AZ_RESOURCE_GROUP=r2dbc-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_POSTGRESQL_USERNAME=spring
AZ_POSTGRESQL_PASSWORD=<YOUR_POSTGRESQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

자리 표시자를 이 문서 전체에서 사용되는 다음 값으로 바꿉니다.

- `<YOUR_DATABASE_NAME>`: PostgreSQL 서버의 이름입니다. Azure에서 고유해야 합니다.
- `<YOUR_AZURE_REGION>`: 사용할 Azure 지역. 기본적으로 `eastus`를 사용할 수 있지만 거주지와 더 가까운 지역을 구성하는 것이 좋습니다. `az account list-locations`를 입력하면 사용 가능한 지역의 전체 목록을 나열할 수 있습니다.
- `<YOUR_POSTGRESQL_PASSWORD>`: PostgreSQL 데이터베이스 서버의 암호입니다. 암호의 길이는 8자 이상이어야 합니다. 다음 범주 중 세 가지 범주의 문자여야 합니다. 영문 대문자, 영문 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등).
- `<YOUR_LOCAL_IP_ADDRESS>`: Spring Boot 애플리케이션을 실행할 로컬 컴퓨터의 IP 주소. 이를 확인하는 간편한 방법 중 하나는 브라우저에서 [whatismyip.akamai.com](http://whatismyip.akamai.com/)으로 이동하는 것입니다.

다음으로, 리소스 그룹을 만듭니다.

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
    | jq
```

> [!NOTE]
> [Azure Cloud Shell](https://shell.azure.com/)에 기본적으로 설치되는 `jq` 유틸리티를 사용하여 JSON 데이터를 표시하고 가독성을 높입니다.
> 이 유틸리티가 마음에 들지 않을 경우 여기서 사용하게 될 모든 명령의 `| jq` 부분을 안전하게 제거하면 됩니다.

## <a name="create-an-azure-database-for-postgresql-instance"></a>Azure Database for PostgreSQL 인스턴스 만들기

먼저 관리형 PostgreSQL 서버를 만듭니다.

> [!NOTE]
> PostgreSQL 서버 만들기에 관한 자세한 정보는 [Azure portal을 사용하여 Azure Database for PostgreSQL 서버 만들기](/azure/postgresql/quickstart-create-server-database-portal)에서 확인할 수 있습니다.

[Azure Cloud Shell](https://shell.azure.com/)에서 다음 스크립트를 실행합니다.

```azurecli
az postgres server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name B_Gen5_1 \
    --storage-size 5120 \
    --admin-user $AZ_POSTGRESQL_USERNAME \
    --admin-password $AZ_POSTGRESQL_PASSWORD \
    | jq
```

이 명령은 작은 PostgreSQL 서버를 만듭니다.

### <a name="configure-a-firewall-rule-for-your-postgresql-server"></a>PostgreSQL 서버에 대한 방화벽 규칙 구성

Azure Database for PostgreSQL 인스턴스는 기본적으로 보호됩니다. 들어오는 연결을 허용하지 않는 방화벽이 있습니다. 데이터베이스를 사용하려면 로컬 IP 주소에서 데이터베이스 서버에 액세스할 수 있도록 하는 방화벽 규칙을 추가해야 합니다.

이 문서의 시작 부분에서 로컬 IP 주소를 구성했기 때문에 다음을 실행하여 서버의 방화벽을 열 수 있습니다.

```azurecli
az postgres server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
    | jq
```

### <a name="configure-a-postgresql-database"></a>PostgreSQL 데이터베이스 구성

이전에 만든 PostgreSQL 서버가 비어 있습니다. Spring Boot 애플리케이션에서 사용할 수 있는 데이터베이스가 없습니다. `demo`라는 새 데이터베이스를 만듭니다.

```azurecli
az postgres db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server-name $AZ_DATABASE_NAME \
    | jq
```

[!INCLUDE [spring-data-create-reactive.md](includes/spring-data-create-reactive.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Spring Initializr를 사용하여 애플리케이션 생성

다음을 입력하여 명령줄에서 애플리케이션을 생성합니다.

```bash
curl https://start.spring.io/starter.tgz -d dependencies=webflux,data-r2dbc -d baseDir=azure-database-workshop -d bootVersion=2.3.0.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="add-the-reactive-postgresql-driver-implementation"></a>반응형 PostgreSQL 드라이버 구현 추가

생성된 프로젝트의 *pom .xml* 파일을 열어 [GitHub의 r2dbc-postgresql 리포지토리](https://github.com/r2dbc/r2dbc-postgresql)의 반응형 PostgreSQL 드라이버를 추가합니다.

`spring-boot-starter-webflux` 종속성 다음에 아래와 같은 코드 조각을 추가합니다.

```xml
<dependency>
    <groupId>io.r2dbc</groupId>
    <artifactId>r2dbc-postgresql</artifactId>
    <scope>runtime</scope>
</dependency>
```

### <a name="configure-spring-boot-to-use-azure-database-for-postgresql"></a>Azure Database for PostgreSQL을 사용하도록 Spring Boot 구성

*src/main/resources/application.properties* 파일을 열고 다음을 추가합니다.

```properties
logging.level.org.springframework.data.r2dbc=DEBUG

spring.r2dbc.url=r2dbc:pool:postgres://$AZ_DATABASE_NAME.postgres.database.azure.com:5432/demo
spring.r2dbc.username=spring@$AZ_DATABASE_NAME
spring.r2dbc.password=$AZ_POSTGRESQL_PASSWORD
spring.r2dbc.properties.sslMode=REQUIRE
```

> [!WARNING]
> 보안상의 이유로 Azure Database for PostgreSQL은 SSL 연결을 사용해야 합니다. 이것이 `spring.r2dbc.properties.sslMode=REQUIRE` 구성 속성을 추가해야 하는 이유입니다. 그렇지 않으면 R2DBC PostgreSQL 드라이버는 안전하지 않은 연결을 시도하며, 이는 곧 실패합니다.

- 두 개의 `$AZ_DATABASE_NAME` 변수를 이 문서의 시작 부분에서 구성한 값으로 바꿉니다.
- `$AZ_POSTGRESQL_PASSWORD` 변수를 이 문서의 시작 부분에서 구성한 값으로 바꿉니다.

> [!NOTE]
> 성능 향상을 위해 `spring.r2dbc.url` 속성은 [r2dbc-pool](https://github.com/r2dbc/r2dbc-pool)을 사용하여 연결 풀을 사용하도록 구성됩니다.

이제 제공된 Maven 래퍼를 사용하여 애플리케이션을 시작할 수 있습니다.

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

실행 중인 애플리케이션을 중지하고 다시 시작합니다. 이제 애플리케이션이 이전에 생성된 `demo` 데이터베이스를 사용하고 내부에 `todo` 테이블을 만듭니다.

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

Java와 함께 Azure를 사용하는 방법에 관한 자세한 정보는 [Java 개발자를 위한 Azure](/azure/developer/java/)와 [Azure DevOps 및 Java 사용하기](/azure/devops/)를 참조하세요.
