---
title: Java용 Azure SDK의 HTTP 클라이언트 및 파이프라인
description: Java용 Azure SDK의 HTTP 클라이언트 및 파이프라인 기능에 대한 개요입니다.
author: srnagar
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: srnagar
ms.openlocfilehash: 93595096bc6d9fde1226f6875b866ea28b0a85bb
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528685"
---
# <a name="http-clients-and-pipelines-in-the-azure-sdk-for-java"></a>Java용 Azure SDK의 HTTP 클라이언트 및 파이프라인

이 문서에서는 Java용 Azure SDK 내에서 HTTP 클라이언트 및 파이프라인 기능을 사용하는 방법에 대해 간략히 설명합니다. 이 기능은 모든 Java용 Azure SDK 라이브러리를 사용하는 개발자에게 일관되고, 강력하며, 유연한 환경을 제공합니다.

## <a name="http-clients"></a>HTTP 클라이언트

Java용 Azure SDK는 `HttpClient` 추상화를 사용하여 구현됩니다. 이 추상화를 통해 여러 HTTP 클라이언트 라이브러리 또는 사용자 지정 구현을 허용하는 플러그형 아키텍처를 사용할 수 있습니다. 그러나 대부분의 사용자에 대한 종속성 관리를 간소화하기 위해 모든 Azure 클라이언트 라이브러리는 `azure-core-http-netty`에 종속됩니다. 이와 같이 [Netty](https://netty.io) HTTP 클라이언트는 모든 Java용 Azure SDK 라이브러리에 사용되는 기본 클라이언트입니다.

Netty가 기본 HTTP 클라이언트이지만, SDK는 프로젝트에 이미 있는 종속성에 따라 세 개의 클라이언트 구현을 제공합니다. 이러한 구현은 다음과 같습니다.

* [Netty](https://netty.io)
* [OkHttp](https://square.github.io/okhttp/)
* JDK 11에 도입된 새로운 [HttpClient](https://openjdk.java.net/groups/net/httpclient/intro.html)

### <a name="replace-the-default-http-client"></a>기본 HTTP 클라이언트 바꾸기

다른 구현을 선호하는 경우 빌드 구성 파일에서 Netty에 대한 종속성을 제외하여 이를 제거할 수 있습니다. Maven *pom.xml* 파일에서 Netty 종속성을 제외하고 다른 종속성을 포함합니다.

다음 예제에서는 `azure-security-keyvault-secrets` 라이브러리에 대한 실제 종속성에서 Netty 종속성을 제외하는 방법을 보여 줍니다. 다음과 같이 적절한 모든 `com.azure` 라이브러리에서 Netty를 제외해야 합니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-security-keyvault-secrets</artifactId>
    <version>4.2.2.</version>
    <exclusions>
      <exclusion>
        <groupId>com.azure</groupId>
        <artifactId>azure-core-http-netty</artifactId>
      </exclusion>
    </exclusions>
</dependency>

<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-core-http-okhttp</artifactId>
  <version>1.3.3</version>
</dependency>
```

> [!NOTE]
> Netty 종속성을 제거하지만 해당 위치에 구현을 제공하지 않으면 애플리케이션이 시작되지 않습니다. `HttpClient` 구현이 클래스 경로에 있어야 합니다.

### <a name="configure-http-clients"></a>HTTP 클라이언트 구성

서비스 클라이언트를 빌드하는 경우 기본적으로 `HttpClient.createDefault()`가 사용됩니다. 이 메서드는 제공된 HTTP 클라이언트 구현을 기반으로 하는 기본 `HttpClient` 인스턴스를 반환합니다. 프록시와 같이 더 복잡한 HTTP 클라이언트가 필요한 경우 각 구현에서 구성된 `HttpClient` 인스턴스를 구성할 수 있는 작성기를 제공합니다. 작성기는 `NettyAsyncHttpClientBuilder`, `OkHttpAsyncHttpClientBuilder` 및 `JdkAsyncHttpClientBuilder`입니다.

다음 예제에서는 Netty, OkHttp 및 JDK 11 HTTP 클라이언트를 사용하여 `HttpClient` 인스턴스를 빌드하는 방법을 보여 줍니다. 이러한 인스턴스는 `http://localhost:3128`을 통해 프록시를 사용하고, 암호가 *weakPassword* 인 *example* 사용자를 인증합니다.

```java
// Netty
HttpClient httpClient = new NettyAsyncHttpClientBuilder()
    .proxy(new ProxyOptions(ProxyOptions.Type.HTTP, new InetSocketAddress("localhost", 3128))
        .setCredentials("example", "weakPassword"))
    .build();

// OkHttp
HttpClient httpClient = new OkHttpAsyncHttpClientBuilder()
    .proxy(new ProxyOptions(ProxyOptions.Type.HTTP, new InetSocketAddress("localhost", 3128))
        .setCredentials("example", "weakPassword"))
    .build();

// JDK 11 HttpClient
HttpClient client = new JdkAsyncHttpClientBuilder()
    .proxy(new ProxyOptions(ProxyOptions.Type.HTTP, new InetSocketAddress("localhost", 3128))
        .setCredentials("example", "weakPassword"))
    .build();
```

이제 생성된 `HttpClient` 인스턴스를 서비스와 통신하기 위한 클라이언트로 사용할 서비스 클라이언트 작성기에 전달할 수 있습니다. 다음 예제에서는 새 `HttpClient` 인스턴스를 사용하여 Azure Storage Blob 클라이언트를 빌드합니다.

```java
BlobClient blobClient = new BlobClientBuilder()
    .connectionString(<connection string>)
    .containerName("container")
    .blobName("blob")
    .httpClient(httpClient)
    .build();
```

관리 라이브러리의 경우 관리자 구성 중에 `HttpClient`를 설정할 수 있습니다.

```java
AzureResourceManager azureResourceManager = AzureResourceManager.configure()
    .withHttpClient(httpClient)
    .authenticate(credential, profile)
    .withDefaultSubscription();
```

## <a name="http-pipeline"></a>HTTP 파이프라인

HTTP 파이프라인은 Azure용 Java 클라이언트 라이브러리에서 일관성과 진단성을 달성하기 위한 핵심 구성 요소 중 하나입니다. HTTP 파이프라인은 다음과 같이 구성됩니다.

* HTTP 전송
* HTTP 파이프라인 정책

클라이언트를 만들 때 고유한 사용자 지정 HTTP 파이프라인을 제공할 수 있습니다. 파이프라인을 제공하지 않으면 클라이언트 라이브러리에서 특정 클라이언트 라이브러리를 통해 작동하도록 구성된 파이프라인을 만듭니다.

### <a name="http-transport"></a>HTTP 전송

HTTP 전송은 서버에 대한 연결을 설정하고 HTTP 메시지를 보내고 받는 역할을 합니다. HTTP 전송은 Azure SDK 클라이언트 라이브러리에 대한 게이트웨이를 만들어 Azure 서비스와 상호 작용합니다. 이 문서의 앞부분에서 설명한 대로 Java용 Azure SDK는 기본적으로 [Netty](https://netty.io/)를 해당 HTTP 전송에 사용합니다. 그러나 SDK는 플러그형 HTTP 전송도 제공하므로 해당하는 경우 다른 구현을 사용할 수 있습니다. 또한 SDK는 OkHttp 및 JDK 11 이상과 함께 제공되는 HTTP 클라이언트에 대한 두 가지 HTTP 전송 구현을 추가로 제공합니다.

### <a name="http-pipeline-policies"></a>HTTP 파이프라인 정책

파이프라인은 각 HTTP 요청-응답 왕복에 대해 실행되는 일련의 단계로 구성됩니다. 각 정책은 전용 목적을 포함하고 있으며, 요청, 응답 또는 때때로 둘 모두에 따라 작동합니다. 모든 클라이언트 라이브러리에는 표준 'Azure Core' 계층이 있으므로 이 계층을 통해 각 정책이 파이프라인에서 순서대로 실행됩니다. 요청을 보내면 정책이 파이프라인에 추가된 순서대로 실행됩니다. 서비스에서 응답을 받으면 정책이 역순으로 실행됩니다. 파이프라인에 추가된 모든 정책은 요청을 보내기 전과 응답을 받은 후에 실행됩니다. 정책은 요청, 응답 또는 둘 모두에 따라 작동할지 여부를 결정해야 합니다. 예를 들어 로깅 정책은 요청 및 응답을 기록하지만, 인증 정책은 요청을 수정하는 데에만 관심이 있습니다.

Azure Core 프레임워크는 정책을 실행하는 데 필요한 컨텍스트와 함께 필요한 요청 및 응답 데이터를 정책에 제공합니다. 그런 다음, 정책에서 지정된 데이터를 사용하여 작업을 수행하고, 컨트롤을 파이프라인의 다음 정책에 전달할 수 있습니다.

![HTTP 파이프라인 다이어그램](./media/http-pipeline.svg)

### <a name="http-pipeline-policy-position"></a>HTTP 파이프라인 정책 위치

클라우드 서비스에 대한 HTTP 요청을 수행하는 경우 일시적인 오류를 처리하고 실패한 시도를 다시 시도해야 합니다. 이 기능은 일반적인 요구 사항이므로 Azure Core는 일시적인 오류를 감시하고 요청을 자동으로 다시 시도할 수 있는 재시도 정책을 제공합니다.

따라서 이 재시도 정책은 전체 파이프라인을 재시도 정책 이전에 실행되는 정책 및 재시도 정책 이후에 실행되는 정책의 두 부분으로 분할합니다. 재시도 정책 이전에 추가된 정책은 API 작업당 한 번만 실행되며, 재시도 정책 이후에 추가된 정책은 재시도 횟수만큼 실행됩니다.

따라서 HTTP 파이프라인을 빌드하는 경우 정책을 각 요청 재시도에 대해 실행할지, 아니면 API 작업당 한 번씩 실행할지 여부를 파악해야 합니다.

### <a name="common-http-pipeline-policies"></a>일반적인 HTTP 파이프라인 정책

REST 기반 서비스용 HTTP 파이프라인에는 인증, 재시도, 로깅, 원격 분석 및 헤더에 요청 ID 지정에 대한 정책이 포함된 구성이 있습니다. Azure Core에는 파이프라인에 추가할 수 있는 이러한 일반적으로 필요한 HTTP 정책이 미리 로드되어 있습니다.

| 정책                | GitHub 링크        |
|-----------------------|--------------------|
| 재시도 정책          | [RetryPolicy.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/http/policy/RetryPolicy.java) |
| Authentication Policy | [BearerTokenAuthenticationPolicy.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/http/policy/BearerTokenAuthenticationPolicy.java) |
| 로깅 정책        | [HttpLoggingPolicy.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/http/policy/HttpLoggingPolicy.java) |
| 요청 ID 정책     | [RequestIdPolicy.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/http/policy/RequestIdPolicy.java) |
| 원격 분석 정책      | [UserAgentPolicy.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/http/policy/UserAgentPolicy.java) |

### <a name="custom-http-pipeline-policy"></a>사용자 지정 HTTP 파이프라인 정책

HTTP 파이프라인 정책은 요청 및 응답을 수정하거나 데코레이팅하는 편리한 메커니즘을 제공합니다. 사용자 지정 정책은 사용자 또는 클라이언트 라이브러리 개발자가 만든 파이프라인에 추가할 수 있습니다. 정책을 파이프라인에 추가하는 경우 이 정책을 호출당 또는 재시도당 실행할지 여부를 지정할 수 있습니다.

사용자 지정 HTTP 파이프라인 정책을 만들려면 기본 정책 형식을 확장하고 몇 가지 추상 메서드를 구현하기만 하면 됩니다. 그러면 정책을 파이프라인에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Java용 Azure SDK의 HTTP 클라이언트 기능에 익숙해졌으므로 사용하는 HTTP 클라이언트를 추가로 사용자 지정하는 방법을 알아보려면 [Java용 Azure SDK에서 프록시 구성](proxying.md)을 참조하세요.
