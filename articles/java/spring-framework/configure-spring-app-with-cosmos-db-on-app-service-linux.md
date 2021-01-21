---
title: Azure App Service on Linux를 통해 Spring 및 Cosmos DB를 사용하는 방법
description: 이 문서에서는 Azure App Service on Linux에서 Java 웹앱을 빌드, 구성, 배포, 문제 해결 및 확장하는 프로세스를 단계별로 연습합니다.
documentationcenter: java
ms.reviewer: joshuapa
ms.date: 10/06/2020
ms.service: cosmos-db
ms.topic: article
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: d07447301dc721d8fd9179e0830f49143387e819
ms.sourcegitcommit: 0eb25e1fdafcd64118843748dc061f60e7e48332
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98626040"
---
# <a name="how-to-use-spring-and-cosmos-db-with-app-service-on-linux"></a>Azure App Service on Linux를 통해 Spring 및 Cosmos DB를 사용하는 방법

이 문서에서는 Azure App Service on Linux에서 Java 웹앱을 빌드, 구성, 배포, 문제 해결 및 확장하는 프로세스를 단계별로 연습합니다.

즉, 다음 구성 요소의 사용법을 보여줍니다.

- [Azure Cosmos DB SQL API를 사용하는 Spring Boot Starter](configure-spring-boot-starter-java-app-with-cosmos-db.md)
- [Azure Cosmos DB](/azure/cosmos-db/sql-api-introduction)
- [App Service Linux](/azure/app-service/containers/app-service-linux-intro)

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 수행하기 위해 다음 필수 구성 요소가 필요합니다.

