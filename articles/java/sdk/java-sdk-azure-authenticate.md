---
title: Java용 Azure 관리 라이브러리를 사용하여 인증
description: Java용 Azure 관리 라이브러리에 서비스 사용자를 인증합니다.
keywords: Azure, Java, SDK, API, Maven, Gradle, 인증, Active Directory, 서비스 사용자
author: bmitchell287
ms.author: brendm
ms.date: 04/16/2017
ms.topic: article
ms.service: multiple
ms.assetid: 10f457e3-578b-4655-8cd1-51339226ee7d
ms.custom: seo-java-september2019, devx-track-java
ms.openlocfilehash: d4266c0089a371dd87711bfb692b5db32d9fc881
ms.sourcegitcommit: 4dac39849ba2e48034ecc91ef578d11aab796e58
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94983942"
---
# <a name="authenticate-with-the-azure-libraries-for-java"></a>Java용 Azure 라이브러리를 사용하여 인증

이 문서에서는 Java용 Azure 라이브러리를 사용하여 인증하는 방법을 보여줍니다.

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="connect-to-services-with-connection-strings"></a>연결 문자열을 사용하여 서비스에 연결

대부분의 Azure 서비스 라이브러리는 인증에 연결 문자열 또는 보안 키를 사용합니다. 예를 들어 SQL Database에는 JDBC 연결 문자열에 사용자 이름과 암호 정보가 포함됩니다.

```java
String url = "jdbc:sqlserver://myazuredb.database.windows.net:1433;" +
        "database=testjavadb;" +
        "user=myazdbuser;" +
        "password=myazdbpass;" +
        "encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;";
        Connection conn = DriverManager.getConnection(url);
```

Azure Storage는 스토리지 키를 사용하여 애플리케이션에 권한을 부여합니다.

```java
final String storageConnection = "DefaultEndpointsProtocol=https;"
        + "AccountName=" + storageName
        + ";AccountKey=" + storageKey
        + ";EndpointSuffix=core.windows.net";
```

서비스 연결 문자열은 [Azure Cosmos DB](/azure/cosmos-db/sql-api-java-application#UseService), [Redis Cache](/azure/redis-cache/cache-java-get-started) 및 [Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-queues)와 같은 다른 Azure 서비스를 인증하는 데 사용됩니다. Azure Portal 또는 CLI를 사용하여 연결 문자열을 가져올 수 있습니다.  또한 Java용 Azure 관리 라이브러리를 사용하여 코드에서 연결 문자열을 작성하는 리소스를 쿼리할 수도 있습니다.

예를 들어 다음 코드에서는 관리 라이브러리를 사용하여 스토리지 계정 연결 문자열을 만듭니다.

```java
// create a new storage account
StorageAccount storage = azure.storageAccounts().getByResourceGroup("myResourceGroup","myStorageAccount");

// create a storage container to hold the file
List<StorageAccountKey> keys = storage.getKeys();
final String storageConnection = "DefaultEndpointsProtocol=https;"
        + "AccountName=" + storage.name()
        + ";AccountKey=" + keys.get(0).value()
        + ";EndpointSuffix=core.windows.net";
```

다른 라이브러리에서는 권한이 부여된 자격 증명을 사용하여 애플리케이션 실행 권한을 부여하는 [서비스 사용자](/azure/active-directory/develop/active-directory-application-objects)를 통해 애플리케이션을 실행해야 합니다. 이 구성은 아래에서 나열하는 관리 라이브러리에 대한 개체 기반 인증 단계와 비슷합니다.

<a name="mgmt-auth"></a>

##  <a name="authenticate-with-the-azure-management-libraries-for-java"></a>Java용 Azure 관리 라이브러리를 사용하여 인증

Java 관리 라이브러리를 사용하여 리소스를 만들고 관리할 때 Azure를 통해 애플리케이션을 인증하는 데에는 두 가지 옵션을 사용할 수 있습니다.

### <a name="authenticate-with-an-applicationtokencredentials-object"></a>ApplicationTokenCredentials 개체를 사용하여 인증

`ApplicationTokenCredentials`의 인스턴스를 만들어 코드 내의 최상위 `Azure` 개체에 서비스 사용자 자격 증명을 제공합니다.

```java
import com.microsoft.azure.credentials.ApplicationTokenCredentials;
import com.microsoft.azure.AzureEnvironment;

// ...

ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(client,
        tenant,
        key,
        AzureEnvironment.AZURE);

Azure azure = Azure
        .configure()
        .withLogLevel(LogLevel.NONE)
        .authenticate(credentials)
        .withDefaultSubscription();
```

`client`, `tenant` 및 `key`는 [파일 기반 인증](#mgmt-file)에 사용되는 것과 동일한 서비스 사용자 값입니다. `AzureEnvironment.AZURE` 값은 Azure 퍼블릭 클라우드에 대한 자격 증명을 만듭니다. 다른 클라우드에 액세스해야 하는 경우 이 값을 다른 값으로 변경합니다(예: `AzureEnvironment.AZURE_GERMANY`).

 환경 변수 또는 [Key Vault](/azure/key-vault/key-vault-whatis)와 같은 비밀 관리 저장소에서 서비스 사용자 값을 읽습니다. 자격 증명이 실수로 버전 제어 기록에 노출되지 않도록 이러한 값은 코드에서 일반 텍스트 문자열로 설정하지 마세요.

<a name="mgmt-file"></a>

### <a name="file-based-authentication-preview"></a>파일 기반 인증(미리 보기)

가장 간단한 인증 방법은 다음 형식을 사용하여 [Azure 서비스 사용자](/azure/active-directory/develop/active-directory-application-objects)에 대한 자격 증명을 포함하는 속성 파일을 만드는 것입니다.

```text
# sample management library properties file
subscription=########-####-####-####-############
client=########-####-####-####-############
key=XXXXXXXXXXXXXXXX
tenant=########-####-####-####-############
managementURI=https\://management.core.windows.net/
baseURL=https\://management.azure.com/
authURL=https\://login.windows.net/
graphURL=https\://graph.windows.net/
```

- subscription: Azure CLI 2.0에서 `az account show`의 *id* 값을 사용합니다.
- client: 애플리케이션을 실행하기 위해 만든 서비스 사용자에서 가져온 출력의 *appId* 값을 사용합니다. 앱에 대한 서비스 사용자가 없는 경우 [Azure CLI 2.0을 사용하여 서비스 사용자를 만듭니다](/cli/azure/create-an-azure-service-principal-azure-cli).
- key: 서비스 사용자 만들기 CLI 출력의 *password* 값을 사용합니다.
- tenant: 서비스 사용자 만들기 CLI 출력의 *tenant* 값을 사용합니다.

코드에서 읽을 수 있는 시스템의 안전한 위치에 JSON 파일을 저장합니다. 셸에서 이 파일의 전체 경로가 포함된 환경 변수를 설정합니다.

```bash
export AZURE_AUTH_LOCATION=/Users/raisa/azureauth.properties
```

`Azure` 진입점 개체를 만들어 라이브러리 작업을 시작합니다. 환경 변수를 통해 속성 파일의 위치를 읽습니다.

```java
// pull in the location of the authentication properties file from the environment
final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));

Azure azure = Azure
        .configure()
        .withLogLevel(LogLevel.NONE)
        .authenticate(credFile)
        .withDefaultSubscription();
```
