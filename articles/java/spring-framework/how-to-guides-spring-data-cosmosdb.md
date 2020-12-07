---
title: Spring Data Azure Cosmos DB 개발자 가이드
description: 이 가이드에서는 Spring Data Azure Cosmos DB SDK를 사용하는 분들이 알고 있어야 하는 사항에 대해 설명합니다.
author: anfeldma-ms
ms.author: anfeldma
ms.topic: conceptual
ms.date: 11/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 45872eba2fd6929cf406df4a551559fd5ad78c40
ms.sourcegitcommit: 63732132cb88206b99876f0bcd035b52c301f315
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523138"
---
# <a name="spring-data-azure-cosmos-db-developers-guide"></a>Spring Data Azure Cosmos DB 개발자 가이드

이 토픽에서는 SQL API를 사용하는 [Spring Data Azure Cosmos DB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos)의 기능에 대해 설명합니다. 이 토픽에는 일반적인 이슈, 해결 방법 및 진단 단계에 대한 지침도 포함되어 있습니다.

[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction)는 개발자가 다양한 표준 API를 사용하여 데이터를 작업할 수 있게 해주는 전역적으로 분산된 데이터베이스 서비스입니다. Spring Data Azure Cosmos DB SDK는 [Spring Data](https://spring.io/projects/spring-data) 프레임워크를 기반으로 하며 SQL API를 사용하여 Azure Cosmos DB와의 통합을 제공합니다. 다른 API에 대한 지원 정보는 다음 토픽에서 찾을 수 있습니다.

- [Azure Cosmos DB에서 Spring Data MongoDB API를 사용하는 방법](./configure-spring-data-mongodb-with-cosmos-db.md)
- [Azure Cosmos DB에서 Spring Data Apache Cassandra API를 사용하는 방법](./configure-spring-data-apache-cassandra-with-cosmos-db.md) 문서에서 구현된 데이터베이스)
- [Azure Cosmos DB SQL API에서 Spring Data Gremlin Starter를 사용하는 방법](./configure-spring-data-gremlin-java-app-with-cosmos-db.md)

