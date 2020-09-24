---
title: Azure Cosmos DB에서 Spring Data MongoDB API를 사용하는 방법
description: Azure Cosmos DB에서 Spring Data MongoDB API를 사용하는 방법을 알아보세요.
services: cosmos-db
documentationcenter: java
ms.date: 12/19/2018
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 529fab3977f0398014d74140abcbd807ac129fa5
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831429"
---
# <a name="how-to-use-spring-data-mongodb-api-with-azure-cosmos-db"></a>Azure Cosmos DB에서 Spring Data MongoDB API를 사용하는 방법

## <a name="overview"></a>개요

이 문서는 [Spring Data]를 사용하여 샘플 애플리케이션을 만들어 [Azure Cosmos DB MongoDB API](/azure/cosmos-db/mongodb-introduction)를 사용하여 정보를 저장 및 검색하는 것을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음 필수 구성 요소가 필요합니다.

* Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택]을 활성화하거나 [체험판 Azure 계정]에 등록할 수 있습니다.
* 지원되는 JDK(Java Development Kit) Azure에서 개발하는 경우 사용할 수 있는 JDK에 대한 자세한 내용은 <https://aka.ms/azure-jdks>를 참조하세요.
* [Apache Maven](http://maven.apache.org/), 버전 3.0 이상
* [Git](https://git-scm.com/downloads) 클라이언트

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

### <a name="create-a-cosmos-db-account-using-the-azure-portal"></a>Azure Portal을 사용하여 Cosmos DB 계정 만들기

> [!NOTE]
> 
> Azure Cosmos DB 계정 만들기에 관한 자세한 정보는 [Azure Cosmos DB 설명서](/azure/cosmos-db/)에서 확인할 수 있습니다.

1. <https://portal.azure.com/>에서 Azure Portal을 찾아 로그인합니다.

1. **+리소스 만들기**를 클릭한 다음 **데이터베이스**를 클릭하고 **Azure Cosmos DB**를 클릭합니다.

   ![Azure Cosmos DB 계정 만들기][COSMOSDB01]

1. 다음 정보를 지정합니다.

   - **구독**: 사용할 Azure 구독을 지정합니다.
   - **리소스 그룹**: 새 리소스 그룹을 만들지 기존 리소스 그룹을 선택할지를 지정합니다.
   - **계정 이름**: Cosmos DB 계정의 고유명을 선택합니다. 이 고유명은 *wingtiptoysmongodb.documents.azure.com* 같은 정규화된 도메인 이름을 만드는 데 사용됩니다.
   - **API**: 이 자습서의 경우 `Azure Cosmos DB for MongoDB API`를 지정합니다.
   - **위치**: 데이터베이스에 가장 가까운 Azure 지역을 지정합니다.

   ![Cosmos DB 계정 설정 지정하기][COSMOSDB02]
   
1. 위 정보를 모두 입력하고 **검토 + 만들기**를 클릭합니다.

1. 검토 페이지의 모든 항목이 올바르면, **만들기**를 클릭합니다.

   ![Cosmos DB 계정 설정 검토하기][COSMOSDB03]

### <a name="retrieve-the-connection-string-for-your-azure-cosmos-db-account"></a>Azure Cosmos DB 계정의 연결 문자열 검토하기

1. <https://portal.azure.com/>에서 Azure Portal을 찾아 로그인합니다.

1. **모든 리소스**를 클릭한 다음, 방금 만든Azure Cosmos DB 계정을 클릭합니다.

1. **연결 문자열**을 클릭하고 **기본 연결 문자열** 필드 값을 복사합니다. 나중에 애플리케이션을 구성할 때 이 값을 사용합니다.

   ![Cosmos DB 연결 문자열 검색하기][COSMOSDB06]

## <a name="configure-the-sample-application"></a>샘플 애플리케이션 구성

1. 명령 셸을 열고 다음 예와 같이 git 명령을 사용하여 샘플 프로젝트를 복제합니다.

   ```shell
   git clone https://github.com/spring-guides/gs-accessing-data-mongodb.git
   ```

1. 동일 프로젝트의 *&lt;project root&gt;/complete/src/main* 디렉터리에 *리소스* 디렉터리를 만들고, *리소스* 디렉터리에 *application.properties* 파일을 만듭니다.

1. 텍스트 편집기에서 *application.properties* 파일을 열어 파일에 다음 줄을 추가하고 샘플 값을 앞서 다룬 적절한 값으로 바꿉니다.

   ```yaml
   spring.data.mongodb.database=wingtiptoysmongodb
   spring.data.mongodb.uri=mongodb://wingtiptoysmongodb:AbCdEfGhIjKlMnOpQrStUvWxYz==@wingtiptoysmongodb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
   ```
   위치:

   | 매개 변수 | Description |
   |---|---|
   | `spring.data.mongodb.database` | 이 문서에서 앞서 다룬 Cosmos DB 계정의 이름을 지정합니다. |
   | `spring.data.mongodb.uri` | 이 문서에서 앞서 다룬 **기본 연결 문자열**을 지정합니다. |

1. *application.properties* 파일을 저장하고 닫습니다.

## <a name="package-and-test-the-sample-application"></a>샘플 애플리케이션 패키지 및 테스트하기

애플리케이션을 빌드하려면 pom.xml 파일이 포함된 */gs-accessing-data-mongodb/complete* 디렉터리로 이동합니다.

1. 다음 예와 같이 Maven을 사용하여 샘플 애플리케이션을 컴파일하고 테스트를 건너뛰도록 Maven을 구성합니다.

   ```shell
   mvn clean package -DskipTests
   ```

1. 다음 예와 같이 샘플 애플리케이션을 시작합니다.

   ```shell
   java -jar target/accessing-data-mongodb-0.0.1-SNAPSHOT.jar
   ```
    
   애플리케이션이 다음과 같이 값을 반환해야 합니다.

   ```json
   Customers found with findAll():
   -------------------------------
   Customer[id=5c1b4ae4d0b5080ac105cc13, firstName='Alice', lastName='Smith']
   Customer[id=5c1b4ae4d0b5080ac105cc14, firstName='Bob', lastName='Smith']
   
   Customer found with findByFirstName('Alice'):
   --------------------------------
   Customer[id=5c1b4ae4d0b5080ac105cc13, firstName='Alice', lastName='Smith']
   Customers found with findByLastName('Smith'):
   --------------------------------
   Customer[id=5c1b4ae4d0b5080ac105cc13, firstName='Alice', lastName='Smith']
   Customer[id=5c1b4ae4d0b5080ac105cc14, firstName='Bob', lastName='Smith']
   ```

## <a name="summary"></a>요약

이 자습서에서는, Spring Data를 사용하는 Java 샘플 애플리케이션을 만들어 Azure Cosmos DB MongoDB API를 사용하여 정보를 저장 및 검색했습니다.

## <a name="next-steps"></a>다음 단계

Spring과 Azure에 대한 자세한 사항은 Azure의 Spring 설명서 센터를 참조합니다.

> [!div class="nextstepaction"]
> [Azure의 Spring](./index.yml)

### <a name="additional-resources"></a>추가 리소스

Java와 함께 Azure를 사용하는 방법에 관한 자세한 정보는 [Java 개발자를 위한 Azure]와 [Azure DevOps 및 Java 사용하기]를 참조하세요.

<!-- URL List -->

[Java 개발자를 위한 Azure]: ../index.yml
[체험판 Azure 계정]: https://azure.microsoft.com/pricing/free-trial/
[Azure DevOps 및 Java 사용하기]: /azure/devops/
[MSDN 구독자 혜택]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Data]: https://spring.io/projects/spring-data
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[COSMOSDB01]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-01.png
[COSMOSDB02]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-02.png
[COSMOSDB03]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-03.png
[COSMOSDB04]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-04.png
[COSMOSDB06]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-06.png