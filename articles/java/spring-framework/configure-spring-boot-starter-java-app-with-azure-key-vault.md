---
title: Spring Boot 애플리케이션의 Azure Key Vault에서 비밀을 읽는 방법에 대한 자습서
description: Spring Boot 애플리케이션의 Azure Key Vault에서 비밀을 읽는 방법에 대한 자습서
services: key-vault
documentationcenter: java
ms.date: 08/15/2020
ms.service: key-vault
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: identity
ms.custom: devx-track-java
ms.openlocfilehash: efe17eba034b92d771d979a896585d4d5362a11d
ms.sourcegitcommit: a139e25190960ba89c9e31f861f0996a6067cd6c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534401"
---
# <a name="tutorial-reading-a-secret-from-azure-key-vault-in-a-spring-boot-application"></a>자습서: Spring Boot 애플리케이션의 Azure Key Vault에서 비밀 읽기

Spring Boot 애플리케이션은 사용자 이름 및 암호와 같은 중요한 정보를 외부화합니다.  중요한 정보를 외부화하면 유지 관리 용이성, 테스트 가능성 및 보안이 향상됩니다.  비밀을 코드 외부에 저장하는 것이 해당 정보를 하드 코딩하거나 빌드 시 인라인 처리하는 것보다 낫습니다.

이 자습서에서는 Azure Key Vault에서 값을 읽는 Spring Boot 앱을 만든 다음, Azure App Service 및 Azure Spring Cloud에 앱을 배포하는 방법을 설명합니다.

> [!div class="checklist"]
> * Azure Key Vault 만들기 및 비밀 저장
> * Spring Initializr로 앱 만들기
> * 앱에 Key Vault 통합 추가
> * Azure App Service에 배포
> * Azure 리소스에 대한 관리 ID를 사용하여 Azure App Service에 다시 배포
> * Azure Spring Cloud에 배포

## <a name="prerequisites"></a>필수 구성 요소

* 활성화된 Azure 구독.
  * Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/).
