---
title: Azure Cosmos DB SQL API에서 Spring Boot Starter를 사용하는 방법
description: Azure Cosmos DB SQL API에서 Spring Boot Initializer를 사용하여 만든 애플리케이션을 구성하는 방법에 대해 알아봅니다.
services: cosmos-db
documentationcenter: java
author: KarlErickson
ms.author: karler
ms.date: 10/13/2020
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: data-services
ms.custom: devx-track-java
ms.openlocfilehash: 10f85c7d1208ff77f2c85ec14e77ced450d5fcc8
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561308"
---
# <a name="how-to-use-the-spring-boot-starter-with-the-azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API에서 Spring Boot Starter를 사용하는 방법

Azure Cosmos DB는 개발자가 SQL, MongoDB, Graph 및 Table API와 같은 다양한 표준 API를 사용하여 데이터를 사용할 수 있도록 하는 전역 분산 데이터베이스 서비스입니다. Microsoft의 Spring Boot Starter를 사용하면 개발자가 SQL API를 사용하여 Azure Cosmos DB와 쉽게 통합하는 Spring Boot 애플리케이션을 사용할 수 있습니다.

이 문서에서는 Azure Portal을 사용하여 Azure Cosmos DB를 만들고, **[Spring Initializr]** 를 사용하여 사용자 지정 Spring Boot 애플리케이션을 만든 다음, [Azure Cosmos DB용 Azure Spring Boot Starter]를 사용자 지정 애플리케이션에 추가하여 Spring Data 및 Cosmos DB SQL API를 통해 Azure Cosmos DB에서 데이터를 저장하고 검색하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택]을 활성화하거나 [체험판 Azure 계정]에 등록할 수 있습니다.
* 지원되는 JDK(Java Development Kit) Azure에서 개발하는 경우 사용할 수 있는 JDK에 대한 자세한 내용은 <https://aka.ms/azure-jdks>를 참조하세요.

## <a name="create-an-azure-cosmos-db-by-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Cosmos DB 만들기

