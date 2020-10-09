---
title: Azure SDK 라이브러리를 사용하여 Azure MySQL 데이터베이스 프로비저닝
description: Python용 Azure SDK 라이브러리의 관리 라이브러리를 사용하여 Azure MySQL, PostgresSQL 또는 MariaDB 데이터베이스를 프로비저닝합니다.
ms.date: 10/05/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: b1f04dbf2fa12aeab58a05191319a27072db6d28
ms.sourcegitcommit: 29b161c450479e5d264473482d31e8d3bf29c7c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91764685"
---
# <a name="example-use-the-azure-libraries-to-provision-a-database"></a>예: Azure 라이브러리를 사용하여 데이터베이스 프로비저닝

이 예제에서는 Python 스크립트에서 Azure SDK 관리 라이브러리를 사용하여 Azure MySQL 데이터베이스를 프로비저닝하는 방법을 보여줍니다. 또한 Azure SDK의 일부가 아닌 mysql-connector 라이브러리를 사용하여 데이터베이스를 쿼리하는 간단한 스크립트를 제공합니다. ([해당 Azure CLI 명령](#for-reference-equivalent-azure-cli-commands)은 이 문서의 뒷부분에 있습니다. Azure Portal을 사용하려면 [PostgreSQL 서버 만들기](/azure/postgresql/quickstart-create-server-database-portal) 또는 [MariaDB 서버 만들기](/azure/mariadb/quickstart-create-mariadb-server-database-using-azure-portal)를 참조하세요.)

비슷한 코드를 사용하여 PostgreSQL 또는 MariaDB 데이터베이스를 프로비저닝할 수 있습니다.

이 문서의 모든 명령은 언급되지 않는 한 Linux/macOS bash 및 Windows 명령 셸에서 동일하게 작동합니다.

## <a name="1-set-up-your-local-development-environment"></a>1: 로컬 개발 환경 설정

아직 수행하지 않은 경우 [Azure에 대한 로컬 Python 개발 환경 구성](configure-local-development-environment.md)의 모든 지침을 따르세요.

로컬 개발을 위한 서비스 주체를 만들고 이 프로젝트의 가상 환경을 만들어 활성화해야 합니다.

## <a name="2-install-the-needed-azure-library-packages"></a>2: 필요한 Azure 라이브러리 패키지 설치

다음과 같은 콘텐츠가 포함된 *requirements.txt*라는 파일을 만듭니다.

```text
azure-mgmt-resource==10.2.0
azure-mgmt-rdbms
azure-cli-core
mysql
mysql-connector
```

azure-mgmt-resource에 대한 특정 버전 요구 사항은 현재 버전의 azure-mgmt-web과 호환되는 버전을 사용하는 것입니다. 이러한 버전은 azure.core를 기반으로 하지 않으므로 인증에 이전 방법을 사용합니다.

가상 환경이 활성화된 터미널 또는 명령 프롬프트에서 다음 요구 사항을 설치합니다.

```cmd
pip install -r requirements.txt
```

> [!NOTE]
> Windows에서 32비트 Python 라이브러리에 mysql 라이브러리를 설치하려는 경우 *mysql.h* 파일에 대한 오류가 생성됩니다. 이 경우 64비트 버전의 Python을 설치하고 다시 시도합니다.

## <a name="3-write-code-to-provision-the-database"></a>3: 코드를 작성하여 데이터베이스 프로비저닝

다음 코드를 사용하여 *provision_db.py*라는 Python 파일을 만듭니다. 주석은 세부 정보를 설명합니다.

```python
import random, os
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient

# For PostgreSQL, use the PostgreSQLManagement class.
# For MariaDB, use the MariaDBManagement class.
from azure.mgmt.rdbms.mysql import MySQLManagementClient

from azure.mgmt.rdbms.mysql.models import ServerForCreate, ServerPropertiesForDefaultCreate, ServerVersion

# Constants we need in multiple places: the resource group name and the region
# in which we provision resources. You can change these values however you want.
RESOURCE_GROUP_NAME = 'PythonAzureExample-DB-rg'
LOCATION = "westus"

# Step 1: Provision the resource group.
resource_client = get_client_from_cli_profile(ResourceManagementClient)

rg_result = resource_client.resource_groups.create_or_update(RESOURCE_GROUP_NAME,
    { "location": LOCATION })

print(f"Provisioned resource group {rg_result.name}")

# For details on the previous code, see Example: Provision a resource group
# at https://docs.microsoft.com/azure/developer/python/azure-sdk-example-resource-group


# Step 2: Provision the database server

# We use a random number to create a reasonably unique database server name.
# If you've already provisioned a database and need to re-run the script, set
# the DB_SERVER_NAME environment variable to that name instead.
#
# Also set DB_USER_NAME and DB_USER_PASSWORD variables to avoid using the defaults.

db_server_name = os.environ.get("DB_SERVER_NAME", f"PythonAzureExample-MySQL-{random.randint(1,100000):05}")
db_admin_name = os.environ.get("DB_ADMIN_NAME", "azureuser")
db_admin_password = os.environ.get("DB_ADMIN_PASSWORD", "ChangePa$$w0rd24")

# Obtain the management client object
mysql_client = get_client_from_cli_profile(MySQLManagementClient)

# Provision the server and wait for the result
poller = mysql_client.servers.create(RESOURCE_GROUP_NAME,
    db_server_name,
    ServerForCreate(
        location=LOCATION,
        properties=ServerPropertiesForDefaultCreate(
            administrator_login=db_admin_name,
            administrator_login_password=db_admin_password,
            version=ServerVersion.five_full_stop_seven
        )
    )
)

server = poller.result()

print(f"Provisioned MySQL server {server.name}")

# Step 3: Provision a firewall rule to allow the local workstation to connect

RULE_NAME = "allow_ip"
ip_address = os.environ["PUBLIC_IP_ADDRESS"]

# For the above code, create an environment variable named PUBLIC_IP_ADDRESS that
# contains your workstation's public IP address as reported by a site like
# https://whatismyipaddress.com/.

# Provision the rule and wait for completion
poller = mysql_client.firewall_rules.create_or_update(RESOURCE_GROUP_NAME,
    db_server_name, RULE_NAME,
    ip_address,  # Start ip range
    ip_address   # End ip range
)

firewall_rule = poller.result()

print(f"Provisioned firewall rule {firewall_rule.name}")

# Step 4: Provision a database on the server

DB_NAME = "example-db1"

poller = mysql_client.databases.create_or_update(RESOURCE_GROUP_NAME,
    db_server_name, DB_NAME)

db_result = poller.result()

print(f"Provisioned MySQL database {db_result.name} with ID {db_result.id}")
```

이 샘플을 실행하려면 워크스테이션의 IP 주소를 사용하여 `PUBLIC_IP_ADDRESS`라는 환경 변수를 생성해야 합니다.

이 코드는 Azure CLI에서 직접 수행할 수 있는 작업을 보여주므로 CLI 기반 인증 메서드(`get_client_from_cli_profile`)를 사용합니다. 두 경우 모두 인증에 동일한 ID를 사용합니다.

프로덕션 스크립트에서 이러한 코드를 사용하려면 대신 [Azure 서비스로 Python 앱을 인증하는 방법](azure-sdk-authenticate.md)에 설명된 대로 `DefaultAzureCredential`(권장) 또는 서비스 주체 기반 메서드를 사용해야 합니다.

### <a name="reference-links-for-classes-used-in-the-code"></a>코드에 사용된 클래스에 대한 참조 링크

- [ResourceManagementClient(azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient)
- [MySQLManagementClient(azure.mgmt.rdbms.mysql)](/python/api/azure-mgmt-rdbms/azure.mgmt.rdbms.mysql.mysqlmanagementclient)
- [ServerForCreate(azure.mgmt.rdbms.mysql.models)](/python/api/azure-mgmt-rdbms/azure.mgmt.rdbms.mysql.models.serverforcreate)
- [ServerPropertiesForDefaultCreate(azure.mgmt.rdbms.mysql.models)](/python/api/azure-mgmt-rdbms/azure.mgmt.rdbms.mysql.models.serverpropertiesfordefaultcreate)
- [ServerVersion(azure.mgmt.rdbms.mysql.models)](/python/api/azure-mgmt-rdbms/azure.mgmt.rdbms.mysql.models.serverversion)

또한 다음을 참조하세요.
    - [PostgreSQLManagementClient(azure.mgmt.rdbms.postgresql)](/python/api/azure-mgmt-rdbms/azure.mgmt.rdbms.postgresql.postgresqlmanagementclient)
    - [MariaDBManagementClient(azure.mgmt.rdbms.mariadb)](/python/api/azure-mgmt-rdbms/azure.mgmt.rdbms.mariadb.mariadbmanagementclient)

## <a name="4-run-the-script"></a>4: 스크립트 실행

```cmd
python provision_db.py
```

## <a name="5-insert-a-record-and-query-the-database"></a>5: 레코드 삽입 및 데이터베이스 쿼리

다음 코드를 사용하여 *use_db.py*라는 파일을 만듭니다. 프로비저닝 코드의 값으로 채워야 하는 `DB_SERVER_NAME`, `DB_ADMIN_NAME` 및 `DB_ADMIN_PASSWORD` 환경 변수의 종속성을 기록합니다.

이 코드는 MySQL에서만 작동합니다. PostgreSQL 및 MariaDB에 대해서는 다른 라이브러리를 사용합니다.

```python
import os
import mysql.connector

db_server_name = os.environ["DB_SERVER_NAME"]
db_admin_name = os.environ.get("DB_ADMIN_NAME", "azureuser")
db_admin_password = os.environ.get("DB_ADMIN_PASSWORD", "ChangePa$$w0rd24")

DB_NAME = "example-db"

connection = mysql.connector.connect(user=f"{db_admin_name}@{db_server_name}",
    password=db_admin_password, host=f"{db_server_name}.mysql.database.azure.com", port=3306,
    database=DB_NAME)

cursor = connection.cursor()

table_name = "ExampleTable1"

sql_create = f"CREATE TABLE {table_name} (name varchar(255), code int)"

cursor.execute(sql_create)
print(f"Successfully created table {table_name}")

sql_insert = f"INSERT INTO {table_name} (name, code) VALUES ('Azure', 1)"
insert_data = "('Azure', 1)"

cursor.execute(sql_insert)
print("Successfully inserted data into table")

sql_select_values= f"SELECT * FROM {table_name}"

cursor.execute(sql_select_values)
row = cursor.fetchone()

while row:
    print(str(row[0]) + " " + str(row[1]))
    row = cursor.fetchone()

connection.commit()
```

이 코드는 모두 mysql.connector API를 사용합니다. Azure와 관련된 유일한 부분은 MySQL 서버에 대한 전체 호스트 도메인(mysql.database.azure.com)입니다.

그런 다음, 코드를 실행합니다.

```cmd
python use_db.py
```

## <a name="6-clean-up-resources"></a>6: 리소스 정리

```azurecli
az group delete -n PythonAzureExample-DB-rg  --no-wait
```

이 예제에서 프로비저닝된 리소스를 유지할 필요가 없으며 구독에서 지속적인 요금을 방지하려면 이 명령을 실행합니다.

[!INCLUDE [resource_group_begin_delete](includes/resource-group-begin-delete.md)]

### <a name="for-reference-equivalent-azure-cli-commands"></a>참조용: 해당 Azure CLI 명령

다음 Azure CLI 명령은 Python 스크립트와 동일한 프로비저닝 단계를 완료합니다. PostgreSQL 데이터베이스의 경우 [`az postgres`](/cli/azure/postgres) 명령을, MariaDB의 경우 [`az mariadb`](/cli/azure/mariadb) 명령을 사용합니다.

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az group create -l centralus -n PythonAzureExample-DB-rg

az mysql server create -l westus -g PythonAzureExample-DB-rg -n PythonAzureExample-MySQL-12345 ^
    -u azureuser -p ChangePa$$w0rd24 --sku-name B_Gen5_1

# Change the IP address to the public IP address of your workstation, that is, the address shown
# by a site like https://whatismyipaddress.com/. 

az mysql server firewall-rule create -g PythonAzureExample-DB-rg --server PythonAzureExample-MySQL-12345 ^
    -n allow_ip --start-ip-address 10.11.12.13 --end-ip-address 10.11.12.13

az mysql db create -g PythonAzureExample-DB-rg --server PythonAzureExample-MySQL-12345 -n example-db
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az group create -l centralus -n PythonAzureExample-DB-rg

az mysql server create -l westus -g PythonAzureExample-DB-rg -n PythonAzureExample-MySQL-12345 \
    -u azureuser -p ChangePa$$w0rd24 --sku-name B_Gen5_1

# Change the IP address to the public IP address of your workstation, that is, the address shown
# by a site like https://whatismyipaddress.com/. 

az mysql server firewall-rule create -g PythonAzureExample-DB-rg --server PythonAzureExample-MySQL-12345 \
    -n allow_ip --start-ip-address 10.11.12.13 --end-ip-address 10.11.12.13

az mysql db create -g PythonAzureExample-DB-rg --server PythonAzureExample-MySQL-12345 -n example-db
```

---

## <a name="see-also"></a>참고 항목

- [예: 리소스 그룹 프로비저닝](azure-sdk-example-resource-group.md)
- [예: Azure Storage 프로비저닝](azure-sdk-example-storage.md)
- [예: Azure Storage 사용](azure-sdk-example-storage-use.md)
- [예: 가상 머신 프로비저닝](azure-sdk-example-virtual-machines.md)
- [예: 웹앱 프로비저닝 및 배포](azure-sdk-example-web-app.md)
