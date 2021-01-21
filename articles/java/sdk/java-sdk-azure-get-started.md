---
title: Azure SDK for Java 시작
description: Azure 클라우드 리소스를 만들어 연결하여 Java 애플리케이션에서 사용하는 방법에 대해 알아봅니다.
keywords: Azure, Java, SDK, API, 인증, 시작
author: bmitchell287
ms.author: brendm
ms.date: 11/20/2020
ms.topic: article
ms.service: multiple
ms.assetid: b1e10b79-f75e-4605-aecd-eed64873e2d3
ms.custom: seo-java-august2019, devx-track-java, devx-track-azurecli
ms.openlocfilehash: f96b8f122967c1b742fb97a0ce5d096885904f59
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561839"
---
# <a name="get-started-with-cloud-development-using-java-on-azure"></a>Azure 에서 Java를 이용하여 클라우드 개발 시작

이 문서에서는 Java에서 Azure 개발을 위한 개발 환경을 설정하는 방법을 안내합니다. 그런 다음, Azure 리소스를 만들고 연결하여 파일 업로드, 웹 애플리케이션 배포와 같은 기본 작업을 수행합니다. 모두 마치고 나면 자체 Java 애플리케이션에서 Azure 서비스를 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 계정. 계정이 없으면 [체험 계정을 얻습니다](https://azure.microsoft.com/free/).
- [Azure Cloud Shell](/azure/cloud-shell/quickstart) 또는 [Azure CLI 2.0](/cli/azure/install-az-cli2)
- [Java 8](../fundamentals/java-jdk-long-term-support.md)(Azure Cloud Shell에 포함되어 있음)
- [Maven 3](https://maven.apache.org/download.cgi)(Azure Cloud Shell에 포함되어 있음)

## <a name="set-up-authentication"></a>인증 설정

이 자습서에서 샘플 코드를 실행하려면 Azure 구독에 대한 읽기 및 만들기 권한이 Java 애플리케이션에 필요합니다. 서비스 주체를 만들고 해당 자격 증명을 사용하여 실행되도록 애플리케이션을 구성합니다. 서비스 주체는 앱에서 실행하는 데 필요한 권한만 부여하는 ID와 연결되는 비대화형 계정을 만드는 방법을 제공합니다.

[Azure CLI 2.0을 사용하여 서비스 주체를 만들고](/cli/azure/create-an-azure-service-principal-azure-cli) 출력을 캡처합니다.

```azurecli-interactive
az ad sp create-for-rbac --name AzureJavaTest
```

다음 형식으로 답장 제공:

```json
{
  "appId": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
  "displayName": "AzureJavaTest",
  "name": "http://AzureJavaTest",
  "password": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
  "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
}
```

그 후에는 다음과 같은 환경 변수를 구성합니다.

- `AZURE_SUBSCRIPTION_ID`: Azure CLI 2.0에서 `az account show`의 *id* 값을 사용합니다.
- `AZURE_CLIENT_ID`: 서비스 주체 출력에서 얻은 출력의 *appId* 값을 사용합니다.
- `AZURE_CLIENT_SECRET`: 서비스 주체 출력의 *password* 값을 사용합니다.
- `AZURE_TENANT_ID`: 서비스 주체 출력의 *tenant* 값을 사용합니다.

더 많은 인증 옵션을 보려면 [Azure ID](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-java)를 참조하세요.

## <a name="tooling"></a>도구

### <a name="create-a-new-maven-project"></a>새 Maven 프로젝트 만들기

> [!NOTE]
> 이 문서에서는 Maven 빌드 도구를 사용하여 샘플 코드를 빌드하고 실행합니다. Gradle 등의 다른 빌드 도구도 Java용 Azure 라이브러리에서 작동합니다.

명령줄을 사용하여 시스템의 새 디렉터리에 Maven 프로젝트를 만듭니다.

```shell
mkdir java-azure-test
cd java-azure-test
mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=AzureApp \
-DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

이 단계를 수행하면 `testAzureApp` 폴더에 기본 Maven 프로젝트가 생성됩니다. `pom.xml` 프로젝트에 다음 항목을 추가하여 이 자습서의 샘플 코드에서 사용되는 라이브러리를 가져옵니다.

```XML
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.0</version>
</dependency>
<dependency>
    <groupId>com.azure.resourcemanager</groupId>
    <artifactId>azure-resourcemanager</artifactId>
    <version>2.1.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.8.0</version>
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

웹앱 또는 API를 프로그래밍 방식으로 배포하려면 [Azure 도구 키트](../toolkit-for-intellij/index.yml)가 필요합니다. 현재 다른 종류의 개발에는 이 도구 키트가 사용되지 않습니다. 다음 단계는 설치 프로세스를 요약한 것입니다. 빠른 시작을 원하는 경우 [Azure Toolkit for IntelliJ](../toolkit-for-intellij/create-hello-world-web-app.md)를 참조하세요.

1. **파일** 메뉴를 선택한 다음, **설정** 을 선택합니다.
1. **리포지토리 찾아보기...** 를 선택하고 **Azure** 를 검색한 다음, **Intellij용 Azure 도구 키트** 를 설치합니다.
1. Intellij를 다시 시작합니다.

### <a name="install-the-azure-toolkit-for-eclipse"></a>Eclipse용 Azure 도구 키트 설치

웹앱 또는 API를 프로그래밍 방식으로 배포하려면 [Azure 도구 키트](../toolkit-for-eclipse/index.yml)가 필요합니다. 현재 다른 종류의 개발에는 이 도구 키트가 사용되지 않습니다. 다음 단계는 설치 프로세스를 요약한 것입니다. 빠른 시작을 원하는 경우 [Azure Toolkit for Eclipse](../toolkit-for-eclipse/create-hello-world-web-app.md)를 참조하세요.

1. **도움말** 메뉴를 선택한 다음, **새 소프트웨어 설치** 를 선택합니다.
1. **작업** 상자에 `http://dl.microsoft.com/eclipse/`를 입력하고 **Enter** 를 선택합니다.
1. **Azure toolkit for Java** 옆의 확인란을 선택합니다. **필요한 소프트웨어를 설치하는 동안 모든 업데이트 사이트 문의** 확인란의 선택을 취소합니다. 그런 후 **다음** 을 선택합니다.

## <a name="create-a-linux-virtual-machine"></a>Linux 가상 머신 만들기

프로젝트의 `src/main/java/com/fabrikam` 디렉터리에 `AzureApp.java`라는 새 파일을 만들고 다음 코드 블록에 붙여넣습니다. `userName` 및 `sshKey` 변수를 컴퓨터에 대한 실제 값으로 업데이트합니다. 이 코드는 미국 동부 Azure 지역에서 실행되는 `sampleResourceGroup` 리소스 그룹에 `testLinuxVM`이라는 새 Linux VM(가상 머신)을 만듭니다.

```java
package com.fabrikam;

import com.azure.core.credential.TokenCredential;
import com.azure.core.http.policy.HttpLogDetailLevel;
import com.azure.core.management.AzureEnvironment;
import com.azure.core.management.Region;
import com.azure.core.management.profile.AzureProfile;
import com.azure.identity.AzureAuthorityHosts;
import com.azure.identity.EnvironmentCredentialBuilder;
import com.azure.resourcemanager.AzureResourceManager;
import com.azure.resourcemanager.compute.models.KnownLinuxVirtualMachineImage;
import com.azure.resourcemanager.compute.models.VirtualMachine;
import com.azure.resourcemanager.compute.models.VirtualMachineSizeTypes;

public class AzureApp {

    public static void main(String[] args) {

        final String userName = "YOUR_VM_USERNAME";
        final String sshKey = "YOUR_PUBLIC_SSH_KEY";

        try {
            TokenCredential credential = new EnvironmentCredentialBuilder()
                    .authorityHost(AzureAuthorityHosts.AZURE_PUBLIC_CLOUD)
                    .build();

            // If you do not set the tenant ID and subscription ID via environment variables,
            // change to create the Azure profile with tenantId, subscriptionId, and Azure environment.
            AzureProfile profile = new AzureProfile(AzureEnvironment.AZURE);

            AzureResourceManager azureResourceManager = AzureResourceManager.configure()
                    .withLogLevel(HttpLogDetailLevel.BASIC)
                    .authenticate(credential, profile)
                    .withDefaultSubscription();

            // Create an Ubuntu virtual machine in a new resource group.
            VirtualMachine linuxVM = azureResourceManager.virtualMachines().define("testLinuxVM")
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

SDK에서 기본 호출을 Azure REST API로 설정하여 VM 및 해당 리소스를 구성할 때 콘솔에서 일부 REST 요청과 응답이 표시됩니다. 프로그램이 완료되면 Azure CLI 2.0을 사용하여 구독의 VM을 확인합니다.

```azurecli-interactive
az vm list --resource-group sampleVmResourceGroup
```

코드가 작동하는 것을 확인했으면 CLI를 사용하여 VM과 해당 리소스를 삭제합니다.

```azurecli-interactive
az group delete --name sampleVmResourceGroup
```

## <a name="deploy-a-web-app-from-a-github-repo"></a>GitHub 리포지토리에서 웹앱 배포

`AzureApp.java`의 main 메서드를 다음으로 바꿉니다. 코드를 실행하기 전에 `appName` 변수를 고유한 값으로 업데이트합니다. 이 코드에서는 공용 GitHub 리포지토리의 `master` 분기에 있는 웹 애플리케이션을 체험 가격 책정 계층에서 실행되는 새 [Azure App Service 웹앱](/azure/app-service-web/app-service-web-overview)으로 배포합니다.

```java
    public static void main(String[] args) {
        try {

            final String appName = "YOUR_APP_NAME";

            TokenCredential credential = new EnvironmentCredentialBuilder()
                    .authorityHost(AzureAuthorityHosts.AZURE_PUBLIC_CLOUD)
                    .build();

            // If you do not set the tenant ID and subscription ID via environment variables,
            // change to create the Azure profile with tenantId, subscriptionId, and Azure environment.
            AzureProfile profile = new AzureProfile(AzureEnvironment.AZURE);
            
            AzureResourceManager azureResourceManager = AzureResourceManager.configure()
                    .withLogLevel(HttpLogDetailLevel.BASIC)
                    .authenticate(credential, profile)
                    .withDefaultSubscription();

            WebApp app = azureResourceManager.webApps().define(appName)
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

Maven을 사용하여 같은 방법으로 코드를 실행합니다.

```shell
mvn clean compile exec:java
```

CLI를 사용하여 애플리케이션을 가리키는 브라우저를 엽니다.

```azurecli-interactive
az appservice web browse --resource-group sampleWebResourceGroup --name YOUR_APP_NAME
```

배포를 확인한 후 구독에서 웹앱과 플랜을 제거합니다.

```azurecli-interactive
az group delete --name sampleWebResourceGroup
```

## <a name="connect-to-an-azure-sql-database"></a>Azure SQL 데이터베이스에 연결

`AzureApp.java`의 현재 main 메서드를 다음 코드로 바꿉니다. 변수의 실제 값을 설정합니다.
이 코드는 원격 액세스를 허용하는 방화벽 규칙을 사용하여 새 SQL 데이터베이스를 만듭니다. 그런 다음, 이 코드는 SQL Database JBDC 드라이버를 사용하여 코드에 연결합니다.

```java
    public static void main(String args[])
    {
        // Create the db using the management libraries.
        try {
            TokenCredential credential = new EnvironmentCredentialBuilder()
                    .authorityHost(AzureAuthorityHosts.AZURE_PUBLIC_CLOUD)
                    .build();

            // If you do not set the tenant ID and subscription ID via environment variables,
            // change to create the Azure profile with tenantId, subscriptionId, and Azure environment.
            AzureProfile profile = new AzureProfile(AzureEnvironment.AZURE);

            AzureResourceManager azureResourceManager = AzureResourceManager.configure()
                    .withLogLevel(HttpLogDetailLevel.BASIC)
                    .authenticate(credential, profile);

            final String adminUser = "YOUR_USERNAME_HERE";
            final String sqlServerName = "YOUR_SERVER_NAME_HERE";
            final String sqlDbName = "YOUR_DB_NAME_HERE";
            final String dbPassword = "YOUR_PASSWORD_HERE";
            final String firewallRuleName = "YOUR_RULE_NAME_HERE";

            SqlServer sampleSQLServer = azureResourceManager.sqlServers().define(sqlServerName)
                    .withRegion(Region.US_EAST)
                    .withNewResourceGroup("sampleSqlResourceGroup")
                    .withAdministratorLogin(adminUser)
                    .withAdministratorPassword(dbPassword)
                    .defineFirewallRule(firewallRuleName)
                        .withIpAddressRange("0.0.0.0","255.255.255.255")
                        .attach()
                    .create();

            SqlDatabase sampleSQLDb = sampleSQLServer.databases().define(sqlDbName).create();

            // Assemble the connection string to the database.
            final String domain = sampleSQLServer.fullyQualifiedDomainName();
            String url = "jdbc:sqlserver://"+ domain + ":1433;" +
                    "database=" + sqlDbName +";" +
                    "user=" + adminUser+ "@" + sqlServerName + ";" +
                    "password=" + dbPassword + ";" +
                    "encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;";

            // Connect to the database, create a table, and insert an entry into it.
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

그런 다음, CLI를 사용하여 리소스를 정리합니다.

```azurecli-interactive
az group delete --name sampleSqlResourceGroup
```

## <a name="write-a-blob-into-a-new-storage-account"></a>새 스토리지 계정에 Blob 쓰기

`AzureApp.java`의 현재 main 메서드를 다음 코드로 바꿉니다. 이 코드는 [Azure 스토리지 계정](/azure/storage/common/storage-introduction)을 만듭니다. 그런 다음, 이 코드는 Java용 Azure Storage 라이브러리를 사용하여 클라우드에 새 텍스트 파일을 만듭니다.

```java
    public static void main(String[] args) {

        try {
            TokenCredential tokenCredential = new EnvironmentCredentialBuilder()
                    .authorityHost(AzureAuthorityHosts.AZURE_PUBLIC_CLOUD)
                    .build();

            // If you do not set the tenant ID and subscription ID via environment variables,
            // change to create the Azure profile with tenantId, subscriptionId, and Azure environment.
            AzureProfile profile = new AzureProfile(AzureEnvironment.AZURE);

            AzureResourceManager azureResourceManager = AzureResourceManager.configure()
                    .withLogLevel(HttpLogDetailLevel.BASIC)
                    .authenticate(tokenCredential, profile)
                    .withDefaultSubscription();

            // Create a new storage account.
            String storageAccountName = "YOUR_STORAGE_ACCOUNT_NAME_HERE";
            StorageAccount storage = azureResourceManager.storageAccounts().define(storageAccountName)
                    .withRegion(Region.US_WEST2)
                    .withNewResourceGroup("sampleStorageResourceGroup")
                    .create();

            // Create a storage container to hold the file.
            List<StorageAccountKey> keys = storage.getKeys();
            PublicEndpoints endpoints = storage.endPoints();
            String accountName = storage.name();
            String accountKey = keys.get(0).value();
            String endpoint = endpoints.primary().blob();

            StorageSharedKeyCredential credential = new StorageSharedKeyCredential(accountName, accountKey);

            BlobServiceClient storageClient =new BlobServiceClientBuilder()
                    .endpoint(endpoint)
                    .credential(credential)
                    .buildClient();

            // Container name must be lowercase.
            BlobContainerClient blobContainerClient = storageClient.getBlobContainerClient("helloazure");
            blobContainerClient.create();

            // Make the container public.
            blobContainerClient.setAccessPolicy(PublicAccessType.CONTAINER, null);

            // Write a blob to the container.
            String fileName = "helloazure.txt";
            String textNew = "Hello Azure";

            BlobClient blobClient = blobContainerClient.getBlobClient(fileName);
            InputStream is = new ByteArrayInputStream(textNew.getBytes());
            blobClient.upload(is, textNew.length());

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
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