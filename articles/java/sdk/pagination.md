---
title: Java용 Azure SDK의 페이지 매김 및 반복
description: 페이지 매김 및 반복과 관련된 Java용 Azure SDK의 개념 소개
author: anuchandy
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: anuchan
ms.openlocfilehash: 0cb9e519931d24dcd97aa4a52742974427df5969
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528625"
---
# <a name="pagination-and-iteration-in-the-azure-sdk-for-java"></a>Java용 Azure SDK의 페이지 매김 및 반복

이 문서에서는 Java용 Azure SDK 페이지 매김 및 반복 기능을 사용하여 대량의 데이터 세트를 효율적이고 생산적으로 작업하는 방법을 소개합니다.

Azure Java SDK 내의 클라이언트 라이브러리에서 제공하는 많은 작업은 둘 이상의 결과를 반환합니다. Azure Java SDK는 일관성을 통해 개발자 환경을 최대화하기 위해 이러한 상황에서 허용되는 반환 형식 세트를 정의합니다. 동기 API에는 `PagedIterable`, 비동기 API에는 `PagedFlux` 반환 형식이 사용됩니다. API는 사용 사례의 계정마다 조금씩 다르지만, 개념적으로 다음과 같은 동일한 요구 사항이 있습니다.

- 컬렉션의 각 요소를 개별적으로 쉽게 반복할 수 있도록 만들고, 연속 토큰의 수동 페이지 매김 또는 추적의 필요성을 무시합니다. `PagedIterable` 및 `PagedFlux`는 지정된 형식 `T`로 역직렬화된 페이지가 매겨진 응답을 반복하여 이 작업을 쉽게 수행할 수 있습니다. `PagedIterable`은 `Iterable` 인터페이스를 구현하고 `Stream`을 수신하는 API를 제공하는 반면, `PagedFlux`는 `Flux`를 제공합니다. 어떤 경우에도 페이지 매김 동작은 투명하며, 결과가 반복되는 한 반복이 계속됩니다.

- 페이지 단위로 명시적으로 반복될 수 있게 만듭니다. 이렇게 하면 요청이 전송되는 시기를 명확하게 이해할 수 있으며, 페이지별 응답 정보에 액세스할 수 있습니다. `PagedIterable` 및 `PagedFlux` 둘 다 개별 요소가 아닌 페이지 단위로 반복하도록 적절한 형식을 반환하는 메서드를 제공합니다.

이 문서는 크게 Java Azure SDK 동기 API와 비동기 API로 분할됩니다. 동기 클라이언트를 사용할 때는 동기 반복 API, 비동기 클라이언트를 사용할 때는 비동기 반복 API를 보게 됩니다.

## <a name="synchronous-pagination-and-iteration"></a>동기 페이지 매김 및 반복

이 섹션에서는 동기 API에 대해 설명합니다.

### <a name="iterate-over-individual-elements"></a>개별 요소 반복

앞서 언급했듯이, 가장 일반적인 사용 사례는 각 요소를 페이지 단위가 아니라 개별적으로 반복하는 것입니다. 다음 코드 예제에서는 `PagedIterable` API를 통해 선호하는 반복 스타일을 사용하여 이 기능을 구현하는 방법을 보여 줍니다.

#### <a name="use-a-for-each-loop"></a>for-each 루프 사용

`PagedIterable`은 `Iterable`을 구현하므로 다음 예와 같이 요소를 반복할 수 있습니다.

```java
PagedIterable<Secret> secrets = client.listSecrets();
for (Secret secret : secrets) {
   System.out.println("Secret is: " + secret);
}
```

#### <a name="use-stream"></a>Stream 사용

`PagedIterable`에는 `stream()` 메서드가 정의되어 있기 때문에 다음 예와 같이 이 메서드를 호출하여 표준 Java Stream API를 사용할 수 있습니다.

```java
client.listSecrets()
      .stream()
      .forEach(secret -> System.out.println("Secret is: " + secret));
```

#### <a name="use-iterator"></a>Iterator 사용

`PagedIterable`은 `Iterable`을 구현하므로 다음 예와 같이 Java 반복기 프로그래밍 스타일을 허용하는 `iterator()` 메서드도 포함하고 있습니다.

```java
Iterator<Secret> secrets = client.listSecrets().iterator();
while (it.hasNext()) {
   System.out.println("Secret is: " + it.next());
}
```

### <a name="iterate-over-pages"></a>페이지 반복

개별 페이지를 작업할 때 페이지 단위로 반복할 수 있습니다. 예를 들어 HTTP 응답 정보가 필요하거나 반복 기록을 유지하려면 연속 토큰이 중요한 경우에는 페이지 단위로 반복할 수 있습니다. 페이지를 반복하든 또는 각 항목을 반복하든, 성능 또는 서비스 호출 수에는 차이가 없습니다. 기본 구현은 요청 시 다음 페이지를 로드하며, 언제든지 `PagedFlux`의 구독을 취소하면 더 이상 서비스가 호출되지 않습니다.

