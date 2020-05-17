---
title: Azure Cosmos DB SQL API에서 Spring Data Gremlin Starter를 사용하는 방법
description: Azure Cosmos DB SQL API에서 Spring Boot Initializer를 사용하여 만든 애플리케이션을 구성하는 방법에 대해 알아봅니다.
services: cosmos-db
documentationcenter: java
ms.date: 01/10/2020
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: data-services
ms.openlocfilehash: f46988e75d3230455796b2472010c3d93bff5c57
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "81669249"
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

> [!IMPORTANT]
>
> 이 문서의 단계를 완료하려면 Spring Boot 버전 2.0 이상이 필요합니다.
>

## <a name="create-an-azure-cosmos-db-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Cosmos DB 만들기

### <a name="create-your-azure-cosmos-database-for-use-with-gremlin-api"></a>Gremlin API에 사용할 Azure Cosmos 데이터베이스 만들기

1. Azure Portal(<https://portal.azure.com/>)로 이동하고 **+리소스 만들기**를 클릭합니다.

1. **데이터베이스**를 클릭한 후 **Azure Cosmos DB**를 클릭합니다.

   ![Azure Cosmos DB 만들기][AZ02]

1. **Azure Cosmos DB** 페이지에서 다음 정보를 입력합니다.

   * 데이터베이스에 사용하려는 **구독**을 선택합니다.
   * 데이터베이스에 새 **리소스 그룹**을 만들지 아니면 기존 리소스 그룹을 선택할지를 지정합니다.
   * 데이터베이스의 Gremlin URI의 일부로 사용할 고유한 **계정 이름**을 입력합니다. 예를 들어 **계정 이름**으로 **wingtiptoysdata**를 입력한 경우, Gremlin URI는 *wingtiptoysdata.gremlin.cosmosdb.azure.com*입니다.
   * API로 **Gremlin(그래프)** 를 선택합니다.
   * 데이터베이스의 **위치**를 지정합니다.
   
이러한 옵션을 지정했으면 **검토 + 만들기**를 클릭합니다.

   ![Azure Cosmos DB 옵션 지정][AZ03]

사양을 검토하고 **만들기**를 클릭하여 데이터베이스를 만듭니다.

1. 데이터베이스가 만들어지면 **리소스로 이동**을 클릭합니다. Azure **대시보드**뿐 아니라 **모든 리소스** 및 **Azure Cosmos DB** 페이지에도 나열됩니다. 해당 위치 중 하나에서 데이터베이스를 클릭하여 캐시에 대한 속성 페이지를 열 수 있습니다.

1. 데이터베이스에 대한 속성 페이지가 표시되면 **키**를 클릭하고 데이터베이스에 대한 URI 및 액세스 키를 복사합니다. 이러한 값은 Spring Boot 애플리케이션에서 사용하게 됩니다.

   ![액세스 키][AZ05]

### <a name="add-a-graph-to-your-azure-cosmos-database"></a>Azure Cosmos 데이터베이스에 그래프를 추가 합니다.

1. **데이터 탐색기**를 클릭한 다음 **새 그래프**를 클릭합니다.

   ![새 그래프][AZ06]

1. **그래프 추가**가 표시되면 다음 정보를 입력합니다.

   * 데이터베이스에 대해 고유한 **데이터베이스 id**를 지정합니다.
   * **스토리지 용량**을 지정하도록 선택할 수 있으며 기본값을 적용할 수도 있습니다.
   * 그래프 대해 고유한 **그래프 id**를 지정합니다.
   * **파티션 키**를 지정합니다. 자세한 내용은 [Azure Cosmos DB에서 분할된 그래프 사용](/azure/cosmos-db/graph-partitioning)을 참조하세요.
**확인**을 클릭합니다.
   
   이러한 옵션을 지정한 경우 **OK**를 클릭하여 그래프를 만듭니다.

   ![그래프 추가][AZ07]

1. 그래프를 만든 후 **데이터 탐색기**를 사용하여 볼 수 있습니다.

   ![Graph 속성 표시][AZ08]

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Spring Initializr를 사용하여 간단한 Spring Boot 애플리케이션 만들기

1. [https://www.microsoft.com]\(<https://start.spring.io/>) 로 이동합니다.

1. **Java**에서 **Maven** 프로젝트를 생성한다고 지정하고, 애플리케이션 **그룹** 및 **아티팩트** 이름을 입력한 다음, 2.0 이상의 **Spring Boot** 버전을 지정하고 **프로젝트를 생성**합니다.

   ![기본 Spring Initializr 옵션][SI01]

   > [!NOTE]
   >
   > Spring Initializr는 **그룹** 및 **아티팩트** 이름을 사용하여 패키지 이름을 만듭니다(예: *com.example.wintiptoysdata).
   >

1. 메시지가 표시되면 로컬 컴퓨터의 경로에 프로젝트를 다운로드합니다.

1. 로컬 시스템에서 파일의 압축을 푼 후에 단순한 Spring Boot 애플리케이션을 편집할 준비를 합니다.

   ![사용자 지정 Spring Boot 프로젝트 파일][SI03]

## <a name="configure-your-spring-boot-app-to-use-the-spring-data-gremlin-starter"></a>Spring Data Gremlin Starter를 사용하도록 Spring Boot 앱 구성

1. 앱의 디렉터리에서 *pom.xml* 파일을 찾습니다. 예:

   `C:\SpringBoot\wingtiptoysdata\pom.xml`

   또는

   `/users/example/home/wingtiptoysdata/pom.xml`

1. 텍스트 편집기에서 *pom.xml* 파일을 열고 Spring Data Gremlin Starter를 `<dependencies>` 목록에 추가합니다.

   ```xml
   <dependency>
      <groupId>com.microsoft.spring.data.gremlin</groupId>
      <artifactId>spring-data-gremlin</artifactId>
      <version>2.0.0</version>
   </dependency>
   ```

   ![pom.xml 파일 편집][PM02]

1. *pom.xml* 파일을 저장하고 닫습니다.

## <a name="configure-your-spring-boot-app-to-use-your-azure-cosmos-db"></a>Azure Cosmos DB를 사용하도록 Spring Boot 앱 구성

1. 앱의 *리소스* 디렉터리를 찾아*application.yml*라는 새 파일을 만듭니다. 다음은 그 예입니다.

   `C:\SpringBoot\wingtiptoysdata\src\main\resources\application.yml`

   또는

   `/users/example/home/wingtiptoysdata/src/main/resources/application.yml`

   ![application.yml 파일을 만듭니다.][RE01]

1. 텍스트 편집기에서 *application.yml* 파일을 찾고 파일에 다음 줄을 추가하고 샘플 값을 데이터베이스의 적절한 속성으로 바꿉니다.

   ```yaml
   gremlin:
      endpoint: wingtiptoys.gremlin.cosmosdb.azure.com
      port: 443
      username: /dbs/wingtiptoysdb/colls/wingtiptoysgraph
      password: 57686f6120447564652c20426f6220526f636b73==
      telemetryAllowed: false
   ```
   
   위치:
   
   | 필드 | Description |
   |---|---|
   | `endpoint` | 이 자습서의 앞부분에서 Azure Cosmos DB를 만들 때 지정한 고유한 **ID**에서 파생된 데이터베이스의 Gremlin URI를 지정합니다. |
   | `port` | TCP/IP 포트를 지정합니다. HTTPS의 경우 **443**입니다. |
   | `username` | 이 자습서의 앞부분에 그래프를 추가할 때 사용한 고유한 **데이터베이스 id**와 **그래프 id**를 지정합니다. "/dbs/ **{Database id}** /colls/ **{Graph id}** "구문을 사용하여 입력해야 합니다. |
   | `password` | 이 자습서의 앞부분에서 복사한 기본 또는 보조 **액세스 키**를 지정합니다. |
   | `telemetryAllowed` | 원격 분석을 사용하려는 경우 **true**를, 그렇지 않으면 **false**를 지정합니다.

1. *application.yml* 파일을 저장하고 닫습니다.

## <a name="add-sample-code-to-implement-basic-database-functionality"></a>기본 데이터베이스 기능을 구현하는 샘플 코드 추가

이 섹션에서는 데이터베이스에서 데이터를 저장 하는 데 필요한 Java 클래스를 만듭니다.

### <a name="modify-the-main-application-class"></a>기본 애플리케이션 클래스 수정

1. 앱의 패키지 디렉터리에서 기본 애플리케이션 Java 파일을 찾습니다. 예:

   `C:\SpringBoot\wingtiptoysdata\src\main\java\com\example\wingtiptoysdata\WingtiptoysdataApplication.java`

   또는

   `/users/example/home/wingtiptoysdata/src/main/java/com/example/wingtiptoysdata/WingtiptoysdataApplication.java`

   ![애플리케이션 Java 파일 찾기][JV01]

1. 텍스트 편집기에서 애플리케이션 Java 파일을 열고 다음 줄을 파일에 추가합니다.

   ```java
   package com.example.wingtiptoysdata;
   
   // These imports are required for the application.
   import com.microsoft.spring.data.gremlin.common.GremlinFactory;
   import com.example.wingtiptoysdata.domain.Network;
   import com.example.wingtiptoysdata.domain.Person;
   import com.example.wingtiptoysdata.domain.Relation;
   import com.example.wingtiptoysdata.repository.NetworkRepository;
   import com.example.wingtiptoysdata.repository.PersonRepository;
   import com.example.wingtiptoysdata.repository.RelationRepository;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import javax.annotation.PostConstruct;
   import javax.annotation.PreDestroy;
   
   @SpringBootApplication
   public class WingtiptoysdataApplication {
   
       // Define several person classes to store personal data.
       private final Person person1 = new Person("01", "Nellie Hughes", "23");
       private final Person person2 = new Person("02", "Delmar Alfred", "34");
       private final Person person3 = new Person("03", "Kelley Hunter", "45");
       private final Person person4 = new Person("04", "Roscoe Guerin", "56");
       private final Person person5 = new Person("05", "Gracie Chavez", "67");
   
       // Define relationship classes to define the relationships between some of the persons.
       private final Relation relation1 = new Relation("0102", "siblings", person1, person2);
       private final Relation relation2 = new Relation("0203", "coworkers", person2, person3);
       private final Relation relation3 = new Relation("0301", "parent", person3, person1);
       private final Relation relation4 = new Relation("0302", "parent", person3, person2);
       private final Relation relation5 = new Relation("0501", "grandparent", person5, person1);
       private final Relation relation6 = new Relation("0502", "grandparent", person5, person2);
   
       // Define the network.
       private final Network network = new Network();
   
       // Autowire the repositories and factory.
       @Autowired
       private PersonRepository personRepo;
       @Autowired
       private RelationRepository relationRepo;
       @Autowired
       private NetworkRepository networkRepo;
       @Autowired
       private GremlinFactory factory;
   
       // Run the Spring application and exit.
    public static void main(String[] args) {
           SpringApplication.run(WingtiptoysdataApplication.class, args);
           System.exit(0);
    }
   
       // Perform post-construct operations.    
       @PostConstruct
       public void setup() {
           // Delete any existing data from the database.
           this.networkRepo.deleteAll();
   
           // Add the relationship classes as edges.
           this.network.getEdges().add(this.relation1);
           this.network.getEdges().add(this.relation2);
           this.network.getEdges().add(this.relation3);
           this.network.getEdges().add(this.relation4);
           this.network.getEdges().add(this.relation5);
           this.network.getEdges().add(this.relation6);
   
           // Add the person classes as vertices.
           this.network.getVertexes().add(this.person1);
           this.network.getVertexes().add(this.person2);
           this.network.getVertexes().add(this.person3);
           this.network.getVertexes().add(this.person4);
           this.network.getVertexes().add(this.person5);
   
           // Save the network.
           this.networkRepo.save(this.network);
       }
   }
   ```

1. 기본 애플리케이션 Java 파일을 저장하고 닫습니다.

### <a name="define-a-basic-class-for-storing-configuration-information"></a>구성 정보를 저장하기 위한 기본 클래스를 정의 합니다.

1. 앱의 패키지 디렉터리 아래 *config*라는 이름의 폴더를 만듭니다. 예를 들어,

   `C:\SpringBoot\wingtiptoysdata\src\main\java\com\example\wingtiptoysdata\config`

   또는

   `/users/example/home/wingtiptoysdata/src/main/java/com/example/wingtiptoysdata/config`

1. *config* 디렉터리 에 *UserRepositoryConfiguration.java*라는 새 Java 파일을 만듭니다. 그리고 파일 텍스트 편집기에서 해당 파일을 열고 다음 줄을 추가합니다.

   ```java
   package com.example.wingtiptoysdata.config;

   import com.microsoft.spring.data.gremlin.common.GremlinConfiguration;
   import com.microsoft.spring.data.gremlin.common.GremlinFactory;
   import com.microsoft.spring.data.gremlin.config.AbstractGremlinConfiguration;
   import com.microsoft.spring.data.gremlin.repository.config.EnableGremlinRepositories;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.context.properties.EnableConfigurationProperties;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.context.annotation.PropertySource;
   
   @Configuration
   @EnableGremlinRepositories(basePackages = "com.example.wingtiptoysdata.repository")
   @EnableConfigurationProperties(GremlinConfiguration.class)
   @PropertySource("classpath:application.yml")
   public class UserRepositoryConfiguration extends AbstractGremlinConfiguration {
   
       @Autowired
       private GremlinConfiguration config;
   
       @Override
       public GremlinConfiguration getGremlinConfiguration() {
           return this.config;
       }
   }
   ```

1. *UserRepositoryConfiguration.java* 파일을 저장 후 닫습니다.

### <a name="define-a-set-of-classes-that-define-the-elements-of-your-graph-database"></a>그래프 데이터베이스의 요소를 정의하는 클래스 집합을 정의합니다.

1. 앱의 패키지 디렉터리 아래 *domain*이라는 이름의 폴더를 만듭니다. 예를 들어,

   `C:\SpringBoot\wingtiptoysdata\src\main\java\com\example\wingtiptoysdata\domain`

   또는

   `/users/example/home/wingtiptoysdata/src/main/java/com/example/wingtiptoysdata/domain`

1. *domain* 디렉터리 에 *Person.java*라는 새 Java 파일을 만듭니다. 그리고 파일 텍스트 편집기에서 해당 파일을 열고 다음 줄을 추가합니다.

   ```java
   package com.example.wingtiptoysdata.domain;
   
   import com.microsoft.spring.data.gremlin.annotation.Vertex;
   import lombok.AllArgsConstructor;
   import lombok.Data;
   import lombok.NoArgsConstructor;
   import org.springframework.data.annotation.Id;
   
   @Data
   @Vertex
   @AllArgsConstructor
   @NoArgsConstructor
   public class Person {
   
       @Id
       private String id;
   
       private String name;
   
       private String age;
   }
   ```

1. *Person.java* 파일을 저장 후 닫습니다.

1. *domain* 디렉터리 에 *Relation.java*라는 새 Java 파일을 만듭니다. 그리고 파일 텍스트 편집기에서 해당 파일을 열고 다음 줄을 추가합니다.

   ```java
   package com.example.wingtiptoysdata.domain;
   
   import com.microsoft.spring.data.gremlin.annotation.Edge;
   import com.microsoft.spring.data.gremlin.annotation.EdgeFrom;
   import com.microsoft.spring.data.gremlin.annotation.EdgeTo;
   import lombok.AllArgsConstructor;
   import lombok.Data;
   import lombok.NoArgsConstructor;
   import org.springframework.data.annotation.Id;
   
   @Data
   @Edge
   @AllArgsConstructor
   @NoArgsConstructor
   public class Relation {
   
       @Id
       private String id;
   
       private String name;
   
       @EdgeFrom
       private Person personFrom;
   
       @EdgeTo
       private Person personTo;
   }
   ```

1. *Relation.java* 파일을 저장 후 닫습니다.

1. *domain* 디렉터리 에 *Network.java*라는 새 Java 파일을 만듭니다. 그리고 파일 텍스트 편집기에서 해당 파일을 열고 다음 줄을 추가합니다.

   ```java
   package com.example.wingtiptoysdata.domain;
   
   import com.microsoft.spring.data.gremlin.annotation.EdgeSet;
   import com.microsoft.spring.data.gremlin.annotation.Graph;
   import com.microsoft.spring.data.gremlin.annotation.VertexSet;
   import lombok.Getter;
   import org.springframework.data.annotation.Id;
   
   import java.util.ArrayList;
   import java.util.List;
   
   @Graph
   public class Network {
   
       @Id
       private String id;
   
       public Network() {
           this.edges = new ArrayList<Object>();
           this.vertexes = new ArrayList<Object>();
       }
   
       @EdgeSet
       @Getter
       private List<Object> edges;
   
       @VertexSet
       @Getter
       private List<Object> vertexes;
   }
   ```

1. *Network.java* 파일을 저장 후 닫습니다.

### <a name="define-a-set-of-classes-that-define-the-repositories-for-your-graph-database"></a>그래프 데이터베이스의 리포지토리를 정의하는 클래스 집합을 정의합니다.

1. 앱의 패키지 디렉터리 아래 *repository*라는 이름의 폴더를 만듭니다. 예를 들어,

   `C:\SpringBoot\wingtiptoysdata\src\main\java\com\example\wingtiptoysdata\repository`

   또는

   `/users/example/home/wingtiptoysdata/src/main/java/com/example/wingtiptoysdata/repository`

1. *repository* 디렉터리에 *NetworkRepository.java*라는 새 Java 파일을 만듭니다. 그리고 파일 텍스트 편집기에서 해당 파일을 열고 다음 줄을 추가합니다.

   ```java
   package com.example.wingtiptoysdata.repository;
   
   import com.microsoft.spring.data.gremlin.repository.GremlinRepository;
   import com.example.wingtiptoysdata.domain.Network;
   import org.springframework.stereotype.Repository;
   
   @Repository
   public interface NetworkRepository extends GremlinRepository<Network, String> {
   }
   ```

1. *NetworkRepository.java* 파일을 저장 후 닫습니다.

1. *repository* 디렉터리에 *PersonRepository.java*라는 새 Java 파일을 만듭니다. 그리고 파일 텍스트 편집기에서 해당 파일을 열고 다음 줄을 추가합니다.

   ```java
   package com.example.wingtiptoysdata.repository;
   
   import com.microsoft.spring.data.gremlin.repository.GremlinRepository;
   import com.example.wingtiptoysdata.domain.Person;
   import org.springframework.stereotype.Repository;
   
   @Repository
   public interface PersonRepository extends GremlinRepository<Person, String> {
   }
   ```

1. *PersonRepository.java* 파일을 저장 후 닫습니다.

1. *repository* 디렉터리에 *RelationRepository.java*라는 새 Java 파일을 만듭니다. 그리고 파일 텍스트 편집기에서 해당 파일을 열고 다음 줄을 추가합니다.

   ```java
   package com.example.wingtiptoysdata.repository;
   
   import com.microsoft.spring.data.gremlin.repository.GremlinRepository;
   import com.example.wingtiptoysdata.domain.Relation;
   import org.springframework.stereotype.Repository;
   
   @Repository
   public interface RelationRepository extends GremlinRepository<Relation, String> {
   }
   ```

1. *RelationRepository.java* 파일을 저장 후 닫습니다.

## <a name="build-and-test-your-app"></a>앱 빌드 및 테스트

1. 명령 프롬프트를 열고 디렉터리를 *pom.xml* 파일이 위치한 폴더로 변경합니다. 예:

   `cd C:\SpringBoot\wingtiptoysdata`

   또는

   `cd /users/example/home/wingtiptoysdata`

1. Maven을 사용하여 Spring Boot 애플리케이션을 빌드하고 실행합니다. 예:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. 애플리케이션에 여러 런타임 메시지가 표시되고 오류가 없는 경우 Azure 포털을 사용하여 Azure Cosmos DB의 내용을 볼 수 있습니다. 이렇게 하려면 데이터베이스의 속성 페이지에서 **데이터 탐색기**를 클릭하고 **Gremlin 쿼리 실행**을 클릭한 다음, 결과 목록에서 항목을 선택하여 데이터를 봅니다.

   ![문서 탐색기를 사용하여 데이터 보기][JV03]

## <a name="next-steps"></a>다음 단계

Spring과 Azure에 대한 자세한 사항은 Azure의 Spring 설명서 센터를 참조합니다.

> [!div class="nextstepaction"]
> [Azure의 Spring](/azure/developer/java/spring-framework)

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
[Java 개발자를 위한 Azure]: /azure/developer/java/
[Build a SQL API app with Java]: /azure/cosmos-db/create-sql-api-java 
[Azure Cosmos DB SQL API용 Spring 데이터]: https://azure.microsoft.com/blog/spring-data-azure-cosmos-db-nosql-data-access-on-azure/
[Spring 데이터 Gremlin Starter]: https://github.com/Microsoft/spring-data-gremlin
[체험판 Azure 계정]: https://azure.microsoft.com/pricing/free-trial/
[Azure DevOps 및 Java 사용하기]: /azure/devops/
[MSDN 구독자 혜택]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[AZ02]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/AZ02.png
[AZ03]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/AZ03.png
[AZ05]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/AZ05.png
[AZ06]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/AZ06.png
[AZ07]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/AZ07.png
[AZ08]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/AZ08.png

[SI01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/SI01.png
[SI03]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/SI03.png

[RE01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/RE01.png
[RE02]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/RE02.png

[PM02]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/PM02.png

[JV01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/JV01.png
[JV02]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/JV02.png
[JV03]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/JV03.png
