---
title: Azure Databases 시작
description: Azure에 호스트되는 데이터베이스를 사용하는 일반적인 작업에 대해 알아봅니다.
ms.topic: how-to
ms.date: 02/09/2021
ms.custom: devx-track-js
ms.openlocfilehash: 5d104dcbfe6aa89db6ee434fafcb2f4796de1b18
ms.sourcegitcommit: b380f6e637b47e6e3822b364136853e1d342d5cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100424202"
---
# <a name="getting-started-with-databases-on-azure"></a>Azure에서 데이터베이스 시작

Azure 클라우드 플랫폼을 사용하면 모든 Azure 데이터베이스를 (서비스로) 사용하거나 사용자 고유의 데이터베이스를 가져올 수 있습니다(BYOD). 서버와 데이터베이스가 설정된 후에는 기존 코드에서 연결 설정만 변경하면 됩니다. 

Azure에서 데이터베이스를 사용하는 경우 JavaScript 앱에서 데이터베이스를 사용하려면 몇 가지 일반적인 작업을 수행해야 합니다. Azure에서 데이터베이스를 가져와서 사용하는 방법에 대해 자세히 알아보세요. 

## <a name="select-a-database-to-use-on-azure"></a>Azure에서 사용할 데이터베이스 선택

Microsoft는 다음 데이터베이스에 대한 관리되는 서비스를 제공합니다.

|데이터베이스|Azure 서비스|자세한 정보|
|--|--|--|
|Cassandra|[Azure Cosmos DB](/azure/cosmos-db/)|[빠른 시작: Node.js SDK 및 Azure Cosmos DB를 사용하여 Cassandra 앱 빌드](/azure/cosmos-db/create-cassandra-nodejs)|
|Gremlin|[Azure Cosmos DB](/azure/cosmos-db/)|[빠른 시작: Azure Cosmos DB Gremlin API 계정을 사용하여 Node.js 애플리케이션 빌드](/azure/cosmos-db/create-graph-nodejs)|
|MongoDB|[Azure Cosmos DB](/azure/cosmos-db/)|[Azure에서 MongoDB를 사용하여 JavaScript 애플리케이션을 개발하는 방법](use-mongodb-as-cosmosdb.md)<br>[빠른 시작: 기존 MongoDB Node.js 웹앱을 Azure Cosmos DB로 마이그레이션](/azure/cosmos-db/create-mongodb-nodejs)|
|MariaDB|[Azure Database for MariaDB](/azure/mariadb/)|[Azure에서 MariaDB를 사용하여 JavaScript 애플리케이션을 개발하는 방법](use-mariadb.md)|
|MySQL|[Azure Database for MySQL](/azure/mysql/)|[빠른 시작: Node.js를 사용하여 MySQL용 Azure Database에서 데이터 연결 및 쿼리](/azure/mysql/connect-nodejs)<br>[Azure에서 MySQL을 사용하여 JavaScript 애플리케이션을 개발하는 방법](use-mysql-db.md)|
|PostgreSQL|[Azure Database for PostgreSQL](/azure/postgresql/)|[빠른 시작: Node.js를 사용하여 Azure Database for PostgreSQL - 단일 서버의 데이터 연결 및 쿼리](/azure/postgresql/connect-nodejs)<br>[Azure에서 PostgreSQL을 사용하여 JavaScript 애플리케이션을 개발하는 방법](use-postgresql-db.md)|
|Redis|[Azure Cache for Redis](/azure/azure-cache-for-redis/)|[빠른 시작: Node.js에서 Azure Cache for Redis 사용](/azure/azure-cache-for-redis/cache-nodejs-get-started)|
|SQL|[Azure Cosmos DB](/azure/cosmos-db/)|[빠른 시작: Node.js를 사용하여 Azure Cosmos DB SQL API 계정에 연결하고 데이터 쿼리](/azure/cosmos-db/create-sql-api-nodejs)|
|테이블|[Azure Cosmos DB](/azure/cosmos-db/)|[Node.js에서 Azure Table Storage 또는 Azure Cosmos DB Table API를 사용하는 방법](/azure/cosmos-db/table-storage-how-to-use-nodejs)|

