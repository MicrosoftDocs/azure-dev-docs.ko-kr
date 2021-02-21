---
title: Azure MariaDB에서 JavaScript 사용
description: MariaDB 데이터베이스를 만들거나 Azure로 이동하려면 MariaDB 리소스가 필요합니다.
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: 320397584f78809e39e7eeaf7c1c6805755a82de
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100004742"
---
# <a name="develop-a-javascript-application-with-mariadb-on-azure"></a>Azure에서 MariaDB를 사용하여 JavaScript 애플리케이션 개발

MariaDB 데이터베이스를 만들거나 사용하거나 Azure로 이동하려면 **Azure Database for MariaDB** 리소스가 필요합니다. 리소스를 만들고 데이터베이스를 사용하는 방법을 알아봅니다.

## <a name="create-an-azure-database-for-mariadb-resource"></a>Azure Database for MariaDB 리소스 만들기 

다음 도구로 리소스를 만들 수 있습니다.

* Azure CLI
* [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.MariaDBServer)
* [@azure/arm-mariadb](https://www.npmjs.com/package/@azure/arm-mariadb)

[!INCLUDE [Azure CLI commands](../../includes/azure-cli-mariadb.md)]

## <a name="view-and-use-your-mariadb-on-azure"></a>Azure에서 MariaDB를 살펴보고 사용
JavaScript로 MariaDB 데이터베이스를 개발할 때 다음 도구 중 하나를 사용합니다.

* [Azure Cloud Shell](https://shell.azure.com/)의 _mysql_ CLI
* [MySQL Workbench](https://www.mysql.com/products/workbench/)
* Visual Studio Code [확장](https://marketplace.visualstudio.com/items?itemName=mtxr.sqltools-driver-mysql)

## <a name="use-sdk-packages-to-develop-your-mariadb-on-azure"></a>SDK 패키지를 사용하여 Azure에서 MariaDB 개발

Azure MariaDB는 이미 제공되고 있는 다음과 같은 npm 패키지를 사용합니다.

* [mariadb](https://www.npmjs.com/package/mariadb)

## <a name="use-mariadb-sdk-to-connect-to-mariadb-on-azure"></a>MariaDB SDK를 사용하여 Azure에서 MariaDB에 연결

Azure에서 JavaScript로 MariaDB에 연결하고 사용하려면 다음 절차를 수행합니다.

1. Node.js 및 npm이 설치되어 있는지 확인합니다.
1. 다음과 같이 새 폴더에 Node.js 프로젝트를 만듭니다.

    ```bash
    mkdir mariaDbDemo && \
        cd mariaDbDemo && \
        npm init -y && \
        npm install mariadb && \
        touch index.js && \
        code .
    ```

    이 명령은 다음 작업을 수행합니다.
    * `mariaDbDemo`라는 프로젝트 폴더 만들기
    * Bash 터미널을 이 폴더로 변경
    * 프로젝트를 초기화하여 `package.json` 파일 만들기
    * mariadb npm 패키지 설치
    * `index.js` 스크립트 파일 만들기
    * Visual Studio Code에서 프로젝트 열기

1. 다음 JavaScript 코드를 `index.js`에 복사합니다.

    ```nodejs
    // To install npm package,
    // run following command at terminal
    // npm install mariadb

    // get mariadb SDK
    const mariadb = require('mariadb');

    // query server and close connection
    const query = async (config) => {
      // creation connection
      const connection = await mariadb.createConnection(config);

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
      host: 'YOUR-RESOURCE_NAME.mariadb.database.azure.com',
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
      { Database: 'information_schema' },
      { Database: 'mysql' },
      { Database: 'performance_schema' },
      { Database: 'quickstartdb' },
      { Database: 'tutorial' },
      meta: [
        ColumnDef {
          _parse: [StringParser],
          collation: [Collation],
          columnLength: 256,
          columnType: 253,
          flags: 1,
          scale: 0,
          type: 'VAR_STRING'
        }
      ]
    ]
    [
      { Tables_in_mysql: '__az_action_history__' },
      { Tables_in_mysql: '__az_changed_static_configs__' },
      { Tables_in_mysql: '__az_replica_information__' },
      { Tables_in_mysql: '__az_replication_current_state__' },
      { Tables_in_mysql: '__az_slave_relay_log_info__' },
      { Tables_in_mysql: '__firewall_rules__' },
      { Tables_in_mysql: '__script_version__' },
      { Tables_in_mysql: 'column_stats' },
      { Tables_in_mysql: 'columns_priv' },
      { Tables_in_mysql: 'db' },
      { Tables_in_mysql: 'event' },
      { Tables_in_mysql: 'func' },
      { Tables_in_mysql: 'general_log' },
      { Tables_in_mysql: 'gtid_slave_pos' },
      { Tables_in_mysql: 'help_category' },
      { Tables_in_mysql: 'help_keyword' },
      { Tables_in_mysql: 'help_relation' },
      { Tables_in_mysql: 'help_topic' },
      { Tables_in_mysql: 'host' },
      { Tables_in_mysql: 'index_stats' },
      { Tables_in_mysql: 'innodb_index_stats' },
      { Tables_in_mysql: 'innodb_table_stats' },
      { Tables_in_mysql: 'plugin' },
      { Tables_in_mysql: 'proc' },
      { Tables_in_mysql: 'procs_priv' },
      { Tables_in_mysql: 'proxies_priv' },
      { Tables_in_mysql: 'roles_mapping' },
      { Tables_in_mysql: 'servers' },
      { Tables_in_mysql: 'slow_log' },
      { Tables_in_mysql: 'table_stats' },
      { Tables_in_mysql: 'tables_priv' },
      { Tables_in_mysql: 'time_zone' },
      { Tables_in_mysql: 'time_zone_leap_second' },
      { Tables_in_mysql: 'time_zone_name' },
      { Tables_in_mysql: 'time_zone_transition' },
      { Tables_in_mysql: 'time_zone_transition_type' },
      { Tables_in_mysql: 'transaction_registry' },
      { Tables_in_mysql: 'user' },
      meta: [
        ColumnDef {
          _parse: [StringParser],
          collation: [Collation],
          columnLength: 292,
          columnType: 253,
          flags: 1,
          scale: 0,
          type: 'VAR_STRING'
        }
      ]
    ]
    [
      { User: 'azureAdmin' },
      { User: 'azure_superuser' },
      { User: 'azure_superuser' },
      { User: 'azure_superuser' },
      meta: [
        ColumnDef {
          _parse: [StringParser],
          collation: [Collation],
          columnLength: 320,
          columnType: 254,
          flags: 16515,
          scale: 0,
          type: 'STRING'
        }
      ]
    ]
    done
    ```

## <a name="next-steps"></a>다음 단계

* [JavaScript 웹앱을 배포하는 방법](../deploy-web-app.md)
* [Azure Database for MariaDB](/azure/mariadb/)
* [Azure Database for MariaDB로 이동하기 위한 마이그레이션 가이드](/azure/mariadb/howto-migrate-dump-restore)