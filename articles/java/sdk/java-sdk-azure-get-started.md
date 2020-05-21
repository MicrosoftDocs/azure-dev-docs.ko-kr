---
title: Azure SDK for Java 시작
description: Azure 클라우드 리소스를 만들어 연결하여 Java 애플리케이션에서 사용하는 방법에 대해 알아봅니다.
keywords: Azure, Java, SDK, API, 인증, 시작
author: rloutlaw
ms.date: 04/16/2017
ms.topic: article
ms.service: multiple
ms.assetid: b1e10b79-f75e-4605-aecd-eed64873e2d3
ms.custom: seo-java-august2019
ms.openlocfilehash: 80e83873150356b53c826d7920df1163e51f2636
ms.sourcegitcommit: 486b55521d7c27666dbb8035bc46fb60d1cbcf0a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550934"
---
# <a name="get-started-with-cloud-development-using-java-on-azure"></a>Azure 에서 Java를 이용하여 클라우드 개발 시작

이 가이드에서는 Java에서 Azure 개발을 위한 개발 환경 설정을 안내합니다. 그런 다음, Azure 리소스를 만들고 연결하여 파일 업로드, 웹 애플리케이션 배포와 같은 기본 작업을 수행합니다. 마치고 나면 자체 Java 애플리케이션에서 Azure Services를 사용할 수 있습니다.

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