1. Azure Portal(<https://portal.azure.com/>)로 이동하고 **리소스 만들기** 를 선택합니다.

1. **데이터베이스** 를 선택한 다음, **Azure Cosmos DB** 를 선택합니다.

    ![Azure Portal에서 Azure Cosmos DB를 선택합니다.][AZ02]

1. **Azure Cosmos DB** 페이지에서 다음 정보를 입력합니다.

    * 데이터베이스에 사용하려는 **구독** 을 선택합니다.
    * 데이터베이스에 새 **리소스 그룹** 을 만들지 아니면 기존 리소스 그룹을 선택할지를 지정합니다.
    * 데이터베이스에 대한 URI로 사용할 고유한 **계정 이름** 을 입력합니다. 예: *contosoaccount*.
    * API에 대한 **Core(SQL)** 를 선택합니다.
    * 데이터베이스의 **위치** 를 지정합니다.

    이러한 옵션이 지정되면 **검토 + 만들기** 를 선택하고 사양을 검토한 후 **만들기** 를 선택합니다.

    ![검토 + 만들기를 선택하여 계속 진행합니다.][AZ03]

1. 데이터베이스를 만들면 Azure **대시보드** 그리고 **모든 리소스** 및 **Azure Cosmos DB** 페이지에 나열됩니다. 이러한 위치에 대한 데이터베이스를 선택하여 캐시의 속성 페이지를 열 수 있습니다.

1. 데이터베이스에 대한 속성 페이지가 표시되면 **키** 를 선택하고 데이터베이스에 대한 URI 및 액세스 키를 복사합니다. 이러한 값은 Spring Boot 애플리케이션에서 사용하게 됩니다.

    ![키 섹션에서 URI 및 액세스 키를 복사합니다.][AZ05]

## <a name="create-a-spring-boot-application-with-the-spring-initializr"></a>Spring Initializr를 사용하여 Spring Boot 애플리케이션 만들기

다음 단계를 사용하여 Azure 지원으로 새 Spring Boot 애플리케이션 프로젝트를 만듭니다. 대안으로 [azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) 리포지토리에서 [azure-spring-boot-sample-cosmosdb](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-boot-sample-cosmos) 샘플을 사용할 수 있습니다. 그런 다음, [앱 빌드 및 테스트](#build-and-test-your-app)로 직접 건너뛸 수 있습니다.

1. [https://www.microsoft.com]\(<https://start.spring.io/>) 로 이동합니다.

1. 다음 옵션을 지정합니다.

   * **Java** 를 사용하는 **Maven** 프로젝트를 생성합니다.
   * **Spring Boot** 버전을 지정합니다.
   * 애플리케이션에 대한 **그룹** 및 **아티팩트** 이름을 지정합니다.
   * Java 버전에 대해 **11** 을 선택합니다.
   * 종속성에서 **Azure 지원** 을 추가합니다.

   >[!div class="mx-imgBorder"]
   >![기본 Spring Initializr 옵션][SI01]

   > [!NOTE]
   > 1. Spring Initializr는 **그룹** 및 **아티팩트** 이름을 사용하여 패키지 이름을 만듭니다(예: *com.example.wingtiptoysdata*).
   > 1. Spring Boot의 버전은 Azure Support에서 지원하는 버전보다 높을 수 있습니다. 프로젝트가 자동으로 생성되면 Spring Boot 버전을 [여기][azure-spring-boot-version-matrix]에서 찾을 수 있는 Azure에서 지원하는 가장 높은 버전으로 수동으로 변경할 수 있습니다.

1. 위에 나열된 옵션이 지정되면 **생성** 을 선택합니다.

1. 메시지가 표시되면 프로젝트를 로컬 컴퓨터의 경로에 다운로드하고 파일을 추출합니다.

이제 간단한 Spring Boot 애플리케이션을 편집할 수 있습니다.

## <a name="configure-your-spring-boot-application-to-use-the-azure-spring-boot-starter"></a>Azure Spring Boot Starter를 사용하도록 Spring Boot 애플리케이션 구성

1. 앱의 디렉터리에서 *pom.xml* 파일을 찾습니다. 예:

    `C:\SpringBoot\wingtiptoysdata\pom.xml`

    또는

    `/users/example/home/wingtiptoysdata/pom.xml`

1. 텍스트 편집기에서 *pom.xml* 파일을 열고, 다음을 `<dependencies>` 요소에 추가합니다.

    ```xml
    <dependency>
        <groupId>com.azure.spring</groupId>
        <artifactId>azure-spring-boot-starter-cosmos</artifactId>
        <version>3.0.0</version>
    </dependency>
    ```

1. *properties* 요소가 필요한 버전의 Java 및 Azure를 표시하는지 확인합니다.

    ```xml
    <properties>
        <java.version>11</java.version>
        <azure.version>3.0.0</azure.version>
    </properties>
    ```

1. *pom.xml* 파일을 저장하고 닫습니다.

## <a name="configure-your-spring-boot-application-to-use-your-azure-cosmos-db"></a>Azure Cosmos DB를 사용하도록 Spring Boot 애플리케이션 구성

1. 앱의 *리소스* 디렉터리에서 *application.properties* 파일을 찾습니다.

    `C:\SpringBoot\wingtiptoysdata\src\main\resources\application.properties`

    또는

    `/users/example/home/wingtiptoysdata/src/main/resources/application.properties`

1. 텍스트 편집기에서 *application.properties* 파일을 찾고 파일에 다음 줄을 추가하고 샘플 값을 데이터베이스의 적절한 속성으로 바꿉니다.

    ```properties
    # Specify the DNS URI of your Azure Cosmos DB.
    azure.cosmos.uri=https://contosoaccount.documents.azure.com:443/
    
    # Specify the access key for your database.
    azure.cosmos.key=replace-your-access-key-here
    
    # Specify the name of your database. 
    azure.cosmos.database=contosoaccount
    azure.cosmos.populateQueryMetrics=true
    ```

1. *application.properties* 파일을 저장하고 닫습니다.

## <a name="add-sample-code-to-implement-basic-database-functionality"></a>기본 데이터베이스 기능을 구현하는 샘플 코드 추가

이 섹션에서는 사용자 데이터를 저장하는 두 개의 Java 클래스를 만든 다음, *User* 클래스의 인스턴스를 만들고 이를 데이터베이스에 저장하도록 기본 애플리케이션 클래스를 수정합니다.

### <a name="define-a-base-class-for-storing-user-data"></a>사용자 데이터를 저장하는 기본 클래스 정의

1. 기본 애플리케이션 Java 파일과 동일한 디렉터리에 *User.java* 라는 새 파일을 만듭니다.

1. 텍스트 편집기에서 *User.java* 파일을 다음 줄을 파일에 추가하여 데이터베이스에서 값을 저장하고 검색하는 일반 사용자 클래스를 정의합니다.

    ```java
    package com.example.wingtiptoysdata;
    
    import com.azure.spring.data.cosmos.core.mapping.Container;
    import com.azure.spring.data.cosmos.core.mapping.PartitionKey;
    import org.springframework.data.annotation.Id;
    
    @Container(containerName = "mycollection")
    public class User {
        @Id
        private String id;
        private String firstName;
        @PartitionKey
        private String lastName;
        private String address;
    
        public User() {
    
        }
    
        public User(String id, String firstName, String lastName, String address) {
            this.id = id;
            this.firstName = firstName;
            this.lastName = lastName;
            this.address = address;
        }
    
        public String getId() {
            return id;
        }
    
        public void setId(String id) {
            this.id = id;
        }
    
        public String getFirstName() {
            return firstName;
        }
    
        public void setFirstName(String firstName) {
            this.firstName = firstName;
        }
    
        public String getLastName() {
            return lastName;
        }
    
        public void setLastName(String lastName) {
            this.lastName = lastName;
        }
    
        public String getAddress() {
            return address;
        }
    
        public void setAddress(String address) {
            this.address = address;
        }
    
        @Override
        public String toString() {
            return String.format("%s %s, %s", firstName, lastName, address);
        }
    }
    ```

1. *User.java* 파일을 저장하고 닫습니다.

### <a name="define-a-data-repository-interface"></a>데이터 리포지토리 인터페이스 정의

1. 기본 애플리케이션 Java 파일과 동일한 디렉터리에 *UserRepository.java* 라는 새 파일을 만듭니다.

1. 텍스트 편집기에서 *UserRepository.java* 파일을 열고 파일에 다음 줄을 추가하여 기본 `ReactiveCosmosRepository` 인터페이스를 확장하는 사용자 리포지토리 인터페이스를 정의합니다.

    ```java
    package com.example.wingtiptoysdata;
    
    import com.azure.spring.data.cosmos.repository.ReactiveCosmosRepository;
    import org.springframework.stereotype.Repository;
    import reactor.core.publisher.Flux;
    
    @Repository
    public interface UserRepository extends ReactiveCosmosRepository<User, String> {
        Flux<User> findByFirstName(String firstName);
    }
    ```

    `ReactiveCosmosRepository` 인터페이스는 이전 버전의 스타터에서 `DocumentDbRepository` 인터페이스를 대체합니다. 새 인터페이스는 동기식, 반응형 API로 기본 저장, 삭제 및 찾기 작업을 지원합니다.

1. *UserRepository.java* 파일을 저장하고 닫습니다.

### <a name="modify-the-main-application-class"></a>기본 애플리케이션 클래스 수정

1. 애플리케이션의 패키지 디렉터리에서 다음과 같은 기본 애플리케이션 Java 파일을 찾습니다.

    `C:\SpringBoot\wingtiptoysdata\src\main\java\com\example\wingtiptoysdata\WingtiptoysdataApplication.java`

    또는

    `/users/example/home/wingtiptoysdata/src/main/java/com/example/wingtiptoysdata/WingtiptoysdataApplication.java`

1. 텍스트 편집기에서 애플리케이션 Java 파일을 열고 다음 줄을 파일에 추가합니다.

    ```java
    package com.example.wingtiptoysdata;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.util.Assert;
    import reactor.core.publisher.Flux;
    import reactor.core.publisher.Mono;
    
    import java.util.Optional;
    
    @SpringBootApplication
    public class WingtiptoysdataApplication implements CommandLineRunner {
    
        private static final Logger LOGGER = LoggerFactory.getLogger(WingtiptoysdataApplication.class);
    
        @Autowired
        private UserRepository repository;
    
        public static void main(String[] args) {
            SpringApplication.run(WingtiptoysdataApplication.class, args);
        }
    
        public void run(String... var1) {
            this.repository.deleteAll().block();
            LOGGER.info("Deleted all data in container.");
    
            final User testUser = new User("testId", "testFirstName", "testLastName", "test address line one");
    
            // Save the User class to Azure Cosmos DB database.
            final Mono<User> saveUserMono = repository.save(testUser);
    
            final Flux<User> firstNameUserFlux = repository.findByFirstName("testFirstName");
    
            //  Nothing happens until we subscribe to these Monos.
            //  findById will not return the user as user is not present.
            final Mono<User> findByIdMono = repository.findById(testUser.getId());
            final User findByIdUser = findByIdMono.block();
            Assert.isNull(findByIdUser, "User must be null");
    
            final User savedUser = saveUserMono.block();
            Assert.state(savedUser != null, "Saved user must not be null");
            Assert.state(savedUser.getFirstName().equals(testUser.getFirstName()), "Saved user first name doesn't match");
    
            firstNameUserFlux.collectList().block();
    
            final Optional<User> optionalUserResult = repository.findById(testUser.getId()).blockOptional();
            Assert.isTrue(optionalUserResult.isPresent(), "Cannot find user.");
    
            final User result = optionalUserResult.get();
            Assert.state(result.getFirstName().equals(testUser.getFirstName()), "query result firstName doesn't match!");
            Assert.state(result.getLastName().equals(testUser.getLastName()), "query result lastName doesn't match!");
    
            LOGGER.info("findOne in User collection get result: {}", result.toString());
        }
    }
    ```

1. 기본 애플리케이션 Java 파일을 저장하고 닫습니다.

## <a name="build-and-test-your-app"></a>앱 빌드 및 테스트

1. 명령 프롬프트를 열고 *pom.xml* 파일이 위치한 폴더로 이동합니다. 예를 들어 다음과 같습니다.

    `cd C:\SpringBoot\wingtiptoysdata`

    또는

    `cd /users/example/home/wingtiptoysdata`

1. 다음 명령을 사용하여 애플리케이션을 빌드하고 실행합니다.

    ```console
    mvnw clean
    ```

    이 명령은 테스트 단계의 일부로 애플리케이션을 자동으로 실행합니다. 다음을 사용할 수도 있습니다.

    ```console
    mvnw spring-boot:run
    ```

    일부 빌드 및 테스트 출력 후 콘솔 창에 다음과 비슷한 메시지가 표시됩니다.

    ```console
    INFO 1365 --- [           main] c.e.w.WingtiptoysdataApplication         : Deleted all data in container.
    
    ... (omitting connection and diagnostics output) ...
    
    INFO 1365 --- [           main] c.e.w.WingtiptoysdataApplication         : findOne in User collection get result: testFirstName testLastName, test address line one
    ```

    위의 출력 메시지는 데이터를 Cosmos DB에 성공적으로 저장한 다음, 다시 검색했음을 의미합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 앞에서 만든 Cosmos DB가 포함된 리소스 그룹을 삭제해야 합니다. Azure Portal에서 이 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Spring과 Azure에 대한 자세한 사항은 Azure의 Spring 설명서 센터를 참조합니다.

> [!div class="nextstepaction"]
> [Azure의 Spring](./index.yml)

### <a name="more-resources"></a>기타 참고 자료

Azure Cosmos DB 및 Java를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Cosmos DB 설명서]

* [Azure Cosmos DB: Java 및 Azure Portal을 사용하여 문서 데이터베이스 만들기][Build a SQL API app with Java]

* [Azure Cosmos DB SQL API용 Spring 데이터]

Azure에서 Spring Boot 애플리케이션을 사용 하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Cosmos DB용 Azure Spring Boot Starter]

* [Azure App Service에서 Linux에 Spring Boot 애플리케이션 배포](deploy-spring-boot-java-app-on-linux.md)

* [Azure Container Service의 Kubernetes 클러스터에 Spring Boot 애플리케이션 실행](deploy-spring-boot-java-app-on-kubernetes.md)

Java와 함께 Azure를 사용하는 방법에 관한 자세한 정보는 [Java 개발자를 위한 Azure]와 [Azure DevOps 및 Java 사용하기]를 참조하세요.

**[Spring Framework]** 는 Java 개발자가 엔터프라이즈 수준의 애플리케이션을 만드는 데 도움이 되는 오픈 소스 솔루션입니다. 해당 플랫폼을 기반으로 하여 빌드되는 인기 있는 프로젝트 중 하나가 [Spring Boot]입니다. 이 프로젝트는 독립 실행형 Java 애플리케이션을 만드는 간단한 방법을 제공합니다. Spring Boot을 시작하는 개발자를 도우려면 <https://github.com/spring-guides/>에서 몇 가지 샘플 Spring Boot 패키지를 사용할 있습니다. 기본 Spring Boot 프로젝트 목록에서 선택하는 것 외에도 **[Spring Initializr]** 를 통해 사용자 지정 Spring Boot 애플리케이션을 만들기 시작하는 개발자에게 도움을 줍니다.

<!-- URL List -->

[Azure Cosmos DB 설명서]: /azure/cosmos-db/
[Java 개발자를 위한 Azure]: ../index.yml
[Build a SQL API app with Java]: /azure/cosmos-db/create-sql-api-java
[Azure Cosmos DB SQL API용 Spring 데이터]: https://azure.microsoft.com/blog/spring-data-azure-cosmos-db-nosql-data-access-on-azure/
[Azure Cosmos DB용 Azure Spring Boot Starter]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmos
[체험판 Azure 계정]: https://azure.microsoft.com/pricing/free-trial/
[Azure DevOps 및 Java 사용하기]: https://azure.microsoft.com/services/devops/java/
[MSDN 구독자 혜택]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[azure-spring-boot-version-matrix]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring#azure-spring-boot

<!-- IMG List -->
[AZ02]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ02.png
[AZ03]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ03.png
[AZ05]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ05.png

[SI01]: media/configure-spring-boot-starter-java-app-with-cosmos-db/SI01.png