#### <a name="use-a-for-each-loop"></a>for-each 루프 사용

`listSecrets()`를 호출하면 `iterableByPage()` API가 들어 있는 `PagedIterable`을 얻게 됩니다. 이 API는 `Iterable<Secret>` 대신 `Iterable<PagedResponse<Secret>>`를 생성합니다. `PagedResponse`는 다음 예와 같이 응답 메타데이터와 연속 토큰에 대한 액세스를 제공합니다.

```java
Iterable<PagedResponse<Secret>> secretPages = client.listSecrets().iterableByPage();
for (PagedResponse<Secret> page : secretPages) {
   System.out.println("Response code: " + page.getStatusCode());
   System.out.println("Continuation Token: " + page.getContinuationToken());
   page.getElements().forEach(secret -> System.out.println("Secret value: " + secret))
}
```

연속 토큰을 허용하는 `iterableByPage` 오버로드도 있습니다. 나중에 같은 반복 지점으로 돌아가고 싶을 때 이 오버로드를 호출하면 됩니다.

#### <a name="use-stream"></a>Stream 사용

다음 예에서는 위에서 설명한 작업을 `streamByPage()` 메서드가 어떻게 수행하는지 보여 줍니다. 또한 이 API에는 나중에 동일한 반복 지점으로 돌아가는 데 사용되는 연속 토큰 오버로드가 포함되어 있습니다.

```java
client.listSecrets()
      .streamByPage()
      .forEach(page -> {
          System.out.println("Response code: " + page.getStatusCode());
          System.out.println("Continuation Token: " + page.getContinuationToken());
          page.getElements().forEach(secret -> System.out.println("Secret value: " + secret))
      });
```

## <a name="asynchronously-observe-pages-and-individual-elements"></a>페이지 및 개별 요소를 비동기적으로 관찰

이 섹션에서는 비동기 API에 대해 설명합니다. 비동기 API에서는 `subscribe()`를 호출하는 주 스레드와 다른 스레드에서 네트워크 호출이 발생합니다. 즉, 결과를 얻기 전에 주 스레드가 종료될 수 있습니다. 개발자는 비동기 작업이 완료되기 전에는 애플리케이션이 종료되지 않도록 만들어야 합니다.

### <a name="observe-individual-elements"></a>개별 요소 관찰

다음 예에서는 `PagedFlux` API를 통해 비동기적으로 개별 요소를 관찰하는 방법을 보여 줍니다. 다양한 방법으로 Flux 유형을 구독할 수 있습니다. 자세한 내용은 [Reactor 3 참조 가이드](https://projectreactor.io/docs/core/release/reference)의 [Flux 또는 Mono를 만들고 구독하는 간단한 방법](https://projectreactor.io/docs/core/release/reference/#_simple_ways_to_create_a_flux_or_mono_and_subscribe_to_it)을 참조하세요. 이 예는 세 개의 람다 식(소비자, 오류 소비자 및 전체 소비자에 대한 람다 식이 각각 하나씩)이 있는 변형 중 하나입니다. 세 가지를 모두 사용하는 것이 좋지만, 경우에 따라 소비자 또는 오류 소비자만 있으면 되는 상황도 있습니다.

 ```java
asyncClient.listSecrets()
    .subscribe(secret -> System.out.println("Secret value: " + secret),
        ex -> System.out.println("Error listing secrets: " + ex.getMessage()),
        () -> System.out.println("Successfully listed all secrets"));
 ```

### <a name="observe-pages"></a>페이지 관찰

 다음 예에서도 마찬가지로 `byPage()` API를 사용하고 소비자, 오류 소비자 및 완료 소비자를 제공하여 `PagedFlux` API를 통해 각 페이지를 비동기적으로 관찰하는 방법을 보여 줍니다.

  ```java
asyncClient.listSecrets().byPage()
    .subscribe(page -> {
            System.out.println("Response code: " + page.getStatusCode());
            System.out.println("Continuation Token: " + page.getContinuationToken());
            page.getElements().forEach(secret -> System.out.println("Secret value: " + secret))
        },
        ex -> System.out.println("Error listing pages with secret: " + ex.getMessage()),
        () -> System.out.println("Successfully listed all pages with secret"));
 ```

## <a name="next-steps"></a>다음 단계

Java용 Azure SDK의 페이지 매김 및 반복에 대해 알아보았으므로, 이제 [Java용 Azure SDK의 장기 실행 작업](lro.md)을 검토하세요. 장기 실행 작업은 일반적으로 서버 쪽에서 작업을 수행하는 데 시간이 다소 소요되어 대부분의 일반 HTTP 요청보다 오래 실행되는 작업입니다.
