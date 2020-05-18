---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 08389c5802d2f6fef5360ac09adb11bb6494beb6
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82988829"
---
### <a name="create-a-docker-image-for-wildfly"></a>WildFly용 Docker 이미지 만들기

Dockerfile을 만들려면 다음과 같은 필수 조건을 갖추어야 합니다.

* 지원되는 JDK
* WildFly 설치
* JVM 런타임 옵션.
* 환경 변수를 전달하는 방법(해당하는 경우).

그런 다음, 다음 섹션에 설명된 단계를 수행할 수 있습니다(해당하는 경우). Dockerfile 및 웹 애플리케이션의 시작 지점으로 [WildFly 컨테이너 빠른 시작 리포지토리](https://github.com/Azure/wildfly-container-quickstart)를 사용할 수 있습니다.

1. [KeyVault FlexVolume 구성](#configure-keyvault-flexvolume)
2. [데이터 원본 설정](#set-up-data-sources)
3. [JNDI 리소스 설정](#set-up-jndi-resources)
4. [WildFly 구성 검토](#review-wildfly-configuration)

#### <a name="configure-keyvault-flexvolume"></a>KeyVault FlexVolume 구성

Azure KeyVault를 만들고 필요한 모든 비밀을 채웁니다. 자세한 내용은 [빠른 시작: Azure CLI를 사용하여 Azure Key Vault에서 비밀을 설정하고 검색합니다](/azure/key-vault/quick-create-cli). 그런 다음, Pod에서 이러한 비밀을 액세스할 수 있도록 [KeyVault FlexVolume](https://github.com/Azure/kubernetes-keyvault-flexvol/blob/master/README.md)을 구성합니다.

WildFly를 부트스트랩하는 데 사용되는 시작 스크립트도 업데이트해야 합니다. 이 스크립트는 서버를 시작하기 전에 WildFly에서 사용하는 키 저장소로 인증서를 가져와야 합니다.

#### <a name="set-up-data-sources"></a>데이터 원본 설정

데이터 원본에 액세스하도록 WildFly를 구성하려면 JDBC 드라이버 JAR을 Docker 이미지에 추가하고 적절한 JBoss CLI 명령을 실행해야 합니다. 이러한 명령은 Docker 이미지를 빌드할 때 데이터 원본을 설정해야 합니다.

다음 단계는 PostgreSQL, MySQL 및 SQL Server에 대한 지침을 제공합니다.

1. [PostgreSQL](https://jdbc.postgresql.org/download.html), [MySQL](https://dev.mysql.com/downloads/connector/j/) 또는 [SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)용 JDBC 드라이버를 다운로드합니다.

    다운로드한 보관 파일의 압축을 풀어 드라이버 .jar 파일을 가져옵니다.

1. `module.xml` 같은 이름으로 파일을 만들고 다음 태그를 추가합니다. `<module name>` 자리 표시자(꺾쇠 괄호 포함)를 PostgreSQL의 경우 `org.postgres`, MySQL의 경우 `com.mysql`, SQL Server의 경우 `com.microsoft`로 바꿉니다. `<JDBC .jar file path>`를 이전 단계의 jar 파일 이름으로 바꿉니다. 예를 들어 Docker 이미지의 파일을 배치할 위치의 전체 경로를 포함합니다(예: `/opt/database`).

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

1. `datasource-commands.cli` 같은 이름으로 파일을 만들고 다음 코드를 추가합니다. `<JDBC .jar file path>`를 이전 단계에서 사용한 값으로 바꿉니다. `<module file path>`를 이전 단계의 파일 이름 및 경로로 바꿉니다(예: `/opt/database/module.xml`).

    **PostgreSQL**

    ```console
    batch
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>
    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)
    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    reload
    run batch
    shutdown
    ```

    **MySQL**

    ```console
    batch
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>
    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)
    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter
    reload
    run batch
    shutdown
    ```

    **SQL Server**

    ```console
    batch
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>
    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)
    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter
    reload
    run batch
    shutdown
    ```

1. 애플리케이션의 JTA 데이터 원본 구성을 업데이트합니다.

    앱의 `src/main/resources/META-INF/persistence.xml` 파일을 열고 `<jta-data-source>` 요소를 찾습니다. 다음과 같이 내용을 바꿉니다.

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

1. Docker 이미지를 빌드할 때 데이터 원본이 생성되도록 `Dockerfile`에 다음을 추가합니다.

    ```console
    RUN /bin/bash -c '<WILDFLY_INSTALL_PATH>/bin/standalone.sh --start-mode admin-only &' && \
    sleep 30 && \
    <WILDFLY_INSTALL_PATH>/bin/jboss-cli.sh -c --file=/opt/database/datasource-commands.cli && \
    sleep 30
    ```

1. `DATABASE_CONNECTION_URL`은 각 데이터베이스 서버마다 다르고 Azure Portal의 값과 다르므로 무엇을 사용할지 확인합니다. 여기에 표시된 URL 형식은 WildFly에서 사용해야 합니다.

    **PostgreSQL**

    ```console
    jdbc:postgresql://<database server name>:5432/<database name>?ssl=true
    ```

    **MySQL**

    ```console
    jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT
    ```

    **SQL Server**

    ```console
    jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

1. 이후 단계에서 배포 YAML을 만들 때는 환경 변수 `DATABASE_CONNECTION_URL`, `DATABASE_SERVER_ADMIN_FULL_NAME` 및 `DATABASE_SERVER_ADMIN_PASSWORD`를 적절한 값과 함께 전달해야 합니다.

WildFly를 사용하여 데이터베이스 연결을 구성하는 방법에 대한 자세한 내용은 [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource) 또는 [SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898)를 참조하세요.

#### <a name="set-up-jndi-resources"></a>JNDI 리소스 설정

WildFly에 구성해야 하는 각 JNDI 리소스를 설정하려면 일반적으로 다음 단계를 사용합니다.

1. 필요한 JAR 파일을 다운로드하여 Docker 이미지에 복사합니다.
2. 이러한 JAR 파일을 참조하는 WildFly *module.xml* 파일을 만듭니다.
3. 특정 JNDI 리소스에 필요한 구성을 만듭니다.
4. Docker 빌드 중에 JNDI 리소스를 등록하는 데 사용할 JBoss CLI 스크립트를 만듭니다.
5. Dockerfile에 모든 항목을 추가합니다.
6. 배포 YAML에서 적절한 환경 변수를 전달합니다.

아래 예제에서는 Azure Service Bus에 대한 JMS 연결을 위한 JNDI 리소스를 만드는 데 필요한 단계를 보여줍니다.

1. [Apache Qpid JMS 공급자](https://qpid.apache.org/components/jms/index.html)를 다운로드합니다.

    다운로드한 보관 파일의 압축을 풀어 드라이버 .jar 파일을 가져옵니다.

1. `module.xml` 같은 이름으로 파일을 만들고 `/opt/servicebus`에 다음 태그를 추가합니다. JAR 파일의 버전 번호가 이전 단계의 JAR 파일 이름과 일치하는지 확인합니다.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="org.jboss.genericjms.provider">
     <resources>
      <resource-root path="proton-j-0.31.0.jar"/>
      <resource-root path="qpid-jms-client-0.40.0.jar"/>
      <resource-root path="slf4j-log4j12-1.7.25.jar"/>
      <resource-root path="slf4j-api-1.7.25.jar"/>
      <resource-root path="log4j-1.2.17.jar"/>
      <resource-root path="netty-buffer-4.1.32.Final.jar" />
      <resource-root path="netty-codec-4.1.32.Final.jar" />
      <resource-root path="netty-codec-http-4.1.32.Final.jar" />
      <resource-root path="netty-common-4.1.32.Final.jar" />
      <resource-root path="netty-handler-4.1.32.Final.jar" />
      <resource-root path="netty-resolver-4.1.32.Final.jar" />
      <resource-root path="netty-transport-4.1.32.Final.jar" />
      <resource-root path="netty-transport-native-epoll-4.1.32.Final-linux-x86_64.jar" />
      <resource-root path="netty-transport-native-kqueue-4.1.32.Final-osx-x86_64.jar" />
      <resource-root path="netty-transport-native-unix-common-4.1.32.Final.jar" />
      <resource-root path="qpid-jms-discovery-0.40.0.jar" />
     </resources>
     <dependencies>
      <module name="javax.api"/>
      <module name="javax.jms.api"/>
     </dependencies>
    </module>
    ```

1. `/opt/servicebus`에 `jndi.properties` 파일을 만듭니다.

    ```console
    connectionfactory.${MDB_CONNECTION_FACTORY}=amqps://${DEFAULT_SBNAMESPACE}.servicebus.windows.net?amqp.idleTimeout=120000&jms.username=${SB_SAS_POLICY}&jms.password=${SB_SAS_KEY}
    queue.${MDB_QUEUE}=${SB_QUEUE}
    topic.${MDB_TOPIC}=${SB_TOPIC}
    ```

1. `servicebus-commands.cli` 같은 이름으로 파일을 만들고 다음 코드를 추가합니다.

    ```console
    batch
    /subsystem=ee:write-attribute(name=annotation-property-replacement,value=true)
    /system-property=property.mymdb.queue:add(value=myqueue)
    /system-property=property.connection.factory:add(value=java:global/remoteJMS/SBF)
    /subsystem=ee:list-add(name=global-modules, value={"name" => "org.jboss.genericjms.provider", "slot" =>"main"}
    /subsystem=naming/binding="java:global/remoteJMS":add(binding-type=external-context,module=org.jboss.genericjms.provider,class=javax.naming.InitialContext,environment=[java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory,org.jboss.as.naming.lookup.by.string=true,java.naming.provider.url=/opt/servicebus/jndi.properties])
    /subsystem=resource-adapters/resource-adapter=generic-ra:add(module=org.jboss.genericjms,transaction-support=XATransaction)
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:add(class-name=org.jboss.resource.adapter.jms.JmsManagedConnectionFactory, jndi-name=java:/jms/${MDB_CONNECTION_FACTORY})
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=ConnectionFactory:add(value=${MDB_CONNECTION_FACTORY})
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=JndiParameters:add(value="java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory;java.naming.provider.url=/opt/servicebus/jndi.properties")
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:write-attribute(name=security-application,value=true)
    /subsystem=ejb3:write-attribute(name=default-resource-adapter-name, value=generic-ra)
    run-batch
    reload
    shutdown
    ```

1. Docker 이미지를 빌드할 때 JNDI 리소스가 생성되도록 `Dockerfile`에 다음을 추가합니다.

    ```console
    RUN /bin/bash -c '<WILDFLY_INSTALL_PATH>/bin/standalone.sh --start-mode admin-only &' && \
    sleep 30 && \
    <WILDFLY_INSTALL_PATH>/bin/jboss-cli.sh -c --file=/opt/servicebus/servicebus-commands.cli && \
    sleep 30
    ```

1. 이후 단계에서 배포 YAML을 만들 때는 환경 변수 `MDB_CONNECTION_FACTORY`, `DEFAULT_SBNAMESPACE`, `SB_SAS_POLICY`, `SB_SAS_KEY`, `MDB_QUEUE`, `SB_QUEUE`, `MDB_TOPIC` 및 `SB_TOPIC`를 적절한 값과 함께 전달해야 합니다.

#### <a name="review-wildfly-configuration"></a>WildFly 구성 검토

이전 지침에서 다루지 않은 추가 마이그레이션 전 단계를 설명하는 [WildFly 관리자 가이드](https://docs.wildfly.org/18/Admin_Guide.html)를 검토합니다.
