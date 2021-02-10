---
title: Java용 Azure SDK의 장기 실행 작업
description: 장기 실행 작업과 관련된 Java용 Azure SDK의 개념 소개
author: anuchandy
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: anuchan
ms.openlocfilehash: a20fb7a638fefd0182bea89e3d5c4555442a4547
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528636"
---
# <a name="long-running-operations-in-the-azure-sdk-for-java"></a>Java용 Azure SDK의 장기 실행 작업

이 문서에서는 Java용 Azure SDK를 사용하여 장기 실행 작업을 사용하는 방법을 소개합니다.

Azure의 특정 작업은 완료하는 데 시간이 오래 걸릴 수 있습니다. 이러한 작업은 빠른 요청/응답 흐름의 표준 HTTP 스타일을 벗어납니다. 예를 들어 원본 URL에서 Storage Blob으로 데이터를 복사하거나 양식을 인식하도록 모델을 학습시키는 작업은 몇 초에서 몇 분 정도 걸릴 수 있는 작업입니다. 이러한 작업을 장기 실행 작업이라고 하며, 'LRO'로 약칭하기도 합니다. LRO는 요청된 작업 및 서버 쪽에서 수행해야 하는 프로세스에 따라 몇 초, 몇 분, 몇 시간, 며칠 이상이 소요될 수 있습니다.

Azure용 Java 클라이언트 라이브러리에는 모든 장기 실행 작업이 `begin` 접두사로 시작되어야 한다는 규칙이 있습니다. 이 접두사는 해당 작업이 장기 실행 작업이며, 이 작업과 상호 작용하는 방법은 일반적인 요청/응답 흐름과 약간 다르다는 것을 나타냅니다. 또한 `begin` 접두사가 있는 작업의 반환 형식은 장기 실행 작업 기능을 전반적으로 지원하기 위해 일반적인 작업과 다릅니다. Java용 Azure SDK에 있는 대부분의 요소와 마찬가지로 장기 실행 작업을 위한 동기 및 비동기 API가 모두 있습니다.

* 동기 클라이언트에서 장기 실행 작업은 `SyncPoller` 인스턴스를 반환합니다.
* 비동기 클라이언트에서 장기 실행 작업은 `PollerFlux` 인스턴스를 반환합니다.

`SyncPoller`와 `PollerFlux`는 모두 장기 실행 서버 쪽 작업과의 상호 작용을 단순화하기 위해 고안된 클라이언트 쪽 추상화입니다. 이 문서의 나머지 부분에서는 이러한 유형으로 작업할 때의 모범 사례를 간략하게 설명합니다.

## <a name="synchronous-long-running-operations"></a>동기식 장기 실행 작업

`SyncPoller`를 반환하는 API를 호출하면 장기 실행 작업이 즉시 시작됩니다. 이 API는 `SyncPoller`를 즉시 반환하므로 장기 실행 작업의 진행 상황을 모니터링하고 최종 결과를 검색할 수 있습니다. 다음 예에서는 `SyncPoller`를 사용하여 장기 실행 작업의 진행 상황을 모니터링하는 방법을 보여 줍니다.

```java
SyncPoller<UploadBlobProgress, UploadedBlobProperties> poller = syncClient.beginUploadFromUri(<URI to upload from>)
PollResponse<UploadBlobProgress> response;

do {
    response = poller.poll();
    System.out.println("Status of long running upload operation: " + response.getStatus());
    Duration pollInterval = response.getRetryAfter();
    TimeUnit.MILLISECONDS.sleep(pollInterval.toMillis());
} while (!response.getStatus().isComplete());
```

이 예에서는 `SyncPoller`에 `poll()` 메서드를 사용하여 장기 실행 작업의 진행 상황에 대한 정보를 검색합니다. 이 코드는 콘솔에 상태를 출력하지만, 더 효과적인 구현에서는 이 상태를 기반으로 관련 의사 결정을 내립니다.

`getRetryAfter()` 메서드는 다음 폴링 전에 대기해야 하는 시간에 대한 정보를 반환합니다. 대부분의 Azure 장기 실행 작업은 HTTP 응답의 일부로 폴링 지연 시간(일반적으로 사용되는 `retry-after` 헤더)을 반환합니다. 응답에 폴링 지연 시간이 포함되지 않은 경우 `getRetryAfter()` 메서드는 장기 실행 작업을 호출할 때 지정된 기간을 반환합니다.