- Java 웹앱을 클라우드에 배포하려면 Azure 구독이 필요합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 Azure 계정](https://azure.microsoft.com/pricing/free-trial/)에 가입할 수 있습니다.
- [Azure CLI 2.0](/cli/azure/install-azure-cli)
- [Java 8 JDK](../fundamentals/java-jdk-install.md)
- [Maven 3](http://maven.apache.org/)

## <a name="clone-the-sample-java-web-app-repository"></a>샘플 Java 웹앱 리포지토리 복제

이 연습을 위해 [Spring Boot](https://spring.io/projects/spring-boot), [Spring Data for Cosmos DB](./configure-spring-boot-starter-java-app-with-cosmos-db.md) 및 [Azure Cosmos DB](/azure/cosmos-db/sql-api-introduction)를 사용하여 빌드된 Java 애플리케이션인 Spring Todo 앱을 사용합니다.
1. Spring Todo 앱을 복제하고 **.prep** 폴더의 내용을 복사하여 프로젝트를 초기화합니다.

    Bash의 경우:

    ```bash
    git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
    yes | cp -rf .prep/* .
    ```

    Windows의 경우:

    ```cmd
    git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
    cd e2e-java-experience-in-app-service-linux-part-2
    xcopy .prep /f /s /e /y
    ```

2. 디렉터리를 복제된 리포지토리의 다음 폴더로 변경합니다.

   ```bash
   cd initial\spring-todo-app
   ```

## <a name="create-an-azure-cosmos-db-from-azure-cli"></a>Azure CLI에서 Azure Cosmos DB를 생성합니다.

다음 절차에서는 CLI를 사용하여 Azure Cosmos 데이터베이스를 만듭니다.

1. Azure CLI에 로그인하고 구독 ID를 설정합니다.

    ```azurecli
    az login
    ```

2. 필요한 경우 구독 ID를 설정합니다.

    ```azurecli
    az account set -s <your-subscription-id>
    ```

3. Azure 리소스 그룹을 만들고 리소스 그룹 이름을 나중에 사용할 수 있도록 메모합니다.

    ```azurecli
    az group create -n <your-azure-group-name> \
    -l <your-resource-group-region>
    ```

4. Cosmos DB를 생성하고 GlobalDocumentDB 형식을 지정합니다.
Cosmos DB의 이름은 소문자만 사용해야 합니다. 응답에서 `documentEndpoint` 필드에 유의해야 합니다. 이 필드는 나중에 필요합니다.

    ```azurecli
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
     ```

5. Azure Cosmos DB 키를 가져오고 `primaryMasterKey` 값을 나중에 사용할 수 있도록 기록합니다.

    ```azurecli
    az cosmosdb keys list -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

다음 절차에서는 개발 컴퓨터에서 애플리케이션을 실행합니다.

1. 선택한 콘솔 내에서, 이 문서의 앞부분에서 수집한 Azure 및 Cosmos DB 연결 정보를 사용하여 다음 코드 섹션과 같은 환경 변수를 구성합니다. **WEBAPP_NAME** 변수에 고유한 이름 및 **REGION** 변수에 값을 제공해야 합니다.

Linux(Bash)의 경우:

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

Windows(Command Prompt)의 경우:

```cmd
set COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
set COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
set COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>
set RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
set WEBAPP_NAME=<put-your-Webapp-name-here>
set REGION=<put-your-REGION-here>
```

> [!NOTE]
> 스크립트를 사용하여 해당 변수를 프로비저닝하려면 .prep 디렉터리의 Bash용 템플릿을 복사하여 시작점으로 사용할 수 있습니다.

2. 디렉터리를 다음으로 변경합니다.

    ```bash
    cd initial/spring-todo-app
    ``` 
 
3. 다음 명령을 사용하여 Spring Todo 앱을 로컬로 실행합니다.

    ```bash
    mvn package spring-boot:run
    ```

4. 애플리케이션이 시작되면 `http://localhost:8080/`에서 Spring Todo 앱에 액세스하여 배포의 유효성을 검사할 수 있습니다.

 ![로컬로 실행 중인 Spring 앱][SCDB01]

## <a name="deploy-to-app-service-linux"></a>App Service Linux에 배포

다음 절차는 Azure의 Linux에 애플리케이션을 배포합니다.

1. 이전에 리포지토리의 **initial/spring-todo-app** 디렉터리에 복사한 pom.xml 파일을 엽니다. 아래의 pom.xml 파일과 같이 [Azure App Service용 Maven 플러그인](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)이 포함되었는지 확인합니다. 버전이 **1.6.0** 으로 설정되지 않은 경우 값을 업데이트합니다.

```xml
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-webapp-maven-plugin</artifactId>
            <version>1.6.0</version>
            <configuration>
            <deploymentType>jar</deploymentType>
            
            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            
            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>jre8</linuxRuntime>
            
            <appSettings>
                <property>
                    <name>COSMOSDB_URI</name>
                    <value>${COSMOSDB_URI}</value>
                </property>
                <property>
                    <name>COSMOSDB_KEY</name>
                    <value>${COSMOSDB_KEY}</value>
                </property>
                <property>
                    <name>COSMOSDB_DBNAME</name>
                    <value>${COSMOSDB_DBNAME}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Dserver.port=80</value>
                </property>
            </appSettings>
            
        </configuration>
    </plugin>            
    ...
</plugins>
```

2. App Service Linux의 Java SE에 배포

    ```bash
    mvn azure-webapp:deploy
    ```

```bash
// Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.6.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Authenticate with Azure CLI 2.0
[INFO] Target Web App doesnt exist. Creating a new one...
[INFO] Creating App Service Plan 'ServicePlan11111111-1111-1111'...
[INFO] Successfully created App Service Plan.
[INFO] Successfully created Web App.
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:19 min
[INFO] Finished at: 2018-10-28T15:32:03-07:00
[INFO] Final Memory: 50M/574M
[INFO] ------------------------------------------------------------------------
```

3. App Service Linux의 Java SE에서 실행 중인 웹앱을 찾습니다.

    ```bash
    https://<WEBAPP_NAME>.azurewebsites.net
    ```

![Azure App Service on Linux에서 실행 중인 Spring 앱][SCDB02]

## <a name="troubleshoot-spring-todo-app-on-azure-by-viewing-logs"></a>로그를 확인하여 Azure에서 Spring Todo 앱 문제 해결

다음 절차에서는 Azure에서 로그 파일을 엽니다.

1. Linux의 Azure App Service에서 배포된 Java 웹앱에 대한 로그를 구성합니다.

    ```azurecli
    az webapp log config --name ${WEBAPP_NAME} \
     --resource-group ${RESOURCEGROUP_NAME} \
     --web-server-logging filesystem
    ```
2. 로컬 컴퓨터에서 Java 웹앱 원격 로그 스트림을 엽니다.

    ```azurecli
    az webapp log tail --name ${WEBAPP_NAME} \
     --resource-group ${RESOURCEGROUP_NAME}
     ```

```bash
bash-3.2$ az webapp log tail --name ${WEBAPP_NAME}  --resource-group ${RESOURCEGROUP_NAME}
2018-10-28T22:50:17  Welcome, you are now connected to log-streaming service.
2018-10-28T22:44:56.265890407Z   _____                               
2018-10-28T22:44:56.265930308Z   /  _  \ __________ _________   ____  
2018-10-28T22:44:56.265936008Z  /  /_\  \___   /  |  \_  __ \_/ __ \ 
2018-10-28T22:44:56.265940308Z /    |    \/    /|  |  /|  | \/\  ___/ 
2018-10-28T22:44:56.265944408Z \____|__  /_____ \____/ |__|    \___  >
2018-10-28T22:44:56.265948508Z         \/      \/                  \/ 
2018-10-28T22:44:56.265952508Z A P P   S E R V I C E   O N   L I N U X
2018-10-28T22:44:56.265956408Z Documentation: https://aka.ms/webapp-linux
2018-10-28T22:44:56.266260910Z Setup openrc ...
2018-10-28T22:44:57.396926506Z Service `hwdrivers needs non existent service dev
2018-10-28T22:44:57.397294409Z  * Caching service dependencies ... [ ok ]
2018-10-28T22:44:57.474152273Z Starting ssh service...
...
...
2018-10-28T22:46:13.432160734Z [INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
2018-10-28T22:46:13.744859424Z [INFO] TomcatWebServer - Tomcat started on port(s): 80 (http) with context path ''
2018-10-28T22:46:13.783230205Z [INFO] TodoApplication - Started TodoApplication in 57.209 seconds (JVM running for 70.815)
2018-10-28T22:46:14.887366993Z 2018-10-28 22:46:14.887  INFO 198 --- [p-nio-80-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring FrameworkServlet 'dispatcherServlet'
2018-10-28T22:46:14.887637695Z [INFO] DispatcherServlet - FrameworkServlet 'dispatcherServlet': initialization started
2018-10-28T22:46:14.998479907Z [INFO] DispatcherServlet - FrameworkServlet 'dispatcherServlet': initialization completed in 111 ms

2018-10-28T22:49:20.572059062Z Sun Oct 28 22:49:20 GMT 2018 GET ======= /api/todolist =======
2018-10-28T22:49:25.850543080Z Sun Oct 28 22:49:25 GMT 2018 DELETE ======= /api/todolist/{4f41ab03-1b12-4131-a920-fe5dfec106ca} ======= 
2018-10-28T22:49:26.047126614Z Sun Oct 28 22:49:26 GMT 2018 GET ======= /api/todolist =======
2018-10-28T22:49:30.201740227Z Sun Oct 28 22:49:30 GMT 2018 POST ======= /api/todolist ======= Milk
2018-10-28T22:49:30.413468872Z Sun Oct 28 22:49:30 GMT 2018 GET ======= /api/todolist =======


```

3. 완료된 경우 [e2e-java-experience-in-app-service-linux-part-2/complete](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2/tree/master/complete)의 코드에 대해 결과를 확인할 수 있습니다.


## <a name="scale-out-the-spring-todo-app"></a>Spring Todo 앱 확장

애플리케이션을 확장하려면 다음 절차를 수행합니다.

1. Azure CLI를 사용하여 Java 웹앱을 확장합니다.

    ```azurecli
    az appservice plan update --number-of-workers 2 \
      --name ${WEBAPP_PLAN_NAME} \
      --resource-group ${RESOURCEGROUP_NAME}
    ```

## <a name="next-steps"></a>다음 단계

- [App Service Linux 개발자 가이드의 Java](/azure/app-service/containers/app-service-linux-java)
- [Java 개발자를 위한 Azure](../index.yml) Spring 및 Azure에 대해 자세히 알아보려면 Azure 설명서 센터의 Spring을 계속합니다.

> [!div class="nextstepaction"]
> [Azure의 Spring](./index.yml)

### <a name="additional-resources"></a>추가 리소스

Azure에서 Spring Boot 애플리케이션을 사용 하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure App Service에서 Linux에 Spring Boot 애플리케이션 배포](deploy-spring-boot-java-app-on-linux.md)

* [Azure Container Service의 Kubernetes 클러스터에 Spring Boot 애플리케이션 실행](deploy-spring-boot-java-app-on-kubernetes.md)

Java와 함께 Azure를 사용하는 방법에 관한 자세한 정보는 [Java 개발자를 위한 Azure]와 [Azure DevOps 및 Java 사용하기]를 참조하세요.

**[Spring Framework]** 는 Java 개발자가 엔터프라이즈 수준의 애플리케이션을 만드는 데 도움이 되는 오픈 소스 솔루션입니다. 해당 플랫폼을 기반으로 하여 빌드되는 인기 있는 프로젝트 중 하나가 [Spring Boot]입니다. 이 프로젝트는 독립 실행형 Java 애플리케이션을 만드는 간단한 방법을 제공합니다. Spring Boot을 시작하는 개발자를 도우려면 <https://github.com/spring-guides/>에서 몇 가지 샘플 Spring Boot 패키지를 사용할 있습니다. 기본 Spring Boot 프로젝트 목록에서 선택하는 것 외에도 **[Spring Initializr]** 를 통해 사용자 지정 Spring Boot 애플리케이션을 만들기 시작하는 개발자에게 도움을 줍니다.

<!-- URL List -->

[Azure Cosmos DB Documentation]: /azure/cosmos-db/
[Java 개발자를 위한 Azure]: ../index.yml
[Build a SQL API app with Java]: /azure/cosmos-db/create-sql-api-java 
[Spring Data for Azure Cosmos DB SQL API]: https://azure.microsoft.com/blog/spring-data-azure-cosmos-db-nosql-data-access-on-azure/
[free Azure account]: https://azure.microsoft.com/pricing/free-trial/
[Azure DevOps 및 Java 사용하기]: https://azure.microsoft.com/services/devops/java/
[MSDN subscriber benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SCDB01]: media/configure-spring-app-with-cosmos-db-on-app-service-linux/SCDB01.png
[SCDB02]: media/configure-spring-app-with-cosmos-db-on-app-service-linux/SCDB02.png