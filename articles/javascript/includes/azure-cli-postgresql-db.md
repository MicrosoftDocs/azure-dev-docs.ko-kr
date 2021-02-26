---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/04/2021
ms.openlocfilehash: 1fe0e6ef7147040d1a5496f6492cc1e63e2e425e
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100005022"
---
## <a name="create-an-azure-database-for-postgresql-server-resource-with-azure-cli"></a>Azure CLI를 사용하여 Azure Database for PostgreSQL 서버 리소스 만들기

[Azure Cloud Shell](https://shell.azure.com)에서 다음 Azure CLI [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) 명령을 사용하여 데이터베이스에 대한 새 PostgreSQL 서버 리소스를 만듭니다. 

```azurecli
az postgres server create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --location eastus \
    --name YOURRESOURCENAME \
    --admin-user YOUR-ADMIN-USER \
    --ssl-enforcement Disabled \
    --enable-public-network Enabled 
```

이 명령은 완료하는 데 몇 분 정도 걸릴 수 있으며 `eastus` 지역에서 공개적으로 사용 가능한 리소스를 만듭니다. 

응답에는 다음을 포함한 서버의 구성 세부 정보가 포함됩니다. 
* 관리자 계정의 자동 생성된 암호
* 연결 문자열

```text
{
  "additionalProperties": {},
  "administratorLogin": "YOUR-ADMIN-USER",
  "byokEnforcement": "Disabled",
  "connectionString": "postgres://YOUR-ADMIN-USER%40YOURRESOURCENAME:123456789@YOURRESOURCENAME.postgres.database.azure.com/postgres?sslmode=require",
  "earliestRestoreDate": "2021-02-08T21:56:20.130000+00:00",
  "fullyQualifiedDomainName": "YOURRESOURCENAME.postgres.database.azure.com",
  "id": "/subscriptions/.../resourceGroups/YOUR-RESOURCE-GROUP/providers/Microsoft.DBforPostgreSQL/servers/YOURRESOURCENAME",
  "identity": null,
  "infrastructureEncryption": "Disabled",
  "location": "eastus",
  "masterServerId": "",
  "minimalTlsVersion": "TLSEnforcementDisabled",
  "name": "YOURRESOURCENAME",
  "password": "123456789",
  "privateEndpointConnections": [],
  "publicNetworkAccess": "Enabled",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "YOUR-RESOURCE-GROUP",
  "sku": {
    "additionalProperties": {},
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Disabled",
  "storageProfile": {
    "additionalProperties": {},
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageAutogrow": "Enabled",
    "storageMb": 51200
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

서버에 연결하기 전에 클라이언트 IP 주소를 허용하도록 방화벽 규칙을 구성해야 합니다. 

## <a name="add-a-firewall-rule-for-your-client-ip-address-to-postgresql-resource"></a>PostgreSQL 리소스에 클라이언트 IP 주소에 대한 방화벽 규칙 추가

기본적으로 방화벽 규칙은 구성되지 않습니다. JavaScript를 사용하여 클라이언트가 서버에 성공적으로 연결되도록 클라이언트 IP 주소를 추가해야 합니다.

[Azure Cloud Shell](https://shell.azure.com)에서 다음 Azure CLI [az postgres server firewall-rule create](/cli/azure/postgres/server#az_postgres_server_firewall_rule_create) 명령을 사용하여 데이터베이스에 대한 새 방화벽 규칙을 만듭니다. 


```azurecli
az postgres server firewall-rule create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --server YOURRESOURCENAME \
    --name AllowMyIP \
    --start-ip-address 123.123.123.123 \
    --end-ip-address 123.123.123.123
```

클라이언트 IP 주소를 모르는 경우 다음 방법 중 하나를 사용합니다.
* Azure Portal을 사용하여 검색된 클라이언트 IP 추가를 포함하는 방화벽 규칙을 보고 변경합니다.
* Node.js 코드를 실행하면 방화벽 규칙 위반에 대한 오류에 클라이언트 IP 주소가 포함됩니다.

인터넷 주소 범위 전체(0.0.0.0-255.255.255.255)를 방화벽 규칙으로 추가할 수 있지만, 이렇게 하면 서버가 공격에 노출됩니다. 

## <a name="create-a-postgresql-database-on-the-server-with-azure-cli"></a>Azure CLI를 사용하여 서버에서 PostgreSQL 데이터베이스 만들기

[Azure Cloud Shell](https://shell.azure.com)에서 다음 Azure CLI [az postgres db create](/cli/azure/postgres/db#az_postgres_db_create) 명령을 사용하여 서버에 새 PostgreSQL 데이터베이스를 만듭니다. 

```azurecli
az postgres db create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --server-name YOURRESOURCENAME \
    --name YOURDATABASENAME
```

## <a name="get-the-postgresql-connection-string-with-azure-cli"></a>Azure CLI를 사용하여 PostgreSQL 연결 문자열 가져오기

[az postgres server show-connection-string](/cli/azure/postgres/server#az_postgres_server_show_connection_string) 명령을 사용하여 이 인스턴스에 대한 PostgreSQL 연결 문자열을 검색합니다.

```azurecli
az postgres server show-connection-string \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --server-name YOURRESOURCENAME
```

이렇게 하면 널리 사용되는 언어에 대한 연결 문자열이 JSON 개체로 반환됩니다. 연결 문자열을 사용하기 전에 `{database}`, `{username}` 및 `{password}`를 고유한 값으로 교체해야 합니다. `YOURRESOURCENAME`을 리소스 이름으로 바꿉니다.

```json
{
  "connectionStrings": {
    "C++ (libpq)": "host=YOURRESOURCENAME.postgres.database.azure.com port=5432 dbname={database} user={username}YOURRESOURCENAME password={password} sslmode=require",
    "ado.net": "Server=YOURRESOURCENAME.postgres.database.azure.com;Database={database};Port=5432;User Id={username}@YOURRESOURCENAME;Password={password};",
    "jdbc": "jdbc:postgresql://YOURRESOURCENAME.postgres.database.azure.com:5432/{database}?user={username}@YOURRESOURCENAME&password={password}",
    "node.js": "var client = new pg.Client('postgres://{username}@YOURRESOURCENAME:{password}@YOURRESOURCENAME.postgres.database.azure.com:5432/{database}');",
    "php": "host=YOURRESOURCENAME.postgres.database.azure.com port=5432 dbname={database} user={username}@YOURRESOURCENAME password={password}",
    "psql_cmd": "postgresql://{username}@YOURRESOURCENAME:{password}@YOURRESOURCENAME.postgres.database.azure.com/{database}?sslmode=require",
    "python": "cnx = psycopg2.connect(database='{database}', user='{username}@YOURRESOURCENAME', host='YOURRESOURCENAME.postgres.database.azure.com', password='{password}', port='5432')",
    "ruby": "cnx = PG::Connection.new(:host => 'YOURRESOURCENAME.postgres.database.azure.com', :user => '{username}@YOURRESOURCENAME', :dbname => '{database}', :port => '5432', :password => '{password}')"
  }
}
``` 