위의 예에서는 `do..while` 루프를 사용하여 장기 실행 작업이 완료될 때까지 반복적으로 폴링합니다. 이러한 중간 결과에 관심이 없다면 `waitForCompletion()`을 대신 호출할 수 있습니다. 이 호출은 장기 실행 작업이 완료될 때까지 현재 스레드를 차단하고 마지막 폴링 응답을 반환합니다.

```java
PollResponse<UploadBlobProgress> response = poller.waitForCompletion();
```

마지막 폴링 응답에 장기 실행 작업이 성공적으로 완료되었다고 표시되면 `getFinalResult()`를 사용하여 최종 결과를 검색할 수 있습니다.

```java
if (LongRunningOperationStatus.SUCCESSFULLY_COMPLETED == response.getStatus()) {
    UploadedBlobProperties result = poller.getFinalResult();
}
```

`SyncPoller`의 다른 유용한 API는 다음과 같습니다.

1. `waitForCompletion(Duration)`: 지정된 시간 제한 기간 동안 장기 실행 작업이 완료될 때까지 기다립니다.
1. `waitUntil(LongRunningOperationStatus)`: 지정된 장기 실행 작업의 상태가 수신될 때까지 기다립니다.
1. `waitUntil(LongRunningOperationStatus, Duration)`: 지정된 장기 실행 작업의 상태가 수신될 때까지 기다리거나, 지정된 시간 제한 기간이 만료될 때까지 기다립니다.

## <a name="asynchronous-long-running-operations"></a>비동기식 장기 실행 작업

다음 예에서는 `PollerFlux`를 사용하여 장기 실행 작업을 관찰하는 방법을 보여 줍니다. 비동기 API에서는 `subscribe()`를 호출하는 주 스레드와 다른 스레드에서 네트워크 호출이 발생합니다. 즉, 결과를 얻기 전에 주 스레드가 종료될 수 있습니다. 개발자는 비동기 작업이 완료되기 전에는 애플리케이션이 종료되지 않도록 만들어야 합니다.

비동기 API는 `PollerFlux`를 즉시 반환하지만 장기 실행 작업 자체는 `PollerFlux`를 구독해야만 시작됩니다. 모든 `Flux` 기반 API는 이 프로세스에 따라 작동합니다. 다음 예에서는 비동기 장기 실행 작업을 보여 줍니다.

```java
asyncClient.beginUploadFromUri(...)
    .subscribe(response -> System.out.println("Status of long running upload operation: " + response.getStatus()));
```

다음 예에서는 장기 실행 작업에 대한 간헐적 상태 업데이트를 가져옵니다. 이러한 업데이트를 사용하여 장기 실행 작업이 예상대로 작동하는지 여부를 확인할 수 있습니다. 이 예에서는 콘솔에 상태를 출력하지만, 더 효과적인 구현에서는 이 상태를 기반으로 관련 오류 처리 결정을 내립니다.

중간 상태 업데이트에는 관심이 없고 최종 결과가 도착할 때 이에 대한 알림만 받으려는 경우에는 다음 예와 유사한 코드를 사용할 수 있습니다.

```java
asyncClient.beginUploadFromUri(...)
    .last()
    .flatMap(response -> {
        if (LongRunningOperationStatus.SUCCESSFULLY_COMPLETED == response.getStatus()) {
            return response.getFinalResult();
        }
        return Mono.error(new IllegalStateException("Polling completed unsuccessfully with status: "+ response.getStatus()));
    })
    .subscribe(
        finalResult -> processFormPages(finalResult),
        ex -> countDownLatch.countDown(),
        () -> countDownLatch.countDown());
```

이 코드에서는 `last()`를 호출하여 장기 실행 작업의 최종 결과를 검색합니다. 이 호출은 모든 폴링이 완료될 때까지 기다리도록 `PollerFlux`에 지시합니다. 완료되면 장기 실행 작업이 터미널 상태에 도달하고 개발자는 해당 상태를 살펴보고 결과를 확인할 수 있습니다. 폴러에서 장기 실행 작업이 성공적으로 완료되었다고 알리면 최종 결과를 검색하고 구독 호출의 소비자에게 전달할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Java용 Azure SDK의 장기 실행 API에 익숙해졌으므로 [Java용 Azure SDK에서 프록시 구성](proxying.md)을 참조하여 HTTP 클라이언트를 추가로 사용자 지정하는 방법을 알아보세요.
