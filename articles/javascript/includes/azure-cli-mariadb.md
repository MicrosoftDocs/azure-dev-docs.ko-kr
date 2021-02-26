---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/08/2021
ms.openlocfilehash: 0c441254e2c053198cc8e41e0d2457adbf3079af
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100004739"
---
## <a name="create-a-mariadb-resource-with-azure-cli"></a>Azure CLI를 사용하여 MariaDB 리소스 만들기

[Azure Cloud Shell](https://shell.azure.com)에서 다음 Azure CLI [az mariadb server create](/cli/azure/mariadb/server#az_mariadb_server_create) 명령을 사용하여 데이터베이스에 대한 새 MariaDB 리소스를 만듭니다. 

```azurecli
az mariadb server create \
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
  "administratorLogin": "mariaAdmin",
  "connectionString": "mysql defaultdb --host mariadb-2.mariadb.database.azure.com --user mariaAdmin@mariadb-2 --password=123456789",
  "databaseName": "defaultdb",
  "earliestRestoreDate": "2021-02-08T19:14:17.317000+00:00",
  "fullyQualifiedDomainName": "mariadb-2.mariadb.database.azure.com",
  "id": "/subscriptions/bb881e62-cf77-4d5d-89fb-29d71e930b66/resourceGroups/my-resource-group/providers/Microsoft.DBforMariaDB/servers/mariadb-2",
  "location": "westus",
  "masterServerId": "",
  "name": "mariadb-2",
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
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

프로그래밍 방식으로 서버에 연결하기 전에 클라이언트 IP 주소를 허용하도록 방화벽 규칙을 구성해야 합니다. 

## <a name="add-firewall-rule-for-your-client-ip-address-to-mariadb-resource"></a>MariaDB 리소스에 클라이언트 IP 주소에 대한 방화벽 규칙 추가

기본적으로 방화벽 규칙은 구성되지 않습니다. JavaScript를 사용하여 클라이언트가 서버에 성공적으로 연결되도록 클라이언트 IP 주소를 추가해야 합니다.

```azurecli
az mariadb server firewall-rule create \
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

[Azure Cloud Shell](https://shell.azure.com)에서 다음 Azure CLI [az mariadb db create](/cli/azure/mariadb/db#az_mariadb_db_create) 명령을 사용하여 서버에 새 MariaDB 데이터베이스를 만듭니다. 

```azurecli
az mariadb db create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --server-name YOURRESOURCENAME \
    --name YOURDATABASENAME
```

## <a name="get-the-mariadb-connection-string-with-azure-cli"></a>Azure CLI를 사용하여 MariaDB 연결 문자열 가져오기

[az mariadb server show-connection-string](/cli/azure/mariadb/server#az_mariadb_server_show_connection_string) 명령을 사용하여 이 인스턴스에 대한 MariaDB 연결 문자열을 검색합니다.

```azurecli
az mariadb server show-connection-string \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --server-name YOURRESOURCENAME \
    --database-name YOURDATABASENAME \
    --admin-user YOUR-ADMIN-NAME \
    --admin-password YOUR-ADMIN-PASSWORD 
```

이렇게 하면 널리 사용되는 언어에 대한 연결 문자열이 JSON 개체로 반환됩니다.

```json
{
  "connectionStrings": {
    "ado.net": "Server=YOURRESOURCENAME.mariadb.database.azure.com; Port=3306; Database=YOURDATABASENAME; Uid=YOUR-ADMIN-NAME@YOURRESOURCENAME; Pwd=YOUR-ADMIN-PASSWORD",
    "jdbc": "jdbc:mariadb://YOURRESOURCENAME.mariadb.database.azure.com:3306/YOURDATABASENAME?user=YOUR-ADMIN-NAME@YOURRESOURCENAME&password=YOUR-ADMIN-PASSWORD",
    "node.js": "var conn = mysql.createConnection({host: 'YOURRESOURCENAME.mariadb.database.azure.com', user: 'YOUR-ADMIN-NAME@YOURRESOURCENAME',password: YOUR-ADMIN-PASSWORD, database: YOURDATABASENAME, port: 3306});",
    "php": "host=YOURRESOURCENAME.mariadb.database.azure.com port=3306 dbname=YOURDATABASENAME user=YOUR-ADMIN-NAME@YOURRESOURCENAME password=YOUR-ADMIN-PASSWORD",
    "python": "cnx = mysql.connector.connect(user='YOUR-ADMIN-NAME@YOURRESOURCENAME', password='YOUR-ADMIN-PASSWORD', host='YOURRESOURCENAME.mariadb.database.azure.com', port=3306, database='YOURDATABASENAME')",
    "ruby": "client = Mysql2::Client.new(username: 'YOUR-ADMIN-NAME@YOURRESOURCENAME', password: 'YOUR-ADMIN-PASSWORD', database: 'YOURDATABASENAME', host: 'YOURRESOURCENAME.mariadb.database.azure.com', port: 3306)"
  }
}
``` 




