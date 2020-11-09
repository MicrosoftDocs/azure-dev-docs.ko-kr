---
title: Spring Boot Initializer 앱 만들기 - Azure Redis Cache
description: Spring Initializer를 사용하여 만든 Spring Boot 애플리케이션을 구성하여 Azure Redis Cache를 사용하여 클라우드에서 Redis를 사용합니다.
services: redis-cache
documentationcenter: java
ms.date: 10/13/2020
ms.service: cache
ms.tgt_pltfrm: cache-redis
ms.topic: conceptual
ms.custom: devx-track-java
ms.openlocfilehash: 7d8ee875339adb741fbddeba6d4328eb22cd3e46
ms.sourcegitcommit: 5c7f5fef798413b1a304cc9ee31c8518b73f27eb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93066279"
---
# <a name="configure-a-spring-boot-initializer-app-to-use-redis-in-the-cloud-with-azure-redis-cache"></a>Azure Redis Cache를 사용하여 클라우드에서 Redis를 사용하도록 Spring Boot Initializer 앱 구성

이 문서에서는 Azure Portal을 사용하여 클라우드에서 Redis Cache를 만들고, **[Spring Initializr]** 를 사용하여 사용자 지정 애플리케이션을 만든 다음 Redis Cache를 사용하여 데이터를 저장하고 검색하는 Java 웹 애플리케이션을 만드는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음 필수 구성 요소가 필요합니다.

* Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택]을 활성화하거나 [체험판 Azure 계정]에 등록할 수 있습니다.
* 지원되는 JDK(Java Development Kit) Azure에서 개발하는 경우 사용할 수 있는 JDK에 대한 자세한 내용은 <https://aka.ms/azure-jdks>를 참조하세요.
* [Apache Maven](http://maven.apache.org/), 버전 3.0 이상

## <a name="create-a-custom-application-using-the-spring-initializr"></a>Spring Initializr를 사용하여 사용자 지정 애플리케이션 만들기

1. [https://www.microsoft.com]\(<https://start.spring.io/>) 로 이동합니다.

1. **Java** 에서 **Maven** 프로젝트를 생성한다고 지정하고, Java 버전 **8** 을 선택하고, 애플리케이션의 **그룹** 및 **아티팩트** 이름을 입력합니다.

1. **Spring Web** 섹션에 대한 종속성을 추가하고 **웹** 상자를 선택한 다음, **NoSQL** 섹션까지 아래로 스크롤하여 **Spring Data Reactive Redis** 상자를 선택합니다. 
1. 페이지 하단까지 스크롤하고 버튼을 클릭하여 **프로젝트를 생성** 합니다.

   ![기본 Spring Initializr 옵션][SI01]

   > [!NOTE]
   >
   > Spring Initializr는 **그룹** 및 **아티팩트** 이름을 사용하여 패키지 이름을 만듭니다(예: *com.contoso.myazuredemo* ).
   >

1. 메시지가 표시되면 로컬 컴퓨터의 경로에 프로젝트를 다운로드합니다.

   ![사용자 지정 Spring Boot 프로젝트 다운로드][SI03]

1. 로컬 시스템에서 파일의 압축을 푼 후에 사용자 지정 Spring Boot 애플리케이션을 편집할 준비를 합니다.

   ![사용자 지정 Spring Boot 프로젝트 파일][SI04]

## <a name="create-a-redis-cache-on-azure"></a>Azure에 Redis 캐시 만들기

1. Azure Portal(<https://portal.azure.com/>)이동하고 **+새로 만들기** 를 클릭합니다.

1. **데이터베이스** 를 클릭하고 **Redis Cache** 를 클릭합니다.

   ![Azure Portal에서 Redis Cache를 선택합니다.][AZ02]

1. **새 Redis Cache** 페이지에서 다음 정보를 지정합니다.

   * 캐시에 대한 **DNS 이름** 을 입력합니다.
   * **구독** , **리소스 그룹** , **위치** 및 **가격 책정 계층** 을 지정합니다.
   * 이 자습서에서는 **포트 6379 차단 해제** 를 선택합니다.

   > [!NOTE]
   >
   > Redis 캐시와 함께 SSL을 사용할 수 있지만 Jedis와 같은 다른 Redis 클라이언트를 사용해야 합니다. 자세한 내용은 [Java와 함께 Azure Redis Cache를 사용하는 방법][Redis Cache with Java]을 참조하세요.
   >

   이러한 옵션을 지정한 경우 **만들기** 를 클릭하여 캐시를 만듭니다.

   ![Azure Portal에서 캐시를 만듭니다.][AZ03]

1. 캐시가 완료되면 Azure **대시보드** 뿐만 아니라 **모든 리소스** 및 **Redis Caches** 페이지에서도 나열된 것을 확인할 수 있습니다. 해당 위치 중 하나에서 캐시를 클릭하여 캐시의 속성 페이지를 열 수 있습니다.

   ![Azure Portal에서 프로비저닝된 리소스입니다.][AZ04]

1. 캐시의 속성 목록이 포함된 페이지가 표시되면 **액세스 키** 를 클릭하고 캐시의 액세스 키를 복사합니다.

   ![액세스 키 섹션 아래에서 액세스 키를 복사합니다.][AZ05]

## <a name="configure-your-custom-spring-boot-to-use-your-redis-cache"></a>Redis Cache를 사용하도록 사용자 지정 Spring Boot 구성

1. 앱의 *리소스* 디렉터리에서 *application.properties* 파일을 찾거나 아직 없는 경우 해당 파일을 만듭니다.

   ![application.properties 파일 찾기][RE01]

1. 텍스트 편집기에서 *application.properties* 파일을 찾고 파일에 다음 줄을 추가하고 샘플 값을 캐시의 적절한 속성으로 바꿉니다.

   ```yaml
   # Specify the DNS URI of your Redis cache.
   spring.redis.host=myspringbootcache.redis.cache.windows.net

   # Specify the port for your Redis cache.
   spring.redis.port=6379

   # Specify the access key for your Redis cache.
   spring.redis.password=57686f6120447564652c2049495320526f636b73=
   ```

   ![application.properties 파일 편집][RE02]

   > [!NOTE] 
   > 
   > SSL을 활성화하는 Jedis와 같은 다른 Redis 클라이언트를 사용하는 경우 *application.properties* 파일에서 사용하고자 하는 SSL과 포트 6380을 지정합니다. 다음은 그 예입니다.
   > 
   > ```yaml
   > # Specify the DNS URI of your Redis cache.
   > spring.redis.host=myspringbootcache.redis.cache.windows.net
   > # Specify the access key for your Redis cache.
   > spring.redis.password=57686f6120447564652c2049495320526f636b73=
   > # Specify that you want to use SSL.
   > spring.redis.ssl=true
   > # Specify the SSL port for your Redis cache.
   > spring.redis.port=6380
   > ```
   > 
   > 자세한 내용은 [Java와 함께 Azure Redis Cache를 사용하는 방법][Redis Cache with Java]을 참조하세요. 
   > 

1. *application.properties* 파일을 저장하고 닫습니다.

1. 패키지의 소스 폴더 아래에서 *controller* 라는 폴더를 만듭니다.

   `C:\SpringBoot\myazuredemo\src\main\java\com\contoso\myazuredemo\controller`

   또는

   `/users/example/home/myazuredemo/src/main/java/com/contoso/myazuredemo/controller`

1. *컨트롤러* 폴더에 *HelloController.java* 라는 새 파일을 만듭니다. 텍스트 편집기에서 파일을 열고 다음 코드를 추가합니다.

   ```java
   package com.contoso.myazuredemo;

   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.data.redis.core.StringRedisTemplate;
   import org.springframework.data.redis.core.ValueOperations;

   @RestController
   public class HelloController {
   
      @Autowired
      private StringRedisTemplate template;

      @RequestMapping("/")
      // Define the Hello World controller.
      public String hello() {
      
         ValueOperations<String, String> ops = this.template.opsForValue();

         // Add a Hello World string to your cache.
         String key = "greeting";
         if (!this.template.hasKey(key)) {
             ops.set(key, "Hello World!");
         }

         // Return the string from your cache.
         return ops.get(key);
      }
   }
   ```
   
   여기에서 `com.contoso.myazuredemo`를 프로젝트의 패키지 이름으로 바꾸어야 합니다.

1. *HelloController.java* 파일을 저장하고 닫습니다.

1. Maven을 사용하여 Spring Boot 애플리케이션을 빌드하고 실행합니다. 예:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. 웹 브라우저를 통해 `http://localhost:8080`으로 이동하여 웹앱을 테스트하거나 사용 가능한 curl이 있는 경우 다음 예제와 같이 구문을 사용합니다.

   ```shell
   curl http://localhost:8080
   ```

   샘플 컨트롤러에서 "Hello World!" 메시지가 표시되어야 합니다. 이 메시지는 Redis Cache에서 동적으로 검색됩니다.

## <a name="next-steps"></a>다음 단계

Spring과 Azure에 대한 자세한 사항은 Azure의 Spring 설명서 센터를 참조합니다.

> [!div class="nextstepaction"]
> [Azure의 Spring](./index.yml)

### <a name="additional-resources"></a>추가 리소스

Azure에서 Spring Boot 애플리케이션을 사용 하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure App Service에서 Linux에 Spring Boot 애플리케이션 배포](deploy-spring-boot-java-app-on-linux.md)

* [Azure Container Service의 Kubernetes 클러스터에 Spring Boot 애플리케이션 실행](deploy-spring-boot-java-app-on-kubernetes.md)

Java와 함께 Azure를 사용하는 방법에 관한 자세한 정보는 [Java 개발자를 위한 Azure]와 [Azure DevOps 및 Java 사용하기]를 참조하세요.

Azure에서 Java로 Redis Cache를 시작하는 방법에 대한 자세한 내용은 [Java에서 Azure Redis Cache를 사용하는 방법][Redis Cache with Java]을 참조하세요.

**[Spring Framework]** 는 Java 개발자가 엔터프라이즈 수준의 애플리케이션을 만드는 데 도움이 되는 오픈 소스 솔루션입니다. 해당 플랫폼을 기반으로 하여 빌드되는 인기 있는 프로젝트 중 하나가 [Spring Boot]입니다. 이 프로젝트는 독립 실행형 Java 애플리케이션을 만드는 간단한 방법을 제공합니다. Spring Boot을 시작하는 개발자를 도우려면 <https://github.com/spring-guides/>에서 몇 가지 샘플 Spring Boot 패키지를 사용할 있습니다. 기본 Spring Boot 프로젝트 목록에서 선택하는 것 외에도 **[Spring Initializr]** 를 통해 사용자 지정 Spring Boot 애플리케이션을 만들기 시작하는 개발자에게 도움을 줍니다.

<!-- URL List -->

[Java 개발자를 위한 Azure]: ../index.yml
[체험판 Azure 계정]: https://azure.microsoft.com/pricing/free-trial/
[Azure DevOps 및 Java 사용하기]: /azure/devops/java/
[MSDN 구독자 혜택]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[Redis Cache with Java]: /azure/redis-cache/cache-java-get-started

<!-- IMG List -->

[AZ01]: media/configure-spring-boot-initializer-java-app-with-redis-cache/AZ01.png
[AZ02]: media/configure-spring-boot-initializer-java-app-with-redis-cache/AZ02.png
[AZ03]: media/configure-spring-boot-initializer-java-app-with-redis-cache/AZ03.png
[AZ04]: media/configure-spring-boot-initializer-java-app-with-redis-cache/AZ04.png
[AZ05]: media/configure-spring-boot-initializer-java-app-with-redis-cache/AZ05.png

[SI01]: media/configure-spring-boot-initializer-java-app-with-redis-cache/SI01.png
[SI02]: media/configure-spring-boot-initializer-java-app-with-redis-cache/SI02.png
[SI03]: media/configure-spring-boot-initializer-java-app-with-redis-cache/SI03.png
[SI04]: media/configure-spring-boot-initializer-java-app-with-redis-cache/SI04.png

[RE01]: media/configure-spring-boot-initializer-java-app-with-redis-cache/RE01.png
[RE02]: media/configure-spring-boot-initializer-java-app-with-redis-cache/RE02.png
