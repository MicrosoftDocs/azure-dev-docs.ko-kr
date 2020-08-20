---
title: 자습서 - Ansible을 사용하여 Azure Database for MySQL에서 데이터베이스 구성
description: Ansible을 사용하여 Azure Database for MySQL 서버를 만들고 구성하는 방법 알아보기
keywords: Ansible, Azure, DevOps, Bash, 플레이북, MySQK, 데이터베이스
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 6264ca6158017fc919e64fa2e33852076c523fc6
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88239985"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>자습서: Ansible을 사용하여 Azure Database for MySQL에서 데이터베이스 구성

[!INCLUDE [ansible-27-note.md](includes/ansible-27-note.md)]

[Azure Database for MySQL](/azure/mysql/overview)은 MySQL Community Edition을 기반으로 하는 관계형 데이터베이스 서비스입니다. Azure Database for MySQL을 사용하면 웹앱에서 MySQL 데이터베이스를 관리할 수 있습니다.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * MySQL 서버 만들기
> * MySQL 데이터베이스 만들기
> * 외부 앱에서 서버에 연결할 수 있도록 방화벽 규칙 구성
> * Azure Cloud Shell에서 MySql 서버에 연결
> * 사용 가능한 MySQL 서버 쿼리
> * 연결된 서버에 있는 모든 데이터베이스 나열

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

이 섹션의 플레이북 코드는 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.  

다음 플레이북을 `rg.yml`로 저장합니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

플레이북을 실행하기 전에 다음 정보를 참조하세요.

* `myResourceGroup`이라는 리소스 그룹이 생성됩니다.
* 리소스 그룹이 `eastus` 위치에 생성됩니다.

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>MySQL 서버 및 데이터베이스 만들기

이 섹션의 플레이북 코드에는 MySQL 서버 및 Azure Database for MySQL 인스턴스를 만듭니다. 새 MySQL 서버는 vCore가 1개 있는 Gen 5 기본 목적 서버로, 이름은 `mysqlserveransible`입니다. 데이터베이스 인스턴스의 이름은 `mysqldbansible`입니다.

가격 책정 계층에 대한 자세한 내용은 [MySQL용 Azure Database 가격 책정 계층](/azure/mysql/concepts-pricing-tiers)을 참조하세요. 

다음 플레이북을 `mysql_create.yml`로 저장합니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus
    mysqlserver_name: mysqlserveransible
    mysqldb_name: mysqldbansible
    admin_username: mysqladmin
    admin_password: <server_admin_password> 
  tasks:
    - name: Create MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        sku:
          name: B_Gen5_1
          tier: Basic
        location: "{{ location }}"
        version: 5.6
        enforce_ssl: True
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        storage_mb: 51200
    - name: Create instance of MySQL Database
      azure_rm_mysqldatabase:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
        name: "{{ mysqldb_name }}"
```

플레이북을 실행하기 전에 다음 정보를 참조하세요.

* `vars` 섹션에서 `mysqlserver_name` 값은 고유해야 합니다.
* `vars` 섹션에서 `<server_admin_password>`를 암호로 바꿉니다.

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>방화벽 규칙 구성

서버 수준 방화벽 규칙을 사용하면 외부 앱에서 Azure MySQL 서비스 방화벽을 통해 서버에 연결할 수 있습니다. 외부 앱의 예로 `mysql` 명령줄 도구 및 MySQL Workbench가 있습니다.

이 섹션의 플레이북 코드는 모든 외부 IP 주소에서의 연결을 허용하는 `extenalaccess`라는 방화벽 규칙을 만듭니다. 

다음 플레이북을 `mysql_firewall.yml`로 저장합니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
  - name: Open firewall to access MySQL Server from outside
    azure_rm_resource:
      api_version: '2017-12-01'
      resource_group: "{{ resource_group }}"
      provider: dbformysql
      resource_type: servers
      resource_name: "{{ mysqlserver_name }}"
      subresource:
        - type: firewallrules
          name: externalaccess
      body:
        properties:
          startIpAddress: "0.0.0.0"
          endIpAddress: "255.255.255.255"
```