**무엇을 선택해야 하는지 도움이 필요하세요?** 
* [수행할 작업](https://azure.microsoft.com/product-categories/databases/)을 기준으로 데이터베이스를 선택하세요.
* [Azure Database Migration Service](/azure/dms/)를 사용하여 Azure로 이동하세요. 

**데이터베이스를 찾지 못했나요?**
데이터베이스를 컨테이너 또는 가상 머신으로 가져오세요. 이러한 서비스를 통해 모든 데이터베이스 유형을 가져올 수 있으며 다른 Azure 리소스에 대한 고가용성 및 보안을 확보할 수 있습니다. 그 대신 인프라(컨테이너 또는 VM)를 직접 관리해야 한다는 단점이 있습니다. 이 문서의 나머지 부분은 컨테이너 또는 VM 사용에 도움이 될 것입니다. 하지만 Azure 데이터베이스 서비스를 선택하는 것이 더 좋습니다. 

## <a name="create-the-server"></a>서버 만들기

데이터베이스가 호스트되는 구독에서 특정 Azure 서비스의 리소스를 만드는 방법으로 서버를 만들 수 있습니다. 

리소스를 만들 때 다음과 같은 도구를 사용합니다.

|도구|목적|
|--|--|
|Azure portal|처음 만드는 데이터베이스 또는 아주 가끔 만드는 데이터베이스에는 Azure Portal을 사용합니다.|
|Azure CLI|반복/스크립팅 가능한 시나리오에 사용합니다.|
|해당 서비스와 관련된 Visual Studio Code 확장|개발 IDE를 벗어나지 않기 위해 사용합니다.|
|해당 서비스와 관련된 npm ARM 라이브러리|JavaScript 언어를 벗어나지 않기 위해 사용합니다.| 

서버를 만든 후에는 서비스에 따라 다음 작업을 수행해야 할 수도 있습니다.

* 방화벽 및 SSL 적용과 같은 보안 설정 구성
* 연결 정보 가져오기
* 데이터베이스 만들기

## <a name="configure-security-settings-for-your-database"></a>데이터베이스의 보안 설정 구성

서비스에 대해 구성하는 일반적인 보안 설정은 다음과 같습니다.

* 클라이언트 IP 주소에 대한 방화벽 열기
* SSL 적용 구성
* 공개 요청 수락 또는 모든 요청이 다른 Azure 서비스에서 제공하도록 요구

## <a name="create-a-database-on-the-azure-server"></a>Azure 서버에 데이터베이스 만들기

서버를 만들 때 사용한 것과 동일한 도구를 사용하여 연결 정보를 가져올 수 있습니다. 이 연결 정보를 사용하여 서버에 액세스합니다. 여전히 애플리케이션과 관련된 데이터베이스를 만들어야 합니다. 

서버에 액세스: 

* pgAdmin, SQL Server Management Studio, MySQL Workbench 등 해당 데이터베이스 유형과 관련된 도구를 사용합니다. 
* Microsoft 도구를 계속 사용합니다.
    * [Azure Cloud Shell](https://shell.azure.com)에는 psql 및 mysql과 같은 여러 데이터베이스 CLI가 포함되어 있습니다.
    * Visual Studio Code 확장
    * JavaScript용 npm 패키지
    * Azure portal

## <a name="programmatically-access-the-server-and-database-with-javascript"></a>JavaScript를 사용하여 서버 및 데이터베이스에 프로그래밍 방식으로 액세스

연결 정보가 있으면 산업 표준 npm 패키지 및 JavaScript를 사용하여 서버에 액세스할 수 있습니다. 

데이터베이스를 만들거나 마이그레이션한 후에는 새 서버와 데이터베이스의 연결 정보만 변경하면 됩니다. 

## <a name="configure-an-azure-web-apps-connection-to-database"></a>Azure 웹앱과 데이터베이스의 연결 구성

Azure 웹앱이 데이터베이스에 연결하는 경우 연결 정보에 대한 앱 설정을 변경해야 합니다. 

## <a name="next-steps"></a>다음 단계

* [Azure에서 MongoDB를 사용하여 JavaScript 애플리케이션 개발](use-mongodb-as-cosmosdb.md)