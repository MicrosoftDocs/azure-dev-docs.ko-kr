---
title: Azure에서 Node.js 앱을 사용하는 데이터베이스
description: Azure는 웹 및 기타 Node.js 앱과 함께 사용할 수 있는 다양한 데이터베이스를 제공합니다.
ms.topic: how-to
ms.date: 12/08/2020
ms.custom: devx-track-js
ms.openlocfilehash: 2aae93a85ca505967f0c999be4addc78ac31ad02
ms.sourcegitcommit: 1901759f41adfac3c3f2ff135bcf72206543b639
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96933286"
---
# <a name="integrate-databases-in-nodejs-apps"></a>Node.js 앱에서 데이터베이스 통합

Azure 데이터베이스는 데이터베이스에 연결할 네이티브 API 또는 Azure SDK와 함께 잘 관리되는 클라우드 데이터 솔루션을 제공합니다. 

## <a name="database-and-data-storage-solutions-on-azure"></a>Azure의 데이터베이스 및 데이터 스토리지 솔루션

다음 표는 Node.js를 통해 Azure 데이터베이스에 연결하고 사용하기 위한 다양한 문서로 연결됩니다. 다양한 데이터베이스 옵션의 단계별 목록은 [데이터베이스 - 완전 관리형 인텔리전트 데이터베이스 서비스](https://azure.microsoft.com/product-categories/databases/)를 참조하세요.

| 서비스 | 빠른 시작 | npm 패키지 |
| --- | --- | --- |
| Cosmos DB의 **SQL Server**| [SQL Server를 사용하여 Node.js Azure Cosmos DB 웹앱 만들기](/azure/cosmos-db/create-sql-api-nodejs) | [@azure/cosmos](https://www.npmjs.com/package/@azure/cosmos) |
| Cosmos DB의 **MongoDB**| [Node.js 및 MongoDB 웹앱 만들기](/azure/app-service-web/app-service-web-tutorial-nodejs-mongodb-app) | 모든 MongoDB 클라이언트 |
| Cosmos DB의 **Cassandra**|[Node.js SDK 및 Azure Cosmos DB를 사용하여 Cassandra 앱 빌드](/azure/cosmos-db/create-cassandra-nodejs)|[npm cassandra 드라이버](https://www.npmjs.com/package/cassandra-driver)|
| Cosmos DB의 **Gremlin**|[Azure Cosmos DB Gremlin API 계정을 사용하여 Node.js 애플리케이션 빌드](/azure/cosmos-db/create-graph-nodejs)|[npm gremlin](https://www.npmjs.com/package/gremlin)|
| Cosmos DB의 **Redis Cache**| [Redis Cache 만들기 및 사용](/azure/redis-cache/cache-nodejs-get-started) | [npm redis](https://www.npmjs.com/package/redis)|
| **Azure SQL 데이터베이스** | [Node.js를 사용하여 Azure SQL 데이터베이스 쿼리](/azure/sql-database/sql-database-connect-query-nodejs) |[npm tedious](https://www.npmjs.com/package/tedious) |
| **MySQL** | [Node.js를 사용하여 데이터 연결 및 쿼리](/azure/mysql/connect-nodejs) | [npm mysql](https://www.npmjs.com/package/mysql)|
| **PostgreSQL** | [Node.js를 사용하여 데이터 연결 및 쿼리](/azure/postgresql/connect-nodejs) |[npm pg](https://www.npmjs.com/package/pg) |

## <a name="cosmos-db-connection-strings-with-azure-cli"></a>Azure CLI에서 Cosmos DB 연결 문자열 사용

다음과 같이 [az cosmosdb keys list](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-list-connection-strings) 명령을 사용합니다.

```azurecli-interactive
az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

## <a name="sql-connection-strings-with-azure-cli"></a>Azure CLI에서 SQL 연결 문자열 사용

다음과 같이 [az sql db show-connection-string](/cli/azure/sql/db?view=azure-cli-latest#az_sql_db_show_connection_string) 명령을 사용합니다.

```azurecli-interactive
az sql db show-connection-string \
    --client {ado.net, jdbc, odbc, php, php_pdo, sqlcmd} \
     [--auth-type {ADIntegrated, ADPassword, SqlPassword}] \
     [--ids] \
     [--name] \
     [--server] \
     [--subscription]
```

## <a name="mysql-username-and-password-with-azure-cli"></a>Azure CLI에서 MySQL 사용자 이름 및 암호 사용

두 항목은 [리소스 생성 시간](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create)에 설정됩니다. 

## <a name="postgresql-username-and-password-with-azure-cli"></a>Azure CLI에서 PostgreSQL 사용자 이름 및 암호 사용

두 항목은 [리소스 생성 시간](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create)에 설정됩니다. 

## <a name="azure-storage-solutions-for-files-and-data"></a>파일 및 데이터를 위한 Azure Storage 솔루션

파일(blob), 테이블 및 큐(메시지) 스토리지에 Azure Storage를 사용할 수도 있습니다.

| 서비스 | 빠른 시작 |권장 SDK |
| --- | --- |--- |
| **Blob** | [JavaScript용 Azure Storage v10 SDK를 사용하여 blob 업로드, 다운로드, 나열 및 삭제](/azure/storage/blobs/storage-quickstart-blobs-nodejs-v10) |[@azure/storage-blob](https://www.npmjs.com/package/@azure/storage-blob)|
| **큐** | [Node.js에서 Queue 스토리지를 사용하는 방법](/azure/storage/queues/storage-nodejs-how-to-use-queues) |[npm @azure/storage-queue](https://www.npmjs.com/package/@azure/storage-queue)|
| **테이블** | [Node.js에서 Table Storage를 사용하는 방법](/azure/cosmos-db/table-storage-how-to-use-nodejs) |[npm azure-storage](https://www.npmjs.com/package/azure-storage)|

## <a name="next-steps"></a>다음 단계

* Azure 함수를 사용하여 [서버리스 코드 작성](develop-serverless-apps.md)