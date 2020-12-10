---
title: Azure용 Spring Boot Starter
description: 이 문서에서는 Azure에 사용할 수 있는 다양한 Spring Boot Starter를 설명 합니다.
documentationcenter: java
ms.date: 09/29/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 89df7fe719cd8c8301c729bb33cb1d3632a80b0d
ms.sourcegitcommit: 09b4a2dbe13601fdf16fcc4082a5075b46ad3459
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96559274"
---
# <a name="spring-boot-starters-for-azure"></a>Azure용 Spring Boot Starter

이 문서에서는 Microsoft Azure 작업을 위해 Java 개발자에게 통합 기능을 제공하는 [Spring Initializr]의 여러 Spring Boot Starter에 대해 설명합니다.

>[!div class="mx-imgBorder"]
![Initializr를 사용하여 Azure Spring Boot Starters 구성][configure-azure-spring-boot-starters-with-initializr]


현재 다음 Spring Boot Starter를 Azure에 사용할 수 있습니다.

* **[Azure Support](#azure-support)**

   Azure Services(예: Service Bus, Storage, Active Directory 등)에 대한 자동 구성 지원을 제공합니다.

* **[Azure Active Directory](#azure-active-directory)**

   인증을 위해 Spring Security와 Azure Active Directory의 통합 지원을 제공합니다.

* **[Azure Key Vault](#azure-key-vault)**

   Azure Key Vault Secrets와의 통합을 위한 Spring 값 주석 처리 지원을 제공합니다.

* **[Azure Storage](#azure-storage)**

   Azure Storage 서비스에 대한 Spring Boot 지원을 제공합니다.
   
   > [!NOTE]
   >
   > Azure Storage에 대한 새 버전의 Spring Boot Starter는 현재 Spring Initializr 내에서 Azure Storage 종속성을 추가하는 것을 지원하지 않습니다. 그러나 프로젝트가 생성된 후 *pom.xml* 파일을 수정하여 종속성을 추가할 수 있습니다.
   > 

<a name="azure-support"></a>
## <a name="azure-support"></a>Azure Support

이 Spring Boot Starter는 Azure Services(예: Service Bus, Storage, Active Directory, Cosmos DB, Key Vault)에 대한 자동 구성 지원을 제공합니다.

이 스타터가 제공하는 여러 Azure 기능을 사용하는 방법의 예는 다음을 참조하세요.

* <https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples>

이 스타터를 Spring Boot 프로젝트에 추가할 때는 *pom.xml* 파일에서 다음을 변경합니다.

* 다음 속성을 `<properties>` 요소에 추가합니다.

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <java.version>1.8</java.version>
      <azure.version>2.3.5</azure.version>
   </properties>
   ```

* 기본 `spring-boot-starter` 종속성은 다음으로 바뀝니다.

    ```xml
    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-starter</artifactId>
        </dependency>
    
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
    ```

* 다음 섹션이 파일에 추가됩니다.

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

<a name="azure-active-directory"></a>
## <a name="azure-active-directory"></a>Azure Active Directory

이 Spring Boot Starter는 인증을 위해 Azure Active Directory와의 통합을 제공하기 위해 Spring Security에 대한 자동 구성 지원을 제공합니다.

이 스타터가 제공하는 Azure Active Directory 기능을 사용하는 방법의 예는 다음을 참조하세요.

* <https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/>

이 스타터를 Spring Boot 프로젝트에 추가할 때는 *pom.xml* 파일에서 다음을 변경합니다.

* 다음 속성을 `<properties>` 요소에 추가합니다.

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <java.version>1.8</java.version>
      <azure.version>2.3.5</azure.version>
   </properties>
   ```

* 기본 `spring-boot-starter` 종속성은 다음으로 바뀝니다.

    ```xml
    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-active-directory-spring-boot-starter</artifactId>
        </dependency>
    
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
    ```

* 다음 섹션이 파일에 추가됩니다.

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

<a name="azure-key-vault"></a>
## <a name="azure-key-vault"></a>Azure Key Vault

이 Spring Boot Starter는 Azure Key Vault Secrets와의 통합을 위한 Spring 값 주석 처리 지원을 제공합니다.

이 스타터가 제공하는 Azure Key Vault 기능을 사용하는 방법의 예는 다음을 참조하세요.

* <https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-boot-sample-keyvault-secrets>

이 스타터를 Spring Boot 프로젝트에 추가할 때는 *pom.xml* 파일에서 다음을 변경합니다.

* 다음 속성을 `<properties>` 요소에 추가합니다.

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <java.version>1.8</java.version>
      <azure.version>2.3.5</azure.version>
   </properties>
   ```

* 기본 `spring-boot-starter` 종속성은 다음으로 바뀝니다.

    ```xml
    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
        </dependency>
    
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
    ```

* 다음 섹션이 파일에 추가됩니다.

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

<a name="azure-storage"></a>
## <a name="azure-storage"></a>Azure Storage

이 Spring Boot Starter는 Azure Storage services에 대한 Spring Boot 통합 지원을 제공합니다.

이 스타터가 제공하는 Azure Storage 기능을 사용하는 방법의 예는 다음을 참조하세요.

* [Azure Storage에 Spring Boot Starter를 사용하는 방법](configure-spring-boot-starter-java-app-with-azure-storage.md)
* <https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-cloud-sample-storage-queue-operation>

이 스타터를 Spring Boot 프로젝트에 추가할 때는 *pom.xml* 파일에서 다음을 변경합니다.

* 다음 속성을 `<properties>` 요소에 추가합니다.

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <java.version>1.8</java.version>
      <azure.version>2.3.5</azure.version>
   </properties>
   ```

* 기본 `spring-boot-starter` 종속성은 다음으로 바뀝니다.

    ```xml
    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>spring-starter-azure-storage</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
    ```

* 다음 섹션이 파일에 추가됩니다.

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

## <a name="next-steps"></a>다음 단계

Spring과 Azure에 대한 자세한 사항은 Azure의 Spring 설명서 센터를 참조합니다.

> [!div class="nextstepaction"]
> [Azure의 Spring](./index.yml)

### <a name="additional-resources"></a>추가 리소스

Azure에서 [Spring Boot] 애플리케이션을 사용하는 방법에 대한 자세한 내용은 [Azure의 Spring]을 참조하세요.

Java와 함께 Azure를 사용하는 방법에 관한 자세한 정보는 [Java 개발자를 위한 Azure]와 [Azure DevOps 및 Java 사용하기]를 참조하세요.

자체 Spring Boot 애플리케이션을 시작하는 데 도움이 필요하면 https://start.spring.io/에서 **Spring Initializr** 를 참조하세요.

<!-- URL List -->

[Java 개발자를 위한 Azure]: ../index.yml
[Azure DevOps 및 Java 사용하기]: /azure/devops/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Azure의 Spring]: ./index.yml
[Spring Framework]: https://spring.io/
[Spring Initializr]: https://start.spring.io/

<!-- IMG List -->

[configure-azure-spring-boot-starters-with-initializr]: media/spring-boot-starters-for-azure/configure-azure-spring-boot-starters-with-initializr.png
