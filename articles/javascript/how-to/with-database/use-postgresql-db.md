---
title: Azure PostgreSQL에서 JavaScript 사용
description: PostgreSQL 데이터베이스를 만들거나 Azure로 이동하려면 PostgreSQL 리소스가 필요합니다.
ms.topic: how-to
ms.date: 02/8/2021
ms.custom: devx-track-js
ms.openlocfilehash: 2633a8b9e8f120a23af7840097c2930127e294ec
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100004985"
---
# <a name="develop-a-javascript-application-with-postgresql-on-azure"></a>Azure에서 PostgreSQL을 사용하여 JavaScript 애플리케이션 개발

PostgreSQL 데이터베이스를 만들거나 사용하거나 Azure로 이동하려면 **Azure Database for PostgreSQL 서버** 리소스가 필요합니다. 리소스를 만들고 데이터베이스를 사용하는 방법을 알아봅니다.

## <a name="create-an-azure-database-for-postgresql-resource"></a>Azure Database for PostgreSQL 리소스 만들기 

다음 도구로 리소스를 만듭니다.

* [Azure CLI](../with-azure-cli/create-postgresql-server-resource.md)
* [Visual Studio Code](../with-visual-studio-code/create-azure-database.md#create-a-postgresql-database)
* [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer)
* [@azure/arm-postgresql](https://www.npmjs.com/package/@azure/arm-postgresql)

## <a name="view-and-use-your-postgresql-server-on-azure"></a>Azure에서 PostgreSQL 서버를 살펴보고 사용
JavaScript로 PostgreSQL 데이터베이스를 개발할 때 다음 도구 중 하나를 사용합니다.

* [Azure Cloud Shell](https://shell.azure.com/) - psql CLI 사용 가능
* [pgAdmin](https://www.pgadmin.org/)
* Visual Studio Code [확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)

## <a name="use-sdk-packages-to-develop-your-postgresql-server-on-azure"></a>SDK 패키지를 사용하여 Azure에서 PostgreSQL 서버 개발

Azure PostgreSQL은 이미 제공되고 있는 다음과 같은 npm 패키지를 사용합니다.

* [pg](https://www.npmjs.com/package/pg)

## <a name="use-pg-sdk-to-connect-to-postgresql-on-azure"></a>pg SDK를 사용하여 Azure에서 PostgreSQL에 연결

Azure에서 JavaScript로 PostgreSQL에 연결하고 사용하려면 다음 절차를 수행합니다.

1. Node.js 및 npm이 설치되어 있는지 확인합니다.
1. 다음과 같이 새 폴더에 Node.js 프로젝트를 만듭니다.

    ```bash
    mkdir DbDemo && \
        cd DbDemo && \
        npm init -y && \
        npm install pg && \
        touch index.js && \
        code .
    ```

    이 명령은 다음 작업을 수행합니다.
    * `DbDemo`라는 프로젝트 폴더 만들기
    * Bash 터미널을 이 폴더로 변경
    * 프로젝트를 초기화하여 `package.json` 파일 만들기
    * async/wait를 사용할 수 있도록 pg npm 패키지 설치
    * `index.js` 스크립트 파일 만들기
    * Visual Studio Code에서 프로젝트 열기

1. 다음 JavaScript 코드를 `index.js`에 복사합니다.

    ```nodejs
    const { Client } = require('pg')
    
    const query = async (connectionString) => {
        
        // create connection
        const connection = new Client(connectionString);
        connection.connect();
        
        // show tables in the postgres database
        const tables = await connection.query('SELECT table_name FROM information_schema.tables where table_type=\'BASE TABLE\';');
        console.log(tables.rows);
    
        // show users configured for the server
        const users = await connection.query('select pg_user.usename FROM pg_catalog.pg_user;');
        console.log(users.rows);
        
        // close connection
        connection.end();
    }
    
    const server='YOURRESOURCENAME';
    const user='YOUR-ADMIN-USER';
    const password='YOUR-PASSWORD';
    const database='postgres';

    const connectionString = `postgres://${user}@${server}:${password}@${server}.postgres.database.azure.com:5432/${database}`;
    
    query(connectionString)
    .then(() => console.log('done'))
    .catch((err) => console.log(err));
    ```

1. `YOUR-ADMIN-USER`, `YOURRESOURCENAME` 및 `YOUR-PASSWORD`를 연결 문자열의 스크립트에 있는 값으로 바꿉니다. 

1. 스크립트를 실행하여 `postgres` 서버에 연결하고 기본 테이블 및 사용자를 확인합니다.

    ```bash
    node index.js
    ```

1. 결과를 확인합니다. 

    ```bash
    [
      { table_name: 'pg_statistic' },
      { table_name: 'pg_type' },
      { table_name: 'pg_authid' },
      { table_name: 'pg_user_mapping' },
      { table_name: 'pg_attribute' },
      { table_name: 'pg_proc' },
      { table_name: 'pg_class' },
      { table_name: 'pg_attrdef' },
      { table_name: 'pg_constraint' },
      { table_name: 'pg_inherits' },
      { table_name: 'pg_index' },
      { table_name: 'pg_operator' },
      { table_name: 'pg_opfamily' },
      { table_name: 'pg_opclass' },
      { table_name: 'pg_am' },
      { table_name: 'pg_amop' },
      { table_name: 'pg_amproc' },
      { table_name: 'pg_language' },
      { table_name: 'pg_largeobject_metadata' },
      { table_name: 'pg_aggregate' },
      { table_name: 'pg_rewrite' },
      { table_name: 'pg_largeobject' },
      { table_name: 'pg_trigger' },
      { table_name: 'pg_event_trigger' },
      { table_name: 'pg_description' },
      { table_name: 'pg_cast' },
      { table_name: 'pg_enum' },
      { table_name: 'pg_namespace' },
      { table_name: 'pg_conversion' },
      { table_name: 'pg_depend' },
      { table_name: 'pg_database' },
      { table_name: 'pg_db_role_setting' },
      { table_name: 'pg_tablespace' },
      { table_name: 'pg_pltemplate' },
      { table_name: 'pg_auth_members' },
      { table_name: 'pg_shdepend' },
      { table_name: 'pg_shdescription' },
      { table_name: 'pg_ts_config' },
      { table_name: 'pg_ts_config_map' },
      { table_name: 'pg_ts_dict' },
      { table_name: 'pg_ts_parser' },
      { table_name: 'pg_ts_template' },
      { table_name: 'pg_extension' },
      { table_name: 'pg_foreign_data_wrapper' },
      { table_name: 'pg_foreign_server' },
      { table_name: 'pg_foreign_table' },
      { table_name: 'pg_policy' },
      { table_name: 'pg_replication_origin' },
      { table_name: 'pg_default_acl' },
      { table_name: 'pg_init_privs' },
      { table_name: 'pg_seclabel' },
      { table_name: 'pg_shseclabel' },
      { table_name: 'pg_collation' },
      { table_name: 'pg_range' },
      { table_name: 'pg_transform' },
      { table_name: 'sql_features' },
      { table_name: 'sql_implementation_info' },
      { table_name: 'sql_languages' },
      { table_name: 'sql_packages' },
      { table_name: 'sql_parts' },
      { table_name: 'sql_sizing' },
      { table_name: 'sql_sizing_profiles' }
    ]
    [ { usename: 'azure_superuser' }, { usename: 'YOUR-ADMIN-USER' } ]
    done
    ```

## <a name="next-steps"></a>다음 단계

* [JavaScript 웹앱을 배포하는 방법](../deploy-web-app.md)
* [Azure Database for PostgreSQL 서버](/azure/postgresql/)