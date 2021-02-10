---
title: Java용 Azure SDK의 비동기 프로그래밍
description: Java용 Azure SDK의 비동기 프로그래밍 모델 개요
author: srnagar
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: srnagar
ms.openlocfilehash: 64a692b10175a7c08ff8a32e56a638209f239c19
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528714"
---
# <a name="asynchronous-programming-in-the-azure-sdk-for-java"></a>Java용 Azure SDK의 비동기 프로그래밍

이 문서에서는 Java용 Azure SDK의 비동기 프로그래밍 모델 개요를 설명합니다.

Azure SDK는 처음에 Azure 서비스와 상호 작용하기 위한 비차단 비동기 API만 포함되어 있었습니다. 이러한 API를 통해 Azure SDK를 사용하여 시스템 리소스를 효율적으로 사용하는 확장 가능한 애플리케이션을 빌드할 수 있습니다. 그러나 [Java용 Azure SDK](https://github.com/Azure/azure-sdk-for-java#client-new-releases)는 더 많은 대상을 충족하는 동기 클라이언트도 포함되어 있으며 비동기 프로그래밍에 익숙하지 않은 사용자도 클라이언트 라이브러리에 접근할 수 있습니다. (Azure SDK 디자인 지침에서 [접근 가능](https://azure.github.io/azure-sdk/general_introduction.html#approachable)을 참조하세요.) 따라서 Java용 Azure SDK의 모든 Java 클라이언트 라이브러리는 비동기 및 동기 클라이언트를 모두 제공합니다. 그러나 프로덕션 시스템에서는 비동기 클라이언트를 사용하여 시스템 리소스 사용을 최대화하는 것이 좋습니다.

## <a name="reactive-streams"></a>반응성 스트림

[Java Azure SDK 디자인 지침](https://azure.github.io/azure-sdk/java_introduction.html)의 [비동기 서비스 클라이언트](https://azure.github.io/azure-sdk/java_introduction.html#async-service-clients) 섹션을 살펴보면, 비동기 API는 Java 8에서 제공된 `CompletableFuture`를 사용하는 대신 반응성 유형을 사용하는 것을 알 수 있습니다. JDK에서 기본적으로 사용할 수 있는 형식이 아닌 반응성 형식을 선택한 이유는 무엇일까요?

Java 8에는 [스트림](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html), [람다](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html) 및 [CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html)와 같은 기능이 도입되었습니다. 이러한 기능은 많은 기능을 제공하지만 몇 가지 제한 사항이 있습니다.

`CompletableFuture`는 콜백 기반의 비 차단 기능과 일련의 비동기 작업을 쉽게 구성할 수 있는 `CompletionStage` 인터페이스를 제공합니다. 람다는 이러한 푸시 기반 API를 더 읽기 쉽게 만듭니다. 스트림은 데이터 요소 컬렉션을 처리하기 위한 기능 스타일 작업을 제공합니다. 그러나 스트림은 동기적이므로 다시 사용할 수 없습니다. `CompletableFuture`를 통해 단일 요청을 만들고, 콜백을 지원하며, _단일_ 응답을 예상할 수 있습니다. 그러나 많은 클라우드 서비스는 데이터를 스트리밍하는 기능이 필요합니다(예: Event Hubs).

반응형 스트림은 소스에서 구독자로 요소를 스트리밍하여 이러한 한계를 극복하는 데 도움이 될 수 있습니다. 구독자가 소스에서 데이터를 요청하면 소스는 원하는 수의 결과를 다시 보냅니다. 모든 결과를 한 번에 보낼 필요는 없습니다. 소스에 보낼 데이터가 있을 때마다 일정 시간 동안 전송됩니다.

이 모델에서 구독자는 데이터가 도착할 때 데이터를 처리하는 이벤트 처리기를 등록합니다. 이러한 푸시 기반 상호 작용은 고유 신호를 통해 구독자에게 알립니다.

- `onSubscribe()` 호출은 데이터 전송이 곧 시작됨을 나타냅니다.
- `onError()` 호출은 오류가 발생했음을 나타내며, 이는 데이터 전송이 끝났음을 의미합니다.
- `onComplete()` 호출은 데이터 전송이 성공적으로 완료되었음을 나타냅니다.

Java 스트림과 달리, 반응형 스트림은 오류를 첫 번째 클래스 이벤트로 처리합니다. 반응형 스트림에는 소스가 구독자에게 오류를 전달하기 위한 전용 채널이 있습니다. 또한 반응형 스트림을 사용하면 구독자가 데이터 전송 속도를 협상하여 이러한 스트림을 푸시-풀 모델로 변환할 수 있습니다.

[반응형 스트림](https://github.com/reactive-streams/reactive-streams-jvm#reactive-streams) 사양은 데이터 전송이 발생하는 방식에 대한 표준을 제공합니다. 개략적인 수준에서 사양은 다음 네 가지 인터페이스를 정의하고 이러한 인터페이스를 구현하는 방법에 대한 규칙을 지정합니다.

- **게시자** 는 데이터 스트림의 소스입니다.
- **구독자** 는 데이터 스트림의 소비자입니다.
- **구독** 은 게시자와 구독자 간의 데이터 전송 상태를 관리합니다.
- **프로세서** 는 게시자와 구독자입니다.

[RxJava](https://github.com/ReactiveX/RxJava), [Akka Streams](https://doc.akka.io/docs/akka/current/stream/stream-introduction.html), [Vert.x](https://vertx.io/docs/#reactive) 및 [Project Reactor](https://projectreactor.io/docs/core/release/reference/)와 같이 이 사양의 구현을 제공하는 잘 알려진 Java 라이브러리가 있습니다.

Java용 Azure SDK는 비동기 API를 제공하는 Project Reactor를 채택했습니다. 이러한 결정을 내리는 주된 요인은 Project Reactor도 사용하는 [Spring Webflux](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html)와 원활하게 통합하는 것이었습니다. RxJava보다 Project Reactor를 선택하는 또 다른 기여 요인은 Project Reactor가 Java 8을 사용했지만 RxJava는 당시 여전히 Java 7에 있었다는 것입니다. Project Reactor는 또한 구성 가능하고 다양한 연산자 세트를 제공하며 데이터 처리 파이프라인을 빌드하기 위한 선언적 코드를 작성할 수 있습니다. Project Reactor의 또 다른 좋은 점은 Project Reactor 유형을 다른 인기 구현 유형으로 변환하기 위한 어댑터가 있다는 것입니다.

## <a name="comparing-apis-of-synchronous-and-asynchronous-operations"></a>동기 및 비동기 작업의 API 비교

동기 클라이언트와 비동기 클라이언트 옵션에 대해 논의했습니다. 아래 표는 이러한 옵션을 사용하여 설계된 API의 모양을 요약한 것입니다.

| API 형식                                           | 값 없음                   | 단일 값            | 다중 값                         |
|----------------------------------------------------|----------------------------|-------------------------|-------------------------------|
| 표준 Java - 동기 API                   | `void`                     | `T`                     | `Iterable<T>`                 |
| 표준 Java - 비동기 API                  | `CompletableFuture<Void>`  | `CompletableFuture<T>`  | `CompletableFuture<List<T>>`   |
| 반응형 스트림 인터페이스                        | `Publisher<Void>`          | `Publisher<T>`          | `Publisher<T>`                 |
| 반응형 스트림의 Project Reactor 구현 | `Mono<Void>`               | `Mono<T>`               | `Flux<T>`                      |

완전성을 위해 Java 9가 4개의 반응형 스트림 인터페이스를 포함하는 [Flow](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/concurrent/Flow.html) 클래스를 도입했음을 언급할 필요가 있습니다. 그러나 이 클래스에는 구현이 포함되지 않습니다.

## <a name="use-async-apis-in-the-azure-sdk-for-java"></a>Java용 Azure SDK에서 비동기 API 사용

반응형 스트림 사양은 게시자 유형을 구분하지 않습니다. 반응형 스트림 사양에서 게시자는 0개 이상의 데이터 요소를 생성하기만 하면 됩니다. 대부분의 경우 최대 하나의 데이터 요소를 생성하는 게시자와 0개 이상의 데이터 요소를 생성하는 게시자를 구분하는 것이 유용합니다. 클라우드 기반 API에서 이 구분은 요청이 단일 값 응답을 반환하는지 또는 컬렉션을 반환하는지 여부를 나타냅니다. Project Reactor는 이를 구분하기 위해 [Mono](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Mono.html)와 [Flux](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html)의 두 가지 유형을 제공합니다. `Mono`를 반환하는 API에는 값이 하나 이상인 응답이 포함되고 `Flux`를 반환하는 API에는 값이 0 이상인 응답이 포함됩니다.

예를 들어 [ConfigurationAsyncClient](/java/api/com.azure.data.appconfiguration.configurationasyncclient)를 사용하여 Azure App Configuration 서비스를 사용하여 저장된 구성을 검색한다고 가정해보겠습니다. (자세한 내용은 [Azure App Configuration란?](/azure/azure-app-configuration/overview)을 참조하세요.)

클라이언트에서 `ConfigurationAsyncClient`를 생성하고 `getConfigurationSetting()`을 호출하면 응답에 단일 값이 포함되어 있음을 나타내는 `Mono`가 반환됩니다. 하지만, 이 메서드를 호출하는 것만으로는 아무 것도 수행되지 않습니다. 클라이언트가 Azure App Configuration 서비스에 아직 요청하지 않았습니다. 이 단계에서, 이 API에서 반환된 `Mono<ConfigurationSetting>`은 데이터 처리 파이프라인의 "어셈블리"에 불과합니다. 즉, 데이터를 사용하는 데 필요한 설정이 완료되었다는 의미입니다. 실제로 데이터 전송을 트리거하려면(즉, 서비스에 요청하고 응답을 받으려면) 반환된 `Mono`를 구독해야 합니다. 따라서 이러한 반응형 스트림을 처리할 때 `subscribe()`를 호출할 때까지 아무 일도 발생하지 않습니다.

다음 예제에서는 `Mono`를 구독하고 구성 값을 콘솔에 인쇄하는 방법을 보여줍니다.

```java
ConfigurationAsyncClient asyncClient = new ConfigurationClientBuilder()
    .connectionString("<your connection string>")
    .buildAsyncClient();

asyncClient.getConfigurationSetting("<your config key>", "<your config value>").subscribe(
    config -> System.out.println("Config value: " + config.getValue()),
    ex -> System.out.println("Error getting configuration: " + ex.getMessage()),
    () -> System.out.println("Successfully retrieved configuration setting"));

System.out.println("Done");
```

클라이언트에서 `getConfigurationSetting()`을 호출한 후 예제 코드는 결과를 구독하고 세 개의 별도의 람다를 제공합니다. 첫 번째 람다는 서비스에서 받은 데이터를 사용하며, 응답에 성공하면 트리거됩니다. 구성을 검색하는 동안 오류가 발생하면 두 번째 람다가 트리거됩니다. 세 번째 람다는 데이터 스트림이 완료되면 호출됩니다. 즉, 이 스트림에서 더 이상의 데이터 요소가 필요하지 않습니다.

> [!NOTE]
> 모든 비동기 프로그래밍과 마찬가지로 구독이 만들어지면 실행은 평소와 같이 진행됩니다. 프로그램을 활성 상태로 유지하고 실행할 항목이 없으면 비동기 작업이 완료되기 전에 종료될 수 있습니다. `subscribe()`를 호출한 기본 스레드는 네트워크에서 Azure App Configuration을 호출하고 응답을 받을 때까지 기다리지 않습니다. 프로덕션 시스템에서는 다른 작업을 계속 처리할 수 있지만 이 예에서는 `Thread.sleep()`을 호출하여 약간의 지연을 추가하거나 `CountDownLatch`를 사용하여 비동기 작업을 완료할 수 있습니다.

다음 예제와 같이 `Flux`를 반환하는 API도 유사한 패턴을 따릅니다. 차이점은 `subscribe()` 메서드에 제공되는 첫 번째 콜백이 응답의 각 데이터 요소에 대해 여러 번 호출된다는 것입니다. 오류 또는 완료 콜백은 정확히 한 번 호출되며 터미널 신호로 간주됩니다. 이러한 신호 중 하나가 게시자로부터 수신되면 다른 콜백이 호출되지 않습니다.

```java
EventHubConsumerAsyncClient asyncClient = new EventHubClientBuilder()
    .connectionString("<your connection string>")
    .consumerGroup("<your consumer group>")
    .buildAsyncConsumerClient();

asyncClient.receive().subscribe(
    event -> System.out.println("Sequence number of received event: " + event.getData().getSequenceNumber()),
    ex -> System.out.println("Error receiving events: " + ex.getMessage()),
    () -> System.out.println("Successfully completed receiving all events"));
```

### <a name="backpressure"></a>역 압력

구독자가 처리할 수 있는 속도보다 더 빠른 속도로 소스가 데이터를 생성하면 어떻게 됩니까? 구독자가 데이터에 압도되어 메모리 부족 오류가 발생할 수 있습니다. 구독자가 따라갈 수 없을 때 속도를 늦추도록 게시자와 다시 통신할 수 있는 방법이 필요합니다. 기본적으로 위의 예에 표시된 대로 `Flux`에서 `subscribe()`를 호출하면 구독자는 바인딩되지 않은 데이터 스트림을 요청하여 게시자에게 데이터를 최대한 빨리 보내도록 알립니다. 이러한 동작은 항상 바람직한 것은 아니며, 구독자는 "역 압력"을 통해 게시 속도를 제어해야 할 수도 있습니다. 역 압력을 통해 구독자는 데이터 요소의 흐름을 제어할 수 있습니다. 구독자는 처리할 수 있는 제한된 수의 데이터 요소를 요청합니다. 구독자는 이러한 요소 처리를 완료한 후 추가 요청을 수행할 수 있습니다. 역 압력을 사용하여 데이터 전송을 위한 푸시 모델을 푸시 풀 모델로 변환할 수 있습니다.

다음 예제에서는 Event Hubs 소비자가 이벤트를 수신하는 속도를 제어하는 방법을 보여줍니다.

```java
EventHubConsumerAsyncClient asyncClient = new EventHubClientBuilder()
    .connectionString("<your connection string>")
    .consumerGroup("<your consumer group>")
    .buildAsyncConsumerClient();

asyncClient.receive().subscribe(new Subscriber<PartitionEvent>() {
    private Subscription subscription;

    @Override
    public void onSubscribe(Subscription subscription) {
        this.subscription = subscription;
        this.subscription.request(1); // request 1 data element to begin with
    }

    @Override
    public void onNext(PartitionEvent partitionEvent) {
        System.out.println("Sequence number of received event: " + partitionEvent.getData().getSequenceNumber());
        this.subscription.request(1); // request another event when the subscriber is ready
    }

    @Override
    public void onError(Throwable throwable) {
        System.out.println("Error receiving events: " + throwable.getMessage());
    }

    @Override
    public void onComplete() {
        System.out.println("Successfully completed receiving all events")
    }
});
```

구독자가 처음 게시자에 "연결"하면 게시자는 구독자에게 데이터 전송 상태를 관리하는 `Subscription` 인스턴스를 전달합니다. 이 `Subscription`은 구독자가 `request()`를 호출하여 처리할 수 있는 데이터 요소 수를 지정하여 역 압력을 적용할 수 있는 매체입니다.

구독자가 `onNext()`(예: `request(10)`)를 호출할 때마다 둘 이상의 데이터 요소를 요청하는 경우 게시자는 사용할 수 있거나 사용할 수 있게 되면 다음 10개 요소를 즉시 전송합니다. 이러한 요소는 구독자 끝의 버퍼에 누적되며, 각 `onNext()` 호출은 10개 이상을 요청하기 때문에 게시자가 더 이상 전송할 데이터 요소가 없거나 구독자의 버퍼 오버플로가 발생하여 메모리 부족 오류가 발생할 때까지 백로그가 계속 늘어납니다.

### <a name="cancel-a-subscription"></a>구독 취소

구독은 게시자와 구독자 간의 데이터 전송 상태를 관리합니다. 구독은 게시자가 구독자에게 모든 데이터 전송을 완료하거나 구독자가 더 이상 데이터를 수신하지 않을 때까지 활성화됩니다. 아래와 같이 구독을 취소할 수 있는 몇 가지 방법이 있습니다.

다음 예제에서는 구독자를 삭제하여 구독을 취소합니다.

```java
EventHubConsumerAsyncClient asyncClient = new EventHubClientBuilder()
    .connectionString("<your connection string>")
    .consumerGroup("<your consumer group>")
    .buildAsyncConsumerClient();

Disposable disposable = asyncClient.receive().subscribe(
    partitionEvent -> {
        Long num = partitionEvent.getData().getSequenceNumber()
        System.out.println("Sequence number of received event: " + num);
    },
    ex -> System.out.println("Error receiving events: " + ex.getMessage()),
    () -> System.out.println("Successfully completed receiving all events"));

// much later on in your code, when you are ready to cancel the subscription,
// you can call the dispose method, as such:
disposable.dispose();
```

다음 예제에서는 `Subscription`에서 `cancel()` 메서드를 호출하여 구독을 취소합니다.

```java
EventHubConsumerAsyncClient asyncClient = new EventHubClientBuilder()
    .connectionString("<your connection string>")
    .consumerGroup("<your consumer group>")
    .buildAsyncConsumerClient();

asyncClient.receive().subscribe(new Subscriber<PartitionEvent>() {
    private Subscription subscription;

    @Override
    public void onSubscribe(Subscription subscription) {
        this.subscription = subscription;
        this.subscription.request(1); // request 1 data element to begin with
    }

    @Override
    public void onNext(PartitionEvent partitionEvent) {
        System.out.println("Sequence number of received event: " + partitionEvent.getData().getSequenceNumber());
        this.subscription.cancel(); // Cancels the subscription. No further event is received.
    }

    @Override
    public void onError(Throwable throwable) {
        System.out.println("Error receiving events: " + throwable.getMessage());
    }

    @Override
    public void onComplete() {
        System.out.println("Successfully completed receiving all events")
    }
});
```

## <a name="conclusion"></a>결론

스레드는 원격 서비스 호출의 응답을 기다리는 데 낭비해서는 안 되는 소중한 리소스입니다. 마이크로서비스 아키텍처의 채택이 증가함에 따라 리소스를 효율적으로 확장하고 사용해야 하는 필요성이 중요하게 되었습니다. 비동기 API는 네트워크 바운딩 작업이 있는 경우에 적합합니다. Java용 Azure SDK는 시스템 리소스를 최대화하는 데 도움이 되는 다양한 API 집합을 비동기 작업에 제공합니다. 비동기식 클라이언트를 사용해 보는 것이 좋습니다.

특정 작업에 가장 적합한 운영자에 대한 자세한 내용은 [Reactor 3 참조 가이드](https://projectreactor.io/docs/core/release/reference/index.html)의 [어떤 운영자가 필요한가요?](https://projectreactor.io/docs/core/release/reference/#which-operator)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 다양한 비동기 프로그래밍 개념을 더 잘 이해할 수 있도록 결과를 반복하는 방법을 배우는 것이 중요합니다. 최상의 반복 전략에 대한 자세한 내용과 페이지 매김을 수행하는 방법에 대한 자세한 내용은 [Java용 Azure SDK의 페이지 매김 및 반복](pagination.md)을 참조하세요.