플레이북을 실행하기 전에 다음 정보를 참조하세요.

* vars 섹션에서 `startIpAddress` 및 `endIpAddress`를 바꿉니다. 연결할 범위에 해당하는 IP 주소 범위를 사용합니다.
* Azure Database for MySQL에 대한 연결은 포트 3306을 통해 통신합니다. 회사 네트워크 내에서 연결하려고 하면 3306 포트를 통한 아웃바운드 트래픽이 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 3306 포트를 열지 않으면 서버에 연결할 수 없습니다.
* 이 플레이북은 REST API를 직접 사용할 수 있는 `azure_rm_resource` 모듈을 사용합니다.

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>서버에 연결

이 섹션에서는 Azure Cloud Shell을 사용하여 이전에 만든 서버에 연결합니다.

1. 아래를 선택하여 shell.azure.com을 엽니다.

   [![Embed 시작](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell 시작")](https://shell.azure.com)

1. 다음 코드를 입력합니다.

    ```sql
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. 프롬프트에서 다음 명령을 입력하여 서버 상태를 쿼리합니다.

    ```sql
    mysql> status
    ```
    
    모든 작업을 적절히 수행하면 다음 결과와 유사한 출력이 표시됩니다.
    
    ```output
    demo@Azure:~$ mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65233
    Server version: 5.6.39.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper
    
    Connection id:          65233
    Current database:
    Current user:           mysqladmin@13.76.42.93
    SSL:                    Cipher in use is AES256-SHA
    Current pager:          stdout
    Using outfile:          ''
    Using delimiter:        ;
    Server version:         5.6.39.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mysqlserveransible.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 36 min 21 sec
    
    Threads: 5  Questions: 559  Slow queries: 0  Opens: 96  Flush tables: 3  Open tables: 10  Queries per second avg: 0.256
    --------------
    ```
    
## <a name="query-mysql-servers"></a>MySQL 서버 쿼리

이 섹션의 플레이북 코드는 `myResourceGroup`에서 MySQL 서버를 쿼리하고, 찾은 서버의 데이터베이스를 나열합니다.

다음 플레이북을 `mysql_query.yml`로 저장합니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Query MySQL Servers in current resource group
      azure_rm_mysqlserver_facts:
        resource_group: "{{ resource_group }}"
      register: mysqlserverfacts

    - name: Dump MySQL Server facts
      debug:
        var: mysqlserverfacts

    - name: Query MySQL Databases
      azure_rm_mysqldatabase_facts:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
      register: mysqldatabasefacts

    - name: Dump MySQL Database Facts
      debug:
        var: mysqldatabasefacts
```

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook mysql_query.yml
```

플레이북을 실행하면 다음 결과와 유사한 출력이 표시됩니다.

```json
"servers": [
    {
        "admin_username": "mysqladmin",
        "enforce_ssl": false,
        "fully_qualified_domain_name": "mysqlserveransible.mysql.database.azure.com",
        "id": "/subscriptions/685ba005-af8d-4b04-8f16-a7bf38b2eb5a/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysqlserveransible",
        "location": "eastus",
        "name": "mysqlserveransible",
        "resource_group": "myResourceGroup",
        "sku": {
            "capacity": 1,
            "family": "Gen5",
            "name": "B_Gen5_1",
            "tier": "Basic"
        },
        "storage_mb": 5120,
        "user_visible_state": "Ready",
        "version": "5.6"
    }
]
```

또한 MySQL 데이터베이스의 경우 다음 출력이 나타납니다.

```json
"databases": [
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "information_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysql",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransibler"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysqldbansible",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "performance_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    }
]
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 이 문서에서 만든 리소스를 삭제합니다. 

다음 플레이북을 `cleanup.yml`로 저장합니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

다음과 같이 `ansible-playbook` 명령을 사용하여 플레이북을 실행합니다.

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure의 Ansible](/azure/ansible/)