- Azure 계정. 계정이 없으면 [체험 계정을 얻습니다](https://azure.microsoft.com/free/).
- [Azure Cloud Shell](/azure/cloud-shell/quickstart) 또는 [Azure CLI 2.0](/cli/azure/install-az-cli2)
- [Java 8](https://www.azul.com/downloads/zulu/)(Azure Cloud Shell에 포함되어 있음)
- [Maven 3](https://maven.apache.org/download.cgi)(Azure Cloud Shell에 포함되어 있음)

## <a name="set-up-authentication"></a>인증 설정

이 자습서에서 샘플 코드를 실행하려면 Azure 구독에 대한 읽기 및 만들기 권한이 Java 애플리케이션에 필요합니다. 서비스 사용자를 만들고 해당 자격 증명을 사용하여 실행되도록 애플리케이션을 구성합니다. 서비스 주체는 앱에서 실행하는 데 필요한 권한만 부여하는 ID와 연결되는 비대화형 계정을 만드는 방법을 제공합니다.

[Azure CLI 2.0을 사용하여 서비스 사용자를 만들고](/cli/azure/create-an-azure-service-principal-azure-cli) 출력을 캡처합니다. 암호 인수에 `MY_SECURE_PASSWORD` 대신 [보안 암호](/azure/active-directory/active-directory-passwords-policy)를 제공합니다. 암호는 8~16자이고 다음 4개 기준 중 3개 이상에 부합해야 합니다.

* 소문자 포함
* 대문자 포함
* 숫자 포함
* @ # $ % ^ & *-_! 기호 중 하나 포함 + = [ ] { } | \ : ' , . ? / ` ~ " ( ) ;

```azurecli-interactive
az ad sp create-for-rbac --name AzureJavaTest --password "MY_SECURE_PASSWORD"
```

다음 형식으로 답장 제공:

```json
{
  "appId": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
  "displayName": "AzureJavaTest",
  "name": "http://AzureJavaTest",
  "password": password,
  "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
}
```

다음으로 시스템의 텍스트 파일에 다음 예제를 복사합니다.

```text
# sample management library properties file
subscription=ssssssss-ssss-ssss-ssss-ssssssssssss
client=cccccccc-cccc-cccc-cccc-cccccccccccc
key=kkkkkkkkkkkkkkkk
tenant=tttttttt-tttt-tttt-tttt-tttttttttttt
managementURI=https\://management.core.windows.net/
baseURL=https\://management.azure.com/
authURL=https\://login.windows.net/
graphURL=https\://graph.windows.net/
```

상위 4개 값을 다음과 같이 바꿉니다.

- subscription: Azure CLI 2.0에서 `az account show`의 *id* 값을 사용합니다.
- client: 서비스 사용자 출력에서 가져온 출력의 *appId* 값을 사용합니다.
- key: 서비스 사용자 출력의 *password* 값을 사용합니다.
- tenant: 서비스 사용자 출력의 *tenant* 값을 사용합니다.

코드에서 읽을 수 있는 시스템의 안전한 위치에 JSON 파일을 저장합니다. 향후 코드에서 이 파일을 사용할 수 있으므로 이 문서에서 애플리케이션의 외부에 저장해 두는 것이 좋습니다.

셸에서 인증 파일의 전체 경로가 포함된 `AZURE_AUTH_LOCATION` 환경 변수를 설정합니다.

```bash
export AZURE_AUTH_LOCATION=/Users/raisa/azureauth.properties
```

Windows 환경에서 작업할 경우 변수를 시스템 속성에 추가합니다. 관리자 권한으로 PowerShell 창을 열고 두 번째 변수를 파일 경로로 바꾼 후 다음 명령을 입력합니다.

```powershell
setx AZURE_AUTH_LOCATION "C:\<fullpath>\azureauth.properties" /m
```

## <a name="tooling"></a>도구

### <a name="create-a-new-maven-project"></a>새 Maven 프로젝트 만들기

> [!NOTE]
> 이 가이드에서는 Maven 빌드 도구를 사용하여 샘플 코드를 빌드하고 실행하지만, Gradle과 같은 다른 빌드 도구도 Java용 Azure 라이브러리에서 작동합니다.

명령줄에서 시스템의 새 디렉터리에 Maven 프로젝트를 만듭니다.

```shell
mkdir java-azure-test
cd java-azure-test
mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=AzureApp \
-DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

이렇게 하면 `testAzureApp` 폴더 아래에 기본 Maven 프로젝트를 만듭니다. `pom.xml` 프로젝트에 다음 항목을 추가하여 이 자습서의 샘플 코드에서 사용되는 라이브러리를 가져옵니다.

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure</artifactId>
    <version>1.3.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-storage</artifactId>
    <version>5.0.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.sqlserver</groupId>
    <artifactId>mssql-jdbc</artifactId>
    <version>6.2.1.jre8</version>
</dependency>
```

최상위 `project` 요소 아래에 `build` 항목을 추가하여 [maven-exec-plugin](https://www.mojohaus.org/exec-maven-plugin/)을 통해 샘플을 실행합니다.

```XML
<build>
    <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.AzureApp</mainClass>
            </configuration>
        </plugin>
    </plugins>
</build>
 ```

### <a name="install-the-azure-toolkit-for-intellij"></a>IntelliJ용 Azure 도구 키트 설치

[Azure 도구 키트](/azure/developer/java/toolkit-for-intellij/installation)는 웹앱이나 API를 프로그래밍 방식으로 설치하지만 현재 다른 개발 유형에는 사용하고 있지 않은 경우에 필요합니다. 다음은 설치 프로세스에 대한 요약입니다. 빠른 시작을 원하는 경우 [Azure Toolkit for IntelliJ 빠른 시작](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app)을 방문하세요.

- **파일** 메뉴를 선택한 다음 **설정...** 을 선택합니다.

- **저장소 찾아보기...** 를 선택하고 “Azure”를 검색한 다음 **Intellij용 Azure 도구 키트**를 설치합니다.

- Intellij를 다시 시작합니다.

### <a name="install-the-azure-toolkit-for-eclipse"></a>Eclipse용 Azure 도구 키트 설치

[Azure 도구 키트](/azure/developer/java/toolkit-for-eclipse)는 웹앱이나 API를 프로그래밍 방식으로 설치하지만 현재 다른 개발 유형에는 사용하고 있지 않은 경우에 필요합니다. 다음은 설치 프로세스에 대한 요약입니다. 빠른 시작을 원하는 경우 [Azure Toolkit for Eclipse 빠른 시작](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)을 방문하세요.

- **도움말** 메뉴를 선택하고 **새 소프트웨어 설치**를 선택합니다.

- **작업:** 필드에 `http://dl.microsoft.com/eclipse/`를 입력하고 Enter를 누릅니다.

- 그런 다음 **Java용 Azure 도구 키트** 옆의 확인란을 선택하고 **필요한 소프트웨어를 설치하는 동안 모든 업데이트 사이트 문의** 확인란의 선택을 취소합니다. 이제 다음을 선택합니다.

## <a name="create-a-linux-virtual-machine"></a>Linux 가상 머신 만들기

프로젝트의 `src/main/java/com/fabirkam` 디렉터리에 `AzureApp.java`라는 새 파일을 만들고 다음 코드 블록에 붙여넣습니다. `userName` 및 `sshKey` 변수를 컴퓨터에 대한 실제 값으로 업데이트합니다. 이 코드에서는 미국 동부 Azure 지역에서 실행되는 `sampleResourceGroup` 리소스 그룹에 `testLinuxVM`이라는 새 Linux VM을 만듭니다.

```java
package com.fabrikam;

import com.microsoft.azure.management.Azure;
import com.microsoft.azure.management.compute.VirtualMachine;
import com.microsoft.azure.management.compute.KnownLinuxVirtualMachineImage;
import com.microsoft.azure.management.compute.VirtualMachineSizeTypes;
import com.microsoft.azure.management.appservice.WebApp;
import com.microsoft.azure.management.storage.StorageAccount;
import com.microsoft.azure.management.storage.SkuName;
import com.microsoft.azure.management.storage.StorageAccountKey;
import com.microsoft.azure.management.sql.SqlDatabase;
import com.microsoft.azure.management.sql.SqlServer;
import com.microsoft.azure.management.resources.fluentcore.arm.Region;
import com.microsoft.azure.management.resources.fluentcore.utils.SdkContext;

import com.microsoft.rest.LogLevel;

import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.blob.*;

import java.io.File;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

public class AzureApp {

    public static void main(String[] args) {

        final String userName = "YOUR_VM_USERNAME";
        final String sshKey = "YOUR_PUBLIC_SSH_KEY";

        try {

            // use the properties file with the service principal information to authenticate
            // change the name of the environment variable if you used a different name in the previous step
            final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
            Azure azure = Azure.configure()
                    .withLogLevel(LogLevel.BASIC)
                    .authenticate(credFile)
                    .withDefaultSubscription();

            // create a Ubuntu virtual machine in a new resource group
            VirtualMachine linuxVM = azure.virtualMachines().define("testLinuxVM")
                    .withRegion(Region.US_EAST)
                    .withNewResourceGroup("sampleVmResourceGroup")
                    .withNewPrimaryNetwork("10.0.0.0/24")
                    .withPrimaryPrivateIPAddressDynamic()
                    .withoutPrimaryPublicIPAddress()
                    .withPopularLinuxImage(KnownLinuxVirtualMachineImage.UBUNTU_SERVER_16_04_LTS)
                    .withRootUsername(userName)
                    .withSsh(sshKey)
                    .withUnmanagedDisks()
                    .withSize(VirtualMachineSizeTypes.STANDARD_D3_V2)
                    .create();

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
}
```

명령줄에서 샘플을 실행합니다.

```shell
mvn compile exec:java
```

SDK에서 기본 호출을 Azure REST API로 설정하여 가상 머신 및 해당 리소스를 구성할 때 콘솔에서 일부 REST 요청과 응답이 표시됩니다. 프로그램이 완료되면 Azure CLI 2.0을 사용하여 구독의 가상 머신을 확인합니다.

```azurecli-interactive
az vm list --resource-group sampleVmResourceGroup
```

코드가 작동하는지 확인했으면 CLI를 사용하여 VM과 해당 리소스를 삭제합니다.

```azurecli-interactive
az group delete --name sampleVmResourceGroup
```

## <a name="deploy-a-web-app-from-a-github-repo"></a>GitHub 리포지토리에서 웹앱 배포

`AzureApp.java`의 main 메서드를 아래 코드로 바꾸고, 이 코드를 실행하기 전에 `appName` 변수를 고유한 값으로 업데이트합니다. 이 코드에서는 공용 GitHub 리포지토리의 `master` 분기에 있는 웹 애플리케이션을 체험 가격 책정 계층에서 실행되는 새 [Azure App Service 웹앱](/azure/app-service-web/app-service-web-overview)으로 배포합니다.

```java
    public static void main(String[] args) {
        try {

            final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
            final String appName = "YOUR_APP_NAME";

            Azure azure = Azure.configure()
                    .withLogLevel(LogLevel.BASIC)
                    .authenticate(credFile)
                    .withDefaultSubscription();

            WebApp app = azure.webApps().define(appName)
                    .withRegion(Region.US_WEST2)
                    .withNewResourceGroup("sampleWebResourceGroup")
                    .withNewWindowsPlan(PricingTier.FREE_F1)
                    .defineSourceControl()
                    .withPublicGitRepository(
                            "https://github.com/Azure-Samples/app-service-web-java-get-started")
                    .withBranch("master")
                    .attach()
                    .create();

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
```

Maven을 사용하기 전에 다음 코드를 실행합니다.

```shell
mvn clean compile exec:java
```

CLI를 사용하여 애플리케이션을 가리키는 브라우저를 엽니다.

```azurecli-interactive
az appservice web browse --resource-group sampleWebResourceGroup --name YOUR_APP_NAME
```

배포를 확인한 후 구독에서 웹앱을 제거하고 계획합니다.

```azurecli-interactive
az group delete --name sampleWebResourceGroup
```

## <a name="connect-to-an-azure-sql-database"></a>Azure SQL 데이터베이스에 연결

`AzureApp.java`의 현재 main 메서드를 `dbPassword` 변수에 대한 실제 값을 설정하는 아래 코드로 바꿉니다.
이 코드에서는 원격 액세스를 허용하는 방화벽 규칙이 있는 새 SQL 데이터베이스를 만든 다음 SQL Database JBDC 드라이버를 사용하여 연결합니다.

```java
    public static void main(String args[])
    {
        // create the db using the management libraries
        try {
            final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
            Azure azure = Azure.configure()
                    .withLogLevel(LogLevel.BASIC)
                    .authenticate(credFile)
                    .withDefaultSubscription();

            final String adminUser = SdkContext.randomResourceName("db",8);
            final String sqlServerName = SdkContext.randomResourceName("sql",10);
            final String sqlDbName = SdkContext.randomResourceName("dbname",8);
            final String dbPassword = "YOUR_PASSWORD_HERE";


            SqlServer sampleSQLServer = azure.sqlServers().define(sqlServerName)
                            .withRegion(Region.US_EAST)
                            .withNewResourceGroup("sampleSqlResourceGroup")
                            .withAdministratorLogin(adminUser)
                            .withAdministratorPassword(dbPassword)
                            .withNewFirewallRule("0.0.0.0","255.255.255.255")
                            .create();

            SqlDatabase sampleSQLDb = sampleSQLServer.databases().define(sqlDbName).create();

            // assemble the connection string to the database
            final String domain = sampleSQLServer.fullyQualifiedDomainName();
            String url = "jdbc:sqlserver://"+ domain + ":1433;" +
                    "database=" + sqlDbName +";" +
                    "user=" + adminUser+ "@" + sqlServerName + ";" +
                    "password=" + dbPassword + ";" +
                    "encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;";

            // connect to the database, create a table and insert a entry into it
            Connection conn = DriverManager.getConnection(url);

            String createTable = "CREATE TABLE CLOUD ( name varchar(255), code int);";
            String insertValues = "INSERT INTO CLOUD (name, code ) VALUES ('Azure', 1);";
            String selectValues = "SELECT * FROM CLOUD";
            Statement createStatement = conn.createStatement();
            createStatement.execute(createTable);
            Statement insertStatement = conn.createStatement();
            insertStatement.execute(insertValues);
            Statement selectStatement = conn.createStatement();
            ResultSet rst = selectStatement.executeQuery(selectValues);

            while (rst.next()) {
                System.out.println(rst.getString(1) + " "
                        + rst.getString(2));
            }


        } catch (Exception e) {
            System.out.println(e.getMessage());
            System.out.println(e.getStackTrace().toString());
        }
    }
```

명령줄에서 샘플을 실행합니다.

```shell
mvn clean compile exec:java
```

그런 다음 CLI를 사용하여 리소스를 정리합니다.

```azurecli-interactive
az group delete --name sampleSqlResourceGroup
```

## <a name="write-a-blob-into-a-new-storage-account"></a>새 스토리지 계정에 Blob 쓰기

`AzureApp.java`의 현재 main 메서드를 아래 코드로 바꿉니다. 이 코드에서는 [Azure 스토리지 계정](/azure/storage/common/storage-introduction)을 만든 다음 Java용 Azure Storage 라이브러리를 사용하여 클라우드에 새 텍스트 파일을 만듭니다.

```java
public static void main(String[] args) {

    try {

        // use the properties file with the service principal information to authenticate
        // change the name of the environment variable if you used a different name in the previous step
        final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
        Azure azure = Azure.configure()
                .withLogLevel(LogLevel.BASIC)
                .authenticate(credFile)
                .withDefaultSubscription();

        // create a new storage account
        String storageAccountName = SdkContext.randomResourceName("st",8);
        StorageAccount storage = azure.storageAccounts().define(storageAccountName)
                    .withRegion(Region.US_WEST2)
                    .withNewResourceGroup("sampleStorageResourceGroup")
                    .create();

        // create a storage container to hold the file
        List<StorageAccountKey> keys = storage.getKeys();
        final String storageConnection = "DefaultEndpointsProtocol=https;"
                + "AccountName=" + storage.name()
                + ";AccountKey=" + keys.get(0).value()
                + ";EndpointSuffix=core.windows.net";

        CloudStorageAccount account = CloudStorageAccount.parse(storageConnection);
        CloudBlobClient serviceClient = account.createCloudBlobClient();

        // Container name must be lower case.
        CloudBlobContainer container = serviceClient.getContainerReference("helloazure");
        container.createIfNotExists();

        // Make the container public
        BlobContainerPermissions containerPermissions = new BlobContainerPermissions();
        containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
        container.uploadPermissions(containerPermissions);

        // write a blob to the container
        CloudBlockBlob blob = container.getBlockBlobReference("helloazure.txt");
        blob.uploadText("hello Azure");

    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }
    }
}
```

명령줄에서 샘플을 실행합니다.

```shell
mvn clean compile exec:java
```

Azure Portal 또는 [Azure Storage Explorer](/azure/vs-azure-tools-storage-explorer-blobs)를 사용하여 스토리지 계정에서 `helloazure.txt` 파일을 찾아볼 수 있습니다.

CLI를 사용하여 스토리지 계정을 정리합니다.

```azurecli-interactive
az group delete --name sampleStorageResourceGroup
```

## <a name="explore-more-samples"></a>더 많은 샘플 탐색

Java용 Azure 관리 라이브러리를 사용하여 리소스를 관리하고 작업을 자동화하는 방법에 대한 자세한 내용은 [가상 머신](java-sdk-azure-virtual-machine-samples.md), [웹앱](java-sdk-azure-web-apps-samples.md) 및 [SQL 데이터베이스](java-sdk-azure-sql-database-samples.md)에 대한 샘플 코드를 참조하세요.

## <a name="reference-and-release-notes"></a>참조 및 릴리스 정보

[참조](/java/api)는 모든 패키지에서 사용할 수 있습니다.

## <a name="get-help-and-give-feedback"></a>도움말 가져오기 및 피드백 제공

[Stack Overflow(영문)](https://stackoverflow.com/questions/tagged/azure+java)의 커뮤니티에 질문을 게시합니다. [GitHub 프로젝트](https://github.com/Azure/azure-sdk-for-java)에서 Java용 Azure 라이브러리에 대한 버그 및 열기 문제를 보고합니다.
