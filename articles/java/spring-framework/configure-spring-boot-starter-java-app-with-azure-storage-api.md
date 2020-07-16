---
title: Azure Storage API에서 Spring Boot Starter를 사용하는 방법
description: Azure Storage API로 Spring Boot Initializer 앱을 구성하는 방법을 알아봅니다.
services: storage
documentationcenter: java
ms.date: 12/19/2018
ms.service: storage
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage
ms.custom: devx-track-java
ms.openlocfilehash: 35aefc9966a6180834051a9c40d63157d81c4f16
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379207"
---
# <a name="how-to-use-the-spring-boot-starter-with-the-azure-storage-api"></a>Azure Storage API에서 Spring Boot Starter를 사용하는 방법

## <a name="overview"></a>개요

이 문서에서는 **Spring Initializr**를 사용하여 사용자 지정 애플리케이션을 만든 다음 해당 애플리케이션을 사용하여 Azure Storage API를 사용하여 Azure Storage에 액세스하는 방법을 살펴봅니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 수행하기 위해 다음 필수 구성 요소가 필요합니다.

* Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [체험판 Azure 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
* [Azure CLI(명령줄 인터페이스)](/cli/azure/overview)
* 지원되는 JDK(Java Development Kit) Azure에서 개발하는 경우 사용할 수 있는 JDK에 대한 자세한 내용은 <https://aka.ms/azure-jdks>를 참조하세요.
* [Apache Maven](http://maven.apache.org/), 버전 3.0 이상

## <a name="create-a-custom-application-using-the-spring-initializr"></a>Spring Initializr를 사용하여 사용자 지정 애플리케이션 만들기

1. [https://www.microsoft.com]\(<https://start.spring.io/>) 로 이동합니다.

1. **Java**에서 **Maven** 프로젝트를 생성한다고 지정하고, 애플리케이션에 대한 **그룹** 및 **아티팩트** 이름을 입력한 다음, Spring Initializr의 **정식 버전으로 전환**하는 링크를 클릭합니다.

   ![기본 Spring Initializr 옵션](media/configure-spring-boot-starter-java-app-with-azure-storage/spring-initializr-basic.png)

   > [!NOTE]
   >
   > Spring Initializr는 **그룹** 및 **아티팩트** 이름을 사용하여 패키지 이름을 만듭니다(예: *com.contoso.wingtiptoysdemo*).
   >

1. **Azure** 섹션까지 아래로 스크롤하고 **Azure Storage**의 확인란을 선택합니다.

   ![전체 Spring Initializr 옵션](media/configure-spring-boot-starter-java-app-with-azure-storage/spring-initializr-advanced.png)

1. 페이지 하단까지 스크롤하고 버튼을 클릭하여 **프로젝트를 생성**합니다.

   ![전체 Spring Initializr 옵션](media/configure-spring-boot-starter-java-app-with-azure-storage/spring-initializr-generate.png)

1. 메시지가 표시되면 로컬 컴퓨터의 경로에 프로젝트를 다운로드합니다.

   ![사용자 지정 Spring Boot 프로젝트 다운로드](media/configure-spring-boot-starter-java-app-with-azure-storage/download-app.png)

## <a name="sign-into-azure-and-select-the-subscription-to-use"></a>Azure에 로그인하고 사용할 구독 선택

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

## <a name="create-an-azure-storage-account"></a>Azure Storage 계정 만들기

1. 이 문서에서 사용할 Azure 리소스에 대한 리소스 그룹을 만듭니다. 예를 들어 다음과 같습니다.
   ```azurecli
   az group create --name wingtiptoysresources --location westus
   ```
   위치:

   | 매개 변수 | Description |
   |---|---|
   | `name` | 리소스 그룹의 고유한 이름을 지정합니다. |
   | `location` | 리소스 그룹을 호스팅할 [Azure 지역](https://azure.microsoft.com/regions/)을 지정합니다. |

   Azure CLI가 다음 에제처럼 리소스 그룹 만들기 결과를 표시합니다.  

   ```json
   {
     "id": "/subscriptions/ssssssss-ssss-ssss-ssss-ssssssssssss/resourceGroups/wingtiptoysresources",
     "location": "westus",
     "managedBy": null,
     "name": "wingtiptoysresources",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```

2. Spring Boot 앱에 대한 리소스 그룹에서 Azure Storage 계정을 만듭니다. 예를 들어 다음과 같습니다.
   ```azurecli
   az storage account create --name wingtiptoysstorage --resource-group wingtiptoysresources --location westus --sku Standard_LRS
   ```
   위치:

   | 매개 변수 | Description |
   |---|---|
   | `name` | 스토리지 계정에 고유한 이름을 지정합니다. |
   | `resource-group` | 이전 단계에서 만든 리소스 그룹의 이름을 지정합니다. |
   | `location` | 스토리지 계정을 호스팅할 [Azure 지역](https://azure.microsoft.com/regions/)을 지정합니다. |
   | `sku` | `Premium_LRS`, `Standard_GRS`, `Standard_LRS`, `Standard_RAGRS`, `Standard_ZRS` 중 하나를 지정합니다. |

   Azure가 프로비전 상태를 포함하는 긴 JSON 문자열을 반환합니다. 예를 들어 다음과 같습니다.

   ```json
   {
     "id": "/subscriptions/ssssssss-ssss-ssss-ssss-ssssssssssss/...",
     "identity": null,
     "kind": "Storage"
       ...
       ... (A long list of values will be displayed here.)
       ...
     "statusOfPrimary": "available",
     "statusOfSecondary": null,
     "tags": {},
     "type": "Microsoft.Storage/storageAccounts"
   }
   ```

3. 스토리지 계정에 대한 연결 문자열을 검색합니다. 예를 들어 다음과 같습니다.
   ```azurecli
   az storage account show-connection-string --name wingtiptoysstorage --resource-group wingtiptoysresources
   ```
   위치:

   | 매개 변수 | Description |
   | ---|---|
   | `name` | 이전 단계에서 만든 스토리지 계정의 고유 이름을 지정합니다. |
   | `resource-group` | 이전 단계에서 만든 리소스 그룹의 이름을 지정합니다. |

   Azure가 스토리지 계정에 대한 연결 문자열을 포함하는 JSON 문자열을 반환합니다. 예를 들어 다음과 같습니다.

   ```json
   {
     "connectionString": "DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName=wingtiptoysstorage;AccountKey=AbCdEfGhIjKlMnOpQrStUvWxYz=="
   }
   ```

## <a name="configure-and-compile-your-spring-boot-application"></a>Spring Boot 애플리케이션 구성 및 컴파일

1. 디렉터리에 다운로드한 프로젝트 아카이브에서 파일을 추출합니다.

1. 프로젝트에서 *src/main/resources* 폴더로 이동하고 텍스트 편집기에서 *application.properties* 파일을 엽니다.

1. 스토리지 계정의 키를 추가합니다. 예를 들어 다음과 같습니다.
   ```yaml
   azure.storage.connection-string=DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName=wingtiptoysstorage;AccountKey=AbCdEfGhIjKlMnOpQrStUvWxYz==
   ```

1. 프로젝트에서 */src/main/java/com/example/wingtiptoysdemo* 폴더로 이동하고 텍스트 편집기에서 *WingtiptoysdemoApplication.java* 파일을 엽니다.

1. 기존 Java 코드를 컨테이너의 Blob를 나열하는 다음 예제로 바꿉니다.

   ```java
   package com.example.wingtiptoysdemo;

   import com.microsoft.azure.storage.*;
   import com.microsoft.azure.storage.blob.*;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.CommandLineRunner;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;

   import java.net.URISyntaxException;

   @SpringBootApplication
   public class WingtiptoysdemoApplication implements CommandLineRunner {

      @Autowired
      private CloudStorageAccount cloudStorageAccount;

      final String containerName = "mycontainer";

      public static void main(String[] args) {
         SpringApplication.run(WingtiptoysdemoApplication.class, args);
      }

      public void run(String... var1)
             throws URISyntaxException, StorageException {
          // Create a container (if it does not exist).
          createContainerIfNotExists(containerName);
          // Upload a blob to the container.
          uploadTextBlob(containerName);
      }

      private void createContainerIfNotExists(String containerName)
            throws URISyntaxException, StorageException {
         try
         {
            // Create a blob client.
            final CloudBlobClient blobClient = cloudStorageAccount.createCloudBlobClient();
            // Get a reference to a container. (Name must be lower case.)
            final CloudBlobContainer container = blobClient.getContainerReference(containerName);
            // Create the container if it does not exist.
            container.createIfNotExists();
         }
         catch (Exception e)
         {
            // Output the stack trace.
            e.printStackTrace();
         }
      }

      private void uploadTextBlob(String containerName)
            throws URISyntaxException, StorageException {
         try
         {
            // Create a blob client.
            final CloudBlobClient blobClient = cloudStorageAccount.createCloudBlobClient();
            // Get a reference to a container. (Name must be lower case.)
            final CloudBlobContainer container = blobClient.getContainerReference(containerName);
            // Get a blob reference for a text file.
            CloudBlockBlob blob = container.getBlockBlobReference("test.txt");
            // Upload some text into the blob.
            blob.uploadText("Hello World!");
         }
         catch (Exception e)
         {
            // Output the stack trace.
            e.printStackTrace();
         }
      }
   }
   ```
   > [!NOTE]
   >
   > 위의 예제는 *application.properties* 파일에서 정의된 스토리지 계정 설정을 자동으로 연결합니다.
   >

1. 애플리케이션 컴파일 및 실행:
   ```shell
   mvn clean package spring-boot:run
   ```

   애플리케이션이 컨테이너를 만들고 텍스트 파일을 Blob 형태로 컨테이너에 업로드합니다. 이것은 [Azure Portal](https://portal.azure.com)의 스토리지 계정 아래 나열됩니다.

   ![Azure Portal에서 blob 나열](media/configure-spring-boot-starter-java-app-with-azure-storage/list-blobs-in-portal.png)

   > [!NOTE]
   > 
   > 애플리케이션을 컴파일할 때 다음 오류 메시지가 표시될 수 있습니다.
   > 
   > `[INFO] ------------------------------------------------------------------------`<br/>
   > `[INFO] BUILD FAILURE`<br/>
   > `[INFO] ------------------------------------------------------------------------`<br/>
   > `[INFO] Total time: 2.616 s`<br/>
   > `[INFO] Finished at: 2017-11-11T13:14:15Z`<br/>
   > `[INFO] Final Memory: 26M/213M`<br/>
   > `[INFO] ------------------------------------------------------------------------`<br/>
   > `[ERROR] Failed to execute goal org.apache.maven.plugins:maven-surefire-plugin:2`<br/>
   > `.18.1:test (default-test) on project wingtiptoysdemo: Execution default-test of`<br/>
   > `goal org.apache.maven.plugins:maven-surefire-plugin:2.18.1:test failed: The for`<br/>
   > `ked VM terminated without properly saying goodbye. VM crash or System.exit called?`<br/>
   > `[ERROR] Command was /bin/sh -c cd /home/robert/SpringBoot/wingtiptoysdemo && /u`<br/>
   > `sr/lib/jvm/java-8-openjdk-amd64/jre/bin/java -jar /home/robert/SpringBoot/wingt`<br/>
   > `iptoysdemo/target/surefire/surefirebooter6371623993063346766.jar /home/robert/S`<br/>
   > `pringBoot/wingtiptoysdemo/target/surefire/surefire5107893623933537917tmp /home/`<br/>
   > `robert/SpringBoot/wingtiptoysdemo/target/surefire/surefire_01414159391084128068tmp`<br/>
   > `[ERROR] -> [Help 1]`<br/>
   > 
   > 이 경우 Maven Surefire 테스트를 사용하지 않게 설정할 수 있습니다. 이를 위해 다음 플러그인 항목을 *pom.xml* 파일에 추가합니다.
   > 
   > ```xml
   > <plugin>
   >   <groupId>org.apache.maven.plugins</groupId>
   >   <artifactId>maven-surefire-plugin</artifactId>
   >   <version>2.20.1</version>
   >   <configuration>
   >     <skipTests>true</skipTests>
   >   </configuration>
   > </plugin>
   > ```
   > 

## <a name="next-steps"></a>다음 단계

Spring과 Azure에 대한 자세한 사항은 Azure의 Spring 설명서 센터를 참조합니다.

> [!div class="nextstepaction"]
> [Azure의 Spring](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>추가 리소스

Microsoft Azure에서 사용할 수 있는 다른 Spring Boot Starter에 대한 자세한 내용은 [Azure용 Spring Boot Starter](spring-boot-starters-for-azure.md)를 참조하세요.

Azure 기능과 Spring 기반 애플리케이션 통합에 대한 다른 정보는 [Azure의 Spring 프레임워크](/azure/developer/java/spring-framework/)를 참조하세요.

Spring Boot 애플리케이션에서 호출할 수 있는 다른 Azure Storage API에 관한 상세 정보는 다음 문서를 참조하세요.
* [Java에서 Azure Blob Storage를 사용하는 방법](/azure/storage/blobs/storage-java-how-to-use-blob-storage)
* [Java에서 Azure Queue Storage를 사용하는 방법](/azure/storage/queues/storage-java-how-to-use-queue-storage)
* [Java에서 Azure Table Storage를 사용하는 방법](/azure/cosmos-db/table-storage-how-to-use-java)
* [Java에서 Azure File Storage를 사용하는 방법](/azure/storage/files/storage-java-how-to-use-file-storage)
