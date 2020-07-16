---
title: Azure Key Vault에 Spring Boot Starter를 사용하는 방법
description: Azure Key Vault 스타터에 Spring Boot Initializer 앱을 구성하는 방법을 알아봅니다.
services: key-vault
documentationcenter: java
ms.date: 10/29/2019
ms.service: key-vault
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.custom: devx-track-java
ms.openlocfilehash: 51252278d7b0cec6de1920c5d6c81a20d164e7df
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379247"
---
# <a name="how-to-use-the-spring-boot-starter-for-azure-key-vault"></a>Azure Key Vault에 Spring Boot Starter를 사용하는 방법

이 문서에서는 **[Spring Initializr]** 를 사용하여 앱을 만드는 방법을 보여 줍니다. 여기서는 Spring Boot Starter를 Azure Key Vault에 사용하여 키 자격 증명 모음에 비밀 형태로 저장된 연결 문자열을 검색합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음 필수 구성 요소가 필요합니다.

* Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택]을 활성화하거나 [체험판 Azure 계정]에 등록할 수 있습니다.
* 지원되는 JDK(Java Development Kit) Azure에서 개발하는 경우 사용할 수 있는 JDK에 대한 자세한 내용은 <https://aka.ms/azure-jdks>를 참조하세요.
* [Apache Maven](http://maven.apache.org/), 버전 3.0 이상

## <a name="create-an-app-using-spring-initializr"></a>Spring Initialzr를 사용하여 앱 만들기

다음 절차에서는 Spring Initializr를 사용하여 애플리케이션을 만듭니다.

1. [https://www.microsoft.com]\(<https://start.spring.io/>) 로 이동합니다.

1. **Java**를 사용하여 **Maven** 프로젝트를 생성할지 지정합니다.  

1. 애플리케이션에 대한 **그룹** 및 **아티팩트** 이름을 입력합니다.

1. **종속성** 섹션에서 **Azure Key Vault**를 입력합니다.

1. 페이지의 아래쪽으로 스크롤하고 **생성**을 클릭합니다.

   ![Spring Boot 프로젝트 생성][secrets-01]

1. 메시지가 표시되면 로컬 컴퓨터의 경로에 프로젝트를 다운로드합니다.

## <a name="sign-into-azure"></a>Azure에 로그인

다음 절차에서는 Azure CLI에서 사용자를 인증합니다.

1. 명령 프롬프트를 엽니다.

1. Azure CLI를 사용하여 Azure 계정에 로그인합니다.

   ```azurecli
   az login
   ```

지시에 따라 로그인 프로세스를 완료합니다.

1. 구독 나열:

   ```azurecli
   az account list
   ```

   Azure가 구독 목록을 반환하며 사용하려는 구독의 GUID를 복사해야 합니다. 예를 들어 다음과 같습니다.

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "ssssssss-ssss-ssss-ssss-ssssssssssss",
       "isDefault": true,
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

1. 다음 예제처럼 Azure에 사용하려는 계정의 GUID를 지정합니다.

   ```azurecli
   az account set -s ssssssss-ssss-ssss-ssss-ssssssssssss
   ```

## <a name="create-a-new-azure-key-vault"></a>새 Azure Key Vault 만들기

다음 절차에서는 키 자격 증명 모음을 만들고 초기화합니다.

1. 다음 예제처럼 키 자격 증명 모음에사용할 Azure 리소스에 대한 리소스 그룹을 만듭니다.

   ```azurecli
   az group create --name vged-rg2 --location westus
   ```

   위치:

   | 매개 변수 | Description |
   |---|---|
   | `name` | 리소스 그룹의 고유한 이름을 지정합니다. |
   | `location` | 리소스 그룹을 호스팅할 [Azure 지역](https://azure.microsoft.com/regions/)을 지정합니다. |

   Azure CLI가 다음 에제처럼 리소스 그룹 만들기 결과를 표시합니다.  

   ```json
   {
     "id": "/subscriptions/ssssssss-ssss-ssss-ssss-ssssssssssss/resourceGroups/vged-rg2",
     "location": "westus",
     "managedBy": null,
     "name": "vged-rg2",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```

2. 리소스 그룹에서 새 키 자격 증명 모음을 만듭니다. 예를 들어 다음과 같습니다.

   ```azurecli
   az keyvault create --resource-group vged-rg2 \
       --name vgedkeyvault \
       --enabled-for-deployment true \
       --enabled-for-disk-encryption true \
       --enabled-for-template-deployment true \
       --sku standard --query properties.vaultUri \
       --location westus
   ```

   위치:

   | 매개 변수 | Description |
   |---|---|
   | `name` | 키 자격 증명 모음의 고유한 이름을 지정합니다. |
   | `enabled-for-deployment` | [키 자격 증명 모음 배포 옵션](/cli/azure/keyvault)을 지정합니다. |
   | `enabled-for-disk-encryption` | [키 자격 증명 모음 암호화 옵션](/cli/azure/keyvault)을 지정합니다. |
   | `enabled-for-template-deployment` | [키 자격 증명 모음 암호화 옵션](/cli/azure/keyvault)을 지정합니다. |
   | `sku` | [키 자격 증명 모음 SKU 옵션](/cli/azure/keyvault)을 지정합니다. |
   | `query` | 이 자습서를 완료하는 데 필요한 키 자격 증명 모은 URI인 값을 응답에서 검색합니다. |
   | `location` | 리소스 그룹을 호스팅할 [Azure 지역](https://azure.microsoft.com/regions/)을 지정합니다. |

   Azure CLI가 나중에 사용하게 되는 키 자격 증명 모음의 URI를 표시합니다. 예를 들어 다음과 같습니다.  

   ```output
   "https://vgedkeyvault.vault.azure.net"
   ```

3. 새 키 자격 증명 모음에 비밀을 저장합니다. 예를 들어 다음과 같습니다.

   ```azurecli
   az keyvault secret set --vault-name "vgedkeyvault" \
       --name "connectionString" \
       --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
   ```

   위치:

   | 매개 변수 | Description |
   |---|---|
   | `vault-name` | 앞의 키 자격 증명 모음 이름을 지정합니다. |
   | `name` | 비밀 이름을 지정합니다. |
   | `value` | 비밀 값을 지정합니다. |

   Azure CLI가 다음 예제처럼 비밀 만들기 결과를 표시합니다.  

   ```json
   {
     "attributes": {
       "created": "2017-12-01T09:00:16+00:00",
       "enabled": true,
       "expires": null,
       "notBefore": null,
       "recoveryLevel": "Purgeable",
       "updated": "2017-12-01T09:00:16+00:00"
     },
     "contentType": null,
     "id": "https://vgedkeyvault.vault.azure.net/secrets/connectionString/123456789abcdef123456789abcdef",
     "kid": null,
     "managed": null,
     "tags": {
       "file-encoding": "utf-8"
     },
     "value": "jdbc:sqlserver://.database.windows.net:1433;database=DATABASE;"
   }
   ```

## <a name="configure-and-compile-your-app"></a>앱 구성 및 컴파일

애플리케이션을 구성하고 컴파일하려면 다음 절차를 수행합니다.

1. 앞서 디렉터리에 다운로드한 Spring Boot 프로젝트 아카이브 파일에서 파일을 추출합니다.

2. 프로젝트에서 *src/main/resources* 폴더로 이동하고 텍스트 편집기에서 *application.properties* 파일을 엽니다.

3. 이 자습서의 앞부분에서 완료한 단계의 값을 사용하여 키 자격 증명 모음의 값을 추가합니다. 예를 들어 다음과 같습니다.

   ```yaml
   azure.keyvault.enabled=true
   azure.keyvault.uri=Your-Keyvault-uri
   azure.keyvault.client-id=Your-Client-ID
   azure.keyvault.tenant-id=Your-Tenant-ID
   ```

   위치:

   |          매개 변수          |                                 Description                                 |
   |-----------------------------|-----------------------------------------------------------------------------|
   |    `azure.keyvault.uri`     |           키 자격 증명 모음을 만든 URI를 지정합니다.           |
    
    
4. 프로젝트의 기본 소스 코드 파일로 이동합니다. 예를 들어 */src/main/java/com/vged/secrets*입니다.

5. 텍스트 편집기에서 애플리케이션의 메인 Java 파일(예: *SecretsApplication.java*)을 열어 다음 줄을 추가합니다.

   ```java
   package com.vged.secrets;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.beans.factory.annotation.Value;
   import org.springframework.boot.CommandLineRunner;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.RestController;
   
   @SpringBootApplication
   @RestController
   public class SecretsApplication implements CommandLineRunner {

      @Value("${connectionString}")
      private String connectionString;

      public static void main(String[] args) {
         SpringApplication.run(SecretsApplication.class, args);
      }
   
      @GetMapping("get")
      public String get() {
         return connectionString;
      }
   
      public void run(String... varl) throws Exception {
         System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
      }
   }
   ```
   이 코드 예제에서는 키 자격 증명 모음에서 연결 문자열을 검색하여 `https://{your-appservice-name}.azurewebsites.net/get` URL에 표시합니다.

6. Java 파일을 저장하고 닫습니다.

7. 테스트를 사용하지 않도록 설정하고, Maven을 사용하여 JAR 파일을 빌드합니다.
    
   ```bash
   mvn clean package -Dmaven.test.skip=true
   ```

## <a name="configure-maven-plugin-for-azure-app-service"></a>Azure App Service용 Maven 플러그인 구성

이 섹션에서는 앱을 Azure App Service에 배포할 수 있도록 Spring Boot 프로젝트를 구성하는 방법을 설명합니다.

1.  [Azure App Service용 Maven 플러그 인 구성] 링크를 따릅니다.
    
    이 링크에서는 새 Azure App Service를 만듭니다. 앱을 기존 앱에 배포하려면 `mvn azure-webapp:config` 명령을 사용하여 배포를 다시 구성하고 Application 부분을 구성으로 선택할 수 있습니다.
    
    ```bash
    [INFO] Scanning for projects...                                                     
    [INFO]                                                                              
    [INFO] ----------------------< com.wingtiptoys:secrets >-----------------------     
    [INFO] Building secrets 0.0.1-SNAPSHOT                                              
    [INFO] --------------------------------[ jar ]---------------------------------     
    [INFO]                                                                              
    [INFO] --- azure-webapp-maven-plugin:1.9.0:config (default-cli) @ secrets ---       
    Please choose which part to config                                                  
    1. Application                                                                      
    2. Runtime                                                                          
    3. DeploymentSlot                                                                   
    Enter index to use: 1                                                              
    Define value for appName(Default: ********):                                      
    Define value for resourceGroup(Default: ********):                                 
    Define value for region(Default: ********):                                           
    Define value for pricingTier(Default: P1v2):                                        
    1. b1                                                                               
    2. b2                                                                               
    3. b3                                                                               
    4. d1                                                                               
    5. f1                                                                               
    6. p1v2 [*]                                                                         
    7. p2v2                                                                             
    8. p3v2                                                                             
    9. s1                                                                               
    10. s2                                                                              
    11. s3                                                                              
    Enter index to use:                                                                 
    Please confirm webapp properties                                                                                                          
    ```
    
    또한 *pom.xml*에서 `<azure-webapp-maven-plugin>`의 `<configuration>` 섹션을 직접 편집할 수도 있습니다. `<resourceGroup>`, `<appName>` 및 `<region>` 값을 특정 App Service로 수정합니다.

2. ID를 App Service에 할당하고, 다음 단계에서 `principalId`의 작동을 중지합니다.

   ```bash
   az webapp identity assign --name your-appservice-name \
      --resource-group vged-rg2
   ```
   
3. MSI에 권한을 부여합니다.

   ```bash
   az keyvault set-policy --name vgedkeyvault \
       --object-id your-managed-identity-objectId \
       --secret-permissions get list
   ```

## <a name="deploy-the-app-to-azure-and-run-app-service"></a>Azure에 앱 배포 및 App Service 실행

이제 웹앱을 Azure에 배포할 준비가 되었습니다. 이렇게 하려면 다음 단계를 수행합니다.

1. *pom.xml* 파일이 변경되면 Maven을 사용하여 JAR 파일을 다시 빌드합니다.

   ```bash
   mvn clean package
   ```
   
2. Maven을 사용하여 앱을 Azure에 배포합니다.

   ```bash
   mvn azure-webapp:deploy
   ```
   
3. App Service를 다시 시작합니다.

4. `connectionString`을 가져오려면 브라우저에서 이 URL(`https://{your-appservice-name}.azurewebsites.net/get`)을 확인합니다.
   

## <a name="summary"></a>요약

이 자습서에서는 **[Spring Initializr]** 를 사용하여 새 Java 웹 애플리케이션을 만들었으며, 민감한 정보를 저장하기 위해 Azure Key Vault를 만들었고 Azure Key Vault를 통해 정보를 검색하도록 애플리케이션을 구성했습니다.

## <a name="next-steps"></a>다음 단계

Spring과 Azure에 대한 자세한 사항은 Azure의 Spring 설명서 센터를 참조합니다.

> [!div class="nextstepaction"]
> [Azure의 Spring](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>추가 리소스

Azure Key Vaults를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Key Vault 설명서].

* [Azure Key Vault 시작]

Azure에서 Spring Boot 애플리케이션을 사용 하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure App Service에 Spring Boot 애플리케이션 배포](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)

* [Azure Container Service의 Kubernetes 클러스터에 Spring Boot 애플리케이션 실행](deploy-spring-boot-java-app-on-kubernetes.md)

Java와 함께 Azure를 사용하는 방법에 관한 자세한 정보는 [Java 개발자를 위한 Azure]와 [Azure DevOps 및 Java 사용하기]를 참조하세요.

App Service에 대한 관리 ID를 사용하는 방법에 대한 자세한 내용은 [App Service에 대한 관리 ID 사용]을 참조하세요.

<!-- URL List -->

[Key Vault 설명서]: /azure/key-vault/
[Azure Key Vault 시작]: /azure/key-vault/key-vault-get-started
[Java 개발자를 위한 Azure]: /azure/developer/java/
[체험판 Azure 계정]: https://azure.microsoft.com/pricing/free-trial/
[Azure DevOps 및 Java 사용하기]: /azure/devops/
[MSDN 구독자 혜택]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[App Service에 대한 관리 ID 사용]: /azure/app-service/overview-managed-identity
[Azure App Service용 Maven 플러그 인 구성]: /azure/developer/java/spring-framework/deploy-spring-boot-java-app-with-maven-plugin#configure-maven-plugin-for-azure-app-service

<!-- IMG List -->

[secrets-01]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-01.png
[secrets-02]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-02.png
[secrets-03]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-03.png

[build-application-01]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/build-application-01.png
[build-application-02]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/build-application-02.png
