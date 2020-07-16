---
title: Spring Data Azure Cosmos DB 개발자 가이드
description: 이 가이드에서는 Spring Data Azure Cosmos DB SDK를 사용하는 분들이 알고 있어야 하는 사항에 대해 설명합니다.
author: kushagraThapar
ms.author: kuthapar
ms.topic: conceptual
ms.date: 1/9/2019
ms.custom: devx-track-java
ms.openlocfilehash: 119a2b3ff761778e111500a9fe0cf33d2dc6b080
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86378447"
---
# <a name="spring-data-azure-cosmos-db-developers-guide"></a>Spring Data Azure Cosmos DB 개발자 가이드

이 토픽에서는 SQL API를 사용하는 [Spring Data Cosmos DB](https://github.com/microsoft/spring-data-cosmosdb)의 기능에 대해 설명합니다. 이 토픽에는 일반적인 이슈, 해결 방법 및 진단 단계에 대한 지침도 포함되어 있습니다.

[Azure Cosmos DB](/azure/cosmos-db/introduction)는 개발자가 다양한 표준 API를 사용하여 데이터를 작업할 수 있게 해주는 전역적으로 분산된 데이터베이스 서비스입니다. Spring Data Cosmos DB SDK는 [Spring Data](https://spring.io/projects/spring-data) 프레임워크를 기반으로 하며 SQL API를 사용하여 Azure Cosmos DB와의 통합을 제공합니다. 다른 API에 대한 지원 정보는 다음 토픽에서 찾을 수 있습니다.

- [Azure Cosmos DB에서 Spring Data MongoDB API를 사용하는 방법](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
- [Azure Cosmos DB에서 Spring Data Apache Cassandra API를 사용하는 방법](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db) 문서에서 구현된 데이터베이스)
- [Azure Cosmos DB SQL API에서 Spring Data Gremlin Starter를 사용하는 방법](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)

Spring Data Cosmos DB SDK는 GitHub의 [spring-data-cosmosdb](https://github.com/microsoft/spring-data-cosmosdb) 리포지토리에서 오픈 소스로 제공됩니다. 이 리포지토리에는 버그를 파일로 작성하거나 이미 파일로 작성된 이슈의 해결 방법을 확인할 수 있는 활성 [이슈](https://github.com/microsoft/spring-data-cosmosdb/issues) 목록이 있습니다. [릴리스](https://github.com/microsoft/spring-data-cosmosdb/releases) 목록을 확인하여 최신 버전에서 이슈가 해결되었는지 확인할 수도 있습니다. Spring Data Cosmos DB SDK 버전 2.2.x 릴리스 학습은 spring-data-commons 버전 2.2.0.RELEASE를 지원하는 반면, SDK의 버전 2.1.x 릴리스 학습은 spring-data-common 버전 2.1.0.RELEASE를 지원합니다.

## <a name="available-features"></a>사용 가능한 기능

다음 섹션에서는 현재 사용할 수 있는 기능에 대해 설명합니다.

### <a name="crudrepository-and-reactivecrudrepository-support"></a>CrudRepository 및 ReactiveCrudRepository 지원

Spring Data Cosmos DB SDK는 Spring Data `CrudRepository` 및 `ReactiveCrudRepository` 인터페이스를 확장하는 `CosmosRepository` 및 `ReactiveCosmosRepository` 인터페이스를 제공합니다.

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

사용량에 따라 `Configuration` 클래스에서 두 리포지토리를 별도로 사용하도록 설정해야 합니다. 다음은 그 예입니다.

```java
@Configuration
@PropertySource(value = {"classpath:application.properties"})
@EnableCosmosRepositories
@EnableReactiveCosmosRepositories
public class TestRepositoryConfig extends AbstractCosmosConfiguration {
    ...
}
```

### <a name="define-a-simple-entity"></a>단순 엔터티 정의

`@Document` 주석을 추가하고 컬렉션 이름, RU(요청 단위), TTL(time-to-live) 및 컬렉션 플래그 자동 생성 같은 컬렉션 관련 속성을 지정하여 엔터티를 정의할 수 있습니다.

기본적으로 컬렉션 이름은 사용자 도메인 클래스의 클래스 이름이 됩니다. 컬렉션 이름을 사용자 지정하려면 도메인 클래스에 `@Document(collection="myCustomCollectionName")` 주석을 추가합니다. 컬렉션 필드는 [SpEL(Spring Expression Language)](https://docs.spring.io/spring/docs/3.0.x/reference/expressions.html) 식도 지원하므로 구성 속성을 통해 프로그래밍 방식으로 컬렉션 이름을 제공할 수 있습니다. 예를 들어 `collection = "${dynamic.collection.name}"` 및 `collection = "#{@someBean.getCollectionName()}"` 같은 식을 사용할 수 있습니다.

다음과 같은 두 가지 방법으로 도메인 클래스의 필드를 Azure Cosmos DB 문서의 `id` 필드에 매핑할 수 있습니다.

- `@Id`를 사용하여 필드에 주석을 추가합니다.
- 필드 이름을 `id`로 설정합니다.

다음 예제에서는 `@Document` 및 `@Id` 주석을 사용하는 방법을 보여줍니다.

```java
@Document(collection = "myCollection")
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

SDK가 분할도 지원합니다. 자세한 내용은 [Azure Cosmos DB에서 분할 및 수평 확장](/azure/cosmos-db/partition-data)을 참조하세요. 도메인 클래스의 필드를 파티션 키 필드로 지정하려면 `@PartitionKey`를 사용하여 주석을 추가합니다. 그런 다음, CRUD 작업을 수행할 때 파티션 값을 지정합니다.

다음 예제에서는 CRUD 작업을 수행할 때 `@PartitionKey` 주석을 사용하는 방법을 보여줍니다.

```java
@Document(ru = "400")
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

    final Address newAddress = new Address("12345", "city");

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

### <a name="configuring-the-application"></a>애플리케이션 구성

다음 단계에 따라 애플리케이션을 구성합니다.

1. `AbstractCosmosConfiguration` 클래스를 확장하여 애플리케이션의 구성(Cosmos DB 키, URL, 데이터베이스 이름 등)을 설정합니다.
1. `@Configuration` 주석을 추가합니다.
1. 리포지토리 사용에 따라 `@EnableCosmosRepositories` 및 `@EnableReactiveCosmosRepositories` 주석 중 하나 또는 둘 다 추가합니다.

`CosmosKeyCredential` 기능을 사용하여 키를 필요할 때 회전할 수 있습니다. `switchToSecondaryKey` 메서드를 사용하여 키를 전환할 수 있습니다.

다음 예제 코드에서는 애플리케이션 구성을 보여주고 `switchToSecondaryKey`의 사용 방법을 보여줍니다.

```java
@Configuration
@EnableCosmosRepositories
public class AppConfiguration extends AbstractCosmosConfiguration {

    @Value("${azure.cosmosdb.uri}")
    private String uri;

    @Value("${azure.cosmosdb.key}")
    private String key;

    @Value("${azure.cosmosdb.secondaryKey}")
    private String secondaryKey;

    @Value("${azure.cosmosdb.database}")
    private String dbName;

    @Value("${azure.cosmosdb.populateQueryMetrics}")
    private boolean populateQueryMetrics;

    private CosmosKeyCredential cosmosKeyCredential;

    @Bean
    public CosmosDBConfig getConfig() {
        this.cosmosKeyCredential = new CosmosKeyCredential(key);
        CosmosDbConfig cosmosdbConfig = CosmosDBConfig.builder(uri,
            this.cosmosKeyCredential, dbName).build();
        cosmosdbConfig.setPopulateQueryMetrics(populateQueryMetrics);
        cosmosdbConfig.setResponseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation());
        return cosmosdbConfig;
    }

    public void switchToSecondaryKey() {
        this.cosmosKeyCredential.key(secondaryKey);
    }
}
```

또한 다음 예제와 같이 연결 모드, 최대 연결 풀 크기, 요청 시간 제한 등을 변경하도록 구성을 사용자 지정할 수 있습니다.

```java
public CosmosDBConfig getConfig() {

    this.cosmosKeyCredential = new CosmosKeyCredential(key);
    ConnectionPolicy customizedConnectionPolicy = new ConnectionPolicy();

    // Set the connection mode to Direct (TCP).
    customizedConnectionPolicy.setConnectionMode(ConnectionMode.DIRECT);

    // Set the maximum number of HTTP/TCP connections to 1000 per application.
    customizedConnectionPolicy.setMaxPoolSize(1000);

    // Set the request timeout to 10 seconds.
    customizedConnectionPolicy.requestTimeoutInMillis(10000);

    // Set the idle connection timeout to two minutes.
    customizedConnectionPolicy.idleConnectionTimeoutInMillis(120000);
    CosmosDBConfig cosmosDbConfig = CosmosDBConfig.builder(uri,   this.cosmosKeyCredential, dbName)
                                                  .connectionPolicy  (customizedConnectionPolic  y)
                                                  .build();
    return cosmosDbConfig;
}
```

### <a name="response-diagnostics-and-query-metrics"></a>응답 진단 및 쿼리 메트릭

Spring Data Cosmos DB SDK의 버전 2.2.x는 응답 진단 문자열과 쿼리 메트릭을 지원합니다.

쿼리 메트릭을 사용하도록 설정하려면 `application.properties` 파일에서 `populateQueryMetrics` 플래그를 **true**로 설정합니다. 그런 다음, `ResponseDiagnosticsProcessor` 인터페이스를 확장하고, 진단 정보를 기록하도록 `processResponseDiagnostics` 메서드를 구현합니다. 마지막으로, 구현 인스턴스를 `CosmosDbConfig.setResponseDiagnosticsProcessor` 메서드로 전달합니다. 다음 코드에서는 구현 예제를 보여줍니다.

```java
@Configuration
@EnableCosmosRepositories
public class AppConfiguration extends AbstractCosmosConfiguration {

    ...

    @Value("${azure.cosmosdb.populateQueryMetrics}")
    private boolean populateQueryMetrics;

    private static class ResponseDiagnosticsProcessorImplementation implements ResponseDiagnosticsProcessor {

        @Override
        public void processResponseDiagnostics(@Nullable ResponseDiagnostics responseDiagnostics) {
            log.info("Response Diagnostics {}", responseDiagnostics);
        }
    }

    @Bean
    public CosmosDBConfig getConfig() {
        this.cosmosKeyCredential = new CosmosKeyCredential(key);
        CosmosDbConfig cosmosdbConfig = CosmosDBConfig.builder(uri, this.cosmosKeyCredential, dbName).build();
        cosmosdbConfig.setPopulateQueryMetrics(populateQueryMetrics);
        cosmosdbConfig.setResponseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation());
        return cosmosdbConfig;
    }
}
```

### <a name="pagination-and-sorting"></a>페이지 매김 및 정렬

Spring Data Cosmos DB SDK는 Spring Data 페이지 매김 및 정렬을 지원합니다. 자세한 내용은 Spring 설명서의 [특수 매개 변수 처리](https://docs.spring.io/spring-data/commons/docs/current/reference/html/#repositories.special-parameters)를 참조하세요.

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

다음 섹션에서는 Spring Data Cosmos DB SDK를 사용할 때 알아야 하는 이슈에 대해 설명합니다.

### <a name="getting-the-correct-cosmos-db-configuration"></a>올바른 Cosmos DB 구성 가져오기

클래스에 다양한 주석과 구성이 있으므로 `AbstractCosmosConfiguration` 인터페이스를 확장하기 어려울 수 있습니다. 가장 일반적인 이슈는 `Enable Repositories` 주석입니다.

리포지토리에서 `CosmosRepository`를 확장할 때 꼭 `@EnableCosmosRepositories` 주석을 추가해야 합니다. 리포지토리에서 `ReactiveCosmosRepository`를 확장할 때 꼭 `@EnableReactiveCosmosRepositories` 주석을 추가해야 합니다. 다음 예제에서는 이러한 주석의 사용 방법을 보여줍니다.

```java
@Configuration
@PropertySource(value = {"classpath:application.properties"})
@EnableCosmosRepositories
@EnableReactiveCosmosRepositories
public class TestRepositoryConfig extends AbstractCosmosConfiguration {
    ...
}
```

`CosmosDBConfig` bean을 만들거나 사용자 지정하는 동안 키를 직접 사용하지 말고 `CosmosKeyCredential` 개체를 사용해야 합니다.

`CosmosKeyCredential` 기능을 사용하여 키를 필요할 때 회전할 수 있습니다. `switchToSecondaryKey` 메서드를 사용하여 키를 전환할 수 있습니다.

Cosmos DB SDK는 내부적으로 동일한 개체를 사용하여 이 개체 내부의 키 값 변화를 감지하므로 `CosmosKeyCredential`은 singleton 개체여야 합니다.

### <a name="custom-query-execution"></a>사용자 지정 쿼리 실행

쿼리 주석 기능은 아직 Spring Data Cosmos DB SDK에서 지원되지 않습니다. 지원될 때까지는 Spring 애플리케이션 컨텍스트에서 노출하는 `cosmosClient` bean에서 직접 사용자 지정 쿼리 및 복합 쿼리를 실행할 수 있습니다.

다음 코드에서는 `cosmosClient` bean을 사용하여 오프셋을 실행하고 쿼리를 제한하는 방법의 간단한 예를 보여줍니다.

```java
final FeedOptions feedOptions = new FeedOptions();

// Enable cross-partition queries.
feedOptions.enableCrossPartitionQuery(true);

// Set the page size.
feedOptions.maxItemCount(20);

// Set the number of parallel operations on the client-side SDK when executing parallel queries.
feedOptions.maxDegreeOfParallelism(2);

// Populate query metrics from Cosmos DB.
feedOptions.populateQueryMetrics(true);

final String query = "SELECT * from c OFFSET " + skipCount + " LIMIT " + takeCount;

final CosmosClient cosmosClient = applicationContext.getBean(CosmosClient.class);

Flux<FeedResponse<CosmosItemProperties>> feedResponseFlux =
    cosmosClient.getDatabase(databaseId)
                .getContainer(collectionId)
                .queryItems(query, feedOptions);
    feedResponseFlux.subscribeOn(Schedulers.parallel())
                    .flatMap(feedResponse -> {
                        List<CosmosItemProperties> results =
                        feedResponseFlux.results();
                        log.info("Results are {}", results);
                        return feedResponse;
                    })
                    .subscribe();
```

### <a name="enable-diagnostics-and-query-metrics"></a>진단 및 쿼리 메트릭 사용

디버깅할 때 Cosmos DB SDK의 응답 진단 문자열과 쿼리 메트릭이 있으면 도움이 됩니다. Cosmos DB SDK는 클라이언트 쪽에서 응답 진단 문자열을 기록합니다. 백 엔드는 쿼리 메트릭을 기록하여 Cosmos DB SDK에 제공합니다.

`ResponseDiagnosticsProcessor.processResponseDiagnostics` 메서드는 Spring Data Cosmos DB SDK의 모든 API 호출 후에 호출됩니다. 따라서 버그를 해결하고 복잡성을 방지하여 구현의 성능을 높여야 합니다. 예를 들어 이 방법의 진단 정보 세트 전체를 기록하면 방대한 양의 관련 정보 때문에 성능 비용이 엄청나게 많이 발생하므로 진단 정보 세트 전체를 기록하면 안 됩니다. 또한 애플리케이션 성능에 영향을 주지 않도록 `Debug` 로깅 수준을 사용해야 합니다.

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

### <a name="api-exceptions"></a>API 예외

Spring Data Cosmos DB SDK 버전 2.2.1은 다음과 같은 향상된 예외 처리 기능을 제공합니다.

- 모든 API는 getter를 통해 `cosmosClientException` 필드를 노출하는 `CosmosDBAccessException`을 throw합니다.
- Cosmos DB SDK는 클라이언트 쪽에서 다시 시도 논리를 구현하는 데 사용할 수 있는 `CosmosClientException`을 throw합니다.
- 일반적으로 다시 시도하는 예외는 `Resource already exists`, `Request rate too large`, `Request timeout exception` 등의 메시지가 표시됩니다.

### <a name="api-or-query-slowness"></a>API 또는 쿼리 속도 저하

API 또는 쿼리 실행 시 대기 시간이 긴 경우 [진단 및 쿼리 메트릭 사용](#enable-diagnostics-and-query-metrics) 섹션에 설명된 대로 진단 문자열과 쿼리 메트릭을 기록해 봅니다. 클라이언트 쪽 속도 저하의 근본 원인일 수 있는 CPU 사용량, 네트워크 대역폭 및 I/O 디스크 공간을 확인합니다.
