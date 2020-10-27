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
ms.openlocfilehash: fdc0892298c18e3be5db1b97b1ad5fe77ac6a537
ms.sourcegitcommit: 76f1a47c58810486856e0d128bd154cf7d355e65
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92200628"
---
# <a name="how-to-use-the-spring-boot-starter-with-the-azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API에서 Spring Boot Starter를 사용하는 방법

Azure Cosmos DB는 개발자가 SQL, MongoDB, Graph 및 Table API와 같은 표준 API를 사용하여 데이터를 사용할 수 있도록 하는 전역 분산 데이터베이스 서비스입니다. Microsoft의 Spring Boot Starter를 사용하면 개발자가 SQL API를 사용하여 Azure Cosmos DB와 쉽게 통합하는 Spring Boot 애플리케이션을 사용할 수 있습니다.

이 문서에서는 Azure Portal을 사용하여 Azure Cosmos DB를 만들고, **[Spring Initializr]** 를 사용하여 사용자 지정 Spring Boot 애플리케이션을 만든 다음, [Azure용 Spring Boot Cosmos DB Starter]를 사용자 지정 애플리케이션에 추가하여 Spring Data 및 Cosmos DB SQL API를 통해 Azure Cosmos DB에서 데이터를 저장하고 검색하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택]을 활성화하거나 [체험판 Azure 계정]에 등록할 수 있습니다.
* 지원되는 JDK(Java Development Kit) Azure에서 개발하는 경우 사용할 수 있는 JDK에 대한 자세한 내용은 <https://aka.ms/azure-jdks>를 참조하세요.

## <a name="create-an-azure-cosmos-db-by-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Cosmos DB 만들기

