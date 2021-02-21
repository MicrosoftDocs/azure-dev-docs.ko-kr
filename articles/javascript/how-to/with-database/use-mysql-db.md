---
title: Azure MySQL에서 JavaScript 사용
description: MySQL 데이터베이스를 만들거나 Azure로 이동하려면 MySQL 리소스가 필요합니다.
ms.topic: how-to
ms.date: 02/8/2021
ms.custom: devx-track-js
ms.openlocfilehash: caab884c0a943f00ccc1701a7d364bb0825bce78
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100005025"
---
# <a name="develop-a-javascript-application-with-mysql-on-azure"></a>Azure에서 MySQL을 사용하여 JavaScript 애플리케이션 개발

MySQL 데이터베이스를 만들거나 사용하거나 Azure로 이동하려면 **Azure Database for MySQL** 리소스가 필요합니다. 리소스를 만들고 데이터베이스를 사용하는 방법을 알아봅니다.

## <a name="create-an-azure-database-for-mysql-resource"></a>Azure Database for MySQL 리소스 만들기 

다음 도구로 리소스를 만들 수 있습니다.

* Azure CLI
* [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.MySQLServer)
* [@azure/arm-mysql](https://www.npmjs.com/package/@azure/arm-mysql)

[!INCLUDE [Azure CLI commands](../../includes/azure-cli-mysql-db.md)]

## <a name="view-and-use-your-mysql-on-azure"></a>Azure에서 MySQL을 살펴보고 사용
JavaScript로 MySQL 데이터베이스를 개발할 때 다음 도구 중 하나를 사용합니다.

* [Azure Cloud Shell](https://shell.azure.com/)의 _mysql_ CLI
* [MySQL Workbench](https://www.mysql.com/products/workbench/)
* Visual Studio Code [확장](https://marketplace.visualstudio.com/items?itemName=mtxr.sqltools-driver-mysql)

## <a name="use-sdk-packages-to-develop-your-mysql-on-azure"></a>SDK 패키지를 사용하여 Azure에서 MySQL 개발

Azure MySQL은 이미 제공되고 있는 다음과 같은 npm 패키지를 사용합니다.

* [MySQL](https://www.npmjs.com/package/MySQL)
* [Promise-mysql](https://www.npmjs.com/package/promise-mysql)

## <a name="use-promise-mysql-sdk-to-connect-to-mysql-on-azure"></a>Promise-mysql SDK를 사용하여 Azure에서 MySQL에 연결

Azure에서 JavaScript로 MySQL에 연결하고 사용하려면 다음 절차를 수행합니다.

1. Node.js 및 npm이 설치되어 있는지 확인합니다.
1. 다음과 같이 새 폴더에 Node.js 프로젝트를 만듭니다.

    ```bash
    mkdir MySQLDemo && \
        cd MySQLDemo && \
        npm init -y && \
        npm install promise-mysql && \
        touch index.js && \
        code .
    ```

    이 명령은 다음 작업을 수행합니다.
    * `MySQLDemo`라는 프로젝트 폴더 만들기
    * Bash 터미널을 이 폴더로 변경
    * 프로젝트를 초기화하여 `package.json` 파일 만들기
    * async/wait를 사용할 수 있도록 promise-mysql npm 패키지 설치
    * `index.js` 스크립트 파일 만들기
    * Visual Studio Code에서 프로젝트 열기

1. 다음 JavaScript 코드를 `index.js`에 복사합니다.

    ```nodejs
    // To install npm package,
    // run following command at terminal
    // npm install promise-mysql

    // get MySQL SDK
    const MySQL = require('promise-mysql');

    // query server and close connection
    const query = async (config) => {
      // creation connection
      const connection = await MySQL.createConnection(config);

      // show databases on server
      const databases = await connection.query('SHOW DATABASES;');
      console.log(databases);

      // show tables in the mysql database
      const tables = await connection.query('SHOW TABLES FROM mysql;');
      console.log(tables);

      // show users configured for the server
      const rows = await connection.query('select User from mysql.user;');
      console.log(rows);

      // close connection
      connection.end();
    };

    const config = {
      host: 'YOUR-RESOURCE_NAME.mysql.database.azure.com',
      user: 'YOUR-ADMIN-NAME@YOUR-RESOURCE_NAME',
      password: 'YOUR-ADMIN-PASSWORD',
      port: 3306,
    };

    query(config)
      .then(() => console.log('done'))
      .catch((err) => console.log(err));
    ```

1. 호스트, 사용자 및 암호를 연결 구성 개체 `config`의 스크립트에 있는 값으로 바꿉니다. 

1. 스크립트를 실행합니다.

    ```bash
    [
      RowDataPacket { Database: 'information_schema' },
      RowDataPacket { Database: 'defaultdb' },
      RowDataPacket { Database: 'dbproducts' },
      RowDataPacket { Database: 'mysql' },
      RowDataPacket { Database: 'performance_schema' },
      RowDataPacket { Database: 'sys' }
    ]
    [
      RowDataPacket { Tables_in_mysql: '__az_action_history__' },
      RowDataPacket { Tables_in_mysql: '__az_changed_static_configs__' },
      RowDataPacket { Tables_in_mysql: '__az_replica_information__' },
      RowDataPacket { Tables_in_mysql: '__az_replication_current_state__' },
      RowDataPacket { Tables_in_mysql: '__firewall_rules__' },
      RowDataPacket { Tables_in_mysql: '__querystore_event_wait__' },
      RowDataPacket { Tables_in_mysql: '__querystore_query_metrics__' },
      RowDataPacket { Tables_in_mysql: '__querystore_query_text__' },
      RowDataPacket {
        Tables_in_mysql: '__querystore_wait_stats_procedure_errors__'
      },
      RowDataPacket {
        Tables_in_mysql: '__querystore_wait_stats_procedure_status__'
      },
      RowDataPacket { Tables_in_mysql: '__recommendation__' },
      RowDataPacket { Tables_in_mysql: '__recommendation_session__' },
      RowDataPacket { Tables_in_mysql: '__script_version__' },
      RowDataPacket { Tables_in_mysql: 'columns_priv' },
      RowDataPacket { Tables_in_mysql: 'db' },
      RowDataPacket { Tables_in_mysql: 'engine_cost' },
      RowDataPacket { Tables_in_mysql: 'event' },
      RowDataPacket { Tables_in_mysql: 'func' },
      RowDataPacket { Tables_in_mysql: 'general_log' },
      RowDataPacket { Tables_in_mysql: 'gtid_executed' },
      RowDataPacket { Tables_in_mysql: 'help_category' },
      RowDataPacket { Tables_in_mysql: 'help_keyword' },
      RowDataPacket { Tables_in_mysql: 'help_relation' },
      RowDataPacket { Tables_in_mysql: 'help_topic' },
      RowDataPacket { Tables_in_mysql: 'innodb_index_stats' },
      RowDataPacket { Tables_in_mysql: 'innodb_table_stats' },
      RowDataPacket { Tables_in_mysql: 'ndb_binlog_index' },
      RowDataPacket { Tables_in_mysql: 'plugin' },
      RowDataPacket { Tables_in_mysql: 'proc' },
      RowDataPacket { Tables_in_mysql: 'procs_priv' },
      RowDataPacket { Tables_in_mysql: 'proxies_priv' },
      RowDataPacket { Tables_in_mysql: 'query_store' },
      RowDataPacket { Tables_in_mysql: 'query_store_wait_stats' },
      RowDataPacket { Tables_in_mysql: 'recommendation' },
      RowDataPacket { Tables_in_mysql: 'server_cost' },
      RowDataPacket { Tables_in_mysql: 'servers' },
      RowDataPacket { Tables_in_mysql: 'slave_master_info' },
      RowDataPacket { Tables_in_mysql: 'slave_relay_log_info' },
      RowDataPacket { Tables_in_mysql: 'slave_worker_info' },
      RowDataPacket { Tables_in_mysql: 'slow_log' },
      RowDataPacket { Tables_in_mysql: 'tables_priv' },
      RowDataPacket { Tables_in_mysql: 'time_zone' },
      RowDataPacket { Tables_in_mysql: 'time_zone_leap_second' },
      RowDataPacket { Tables_in_mysql: 'time_zone_name' },
      RowDataPacket { Tables_in_mysql: 'time_zone_transition' },
      RowDataPacket { Tables_in_mysql: 'time_zone_transition_type' },
      RowDataPacket { Tables_in_mysql: 'user' }
    ]
    [
      RowDataPacket { User: 'mySqlAdmin' },
      RowDataPacket { User: 'azure_superuser' },
      RowDataPacket { User: 'azure_superuser' },
      RowDataPacket { User: 'mysql.session' },
      RowDataPacket { User: 'mysql.sys' }
    ]
    done
    ```

## <a name="next-steps"></a>다음 단계

* [JavaScript 웹앱을 배포하는 방법](../deploy-web-app.md)
* [Azure Database for MySQL](/azure/mysql/)
* [덤프 및 복원을 사용하여 마이그레이션](/azure/mysql/concepts-migrate-dump-restore)
* [MySQL Workbench를 사용하여 마이그레이션](/azure/mysql/concepts-migrate-import-export)