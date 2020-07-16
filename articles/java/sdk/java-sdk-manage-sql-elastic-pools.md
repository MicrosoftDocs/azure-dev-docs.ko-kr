---
title: Java를 사용하여 SQL 데이터베이스 탄력적 풀 관리 | Microsoft Docs
description: Java용 Azure SDK를 사용하여 Azure SQL 데이터베이스를 만들고 구성하는 샘플 코드
author: rloutlaw
ms.assetid: 9b461de8-46bc-4650-8e9e-59531f4e2a53
ms.topic: article
ms.date: 3/30/2017
ms.reviewer: asirveda
ms.custom: devx-track-java
ms.openlocfilehash: 5d48192ba940e7afb019de5afad045eb4e8ed12a
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379437"
---
# <a name="manage-azure-sql-databases-in-elastic-pools-from-your-java-applications"></a>Java 애플리케이션에서 탄력적 풀의 Azure SQL 데이터베이스 관리

[이 샘플](https://github.com/Azure-Samples/sql-database-java-manage-sql-dbs-in-elastic-pool)에서는 단일 계획에서 여러 데이터베이스의 리소스를 관리하고 크기 조정할 수 있는 [탄력적 풀](/azure/sql-database/sql-database-elastic-pool)이 있는 SQL 데이터베이스 서버를 만듭니다.

## <a name="run-the-sample"></a>샘플 실행

[인증 파일](https://docs.microsoft.com/azure/java/java-sdk-azure-authenticate#mgmt-file)을 만들고 컴퓨터에서 파일의 전체 경로가 포함된 `AZURE_AUTH_LOCATION` 환경 변수를 설정합니다. 다음을 실행합니다.

```
git clone https://github.com/Azure-Samples/sql-database-java-manage-sql-dbs-in-elastic-pool
cd sql-database-java-manage-sql-dbs-in-elastic-pool
mvn clean compile exec:java
```

[GitHub에서 전체 샘플 코드(영문)](https://github.com/Azure-Samples/sql-database-java-manage-sql-dbs-in-elastic-pool)를 봅니다.

## <a name="authenticate-with-azure"></a>Azure를 사용하여 인증

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="create-a-sql-database-server-with-an-elastic-pool"></a>탄력적 풀이 있는 SQL 데이터베이스 서버 만들기

```java
SqlServer sqlServer = azure.sqlServers().define(sqlServerName)
                    .withRegion(Region.US_EAST)
                    .withNewResourceGroup(rgName)
                    .withAdministratorLogin(administratorLogin)
                    .withAdministratorPassword(administratorPassword)
                    // Use ElasticPoolEditions.STANDARD as the edition
                    // and create two databases
                    .withNewElasticPool(elasticPoolName, ElasticPoolEditions.STANDARD, 
                        database1Name, database2Name)
                    .create();
```

현재 버전 값은 [ElasticPoolEditions 클래스 참조](/java/api/com.microsoft.azure.management.sql.elasticpooleditions)를 참조하세요. 버전 리소스 특성을 비교하려면 [SQL 데이터베이스 탄력적 풀 설명서](/azure/sql-database/sql-database-elastic-pool)를 검토합니다. 

## <a name="change-database-transaction-unit-dtu-settings-in-an-elastic-pool"></a>탄력적 풀의 DTU(데이터베이스 트랜잭션 단위) 설정 변경

```java
// Set an pool of 200 eDTUs to share between the databases
// and ensure that a database always has 10 DTUs available but never uses more than 50
elasticPool = elasticPool.update()
                    .withDtu(200)
                    .withDatabaseDtuMin(10)
                    .withDatabaseDtuMax(50)
                    .apply();
```

SQL 데이터베이스에 리소스를 할당하는 방법에 대해 자세한 내용은 [DTU 및 eDTU 설명서](/azure/sql-database/sql-database-what-is-a-dtu)를 검토합니다.

## <a name="create-a-new-database-and-add-it-to-an-elastic-pool"></a>새 데이터베이스 만들기 및 탄력적 풀에 추가

```java
// Add a newly created database to the elastic pool
SqlDatabase anotherDatabase = sqlServer.databases().define(anotherDatabaseName).create();
elasticPool.update().withExistingDatabase(anotherDatabase).apply();            
```

API에서 첫 번째 문의 [S0 계층](/azure/sql-database/sql-database-service-tiers)에 `anotherDatabase`를 만듭니다. `anotherDatabase`를 탄력적 풀로 이동하면 풀 설정에 따라 데이터베이스 리소스가 할당됩니다.

## <a name="remove-a-database-from-an-elastic-pool"></a>탄력적 풀에서 데이터베이스 제거
```java
// Assign an edition since the database resources are no longer managed in the pool 
anotherDatabase = anotherDatabase.update()
                     .withoutElasticPool()
                     .withEdition(DatabaseEditions.STANDARD)
                     .apply();
```

값을 `withEdition()`으로 전달하려면 [DatabaseEditions 클래스 참조](/java/api/com.microsoft.azure.management.sql.databaseeditions)를 참조하세요.

## <a name="list-current-database-activities-in-an-elastic-pool"></a>탄력적 풀의 현재 데이터베이스 작업 나열
```java
// get a list of in-flight elastic operations on databases in the pool and log them 
for (ElasticPoolDatabaseActivity databaseActivity : elasticPool.listDatabaseActivities()) {
    System.out.println("Database " + databaseActivity.databaseName() 
        + " performing operation " + databaseActivity.operation() + 
        " with objective " + databaseActivity.requestedServiceObjective());
}
```

데이터베이스 작업에는 탄력적 풀 내외로 데이터베이스를 이동하고 탄력적 풀에서 데이터베이스를 업데이트하는 작업이 포함됩니다.


## <a name="list-databases-in-an-elastic-pool"></a>탄력적 풀에 데이터베이스 나열
```java
// Log a list of databases in the elastic pool 
for (SqlDatabase databaseInServer : elasticPool.listDatabases()) {
    System.out.println(databaseInServer.name());
}
```

데이터베이스를 더 자세히 쿼리하려면 [com.microsoft.Azure.management.sql.SqlDatabase](/java/api/com.microsoft.azure.management.sql.sqldatabase)에 있는 메서드를 검토합니다.

## <a name="delete-an-elastic-pool"></a>탄력적 풀 삭제
```java
sqlServer.elasticPools().delete(elasticPoolName);
```

탄력적 풀은 삭제하기 전에 비어 있어야 합니다.

## <a name="sample-code-summary"></a>샘플 코드 요약

이 샘플에서는 단일 탄력적 풀에서 관리되는 두 개의 데이터베이스가 있는 SQL 서버를 만듭니다. 탄력적 풀 리소스 제한을 업데이트한 다음 풀에 추가 데이터베이스를 추가합니다. 그런 다음 코드에서 탄력적 풀의 구성과 상태를 읽습니다. 

만든 리소스를 모두 삭제한 후에 샘플이 종료됩니다.

| 샘플에 사용되는 클래스 | 메모 |
|-------|-------|
| [SqlServer](/java/api/com.microsoft.azure.management.sql.sqlserver) | `azure.sqlServers().define()...create()` 흐름 체인으로 만든 Azure의 SQL DB 서버입니다. 탄력적 풀과 데이터베이스를 만들고 사용하는 메서드를 제공합니다. 
| [SqlDatabase](/java/api/com.microsoft.azure.management.sql.sqldatabase) | SQL 데이터베이스를 나타내는 클라이언트 쪽 개체입니다. `sqlServer().define()...create()`를 통해 만들어집니다. 
| [DatabaseEditions](/java/api/com.microsoft.azure.management.sql.databaseeditions) | 탄력적 풀 외부에서 데이터베이스를 만들거나 이동할 때 데이터베이스 리소스를 설정하는 데 사용되는 정적 상수 필드입니다.  
| [SqlElasticPool](/java/api/com.microsoft.azure.management.sql.sqlelasticpool) | Azure에서 SqlServer를 만든 흐름 체인의 `withNewElasticPool()` 섹션에서 만들어집니다. 탄력적 풀 및 탄력적 풀 자체에서 실행 중인 데이터베이스에 대한 리소스 제한을 설정하는 메서드를 제공합니다. 
| [ElasticPoolEditions](/java/api/com.microsoft.azure.management.sql.elasticpooleditions) | 탄력적 풀에 사용할 수 있는 리소스를 정의하는 상수 필드의 클래스입니다. 계층에 대한 자세한 내용은 [SQL 데이터베이스 탄력적 풀 설명서](/azure/sql-database/sql-database-elastic-pool)를 참조하세요. 
| [ElasticPoolDatabaseActivity](/java/api/com.microsoft.azure.management.sql.elasticpooldatabaseactivity) | `SqlElasticPool.listDatabaseActivities()`에서 검색됩니다. 이 형식의 각 개체는 탄력적 풀의 데이터베이스에서 수행된 작업을 나타냅니다.
| [ElasticPoolActivity](/java/api/com.microsoft.azure.management.sql.elasticpoolactivity) | `SqlElasticPool.listActivities()`의 목록에서 검색됩니다. 목록의 각 개체는 탄력적 풀(탄력적 풀의 데이터베이스가 아님)에서 수행된 작업을 나타냅니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next-steps](includes/java-next-steps.md)]