1. Azure Portal(<https://portal.azure.com/>)로 이동하고 **리소스 만들기** 를 클릭합니다.

1. **데이터베이스** 를 클릭한 후 **Azure Cosmos DB** 를 클릭합니다.

    ![Azure Portal에서 Azure Cosmos DB를 선택합니다.][AZ02]

1. **Azure Cosmos DB** 페이지에서 다음 정보를 입력합니다.

    * 데이터베이스에 사용하려는 **구독** 을 선택합니다.
    * 데이터베이스에 새 **리소스 그룹** 을 만들지 아니면 기존 리소스 그룹을 선택할지를 지정합니다.
    * 데이터베이스에 대한 URI로 사용할 고유한 **계정 이름** 을 입력합니다. 예를 들어 *wingtiptoysdata* 입니다.
    * API에 대한 **Core(SQL)** 를 선택합니다.
    * 데이터베이스의 **위치** 를 지정합니다.

    이러한 옵션이 지정되면 **검토 + 만들기** 를 클릭하고 사양을 검토한 후 **만들기** 를 클릭합니다.

    ![검토 + 만들기를 선택하여 계속 진행합니다.][AZ03]

1. 데이터베이스를 만든 경우 Azure **대시보드** 뿐 아니라 **모든 리소스** 및 **Azure Cosmos DB** 페이지에도 나열됩니다. 해당 위치 중 하나에서 데이터베이스를 클릭하여 캐시에 대한 속성 페이지를 열 수 있습니다.

1. 데이터베이스에 대한 속성 페이지가 표시되면 **키** 를 클릭하고 데이터베이스에 대한 URI 및 액세스 키를 복사합니다. 이러한 값은 Spring Boot 애플리케이션에서 사용하게 됩니다.

    ![키 섹션에서 URI 및 액세스 키를 복사합니다.][AZ05]

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Spring Initializr를 사용하여 간단한 Spring Boot 애플리케이션 만들기

다음 단계를 사용하여 Azure 지원으로 새 Spring Boot 애플리케이션 프로젝트를 만듭니다. 대안으로 [azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) 리포지토리에서 [azure-spring-boot-sample-cosmosdb](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-boot-sample-cosmos) 샘플을 사용할 수 있습니다. 그런 다음, [앱 빌드 및 테스트](#build-and-test-your-app)로 직접 건너뛸 수 있습니다.

1. [https://www.microsoft.com]\(<https://start.spring.io/>) 로 이동합니다.

1. 다음 옵션을 지정합니다.

   * **Java** 를 사용하는 **Maven** 프로젝트를 생성합니다.
   * **Spring Boot** 버전을 지정합니다.
   * 애플리케이션에 대한 **그룹** 및 **아티팩트** 이름을 지정합니다.
   * Java 버전에 대해 **8** 을 선택합니다.
   * 종속성에서 **Azure 지원** 을 추가합니다.

   >[!div class="mx-imgBorder"]
   >![기본 Spring Initializr 옵션][SI01]

   > [!NOTE]
   > Spring Initializr는 **그룹** 및 **아티팩트** 이름을 사용하여 패키지 이름을 만듭니다(예: *com.example.wingtiptoysdata* ).

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
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
        <version>2.3.3</version>
    </dependency>
    ```

1. *properties* 요소가 필요한 버전의 Java 및 Azure를 표시하는지 확인합니다.

    ```xml
    <properties>
       <java.version>1.8</java.version>
       <azure.version>2.2.0</azure.version>
    </properties>
    ```

1. *pom.xml* 파일을 저장하고 닫습니다.

## <a name="configure-your-spring-boot-application-to-use-your-azure-cosmos-db"></a>Azure Cosmos DB를 사용하도록 Spring Boot 애플리케이션 구성

1. 앱의 *리소스* 디렉터리에서 *application.properties* 파일을 찾습니다.

    `C:\SpringBoot\wingtiptoysdata\src\main\resources\application.properties`

    또는

    `/users/example/home/wingtiptoysdata/src/main/resources/application.properties`

1. 텍스트 편집기에서 *application.properties* 파일을 찾고 파일에 다음 줄을 추가하고 샘플 값을 데이터베이스의 적절한 속성으로 바꿉니다.

    ```text
    # Specify the DNS URI of your Azure Cosmos DB.
    azure.cosmosdb.uri=https://wingtiptoys.documents.azure.com:443/

    # Specify the access key for your database.
    azure.cosmosdb.key=57686f6120447564652c20426f6220526f636b73==

    # Specify the name of your database.
    azure.cosmosdb.database=wingtiptoysdata
    ```

1. *application.properties* 파일을 저장하고 닫습니다.

## <a name="add-sample-code-to-implement-basic-database-functionality"></a>기본 데이터베이스 기능을 구현하는 샘플 코드 추가

이 섹션에서는 사용자 데이터를 저장하는 두 개의 Java 클래스를 만든 다음, *User* 클래스의 인스턴스를 만들고 이를 데이터베이스에 저장하도록 기본 애플리케이션 클래스를 수정합니다.

### <a name="define-a-base-class-for-storing-user-data"></a>사용자 데이터를 저장하는 기본 클래스 정의

1. 기본 애플리케이션 Java 파일과 동일한 디렉터리에 *User.java* 라는 새 파일을 만듭니다.

1. 텍스트 편집기에서 *User.java* 파일을 다음 줄을 파일에 추가하여 데이터베이스에서 값을 저장하고 검색하는 일반 사용자 클래스를 정의합니다.

    ```java
    package com.example.wingtiptoysdata;

    import com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document;
    import com.microsoft.azure.spring.data.cosmosdb.core.mapping.PartitionKey;
    import org.springframework.data.annotation.Id;

    @Document(collection = "mycollection")
    public class User {

        @Id
        private String id;
        private String firstName;

        @PartitionKey
        private String lastName;
        private String address;

        public User(String id, String firstName, String lastName, String address) {
            this.id = id;
            this.firstName = firstName;
            this.lastName = lastName;
            this.address = address;
        }

        public User() {
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

    import com.microsoft.azure.spring.data.cosmosdb.repository.ReactiveCosmosRepository;
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

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.util.Assert;
    import reactor.core.publisher.Flux;
    import reactor.core.publisher.Mono;

    import javax.annotation.PostConstruct;
    import javax.annotation.PreDestroy;
    import java.util.Optional;

    @SpringBootApplication
    public class WingtiptoysdataApplication implements CommandLineRunner {

        private static final Logger LOGGER = LoggerFactory.getLogger(WingtiptoysdataApplication.class);

        @Autowired
        private UserRepository repository;

        public static void main(String[] args) {
            SpringApplication.run(WingtiptoysdataApplication.class, args);
        }

        public void run(String... var1) throws Exception {
            final User testUser = new User("1", "Tasha", "Calderon", "4567 Main St Buffalo, NY 98052");

            LOGGER.info("Saving user: {}", testUser);

            // Save the User class to Azure CosmosDB database.
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

            LOGGER.info("Saved user");

            firstNameUserFlux.collectList().block();

            final Optional<User> optionalUserResult = repository.findById(testUser.getId()).blockOptional();
            Assert.isTrue(optionalUserResult.isPresent(), "Cannot find user.");

            final User result = optionalUserResult.get();
            Assert.state(result.getFirstName().equals(testUser.getFirstName()), "query result firstName doesn't match!");
            Assert.state(result.getLastName().equals(testUser.getLastName()), "query result lastName doesn't match!");

            LOGGER.info("Found user by findById : {}", result);
        }

        @PostConstruct
        public void setup() {
            LOGGER.info("Clear the database");
            this.repository.deleteAll().block();
        }

        @PreDestroy
        public void cleanup() {
            LOGGER.info("Cleaning up users");
            this.repository.deleteAll().block();
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
    mvnw clean test
    ```

    이 명령은 테스트 단계의 일부로 애플리케이션을 자동으로 실행합니다. 다음을 사용할 수도 있습니다.

    ```console
    mvnw clean spring-boot:run
    ```

    일부 빌드 및 테스트 출력 후 콘솔 창에 다음과 비슷한 메시지가 표시됩니다.

    ```console
      .   ____          _            __ _ _
     /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
    ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
     \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
      '  |____| .__|_| |_|_| |_\__, | / / / /
     =========|_|==============|___/=/_/_/_/
     :: Spring Boot ::            (v2.2.0.RC1)
    >
    > 2019-10-04 15:19:06.817  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplicationTests    : Starting WingtiptoysdataApplicationTests on devmachine03 with PID 30013 (started by <user> in /d/source/repos/wingtiptoysdata)
    > 2019-10-04 15:19:06.818  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplicationTests    : No active profile set, falling back to default profiles: default
    > 2019-10-04 15:19:08.329  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data repositories in DEFAULT mode.
    > 2019-10-04 15:19:09.720  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 1369ms. Found 1 repository interfaces.
    > 2019-10-04 15:19:09.734  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data repositories in DEFAULT mode.
    > 2019-10-04 15:19:09.748  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 13ms. Found 0 repository interfaces.

    ... (omitting Cosmos DB connection output) ...

    > 2019-10-04 15:19:46.584  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplicationTests    : Started WingtiptoysdataApplicationTests in 40.702 seconds (JVM running for 44.647)
    > 2019-10-04 15:19:46.587  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplication         : Saving user: Tasha Calderon, 4567 Main St Buffalo, NY 98052
    > 2019-10-04 15:19:47.122  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplication         : Saved user
    > 2019-10-04 15:19:47.289  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplication         : Found user by findById : Tasha Calderon, 4567 Main St Buffalo, NY 98052
    > [INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 44.003 s - in com.example.wingtiptoysdata.WingtiptoysdataApplicationTests
    > 2019-10-04 15:19:48.124  INFO 30013 --- [extShutdownHook] c.a.d.c.internal.RxDocumentClientImpl    : Shutting down ...
    > 2019-10-04 15:19:48.194  INFO 30013 --- [extShutdownHook] c.a.d.c.internal.RxDocumentClientImpl    : Shutting down ...
    > 2019-10-04 15:19:48.200  INFO 30013 --- [extShutdownHook] c.e.w.WingtiptoysdataApplication         : Cleaning up users
    > [INFO]
    > [INFO] Results:
    > [INFO]
    > [INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
    > [INFO]
    > [INFO]
    > [INFO] --- maven-jar-plugin:3.1.2:jar (default-jar) @ wingtiptoysdata ---
    > [INFO] Building jar: /d/source/repos/wingtiptoysdata/target/wingtiptoysdata-0.0.1-SNAPSHOT.jar
    > [INFO]
    > [INFO] --- spring-boot-maven-plugin:2.2.0.RC1:repackage (repackage) @ wingtiptoysdata ---
    > [INFO] Replacing main artifact with repackaged archive
    > [INFO] ------------------------------------------------------------------------
    > [INFO] BUILD SUCCESS
    > [INFO] ------------------------------------------------------------------------
    > [INFO] Total time:  02:18 min
    > [INFO] Finished at: 2019-10-04T15:20:05-07:00
    > [INFO] ------------------------------------------------------------------------
    ```

    ![애플리케이션에서 성공적인 출력][JV02]

    `Saved user` 및 `Found user` 메시지는 데이터를 Cosmos DB에 성공적으로 저장한 다음, 다시 검색했음을 의미합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 앞에서 만든 Cosmos DB가 포함된 리소스 그룹을 삭제해야 합니다. Azure Portal에서 이 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Spring과 Azure에 대한 자세한 사항은 Azure의 Spring 설명서 센터를 참조합니다.

> [!div class="nextstepaction"]
> [Azure의 Spring](./index.yml)

### <a name="additional-resources"></a>추가 리소스

Azure Cosmos DB 및 Java를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Cosmos DB 설명서]

* [Azure Cosmos DB: Java 및 Azure Portal을 사용하여 문서 데이터베이스 만들기][Build a SQL API app with Java]

* [Azure Cosmos DB SQL API용 Spring 데이터]

Azure에서 Spring Boot 애플리케이션을 사용 하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure용 Spring Boot Cosmos DB Starter]

* [Azure App Service에서 Linux에 Spring Boot 애플리케이션 배포](deploy-spring-boot-java-app-on-linux.md)

* [Azure Container Service의 Kubernetes 클러스터에 Spring Boot 애플리케이션 실행](deploy-spring-boot-java-app-on-kubernetes.md)

Java와 함께 Azure를 사용하는 방법에 관한 자세한 정보는 [Java 개발자를 위한 Azure]와 [Azure DevOps 및 Java 사용하기]를 참조하세요.

**[Spring Framework]** 는 Java 개발자가 엔터프라이즈 수준의 애플리케이션을 만드는 데 도움이 되는 오픈 소스 솔루션입니다. 해당 플랫폼을 기반으로 하여 빌드되는 인기 있는 프로젝트 중 하나가 [Spring Boot]입니다. 이 프로젝트는 독립 실행형 Java 애플리케이션을 만드는 간단한 방법을 제공합니다. Spring Boot을 시작하는 개발자를 도우려면 <https://github.com/spring-guides/>에서 몇 가지 샘플 Spring Boot 패키지를 사용할 있습니다. 기본 Spring Boot 프로젝트 목록에서 선택하는 것 외에도 **[Spring Initializr]** 를 통해 사용자 지정 Spring Boot 애플리케이션을 만들기 시작하는 개발자에게 도움을 줍니다.

<!-- URL List -->

[Azure Cosmos DB 설명서]: /azure/cosmos-db/
[Java 개발자를 위한 Azure]: ../index.yml
[Build a SQL API app with Java]: /azure/cosmos-db/create-sql-api-java
[Azure Cosmos DB SQL API용 Spring 데이터]: https://azure.microsoft.com/blog/spring-data-azure-cosmos-db-nosql-data-access-on-azure/
[Azure용 Spring Boot Cosmos DB Starter]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmos
[체험판 Azure 계정]: https://azure.microsoft.com/pricing/free-trial/
[Azure DevOps 및 Java 사용하기]: https://azure.microsoft.com/services/devops/java/
[MSDN 구독자 혜택]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[AZ01]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ01.png
[AZ02]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ02.png
[AZ03]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ03.png
[AZ04]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ04.png
[AZ05]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ05.png

[SI01]: media/configure-spring-boot-starter-java-app-with-cosmos-db/SI01.png

[JV02]: media/configure-spring-boot-starter-java-app-with-cosmos-db/JV02.png