* [Azure CLI 버전 2.0.67 이상을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)하고 다음 명령을 사용하여 Azure Spring Cloud 확장을 설치합니다. `az extension add --name spring-cloud`
* 지원되는 JDK(Java Development Kit) Azure에서 개발하는 경우 사용할 수 있는 JDK에 대한 자세한 내용은 <https://aka.ms/azure-jdks>를 참조하세요.
* [Apache Maven](http://maven.apache.org/), 버전 3.0 이상
* `curl` 명령.  UNIX와 유사한 대부분의 운영 체제에는 이 명령이 미리 설치되어 있습니다.  OS별 클라이언트는 [공식 curl 웹 사이트](https://curl.haxx.se/)에서 사용할 수 있습니다.
* `jq` 명령. UNIX와 유사한 대부분의 운영 체제에는 이 명령이 미리 설치되어 있습니다.  OS별 클라이언트는 [공식 jq 웹 사이트](https://stedolan.github.io/jq/)에서 사용할 수 있습니다.

## <a name="create-a-new-azure-key-vault"></a>새 Azure Key Vault 만들기

다음 섹션에서는 Azure에 로그인하고 Azure Key Vault를 만드는 방법을 보여줍니다.

### <a name="sign-into-azure-and-set-your-subscription"></a>Azure에 로그인하고 구독 설정

먼저 다음 단계에 따라 Azure CLI를 사용하여 인증합니다.

1. 필요에 따라 로그아웃하고 일부 인증 파일을 삭제하여 남아있는 자격 증명을 제거합니다.

   ```azurecli
   az logout
   rm ~/.azure/accessTokens.json
   rm ~/.azure/azureProfile.json
   ```

1. Azure CLI를 사용하여 Azure 계정에 로그인합니다.

   ```azurecli
   az login
   ```

   지시에 따라 로그인 프로세스를 완료합니다.

1. 구독 나열:

   ```azurecli
   az account list
   ```

   Azure에서 구독 목록이 반환됩니다. 사용하려는 구독의 `id`를 복사합니다. 예:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "ssssssss-ssss-ssss-ssss-ssssssssssss",
       "name": "Converted Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "tttttttt-tttt-tttt-tttt-tttttttttttt",
       "user": {
         "name": "contoso@microsoft.com",
         "type": "user"
       }
     }
   ]
   ```

1. Azure에 사용하려는 구독에 대한 GUID를 지정합니다. 예:

   ```azurecli
   az account set -s ssssssss-ssss-ssss-ssss-ssssssssssss
   ```

### <a name="create-a-service-principal-for-use-in-by-your-app"></a>앱에서 사용할 서비스 주체 만들기

Azure AD *서비스 주체*는 구독 내 Azure 리소스에 대한 액세스를 제공합니다. 서비스 주체를 서비스의 사용자 ID로 생각할 수 있습니다.  "서비스"는 이 자습서에서 빌드된 샘플 앱을 포함하여 Azure 리소스에 액세스해야 하는 애플리케이션, 서비스 또는 플랫폼입니다. 지정한 리소스에 대해서만 액세스 권한이 있는 서비스 주체를 구성할 수 있습니다. 그런 다음, 서비스 주체의 자격 증명을 사용하여 해당 리소스에 액세스하도록 애플리케이션이나 서비스를 구성합니다.

이 명령을 사용하여 서비스 주체를 만듭니다.

```azurecli
az ad sp create-for-rbac --name contososp
```

`name` 옵션의 값은 구독 내에서 고유해야 합니다.  명령에서 반환된 값은 자습서의 뒷부분에서 사용할 수 있도록 따로 저장합니다.  반환 JSON은 다음과 같습니다.

```json
{
  "appId": "8r7o486s-o5q9-450s-8457-pr26p86n0497",
  "displayName": "ejbcontososp",
  "name": "http://ejbcontososp",
  "password": "4bt.lCKJKlbYLn_3XF~wWtUwyHU0jKggu2",
  "tenant": "72s988os-86s1-41ns-91no-2d7cd011db47"
}
```

### <a name="create-the-key-vault-instance"></a>Key Vault 인스턴스 만들기

다음 절차에서는 Key Vault를 만들고 초기화합니다.

1. 리소스를 보관할 Azure 지역을 결정합니다.
   1. [지역 및 위치 목록](https://azure.microsoft.com/regions/)을 검토할 수 있습니다.
   1. `az account list-locations` 명령을 사용하여 선택한 지역의 올바른 `Name`을 찾을 수 있습니다.

      ```azurecli
      az account list-locations -o table
      ```

      이 자습서에서는 `eastus`를 사용합니다.
1. Key Vault 및 App Service 앱을 보관할 리소스 그룹을 만듭니다.  값은 Azure 구독 내에서 고유해야 합니다.  이 자습서에서는 `contosorg`를 사용합니다.

   ```azurecli
   az group create --name contosorg --location eastus
   ```

1. 리소스 그룹에 새 Key Vault를 만듭니다.

   ```azurecli
   az keyvault create \
       --resource-group contosorg \
       --name contosokv \
       --enabled-for-deployment true \
       --enabled-for-disk-encryption true \
       --enabled-for-template-deployment true \
       --location eastus
       --query properties.vaultUri \
       --sku standard 
   ```

    > [!NOTE]
    > `--name` 옵션의 값은 Azure 구독 내에서 고유해야 합니다.

   아래 표는 위에 표시된 옵션을 설명합니다.

   | 매개 변수 | 설명 |
   |---|---|
   | `enabled-for-deployment` | [Key Vault 배포 옵션](/cli/azure/keyvault)을 지정합니다. |
   | `enabled-for-disk-encryption` | [Key Vault 암호화 옵션](/cli/azure/keyvault)을 지정합니다. |
   | `enabled-for-template-deployment` | [Key Vault 암호화 옵션](/cli/azure/keyvault)을 지정합니다. |
   | `location` | 리소스 그룹을 호스팅할 [Azure 지역](https://azure.microsoft.com/regions/)을 지정합니다. |
   | `name` | Key Vault의 고유한 이름을 지정합니다. |
   | `query` | 응답에서 Key Vault URI를 검색합니다.  이 자습서를 완료하려면 URI가 필요합니다. |
   | `sku` | [Key Vault SKU 옵션](/cli/azure/keyvault)을 지정합니다. |

   Azure CLI는 나중에 사용하려는 Key Vault의 URI를 표시합니다. 예:

   ```output
   "https://contosokv.vault.azure.net/"
   ```

1. 해당 관리 ID에서 `get` 및 `list` 작업을 허용하도록 Key Vault를 구성합니다.  `object-id` 값은 위 `az ad sp create-for-rbac` 명령의 `appId`입니다.

   ```azurecli
   az keyvault set-policy --name contosokv --spn http://ejbcontososp --secret-permissions get list
   ```

   출력은 Key Vault에 대한 정보로 가득 찬 JSON 개체입니다.  값이 `Microsoft.KeyVault/vaults`인 `type` 항목이 있습니다.

   아래 표는 위에 표시된 속성을 설명합니다.

   | 매개 변수 | Description |
   |---|---|
   | name | Key Vault 이름입니다. |
   | spn | 위의 `az ad sp create-for-rbac` 명령 출력의 `name`입니다. |
   | secret-permissions | 명명된 보안 주체에서 허용할 작업 목록입니다. |

    > [!NOTE]
    > 최소 권한의 원칙으로 리소스에 가능한 최소 권한 세트를 부여하는 것을 권장하지만 Key Vault 통합 설계에는 `get` 및 `list` 이상이 필요합니다.

1. 새 Key Vault에 비밀을 저장합니다.  일반적인 사용 사례는 JDBC 연결 문자열을 저장하는 것입니다.  예를 들면 다음과 같습니다.

   ```azurecli
   az keyvault secret set --name "connectionString" \
       --vault-name "contosokv" \
       --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
   ```

   아래 표는 위에 표시된 옵션을 설명합니다.

   | 매개 변수 | 설명 |
   |---|---|
   | `name` | 비밀 이름을 지정합니다. |
   | `value` | 비밀 값을 지정합니다. |
   | `vault-name` | 앞의 Key Vault 이름을 지정합니다. |

   Azure CLI가 다음 예제처럼 비밀 만들기 결과를 표시합니다.

   ```json
   {
     "attributes": {
       "created": "2020-08-24T21:48:09+00:00",
       "enabled": true,
       "expires": null,
       "notBefore": null,
       "recoveryLevel": "Purgeable",
       "updated": "2020-08-24T21:48:09+00:00"
     },
     "contentType": null,
     "id": "https://contosokv.vault.azure.net/secrets/connectionString/123456789abcdef123456789abcdef",
     "kid": null,
     "managed": null,
     "tags": {
       "file-encoding": "utf-8"
     },
     "value": "jdbc:sqlserver://.database.windows.net:1433;database=DATABASE;"
   }
   ```

Key Vault를 만들고 비밀을 저장했으면, 다음 섹션에서는 Spring Initializr를 사용하여 앱을 만드는 방법을 보여줍니다.

## <a name="create-the-app-with-spring-initializr"></a>Spring Initializr로 앱 만들기

이 섹션에서는 Spring Initializr와 `RestController`를 사용하여 Spring Boot 애플리케이션을 로컬에서 만들고 실행하는 방법을 보여줍니다.

1. [https://www.microsoft.com]\(<https://start.spring.io/>) 로 이동합니다.
1. 이 목록 다음에 나오는 그림에 표시된 대로 항목을 선택합니다.
   1. **프로젝트**: `Maven Project`
   1. **언어**: `Java`
   1. **Spring Boot**: `2.3.3`
   1. **그룹**: `com.contoso`(여기에 유효한 Java 패키지 이름을 입력할 수 있습니다.)
   1. **아티팩트**: *keyvault*(여기에 유효한 Java 클래스 이름을 입력할 수 있습니다.)
   1. **패키징**: `Jar`
   1. **Java**: `11`(8을 선택할 수 있지만 이 자습서에서는 11로 유효성이 검사되었습니다.)
1. **종속성 추가...** 를 선택합니다.
1. 텍스트 필드에 `Spring Web`을 입력하고 Ctrl + Enter를 누릅니다.
1. 텍스트 필드에 `Azure Key Vault`를 입력하고 Enter를 누릅니다.  화면이 다음과 같이 표시됩니다.
   :::image type="content" source="media/configure-spring-boot-starter-java-app-with-azure-key-vault/spring-initializr-choices.png" alt-text="올바른 항목이 선택된 Spring Initializr":::
1. 페이지 맨 아래에서 **생성**을 선택합니다.
1. 메시지가 표시되면 로컬 컴퓨터의 경로에 프로젝트를 다운로드합니다.  설명을 위해 현재 사용자의 홈 디렉터리에 *keyvault* 디렉터리를 사용합니다.  위의 값은 해당 디렉터리에 *keyvault.zip* 파일을 제공합니다.

다음 단계에 따라 애플리케이션을 검사하고 로컬에서 실행합니다.

1. *keyvault.zip* 파일의 압축을 풉니다.  파일 레이아웃은 다음과 같은 모습니다.  이 자습서에서는 *test* 디렉터리와 해당 콘텐츠를 무시합니다.

   ```bash
   ├── HELP.md
   ├── mvnw
   ├── mvnw.cmd
   ├── pom.xml
   └── src
       ├── main
       │   ├── java
       │   │   └── com
       │   │       └── contoso
       │   │           └── keyvault
       │   │               └── KeyvaultApplication.java
       │   └── resources
       │       ├── application.properties
       │       ├── static
       │       └── templates
   ```

1. 텍스트 편집기에서 *KeyvaultApplication.java* 파일을 엽니다.  파일을 다음과 같이 수정합니다.

   * 클래스에 `@RestController` 주석을 추가합니다.  `@RestController`는 클래스가 RESTful HTTP 요청에 응답할 수 있음을 Spring Boot에 알립니다.
   * `@GetMapping(get)` 주석이 있는 메서드가 클래스에 있습니다.  `@GetMapping`은 해당 메서드에 대한 `/get` 경로를 사용하여 HTTP 요청을 보내도록 Spring Boot에 알려서, 해당 메서드의 응답이 HTTP 클라이언트에 반환되도록 합니다.
   * 클래스에 프라이빗 인스턴스 변수 `connectionString`이 있습니다.  이 인스턴스 변수의 값은 `get()` 메서드에서 반환됩니다.

   ```java
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.RestController;

   @SpringBootApplication
   @RestController
   public class KeyvaultApplication {

      public static void main(String[] args) {
        SpringApplication.run(KeyvaultApplication.class, args);
      }

     @GetMapping("get")
     public String get() {
       return connectionString;
     }

     private String connectionString = "defaultValue\n";  

     public void run(String... varl) throws Exception {
       System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
     }  

   }
   ```

1. *pom.xml* 파일이 있는 최상위 수준 *keyvault* 디렉터리에서 `mvn spring-boot:run`을 입력합니다.  
1. 명령 출력의 **Completed initialization** 메시지는 서버가 준비되었다는 의미입니다.  별도의 셸 창에 다음 명령을 입력합니다.

   ```bash
   $ curl http://localhost:8080/get
   ```

   출력에 `defaultValue`가 표시됩니다.

1. `mvn spring-boot:run`에서 실행되는 프로세스를 종료합니다.  Ctrl + C를 입력하거나 `jps` 명령을 사용하여 `Launcher` 프로세스의 PID를 가져오고 종료할 수 있습니다.

다음 섹션에서는 로컬에서 실행 중인 애플리케이션에 Key Vault 통합을 추가하는 방법을 보여줍니다.

## <a name="add-key-vault-integration-to-the-app"></a>앱에 Key Vault 통합 추가

다음 단계에서는 Spring Boot 애플리케이션 `KeyvaultApplication`에 필요한 수정 사항을 보여줍니다.

Key Vault를 통해 애플리케이션 코드에서 비밀을 외부화하는 것이 가능하듯이 Spring 구성을 통해 코드에서 구성을 외부화할 수 있습니다.  Spring 구성의 가장 간단한 형태는 *application.properties* 파일입니다.  Maven 프로젝트에서는 *src/main/resources/application.properties*에 이 파일이 있습니다.  Spring Initializer는 이 위치에 길이가 0인 파일이 포함되어 있습니다.

다음 단계를 수행하여 이 파일에 필요한 구성을 추가합니다.

1. 편집기에서 *src/main/resources/application.properties*를 열고 다음 내용에서 Azure 구독에 대한 값을 조정하여 포함시킵니다.

   ```txt
   azure.keyvault.client-id=685on005-ns8q-4o04-8s16-n7os38o2ro5n
   azure.keyvault.client-key=4bt.lCKJKlbYLn_3XF~wWtUwyHU0jKggu2
   azure.keyvault.enabled=true
   azure.keyvault.tenant-id=72s988os-86s1-41ns-91no-2q7pq011qo47
   azure.keyvault.uri=https://contosokv.vault.azure.net/
   ```

   아래 표는 위에 표시된 속성을 설명합니다.

   | 매개 변수 | 설명 |
   |---|---|
   | azure.keyvault.client-id | `az ad sp create-for-rbac`에서 반환된 JSON의 `appId`입니다.|
   | azure.keyvault.client-key | `az ad sp create-for-rbac`에서 반환된 JSON의 `password`입니다.|
   | azure.keyvault.enabled | 이 구성은 배포 시 `enabled` 또는 `disabled`를 설정해야 하는 경우 유용할 수 있습니다.  Spring 구성에 대한 자세한 내용은 [Spring 설명서](https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/htmlsingle/#boot-features-external-config)를 참조하세요.
   | azure.keyvault.tenant-id | `az ad sp create-for-rbac`에서 반환된 JSON의 `tenant`입니다.|
   | azure.keyvault.uri | 위의 `az keyvault create` 명령에서 출력되는 값입니다. |

   사용 가능한 전체 속성 목록은 [속성 참조](https://aka.ms/azure-spring-boot-starter-keyvault-secrets)에 설명되어 있습니다.

1. 파일을 저장하고 닫습니다.

*KeyvaultApplication.java* 파일(또는 해당하는 클래스 이름)을 간단히 변경합니다.

1. 텍스트 편집기에서 *src/main/java/com/contoso/keyvault/KeyvaultApplication.java*를 엽니다.
1. 아래의 가져오기를 추가합니다.

   ```java
   import org.springframework.beans.factory.annotation.Value;
   ```

1. `connectionString` 인스턴스 변수에 주석을 추가합니다.

   ```java
   @Value("${connectionString}")
   private String connectionString;  
   ```

   Key Vault 통합은 Key Vault의 값으로 채워지는 Spring `PropertySource`를 제공합니다.  [참조 설명서](https://aka.ms/azure-spring-boot-starter-keyvault-secrets)에 몇 가지 추가 구현 정보가 제공됩니다.

1. *pom.xml* 파일이 있는 최상위 수준 *keyvault* 디렉터리에서 `mvn clean package spring-boot:run`을 입력합니다.  
1. 명령 출력의 **initialization completed** 메시지는 서버가 준비되었다는 의미입니다.  별도의 셸 창에 다음 명령을 입력합니다.

   ```bash
   $ curl http://localhost:8080/get
   ```

   출력에 `defaultValue` 대신 `jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE`가 표시됩니다.

1. `mvn spring-boot:run`에서 실행되는 프로세스를 종료합니다.  Ctrl + C를 입력하거나 `jps` 명령을 사용하여 `Launcher` 프로세스의 PID를 가져오고 종료할 수 있습니다.


다음 섹션에서는 이 앱을 Azure App Service에 배포하는 방법을 보여줍니다.

## <a name="deploy-to-azure-app-service"></a>Azure App Service에 배포

이 섹션의 단계에 따라 Azure App Service에 `KeyvaultApplication`을 배포합니다.

### <a name="use-the-azure-maven-web-app-plugin-to-deploy-the-application-to-azure-app-service"></a>Azure Maven 웹앱 플러그인을 사용하여 Azure App Service에 애플리케이션을 배포합니다.

다음 단계를 수행하여 POM이 Azure App Service에 `KeyvaultApplication`을 배포할 준비가 되도록 합니다.

1. 최상위 수준 *keyvault* 디렉터리에서 *pom.xml* 파일을 엽니다.
1. `<build><plugins>` 섹션에서 이 XML을 삽입하여 `azure-webapp-maven-plugin`을 추가합니다.

   ```xml
    <plugin>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-webapp-maven-plugin</artifactId>
     <version>1.9.1</version>
    </plugin>
   ```

   > [!NOTE]
   > 형식은 신경 쓸 필요가 없습니다.  이 프로세스 중에 `azure-webapp-maven-plugin`이 전체 POM의 형식을 다시 지정합니다.

1. *pom.xml*을 저장하고 닫습니다.
1. 명령줄에서 새로 추가된 플러그인의 `config` 목표를 호출합니다.  Maven 플러그인에 몇 가지 질문이 표시되고, 답변에 기반하여 *pom.xml* 파일이 편집됩니다.  사용자가 POM을 추가로 편집합니다.

   ```bash
   mvn azure-webapp:config
   ```

1. OS에 `linux`가 선택되어 있는지 확인합니다.
1. `javaVersion`의 경우 Spring Initializr에서 선택한 Java 버전이 선택되어 있는지 확인합니다.  위에서는 `11`을 선택했으므로 여기에 11이 선택되어 있습니다.
1. 나머지 질문에 대해 기본값을 수락합니다.
1. 확인하라는 메시지가 표시되면, 계속하려면 Y로 답변하고 질문에 다시 대답하려면 N으로 답변합니다.  플러그인 실행이 완료되면 POM을 편집할 준비가 된 것입니다.

다음 단계에 따라 POM에 필요한 추가 편집을 수행합니다.

1. 최상위 수준 *keyvault* 디렉터리에서 *pom.xml* 파일을 엽니다.
1. '<plugins> 섹션에서 `azure-webapp-maven-plugin` 항목을 찾습니다.
1. `<resourceGroup>`, `<appName>`, `<region>` 값을 수정합니다.  
   1. `<resourceGroup>`의 값을 Key Vault를 만들 때 지정한 값으로 설정합니다.
   1. 구독 내에서 고유한 `<appName>`에 적절한 값을 선택합니다.
   1. `<region>`의 값을 Key Vault를 만들 때 지정한 값으로 설정합니다.
1. 서버가 TCP 포트 80에서 수신 대기하도록 하는 `<appSettings>` 요소를 포함합니다.
1. `azure-webapp-maven-plugin`에 대해 수정된 전체 `<plugin>`이 다음에 표시됩니다.  직접 수정해야 하는 값은 `*`로 표시됩니다.

   ```xml
   <plugins> 
     <plugin> 
       <groupId>org.springframework.boot</groupId>  
       <artifactId>spring-boot-maven-plugin</artifactId> 
     </plugin>  
     <plugin> 
       <groupId>com.microsoft.azure</groupId>  
       <artifactId>azure-webapp-maven-plugin</artifactId>  
       <version>1.9.1</version>  
       <configuration>
         <schemaVersion>V2</schemaVersion>
         *<resourceGroup>contosorg</resourceGroup>
         *<appName>contosokeyvault</appName>
         <pricingTier>P1v2</pricingTier>
         *<region>eastus</region>
         <runtime>
           <os>linux</os>
           <javaVersion>java11</javaVersion>
           <webContainer>java11</webContainer>
         </runtime>
         *<!-- Begin of App Settings  -->
         *<appSettings>
         *  <property>
         *    <name>JAVA_OPTS</name>
         *    <value>-Dserver.port=80</value>
         *  </property>
         *</appSettings>
         *<!-- End of App Settings  -->          
         <deployment>
           <resources>
             <resource>
               <directory>${project.basedir}/target</directory>
               <includes>
                 <include>*.jar</include>
               </includes>
             </resource>
           </resources>
         </deployment>
       </configuration>
     </plugin> 
   </plugins>
   ```

1. POM을 저장하고 닫습니다.
1. Azure App Service에 앱을 배포합니다.

   ```bash
   mvn -DskipTests clean package azure-webapp:deploy
   ```

1. 이 명령은 제어할 수 없는 여러 요인에 따라 몇 분 정도 걸릴 수 있습니다.  다음과 같은 출력이 표시되면 앱이 성공적으로 배포된 것입니다.

   ```bash
   [INFO] Deploying the zip package contosokeyvault-22b7c1a3-b41b-4082-a9f0-9339723fa36a11893059035499017844.zip...
   [INFO] Successfully deployed the artifact to https://contosokeyvault.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ------------------------------------------------------------------------
   [INFO] Total time:  01:45 min
   [INFO] Finished at: 2020-08-16T22:47:48-04:00
   [INFO] ------------------------------------------------------------------------
   ```

1. 배포가 완료될 때까지 3~5분 정도 기다립니다.  그런 다음, 위와 같은 `curl` 명령을 사용하여 배포에 액세스할 수 있지만 이번에는 `BUILD SUCCESS` 출력에 표시된 호스트 이름을 사용합니다.  예를 들어 이 `curl` 명령 출력은 성공을 나타냅니다.

   ```bash
   curl https://contosokeyvault.azurewebsites.net/get
   jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;
   ```

이제 Azure App Service에 앱을 배포했습니다.

## <a name="redeploy-to-azure-app-service-and-use-managed-identities-for-azure-resources"></a>Azure App Service에 다시 배포하고 Azure 리소스에 대한 관리 ID 사용

이 섹션에서는 앱의 Azure 리소스와 ID를 연결하는 방법을 설명합니다.  Azure에서 보안이 적용되고 액세스가 추적될 수 있도록 하는 데 필요합니다.  사용한 리소스에 대해서만 지불하는 것이 클라우드 컴퓨팅의 기본 원칙입니다.  이러한 세분화된 리소스 추적은 모든 리소스가 ID와 연결된 경우에만 가능합니다.  Azure App Service와 Azure Key Vault는 Azure 리소스에 대한 관리 ID를 활용하는 여러 Azure 서비스 중 두 가지입니다.  [Azure 리소스에 대한 관리 ID란?](/azure/active-directory/managed-identities-azure-resources/overview)에서 이 중요한 기술에 대해 자세히 알아보세요.

> [!NOTE]
> Azure 리소스에 대한 관리 ID는 이전에 MSI(관리 서비스 ID)로 알려진 서비스에 대한 새 이름입니다.

다음 단계에 따라 Azure App Service 앱에 대한 관리 ID를 만든 다음, 해당 ID가 Key Vault에 액세스하도록 허용합니다.

1. App Service 앱에 대한 관리 ID를 만듭니다.  옵션을 POM의 `<resourceGroup>` 및 `<appName>` 요소 값으로 바꿉니다.

   ```azurecli
   az webapp identity assign --resource-group contosorg --name contosokeyvault
   ```

   출력은 다음과 비슷합니다.  다음 단계를 위해 `principalId` 값을 적어둡니다.

   ```json
   {
     "principalId": "2r7s6r00-92o9-4sq7-n10r-popq294ssr8s",
     "tenantId": "72s988os-86s1-41ns-91no-2q7pq011qo47",
     "type": "SystemAssigned",
     "userAssignedIdentities": null
   }
   ```

1. 이전 단계에서 만든 Azure 리소스에 대한 관리 ID의 이름을 지정하도록 *application.properties*를 편집합니다.

   1. `azure.keyvault.client-key`를 제거합니다.
   1. 이전 단계의 `principalId` 값을 갖도록 `azure.keyvault.client-id`를 업데이트합니다.  완성된 파일은 다음과 같은 모습입니다.

   ```bash
   azure.keyvault.client-id=56rqs994-0o66-43o3-9roo-8e3534d0cb23
   azure.keyvault.enabled=true
   azure.keyvault.tenant-id=72s988os-86s1-41ns-91ab-2q7pq011qo47
   azure.keyvault.uri=https://contosokv.vault.azure.net/    
   ```

1. Key Vault를 구성하면 관리 ID의 `get` 및 `list` 작업이 가능합니다.  `object-id`의 값은 이전 출력의 `principalId`입니다.

   ```azurecli
   az keyvault set-policy --name contosokv \
     --object-id 2r7s6r00-92o9-4sq7-n10r-popq294ssr8s --secret-permissions get list
   ```

   출력은 Key Vault에 대한 정보로 가득 찬 JSON 개체입니다.  값이 `Microsoft.KeyVault/vaults`인 `type` 항목이 있습니다.

   아래 표는 위에 표시된 속성을 설명합니다.

   | 매개 변수 | Description |
   |---|---|
   | name | Key Vault 이름입니다. | 
   | object-id | 이전 명령의 `principalId`입니다. |
   | secret-permissions | 명명된 보안 주체에서 허용할 작업 목록입니다. |

1. 애플리케이션을 패키지하고 다시 배포합니다.

   ```bash
   mvn -DskipTests clean package azure-webapp:deploy
   ```

1. 추가적으로, 배포가 안정될 때까지 몇 분 더 기다립니다.  그런 다음, 위와 같은 `curl` 명령을 사용하여 배포에 연결할 수 있지만 이번에는 `BUILD SUCCESS` 출력에 표시된 호스트 이름을 사용합니다.  예를 들어 이 `curl` 명령 출력은 성공을 나타냅니다.

   ```bash
   curl https://contosokeyvault.azurewebsites.net/get
   jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;
   ```

`defaultValue`를 반환하는 대신 `connectionString`의 값을 조회하여 Key Vault에서 반환합니다.  다음 섹션에서는 Azure Spring Cloud에 동일한 앱을 배포합니다.

## <a name="deploy-to-azure-spring-cloud"></a>Azure Spring Cloud에 배포

Azure Spring Cloud는 Azure에서 Spring Boot 애플리케이션을 배포하고 실행할 수 있는 완전 관리형 플랫폼입니다.  Azure Spring Cloud에 대한 개요는 [Azure Spring Cloud란?](/azure/spring-cloud/spring-cloud-overview)을 참조하세요.

이 섹션에서는 Azure Spring Cloud의 새 인스턴스로 이전에 만든 기존 Spring Boot 앱 및 Key Vault를 사용합니다.

다음 단계에서는 Azure Spring Cloud 리소스를 만들고 여기에 앱을 배포하는 방법을 보여줍니다.  [필수 구성 요소](#prerequisites)에 표시된 대로 Azure Spring Cloud용 Azure CLI 확장을 설치했는지 확인합니다.

1. 서비스 인스턴스의 이름을 결정합니다.  Azure 구독 내에서 Azure Spring Cloud를 사용하려면 Azure Spring Cloud 유형의 Azure 리소스를 만들어야 합니다.  다른 모든 Azure 리소스와 마찬가지로 서비스 인스턴스는 리소스 그룹 내에 유지되어야 합니다.  이미 만든 리소스 그룹을 사용하여 서비스 인스턴스를 유지합니다.  다음 명령을 사용하여 서비스 인스턴스를 만듭니다. 

   ```bash
   az spring-cloud create --resource-group "contosorg" --name "contososvc" 
   ```

   이 명령을 완료하는 데 몇 분 정도 걸립니다.

1. 서비스 내에서 Spring Cloud 앱을 만듭니다.

   ```bash
   az spring-cloud app create --resource-group "contosorg" --name "contosoascsapp" --assign-identity --is-public true
     --runtime-version Java_11 --service "contososvc"
   ```

   아래 표는 위에 표시된 옵션을 설명합니다.

   | 매개 변수 | 설명 |
   |---|---|
   | assign-identity | 서비스가 Azure 리소스의 관리 ID에 대한 ID를 만들도록 합니다. |
   | is-public | 서비스에 공용 DNS 도메인 이름을 할당합니다. |
   | name | 앱의 이름입니다. |
   | resource-group | 기존 서비스 인스턴스를 만든 리소스 그룹의 이름입니다. |
   | runtime-version | Java 런타임 버전입니다.  **이 값은 위의 Spring Initializr에서 선택한 값과 일치해야 합니다.** |
   | 서비스 | 기존 서비스의 이름입니다. |

   *service*와 *app*의 차이를 이해하려면 [Azure Spring Cloud의 앱 및 배포 이해](/azure/spring-cloud/spring-cloud-concept-understand-app-and-deployment)를 참조하세요.

1. Azure 리소스에 대한 관리 ID를 가져옵니다.  이 앱에서 액세스를 허용하도록 기존 Key Vault를 구성하는 데 사용합니다.

   ```bash
   SERVICE_IDENTITY=$(az spring-cloud app show --resource-group "contosorg" --name "contosoascsapp" --service "contososvc" | jq -r '.identity.principalId')
   az keyvault set-policy --name "contosokv" --object-id <the value of the environment variable SERVICE_IDENTITY> --secret-permissions set get list
   ```

1. 기존 Spring Boot 앱에는 필요한 구성이 포함된 *application.properties* 파일이 이미 있으므로 다음 명령을 사용하여 이 앱을 Spring Cloud에 직접 배포할 수 있습니다.  POM을 포함하는 디렉터리에서 명령을 실행합니다.

   ```bash
   az spring-cloud app deploy --resource-group "contosorg" --name "contosoascsapp" --jar-path target/keyvault-0.0.1-SNAPSHOT.jar \
     --service "contososvc"
   ```

   이 명령은 서비스 내의 앱 내에서 배포를 만듭니다.  서비스 인스턴스, 앱 및 배포의 개념에 대한 자세한 내용은 [Azure Spring Cloud의 앱 및 배포 이해](/azure/spring-cloud/spring-cloud-concept-understand-app-and-deployment)를 참조하세요.

   배포에 성공하지 못하면 [애플리케이션 로그 구성](https://aka.ms/azure-spring-cloud-configure-logs)에 설명된 대로 문제 해결을 위해 로그를 구성합니다.  로그에는 문제를 진단하고 해결하는 데 유용한 정보가 있을 수 있습니다.

1. 앱이 성공적으로 배포되면 `curl`을 사용하여 Key Vault 통합이 작동하는지 확인할 수 있습니다.  `--is-public`을 지정했기 때문에 서비스의 기본 URL은 `https://<service name>-<app name>.azuremicroservices.io/`입니다.  적절한 값을 바꾸고 `@GetMapping` 주석의 값을 추가하면 다음 `curl` 명령이 됩니다.

   ```bash
   curl https://contososvc-contosoascsapp.azuremicroservices.io/get
   ```

   출력에 `jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE`가 표시됩니다.

## <a name="summary"></a>요약

**Spring Initializr**를 사용하여 새로운 Java 웹 애플리케이션을 만들었습니다.  중요한 정보를 저장하기 위해 Azure Key Vault를 만든 다음, Key Vault의 정보를 검색하도록 애플리케이션을 구성했습니다.  로컬에서 테스트한 후 Azure App Service 및 Azure Spring Cloud에 앱을 배포했습니다.

## <a name="next-steps"></a>다음 단계

Spring과 Azure에 대한 자세한 사항은 Azure의 Spring 설명서 센터를 참조합니다.

> [!div class="nextstepaction"]
> [Application Insights를 사용하도록 Spring Boot Initializer 앱 구성](configure-spring-boot-java-applicationinsights.md)
