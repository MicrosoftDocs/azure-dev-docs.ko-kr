---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/04/2021
ms.openlocfilehash: 0edd926e2acf016eb6389fe58c7f1d5e78628dbf
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100004729"
---
## <a name="create-an-azure-database-for-mysql-resource-with-azure-cli"></a>Azure CLI를 사용하여 Azure Database for MySQL 리소스 만들기

[Azure Cloud Shell](https://shell.azure.com)에서 다음 Azure CLI [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) 명령을 사용하여 데이터베이스에 대한 새 MySQL 리소스를 만듭니다. 

```azurecli
az mysql server create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOURRESOURCENAME \
    --location eastus \
    --admin-user YOUR-ADMIN-NAME \
    --ssl-enforcement Disabled \
    --enable-public-network Enabled 
```

이 명령은 완료하는 데 몇 분 정도 걸릴 수 있으며 `eastus` 지역에서 공개적으로 사용 가능한 리소스를 만듭니다. 

응답에는 다음을 포함한 서버의 구성 세부 정보가 포함됩니다. 
* 관리자 계정의 자동 생성된 암호
* mysql CLI를 사용하여 서버에 연결하는 명령

```text
{
  "additionalProperties": {},
  "administratorLogin": "mySqlAdmin",
  "byokEnforcement": "Disabled",
  "connectionString": "mysql defaultdb --host mysqldb-2.mysql.database.azure.com --user mySqlAdmin@mysqldb-2 --password=123456789",
  "databaseName": "defaultdb",
  "earliestRestoreDate": "2021-02-08T16:48:01.673000+00:00",
  "fullyQualifiedDomainName": "mysqldb-2.mysql.database.azure.com",
  "id": "/subscriptions/.../resourceGroups/my-resource-group/providers/Microsoft.DBforMySQL/servers/mysqldb-2",
  "identity": null,
  "infrastructureEncryption": "Disabled",
  "location": "westus",
  "masterServerId": "",
  "minimalTlsVersion": "TLSEnforcementDisabled",
  "name": "mysqldb-2",
  "password": "123456789",
  "privateEndpointConnections": [],
  "publicNetworkAccess": "Enabled",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "my-resource-group",
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
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

프로그래밍 방식으로 서버에 연결하기 전에 클라이언트 IP 주소를 허용하도록 방화벽 규칙을 구성해야 합니다. 

## <a name="add-firewall-rule-for-your-client-ip-address-to-mysql-resource"></a>MySQL 리소스에 클라이언트 IP 주소에 대한 방화벽 규칙 추가

기본적으로 방화벽 규칙은 구성되지 않습니다. JavaScript를 사용하여 클라이언트가 서버에 성공적으로 연결되도록 클라이언트 IP 주소를 추가해야 합니다.

```azurecli
az mysql server firewall-rule create \
--resource-group YOUR-RESOURCE-GROUP \
--server YOURRESOURCENAME \
--name AllowMyIP --start-ip-address 123.123.123.123 \
--end-ip-address 123.123.123.123
```

클라이언트 IP 주소를 모르는 경우 다음 방법 중 하나를 사용합니다.
* Azure Portal을 사용하여 검색된 클라이언트 IP 추가를 포함하는 방화벽 규칙을 보고 변경합니다.
* Node.js 코드를 실행하면 방화벽 규칙 위반에 대한 오류에 클라이언트 IP 주소가 포함됩니다.

인터넷 주소 범위 전체(0.0.0.0-255.255.255.255)를 방화벽 규칙으로 추가할 수 있지만, 이렇게 하면 서버가 공격에 노출됩니다. 

## <a name="create-a-database-on-the-server-with-azure-cli"></a>Azure CLI를 사용하여 서버에서 데이터베이스 만들기

[Azure Cloud Shell](https://shell.azure.com)에서 다음 Azure CLI [az mysql db create](/cli/azure/mysql/db#az_mysql_db_create) 명령을 사용하여 서버에 새 MySQL 데이터베이스를 만듭니다. 

```azurecli
az mysql db create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --server-name YOURRESOURCENAME \
    --name YOURDATABASENAME
```


## <a name="get-the-mysql-connection-string-with-azure-cli"></a>Azure CLI를 사용하여 MySql 연결 문자열 가져오기

[az mysql server show-connection-string](/cli/azure/mysql/server#az_mysql_server_show_connection_string) 명령을 사용하여 이 인스턴스에 대한 MySql 연결 문자열을 검색합니다.

```azurecli
az mysql server show-connection-string \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --server-name YOURRESOURCENAME
```

이렇게 하면 널리 사용되는 언어에 대한 연결 문자열이 JSON 개체로 반환됩니다. 연결 문자열을 사용하기 전에 `{database}`, `{username}` 및 `{password}`를 고유한 값으로 교체해야 합니다. 

```json
{
  "connectionStrings": {
    "ado.net": "Server=YOURRESOURCENAME.mysql.database.azure.com; Port=3306; Database={database}; Uid={username}@YOURRESOURCENAME; Pwd={password}",
    "jdbc": "jdbc:mysql://YOURRESOURCENAME.mysql.database.azure.com:3306/{database}?user={username}@YOURRESOURCENAME&password={password}",
    "mysql_cmd": "mysql {database} --host YOURRESOURCENAME.mysql.database.azure.com --user {username}@YOURRESOURCENAME --password={password}",
    "node.js": "var conn = mysql.createConnection({host: 'YOURRESOURCENAME.mysql.database.azure.com', user: '{username}@YOURRESOURCENAME',password: {password}, database: {database}, port: 3306});",
    "php": "host=YOURRESOURCENAME.mysql.database.azure.com port=3306 dbname={database} user={username}@YOURRESOURCENAME password={password}",
    "python": "cnx = mysql.connector.connect(user='{username}@YOURRESOURCENAME', password='{password}', host='YOURRESOURCENAME.mysql.database.azure.com', port=3306, database='{database}')",
    "ruby": "client = Mysql2::Client.new(username: '{username}@YOURRESOURCENAME', password: '{password}', database: '{database}', host: 'YOURRESOURCENAME.mysql.database.azure.com', port: 3306)"
  }
}
``` 