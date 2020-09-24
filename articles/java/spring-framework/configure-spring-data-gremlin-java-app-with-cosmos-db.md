---
title: Azure Cosmos DB SQL API에서 Spring Data Gremlin Starter를 사용하는 방법
description: Azure Cosmos DB SQL API에서 Spring Boot Initializr를 사용하여 만든 애플리케이션을 구성하는 방법에 대해 알아봅니다.
services: cosmos-db
documentationcenter: java
ms.date: 08/03/2020
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: data-services
ms.custom: devx-track-java
ms.openlocfilehash: 775e547f61f4f4b2c649505607cf4d7310d0c6c3
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831839"
---
# <a name="how-to-use-the-spring-data-gremlin-starter-with-the-azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API에서 Spring Data Gremlin Starter를 사용하는 방법

## <a name="overview"></a>개요

Spring 데이터 Gremlin Starter는 개발자가 Gremlin 호환 데이터 저장소에서 사용할 수 있는 Apache의 Gremlin 쿼리 언어에 대한 Spring 데이터 지원을 제공합니다.

이 문서에서는 Gremlin API와 함께 사용할 Azure Portal을 사용하여 Azure Cosmos DB를 만들고, **[Spring Initializr]** 를 사용하여 사용자 지정 java 애플리케이션을 만들고, Spring Data Gremlin Starter 기능을 사용자 지정 애플리케이션에 추가하여 데이터를 저장하고 Gremlin을 사용하여 Azure Cosmos DB에서 데이터를 검색하는 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 수행하기 위해 다음 필수 구성 요소가 필요합니다.

* Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택]을 활성화하거나 [체험판 Azure 계정]에 등록할 수 있습니다.
* 지원되는 JDK(Java Development Kit) Azure에서 개발하는 경우 사용할 수 있는 JDK에 대한 자세한 내용은 <https://aka.ms/azure-jdks>를 참조하세요.
* [Apache Maven](http://maven.apache.org/), 버전 3.0 이상


## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

### <a name="create-a-cosmos-db-account-using-the-azure-portal"></a>Azure Portal을 사용하여 Cosmos DB 계정 만들기

1. Azure Portal(<https://portal.azure.com/>)로 이동하여 `+Create a resource`를 선택합니다.

   >[!div class="mx-imgBorder"]
   >![create-a-resource][create-a-resource-01]

1. `Databases`, `Azure Cosmos DB`을 차례로 선택합니다.

   >[!div class="mx-imgBorder"]
   >![create-azure-cosmos-db][create-a-resource-02]

1. `Azure Cosmos DB` 페이지에서 다음 정보를 입력합니다.

   * 데이터베이스에 사용하려는 `Subscription`을 선택합니다.
   * 데이터베이스에 새 `Resource Group`을 만들지 아니면 기존 리소스 그룹을 선택할지를 지정합니다.
   * 데이터베이스에 대한 Gremlin URI의 일부로 사용할 고유한 `Account Name`을 입력합니다. 예를 들어 `Account Name`에 대해 `account-sample`을 입력한 경우 Gremlin URI는 `account-samplewingtiptoysdata.gremlin.cosmosdb.azure.com`이 됩니다.
   * API에 대한 `Gremlin (Graph)` 를 선택합니다.
   * 데이터베이스의 `Location`를 지정합니다.
   
1. 이러한 옵션을 지정했으면 `Review + create`를 선택합니다.

   >[!div class="mx-imgBorder"]
   >![create-azure-cosmos-db-account][create-a-resource-03]

1. 사양을 검토하고, `Create`를 선택하여 데이터베이스를 만듭니다.

1. 데이터베이스가 만들어지면 **리소스로 이동**을 선택합니다. Azure **대시보드**뿐 아니라 **모든 리소스** 및 **Azure Cosmos DB** 페이지에도 나열됩니다. 해당 위치 중 하나에서 데이터베이스를 선택하여 캐시에 대한 속성 페이지를 열 수 있습니다.

1. 데이터베이스에 대한 속성 페이지가 표시되면 **키**를 선택하고 데이터베이스에 대한 URI 및 액세스 키를 복사합니다. 이러한 값은 Spring Boot 애플리케이션에서 사용하게 됩니다.

### <a name="add-a-graph-to-your-azure-cosmos-database"></a>Azure Cosmos 데이터베이스에 그래프를 추가 합니다.

1. Cosmos DB 페이지에서 `Data Explorer`, `New Graph`를 차례로 선택합니다.

   >[!div class="mx-imgBorder"]
   >![new-graph][create-a-graph-01]

1. `Add Graph`가 표시되면 다음 정보를 입력합니다.

   * 데이터베이스에 대해 고유한 `Database id`를 지정합니다.
   * `Storage capacity`를 지정하도록 선택하거나 기본값을 적용할 수 있습니다.
   * 그래프에 대해 고유한 `Graph id`를 지정합니다.
   * `Partition key`를 지정합니다. 자세한 내용은 [그래프 파티션]을 참조하세요. `OK`를 선택합니다.
   
   이러한 옵션을 지정했으면 `OK`를 선택하여 그래프를 만듭니다.

   >[!div class="mx-imgBorder"]
   >![add-graph][create-a-graph-02]

1. 그래프가 만들어지면 `Data Explorer`를 사용하여 볼 수 있습니다.

   >[!div class="mx-imgBorder"]
   >![graph-detail][create-a-graph-03]
   
   

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Spring Initializr를 사용하여 간단한 Spring Boot 애플리케이션 만들기

1. [https://www.microsoft.com]\(<https://start.spring.io/>) 로 이동합니다.

1. **Java**에서 **Maven** 프로젝트를 생성한다고 지정하고, 애플리케이션 **그룹** 및 **아티팩트** 이름을 입력한 다음, **Spring Boot** 버전을 2.3.1 버전으로 지정하고 **GENERATE**를 선택합니다.

> [!NOTE]
>
> Spring Initializr는 **그룹** 및 **아티팩트** 이름을 사용하여 패키지 이름을 만듭니다(예: `com.example.wintiptoysdata`).


   >[!div class="mx-imgBorder"]
   >![spring-initializr][spring-initializr-01]

1. 메시지가 표시되면 로컬 컴퓨터의 경로에 프로젝트를 다운로드합니다.

1. 로컬 시스템에서 파일을 추출한 후에 IDE로 가져옵니다.


## <a name="configure-your-spring-boot-app-to-use-the-spring-data-gremlin-starter"></a>Spring Data Gremlin Starter를 사용하도록 Spring Boot 앱 구성

기존 [Azure Spring Data Gremlin 샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-data-sample-gremlin)의 구성을 복제하겠습니다. 샘플로 이동하여 이 섹션의 단계에 따라 Spring Boot 앱을 구성합니다.

1. 앱의 디렉터리에서 *pom.xml* 파일을 찾습니다. 예:

   *C:\SpringBoot\wingtiptoysdata\pom.xml*

   또는

   */users/example/home/wingtiptoysdata/pom.xml*

1. *pom.xml* 파일을 열고 Spring Data Gremlin Starter를 `<dependencies>` 목록에 추가합니다.

   ```xml
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-gremlin</artifactId>
      <version>2.3.1-beta.1</version> <!-- {x-version-update;com.azure:azure-spring-data-gremlin;current} -->
    </dependency>
   ```

1. *pom.xml* 파일을 저장하고 닫습니다.

1. *src/test/* 폴더로 이동하고 모든 콘텐츠를 삭제합니다.

1. 샘플 앱에서 *src/main/java* 폴더로 이동하고 동일한 디렉터리를 복사하여 로컬 Spring Boot 앱에 덮어씁니다.

1. *src/main/resources/application.properties* 파일에서 다음을 포함하도록 구성을 업데이트합니다.

   | 필드              | 설명                                                                                                                                                                                                             |
   |--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   | `endpoint`         | 이 빠른 시작의 앞부분에서 Azure Cosmos DB를 만들 때 지정한 고유한 **ID**에서 파생된 데이터베이스의 Gremlin URI를 지정합니다.                                                 |
   | `port`             | TCP/IP 포트를 지정합니다. HTTPS의 경우 **443**입니다.                                                                                                                                                           |
   | `username`         | 이 빠른 시작의 앞부분에 그래프를 추가할 때 사용한 고유한 **데이터베이스 ID**와 **그래프 ID**를 지정합니다. "/dbs/ **{Database ID}** /colls/ **{Graph ID}** " 구문을 사용하여 입력해야 합니다. |
   | `password`         | 이 빠른 시작의 앞부분에서 복사한 기본 또는 보조 **액세스 키**를 지정합니다.                                                                                                                      |
   | `sslEnabled`       | SSL을 사용하도록 설정할지 여부를 지정합니다.                                                                                                                                                                                           |
   | `telemetryAllowed` | 원격 분석을 사용하려는 경우 **true**를, 그렇지 않으면 **false**를 지정합니다.
   | `maxContentLength` | 최대 콘텐츠 길이를 지정합니다.                                                                                                                                                                                           |

## <a name="build-and-run-the-project"></a>프로젝트 빌드 및 실행

1. Maven을 사용하여 Spring Boot 애플리케이션을 빌드하고 실행합니다. 예:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. 앱이 성공적으로 시작되면 Azure Portal에서 그래프를 확인할 수 있습니다.

   >[!div class="mx-imgBorder"]
   >![execute-result][execute-result-01]


## <a name="next-steps"></a>다음 단계

Azure의 Spring에 대해 자세히 알아보려면 Azure의 Spring 설명서를 계속 참조하세요.

> [!div class="nextstepaction"]
> [Azure의 Spring](./index.yml)

### <a name="additional-resources"></a>추가 리소스

Azure Gremlin 및 Graph API에 대한 지원에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Cosmos DB: Graph API 소개](/azure/cosmos-db/graph-introduction)

* [Azure Cosmos DB Gremlin 그래프 지원](/azure/cosmos-db/gremlin-support)

* [Azure Cosmos DB: Java 및 Azure Portal을 사용하여 그래프 데이터베이스 만들기](/azure/cosmos-db/create-graph-java)

* [자습서: Gremlin을 사용하여 Azure Cosmos DB Graph API 쿼리](/azure/cosmos-db/tutorial-query-graph)

* [Spring 데이터 Gremlin Starter]

Azure Cosmos DB 및 Java를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Cosmos DB 설명서]

* [Azure Cosmos DB: Java 및 Azure Portal을 사용하여 문서 데이터베이스 만들기][Build a SQL API app with Java]

* [Azure Cosmos DB SQL API용 Spring 데이터]

Azure에서 Spring Boot 애플리케이션을 사용 하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure App Service에 Spring Boot 애플리케이션 배포](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)

* [Azure Container Service의 Kubernetes 클러스터에 Spring Boot 애플리케이션 실행](deploy-spring-boot-java-app-on-kubernetes.md)

Java와 함께 Azure를 사용하는 방법에 관한 자세한 정보는 [Java 개발자를 위한 Azure]와 [Azure DevOps 및 Java 사용하기]를 참조하세요.

**[Spring Framework]** 는 Java 개발자가 엔터프라이즈 수준의 애플리케이션을 만드는 데 도움이 되는 오픈 소스 솔루션입니다. 해당 플랫폼을 기반으로 하여 빌드되는 인기 있는 프로젝트 중 하나가 [Spring Boot]입니다. 이 프로젝트는 독립 실행형 Java 애플리케이션을 만드는 간단한 방법을 제공합니다. Spring Boot을 시작하는 개발자를 도우려면 <https://github.com/spring-guides/>에서 몇 가지 샘플 Spring Boot 패키지를 사용할 있습니다. 기본 Spring Boot 프로젝트 목록에서 선택하는 것 외에도 **[Spring Initializr]** 를 통해 사용자 지정 Spring Boot 애플리케이션을 만들기 시작하는 개발자에게 도움을 줍니다.

<!-- URL List -->

[Azure Cosmos DB 설명서]: /azure/cosmos-db/
[Java 개발자를 위한 Azure]: ../index.yml
[Build a SQL API app with Java]: /azure/cosmos-db/create-sql-api-java 
[Azure Cosmos DB SQL API용 Spring 데이터]: https://azure.microsoft.com/blog/spring-data-azure-cosmos-db-nosql-data-access-on-azure/
[Spring 데이터 Gremlin Starter]: https://github.com/Microsoft/spring-data-gremlin
[체험판 Azure 계정]: https://azure.microsoft.com/pricing/free-trial/
[Azure DevOps 및 Java 사용하기]: /azure/devops/
[MSDN 구독자 혜택]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[그래프 파티션]: /azure/cosmos-db/graph-partitioning
[azure-spring-data-sample-gremlin]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-data-sample-gremlin

<!-- IMG List -->

[create-a-resource-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-resource-01.png
[create-a-resource-02]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-resource-02.png
[create-a-resource-03]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-resource-03.png

[create-a-graph-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-graph-01.png
[create-a-graph-02]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-graph-02.png
[create-a-graph-03]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-graph-03.png

[spring-initializr-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/spring-initializr-01.png

[get-password-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/get-password-01.png

[execute-result-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/execute-result-01.png