Spring Data Azure Cosmos DB SDK는 GitHub의 [azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) 리포지토리에서 오픈 소스로 제공됩니다. 이 리포지토리에는 버그를 파일로 작성하거나 이미 파일로 작성된 이슈의 해결 방법을 확인할 수 있는 활성 [이슈](https://github.com/Azure/azure-sdk-for-java/issues) 목록이 있습니다. [릴리스](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md) 목록을 확인하여 최신 버전에서 이슈가 해결되었는지 확인할 수도 있습니다. 

## <a name="available-features"></a>사용 가능한 기능

다음 섹션에서는 현재 사용할 수 있는 기능에 대해 설명합니다.

### <a name="crudrepository-and-reactivecrudrepository-support"></a>CrudRepository 및 ReactiveCrudRepository 지원

Spring Data Azure Cosmos DB SDK는 Spring Data `CrudRepository` 및 `ReactiveCrudRepository` 인터페이스를 확장하는 `CosmosRepository` 및 `ReactiveCosmosRepository` 인터페이스를 제공합니다.

다음 예제에서는 이러한 인터페이스를 확장하는 방법을 보여줍니다.

```java
@Repository
public interface SampleRepository extends CosmosRepository<SampleEntity, String> {
    List<SampleEntity> findByName(String name);
}

@Repository
public interface ReactiveSampleRepository extends ReactiveCosmosRepository<SampleEntity, String> {
    Flux<SampleEntity> findByName(String name);
}
```

사용량에 따라 `Configuration` 클래스에서 두 리포지토리를 별도로 사용하도록 설정해야 합니다. 예:

```java
@Configuration
@EnableConfigurationProperties(CosmosProperties.class)
@EnableCosmosRepositories
@EnableReactiveCosmosRepositories
@PropertySource("classpath:application.properties")
public class TestRepositoryConfig extends AbstractCosmosConfiguration {
    ...
}
```

### <a name="define-a-simple-entity"></a>단순 엔터티 정의

`@Container` 주석을 추가하고 컬렉션 이름, RU(요청 단위), TTL(time-to-live) 및 컬렉션 플래그 자동 생성 같은 컬렉션 관련 속성을 지정하여 엔터티를 정의할 수 있습니다.

기본적으로 컬렉션 이름은 사용자 도메인 클래스의 클래스 이름이 됩니다. 컬렉션 이름을 사용자 지정하려면 도메인 클래스에 `@Container(containerName="myCustomCollectionName")` 주석을 추가합니다. `containerName` 필드는 [SpEL(Spring Expression Language)](https://docs.spring.io/spring/docs/3.0.x/reference/expressions.html) 식도 지원하므로 구성 속성을 통해 프로그래밍 방식으로 컬렉션 이름을 제공할 수 있습니다. 예를 들어 `containerName = "${dynamic.container.name}"` 및 `containerName = "#{@someBean.getContainerName()}"` 같은 식을 사용할 수 있습니다.

다음과 같은 두 가지 방법으로 도메인 클래스의 필드를 Azure Cosmos DB 문서의 `id` 필드에 매핑할 수 있습니다.

- `@Id`를 사용하여 필드에 주석을 추가합니다.
- 필드 이름을 `id`로 설정합니다.

다음 예제에서는 `@Container` 및 `@Id` 주석을 사용하는 방법을 보여줍니다.

```java
@Container(containerName = "myContainer")
class MyDocument {

    @Id
    private String myId;

    @PartitionKey
    private String data;

    @Version
    private String _etag;
}
```

기본적으로 `IndexingPolicy`는 Azure 서비스에서 설정됩니다. 사용자 지정하려면 도메인 클래스에 `@DocumentIndexingPolicy` 주석을 추가합니다. 이 주석은 다음과 같은 네 가지 특성이 있습니다.

```java
boolean automatic;     // Indicates whether the indexing policy is automatic.
IndexingMode mode;     // The indexing policy mode; the options are Consistent, Lazy, or None.
String[] includePaths; // Included paths for indexing.
String[] excludePaths; // Excluded paths for indexing.
```

SDK가 분할도 지원합니다. 자세한 내용은 [Azure Cosmos DB에서 분할 및 수평 확장](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#:~:text=%20Partitioning%20and%20horizontal%20scaling%20in%20Azure%20Cosmos,partitions.%20Azure%20Cosmos%20DB%20transparently%20and...%20More%20)을 참조하세요. 도메인 클래스의 필드를 파티션 키 필드로 지정하려면 `@PartitionKey`를 사용하여 주석을 추가합니다. 그런 다음, CRUD 작업을 수행할 때 파티션 값을 지정합니다.

다음 예제에서는 CRUD 작업을 수행할 때 `@PartitionKey` 주석을 사용하는 방법을 보여줍니다.

```java
@Container(ru = "400")
public class Address {
    @Id
    String postalCode;

    @PartitionKey
    String city;

    String street;
    String country;
    String phoneNumber;

    ...
}

class AddressService {

    @Autowired
    AddressRepository repository;

    final Address newAddress = new Address("12345", "Seattle");

    // There's no need to specify a partition key in the save operation.
    repository.save(updatedAddress);

    // Provide a partition key when performing a find-by-id operation.
    final Optional<Address> addressById = repository.findById("12345", new PartitionKey("city"));

    final Address foundAddress = addressById.get();

    // Provide a partition key when performing a delete-by-id operation.
    repository.deleteById(foundAddress.getPostalCode(), new PartitionKey(foundAddress.getCity())); 
}
```

SDK가 `findByAFieldAndBField` 같은 Spring Data 사용자 지정 쿼리 찾기 작업도 지원합니다. 자세한 내용은 Spring 설명서의 [쿼리 메서드 정의](https://docs.spring.io/spring-data/commons/docs/current/reference/html/#repositories.query-methods.details)를 참조하세요.

## <a name="best-practices"></a>모범 사례

다음 섹션에서는 SDK 사용 시 모범 사례에 대해 설명합니다.

### <a name="pulling-configuration-properties-into-the-application"></a>구성 속성을 애플리케이션으로 끌어오기

**application.properties** 설정을 Java 액세스 메서드로 노출하는 속성 클래스를 만들 수 있습니다. **application.properties** 구조는

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

# Populate query metrics
cosmos.queryMetricsEnabled=true
```

이 구조를 미러링하고 다음과 같이 구조화된 Java 클래스 `CosmosProperties`를 만듭니다.

```java
@ConfigurationProperties(prefix = "cosmos")
public class CosmosProperties {

    private String uri;

    private String key;

    private String secondaryKey;

    private boolean queryMetricsEnabled;

    public String getUri() {
        return uri;
    }

    public void setUri(String uri) {
        this.uri = uri;
    }

    public String getKey() {
        return key;
    }

    public void setKey(String key) {
        this.key = key;
    }

    public String getSecondaryKey() {
        return secondaryKey;
    }

    public void setSecondaryKey(String secondaryKey) {
        this.secondaryKey = secondaryKey;
    }

    public boolean isQueryMetricsEnabled() {
        return queryMetricsEnabled;
    }

    public void setQueryMetricsEnabled(boolean enableQueryMetrics) {
        this.queryMetricsEnabled = enableQueryMetrics;
    }
}
```

이 클래스에는 각 **application.properties** 구성 속성에 해당하는 멤버가 있으며, 각 멤버 `CosmosProperties`에 대해 *get* 및 *set* 메서드가 표시됩니다. `@ConfigurationProperties` 주석은 구성 속성을 나타내는 것으로 클래스를 식별하고, `prefix = "cosmos"` 인수는 `CosmosProperties`의 지정된 *멤버* 가 **application.properties** 의 `cosmos.member` 속성에 매핑됨을 나타냅니다.

다음 섹션에서는 자동화된 구성 흐름에 `CosmosProperties` 클래스를 통합하는 방법을 보여줍니다. 구성 시 `CosmosProperties` 인스턴스가 만들어지고 해당 인스턴스 메서드가 **application.properties** 의 구성 설정으로 채워집니다. 이 속성 인스턴스를 사용하면 애플리케이션이 런타임에서 구성 속성을 읽고 수정할 수 있습니다.

### <a name="configuring-the-application-based-on-properties"></a>속성을 기반으로 애플리케이션 구성

다음 단계는 애플리케이션 구성을 자동화하는 구성 클래스를 만드는 것입니다. 여기 예제에서 만드는 방법을 살펴보도록 하겠습니다.

```java
@Configuration
@EnableConfigurationProperties(CosmosProperties.class)
@EnableCosmosRepositories
@EnableReactiveCosmosRepositories
@PropertySource("classpath:application.properties")
public class AppConfiguration extends AbstractCosmosConfiguration {

    private static final Logger logger = LoggerFactory.getLogger(QuickstartSampleConfiguration.class);

    @Autowired
    private CosmosProperties properties;

    private AzureKeyCredential azureKeyCredential;

    @Bean
    public CosmosClientBuilder cosmosClientBuilder() {
        this.azureKeyCredential = new AzureKeyCredential(properties.getKey());
        return new CosmosClientBuilder()
            .endpoint(properties.getUri())
            .key(this.azureKeyCredential)
    }

    @Bean
    public CosmosConfig cosmosConfig() {
        DirectConnectionConfig directConnectionConfig = DirectConnectionConfig.getDefaultConfig();        
        return CosmosConfig.builder()
                           .responseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation())
                           .enableQueryMetrics(properties.isQueryMetricsEnabled())
                           .directMode(directConnectionConfig);                           
                           .build();
    }

    @Override
    protected String getDatabaseName() {
        return "testdb";
    }

    private static class ResponseDiagnosticsProcessorImplementation implements ResponseDiagnosticsProcessor {

        @Override
        public void processResponseDiagnostics(@Nullable ResponseDiagnostics responseDiagnostics) {
            logger.info("Response Diagnostics {}", responseDiagnostics);
        }
    }

    public void switchToSecondaryKey() {
        this.cosmosKeyCredential.key(secondaryKey);
    }
}
```

구성 클래스의 구조를 만들려면 다음을 수행합니다.

1. `AbstractCosmosConfiguration` 클래스를 확장하여 애플리케이션의 구성(Cosmos DB 키, URL, 데이터베이스 이름 등)을 설정합니다.
1. `@Configuration` 주석을 추가합니다.
1. 리포지토리 사용에 따라 `@EnableCosmosRepositories` 및 `@EnableReactiveCosmosRepositories` 주석 중 하나 또는 둘 다 추가합니다.
1. **application.properties** 에서 속성의 키/값 쌍을 추출하기 위한 신호인 `@PropertySource("classpath:application.properties")` 주석을 추가합니다.
1. **application.properties** 의 키/값 쌍을 저장할 수 있는 클래스에 Spring Data를 가리키는 `@EnableConfigurationProperties` 주석을 추가합니다. 이 주석은 클래스 정의를 인수로 간주합니다. `CosmosProperties.class`를 전달해야 합니다.

구성 클래스는 다음 멤버를 활용합니다.

1. Spring Data가 모든 로그 출력에 사용할 log4j2 `logger` 멤버를 선언하고 정의합니다.
1. `@Autowired` `CosmosProperties` 멤버를 선언합니다. **이는 application.properties 설정이 보관되는 위치입니다.**

`AzureKeyCredential` 기능을 사용하여 키를 필요할 때 회전할 수 있습니다. 이를 사용하려면 `AzureKeyCredential` 멤버를 정의합니다. 위의 예와 같이 `switchToSecondaryKey` 메서드를 추가하여 키를 전환할 수 있습니다.

다음으로 자동화된 구성을 수행하는 방법을 정의해야 합니다.
1. `CosmosClientBuilder`를 사용하여 클라이언트 초기화를 처리하려면 `@Bean` `cosmosClientBuilder` 메서드를 정의합니다. 이 메서드의 목적은 기본적인 클라이언트 설정(예: 계정 엔드포인트 URI 및 액세스 키 지정)을 수행하는 것입니다. 일반적으로 계정 엔드포인트 URI 및 액세스 키는 **application.properties** 에 정의되며 `properties`에 채워집니다. `properties.getKey()`로 `azureKeyCredential` 멤버를 초기화한 다음, `properties.getUri()` 및 `this.azureKeyCredential`을 `endpoint` 및 `key` 빌더 메서드에 각각 피드할 수 있습니다. 

위의 예제에서 `cosmosClientBuilder`는 클라이언트 작성기에서 `build()`를 호출하지 않습니다. 이 호출은 완성되지 않은 빌더 구조를 반환합니다. Spring Data에서는 두 가지 단계로 구성을 수행할 수 있습니다. 첫 번째 `cosmosClientBuilder`에서는 기본 구성을 적용하고 구성 구조를 반환할 수 있습니다. 그런 다음, Spring Data에서 메트릭 및 진단과 같은 고급 구성을 정의할 수 있는 `cosmosConfig` 메서드를 호출합니다. 다음으로 `cosmosConfig` 메서드에서 이 고급 구성을 살펴보겠습니다.
1. 위와 같이 `@Bean` `cosmosConfig` 메서드를 만듭니다.
1. Azure Cosmos DB는 각 요청과 관련된 서버측 진단을 반환할 수 있습니다. Spring Data를 사용하면 고객 진단 프로세서를 정의하여 기록하기 전에 원시 진단 출력을 변환할 수 있습니다. 위에 표시된 것처럼 `ResponseDiagnosticsProcessor`를 구현하고 `processResponseDiagnostics` 메서드를 재정의하는 클래스를 정의합니다. 진단 출력 처리 방법을 제어하기 위해 `processResponseDiagnostics`를 정의할 수 있습니다. 위의 예제는 원시 진단을 기록하기만 하면 됩니다.
1. 진단을 사용하도록 설정하고 진단 프로세서를 초기화하려면 `responseDiagnosticsProcessor` 빌더 메서드를 호출하여 고객 프로세서 클래스의 새 인스턴스를 전달합니다.

    ```java
    return CosmosConfig.builder()
                       .responseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation())
    ```
1. 또한 Azure Cosmos DB에는 쿼리 메트릭이라고 불리는 쿼리에 대한 보다 구체적인 성능 메트릭 기능도 있습니다. 이전 섹션에 나온 것처럼 쿼리 메트릭을 활성화/비활성화하는 **application.properties** 설정을 사용하는 것이 가장 좋습니다. `cosmosConfig`에서 `.enableQueryMetrics(properties.isQueryMetricsEnabled())` 빌더 메서드를 태킹하여 이 구성 설정을 적용합니다.
1. 최소 지연 시간 및 최대 처리량을 위해 직접 모드 연결이 권장되므로 클라이언트 빌더에서도 구성할 수 있습니다.

`cosmosConfig`의 고급 구성이 완료되면 구성 구조에서 `build()`를 호출하여 클라이언트 생성을 트리거해야 합니다. 그러면 구성 설정에 따라 Azure Cosmos DB 클라이언트 인스턴스가 생성됩니다.

구성 프로세스를 정의하는 마지막 단계는 Azure Cosmos DB 데이터베이스의 이름을 문자열로 반환하는 `@Override` 메서드 `getDatabaseName()`을 추가하는 것입니다.

### <a name="customizing-the-configuration"></a>구성 사용자 지정

또한 다음 예제와 같이 연결 모드, 최대 연결 풀 크기, 요청 시간 제한 등을 변경하도록 구성을 사용자 지정할 수 있습니다.

```java
    @Bean
    public CosmosConfig cosmosConfig() {

        // Set the connection mode to Direct (TCP) which applies to data plane operations
        DirectConnectionConfig directConnectionConfig = DirectConnectionConfig.getDefaultConfig(); 

        // Even in Direct mode, some control plane operations always pass through the gateway as HTTP requests (i.e. container/database CRUD.)
        // Optionally, you can customize connection properties for these specific operations which are
        // always Gateway mode
        GatewayConnectionConfig gatewayConnectionConfig = GatewayConnectionConfig.getDefaultConfig(); 

        // Set the maximum number of HTTP connections to 1000 per application.
        gatewayConnectionConfig.setMaxConnectionPoolSize(1000);

        // Set the request timeout to 10 seconds.
        gatewayConnectionConfig.setIdleConnectionTimeout(Duration.ofMillis(10000));

        return CosmosConfig.builder()
                           .responseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation())
                           .enableQueryMetrics(properties.isQueryMetricsEnabled())
                           .directMode(directConnectionConfig, gatewayConnectionConfig); // directMode() has an override which accepts Gateway config                        
                           .build();
    }
```

### <a name="response-diagnostics-and-query-metrics"></a>응답 진단 및 쿼리 메트릭

버전 2부터 Spring Data Azure Cosmos DB SDK는 응답 진단 문자열과 쿼리 메트릭을 지원합니다.

쿼리 메트릭을 사용하도록 설정하려면 `application.properties` 파일에서 `queryMetricsEnabled` 플래그를 **true** 로 설정합니다. 그런 다음, 이전 섹션에서 설명한 프로세스를 따라 `ResponseDiagnosticsProcessor` 인터페이스를 확장하고 `processResponseDiagnostics` 메서드를 구현하여 진단 정보를 기록합니다. 마지막으로, 구현 인스턴스를 `CosmosDbConfig.setResponseDiagnosticsProcessor` 메서드로 전달합니다.

### <a name="pagination-and-sorting"></a>페이지 매김 및 정렬

Spring Data Azure Cosmos DB SDK는 Spring Data 페이지 매김 및 정렬을 지원합니다. 자세한 내용은 Spring 설명서의 [특수 매개 변수 처리](https://docs.spring.io/spring-data/commons/docs/current/reference/html/#repositories.special-parameters)를 참조하세요.

데이터베이스 계정에서 사용 가능한 RU(요청 단위)에 따라 Cosmos DB는 요청된 크기보다 작거나 같은 문서를 반환할 수 있습니다. 자세한 내용은 [Azure Cosmos DB의 요청 단위](/azure/cosmos-db/request-units)를 참조하세요.

각 반복에서 반환되는 문서 수는 변수이므로 `totalPageSize` 값을 사용하면 안 됩니다. 대신, 다음 예제와 같이 `Pageable` 개체를 반복해야 합니다.

```java
final Sort sort = Sort.by(Sort.Direction.DESC, "name");
final CosmosPageRequest pageRequest = new CosmosPageRequest(0, pageSize,   null, sort);
Page<T> page = tRepository.findAll(pageRequest);
List<T> pageContent = page.getContent();
while(page.hasNext()) {
    Pageable nextPageable = page.nextPageable();
    page = repository.findAll(nextPageable);
    pageContent = page.getContent();
}
```

## <a name="common-issues-and-workarounds"></a>일반적인 이슈 및 해결 방법

다음 섹션에서는 Spring Data Azure Cosmos DB SDK를 사용할 때 알아야 하는 이슈에 대해 설명합니다.

### <a name="getting-the-correct-cosmos-db-configuration"></a>올바른 Cosmos DB 구성 가져오기

클래스에 다양한 주석과 구성이 있으므로 `AbstractCosmosConfiguration` 인터페이스를 확장하기 어려울 수 있습니다. 가장 일반적인 이슈는 `Enable Repositories` 주석입니다.

리포지토리에서 `CosmosRepository`를 확장할 때 꼭 `@EnableCosmosRepositories` 주석을 추가해야 합니다. 리포지토리에서 `ReactiveCosmosRepository`를 확장할 때 꼭 `@EnableReactiveCosmosRepositories` 주석을 추가해야 합니다. 다음 예제에서는 이러한 주석의 사용 방법을 보여줍니다.

```java
@Configuration
@EnableConfigurationProperties(CosmosProperties.class)
@EnableCosmosRepositories
@EnableReactiveCosmosRepositories
@PropertySource("classpath:application.properties")
public class TestRepositoryConfig extends AbstractCosmosConfiguration {
    ...
}
```

`CosmosDBConfig` bean을 만들거나 사용자 지정하는 동안 키를 직접 사용하지 말고 `AzureKeyCredential` 개체를 사용해야 합니다.

`AzureKeyCredential` 기능을 사용하여 키를 필요할 때 회전할 수 있습니다. `switchToSecondaryKey` 메서드를 사용하여 키를 전환할 수 있습니다.

Cosmos DB SDK는 내부적으로 동일한 개체를 사용하여 이 개체 내부의 키 값 변화를 감지하므로 `AzureKeyCredential`은 singleton 개체여야 합니다.

### <a name="custom-query-execution"></a>사용자 지정 쿼리 실행

Spring Data Azure Cosmos DB SDK 3.x.x는 사용자 지정 쿼리를 정의하기 위한 `@query` 주석을 지원합니다!

다음 코드에서는 `@query` 주석을 사용하여 오프셋을 정의하고 쿼리를 제한하는 방법의 간단한 예를 보여 줍니다.

```java
@Repository
public interface SampleRepository extends CosmosRepository<SampleEntity, String> {

    ...

    @Query(value = "SELECT * from c OFFSET @skipCount LIMIT @takeCount")
    List<SampleEntity> findByName(@Param("skipCount") int skipCount, @Param("takeCount") int takeCount);
}
```

### <a name="enable-diagnostics-and-query-metrics"></a>진단 및 쿼리 메트릭 사용

디버깅할 때 Cosmos DB SDK의 응답 진단 문자열과 쿼리 메트릭이 있으면 도움이 됩니다. Cosmos DB SDK는 클라이언트 쪽에서 응답 진단 문자열을 기록합니다. 백 엔드는 쿼리 메트릭을 기록하여 Cosmos DB SDK에 제공합니다.

`ResponseDiagnosticsProcessor.processResponseDiagnostics` 메서드는 Spring Data Azure Cosmos DB SDK에서 모든 API 호출 후에 호출됩니다. 따라서 버그를 해결하고 복잡성을 방지하여 구현의 성능을 높여야 합니다. 예를 들어 이 방법의 진단 정보 세트 전체를 기록하면 방대한 양의 관련 정보 때문에 성능 비용이 엄청나게 많이 발생하므로 진단 정보 세트 전체를 기록하면 안 됩니다. 또한 애플리케이션 성능에 영향을 주지 않도록 `Debug` 로깅 수준을 사용해야 합니다.

다음 코드 조각은 `ResponseDiagnosticsProcessor` 인터페이스 구현 방법의 예를 보여줍니다.

```java
private static class ResponseDiagnosticsProcessorImplementation implements ResponseDiagnosticsProcessor {

    @Override
    public void processResponseDiagnostics(@Nullable ResponseDiagnostics responseDiagnostics) {

        // To log everything:
        if (log.isDebugEnabled()) {
            log.debug("Response diagnostics {}", responseDiagnostics);
        }

        // To log Cosmos DB response diagnostics:
        if (responseDiagnostics != null && log.isDebugEnabled()) {
            CosmosResponseDiagnostics cosmosResponseDiagnostics = responseDiagnostics.getCosmosResponseDiagnostics();
            log.debug("Cosmos DB response diagnostics {}", cosmosResponseDiagnostics);
        }

        // To log just the request latency:
        if (responseDiagnostics != null && log.isDebugEnabled()) {
            CosmosResponseDiagnostics cosmosResponseDiagnostics = responseDiagnostics.getCosmosResponseDiagnostics();
            log.debug("Request latency {}", cosmosResponseDiagnostics.requestLatency());
        }

        // To log query metrics:
        if (responseDiagnostics != null && log.isDebugEnabled()) {
            FeedResponseDiagnostics feedResponseDiagnostics =
                responseDiagnostics.getFeedResponseDiagnostics();
            log.debug("Query metrics {}", feedResponseDiagnostics);
        }
    }
}
```

## <a name="how-to-troubleshoot"></a>문제 해결 방법

다음 섹션에서는 일반적인 이슈를 해결하는 방법을 설명합니다.

### <a name="connection-issues"></a>연결 문제

연결 이슈가 발생하는 경우 [올바른 Cosmos DB 구성 가져오기](#getting-the-correct-cosmos-db-configuration) 섹션에 설명된 대로 구성 클래스의 필수 주석 중에 누락된 것은 없는지, 모두 올바른지 확인합니다.

### <a name="naming-changes"></a>이름 변경

Spring Data Azure Cosmos DB SDK 버전 3.1.0+에는 클래스, 메서드, 주석 및 Maven 아티팩트의 이름/인터페이스가 다음과 같이 변경되었습니다.
* 그룹 ID가 `com.azure`로 업데이트되었습니다.
* 아티팩트 ID가 `azure-spring-data-cosmos`로 업데이트되었습니다.
* 동기화 API 반환 형식이 `List` 대신 `Iterable` 형식으로 업데이트되었습니다.
* `CosmosDbFactory`가 `CosmosFactory`로 변경되었습니다.
* `CosmosDBConfig`가 `CosmosConfig`로 변경되었습니다.
* `CosmosDBAccessException`이 `CosmosAccessException`으로 변경되었습니다.
* `Document` 주석이 `Container` 주석으로 변경되었습니다.
* `DocumentIndexingPolicy` 주석이 `CosmosIndexingPolicy` 주석으로 변경되었습니다.
* `DocumentQuery`가 `CosmosQuery`로 변경되었습니다.
* **application.properties** 플래그 `populateQueryMetrics`가 `queryMetricsEnabled`로 변경되었습니다.

### <a name="key-bug-fixes"></a>주요 버그 수정

Spring Data Azure Cosmos DB SDK의 버전 3.1.0+에는 다음과 같은 주요 버그 수정이 있습니다.
* 주석이 달린 쿼리가 주석이 달린 컨테이너 이름을 선택하지 않는 문제를 해결했습니다.
* Netty I/O 스레드가 차단되지 않도록 진단 로깅 작업을 병렬 스레드로 예약합니다.
* 삭제 작업에 대한 낙관적 잠금이 수정되었습니다.
* IN 절에 대한 쿼리 이스케이프 문제가 해결되었습니다.
* @Id에 대해 긴 데이터 유형을 허용하여 문제를 해결했습니다.
* @PartitionKey 주석에 대해 데이터 유형으로 boolean, long, int, double을 허용하여 문제를 해결했습니다.
* 무시 사례 쿼리를 위해 IgnoreCase & AllIgnoreCase 키워드를 수정했습니다.
* 컨테이너를 자동으로 만들 때 기본 요청 단위 값 4000이 제거되었습니다.
* @GeneratedValue 주석과 함께 사용할 경우 중첩된 파티션 키 버그가 수정되었습니다.

### <a name="api-or-query-slowness"></a>API 또는 쿼리 속도 저하

API 또는 쿼리 실행 시 대기 시간이 긴 경우 [진단 및 쿼리 메트릭 사용](#enable-diagnostics-and-query-metrics) 섹션에 설명된 대로 진단 문자열과 쿼리 메트릭을 기록해 봅니다. 클라이언트 쪽 속도 저하의 근본 원인일 수 있는 CPU 사용량, 네트워크 대역폭 및 I/O 디스크 공간을 확인합니다.