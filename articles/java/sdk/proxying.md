---
title: Java용 Azure SDK에서 프록시 구성
description: 프록시 사용과 관련된 Java용 Azure SDK의 개념 소개
author: alzimmermsft
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: alzimmer
ms.openlocfilehash: 03acb768d348505b03419eb132c1d9623633b442
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118171"
---
# <a name="configure-proxies-in-the-azure-sdk-for-java"></a>Java용 Azure SDK에서 프록시 구성

이 문서에서는 프록시를 적절히 사용하도록 Java용 Azure SDK를 구성하는 방법을 소개합니다.

## <a name="http-proxy-configuration"></a>HTTP 프록시 구성

Java용 Azure 클라이언트 라이브러리는 `HttpClient`의 프록시를 구성하는 여러 가지 방법을 제공합니다.

각 프록시 제공 방법마다 고유한 장단점이 있으며 다양한 수준의 캡슐화가 제공됩니다. `HttpClient`의 프록시를 구성하면 나머지 수명 동안 해당 프록시가 사용됩니다. 프록시를 개별 `HttpClient`에 연결하면 애플리케이션이 여러 `HttpClient` 인스턴스를 사용할 수 있으므로 각각 다른 프록시를 사용하여 애플리케이션의 프록시 사용 요구 사항을 충족할 수 있습니다.

프록시 구성 옵션은 다음과 같습니다.

* [환경 프록시 사용](#use-an-environment-proxy)
* [구성 프록시 사용](#use-a-configuration-proxy)
* [명시적 프록시 사용](#use-an-explicit-proxy)

### <a name="use-an-environment-proxy"></a>환경 프록시 사용

기본적으로 HTTP 클라이언트 작성기는 환경의 프록시 구성을 검사합니다. 이 프로세스에서는 Java `Configuration` API용 Azure SDK를 사용합니다. 작성기가 만든 클라이언트는 `Configuration.getGlobalConfiguration()`을 호출하여 검색된 '글로벌 구성'의 복사본으로 구성됩니다. 이 호출은 시스템 환경의 모든 HTTP 프록시 구성을 읽습니다.

작성기는 환경을 검사하여 다음 환경 구성을 지정된 순서대로 검색합니다.

1. `HTTPS_PROXY`
2. `HTTP_PROXY`
3. `https.proxy*`
4. `http.proxy*`

`*`는 잘 알려진 Java 프록시 속성을 나타냅니다. 자세한 내용은 Oracle 설명서의 [Java 네트워킹 및 프록시](https://docs.oracle.com/javase/8/docs/technotes/guides/net/proxies.html)를 참조하세요.

작성기가 환경 구성을 찾으면 `ProxyOptions.fromConfiguration(Configuration.getGlobalConfiguration())`을 호출하여 `ProxyOptions` 인스턴스를 만듭니다. 이 문서의 다음 부분에서는 `ProxyOptions` 유형에 대해 자세히 설명합니다.

> [!Important]
> 프록시 구성을 사용하려면 시스템 환경 속성 `java.net.useSystemProxies`를 `true`로 설정해야 합니다.

또한 시스템 환경 변수에 있는 프록시 구성을 사용하지 않는 HTTP 클라이언트 인스턴스를 만들 수도 있습니다. 기본 동작을 재정의하려면 HTTP 클라이언트 작성기에서 다르게 구성된 `Configuration`을 명시적으로 설정합니다. 작성기에서 `Configuration`을 설정하면 `Configuration.getGlobalConfiguration()`이 더 이상 호출되지 않습니다. 예를 들어 `Configuration.NONE`을 사용하여 `configuration(Configuration)`을 호출하는 경우 명시적으로 작성기에서 환경의 구성을 검사하지 않도록 방지할 수 있습니다.

다음 예에서는 `localhost:8888` 값이 지정된 `HTTP_PROXY` 환경 변수를 사용하여 Fiddler를 프록시로 사용합니다. 이 코드는 Netty와 OkHttp HTTP 클라이언트를 만드는 방법을 보여 줍니다. HTTP 클라이언트 구성에 대한 자세한 내용은 [HTTP 클라이언트 및 파이프라인](http-client-pipeline.md)을 참조하세요.

```bash
export HTTP_PROXY=localhost:8888
```

```java
HttpClient nettyHttpClient = new NettyAsyncHttpClientBuilder().build();
HttpClient okhttpHttpClient = new OkHttpAsyncHttpClientBuilder().build();
```

환경 프록시가 사용되지 않도록 하려면 다음 예와 같이 `Configuration.NONE`을 사용하여 HTTP 클라이언트 작성기를 구성합니다.

```java
HttpClient nettyHttpClient = new NettyAsyncHttpClientBuilder()
    .configuration(Configuration.NONE)
    .build();

HttpClient okhttpHttpClient = new OkHttpAsyncHttpClientBuilder()
    .configuration(Configuration.NONE)
    .build();
```

### <a name="use-a-configuration-proxy"></a>구성 프록시 사용

환경에서 읽는 대신 환경에서 이미 수락된 것과 동일한 프록시 설정을 사용하여 사용자 지정 `Configuration`을 사용하도록 HTTP 클라이언트 작성기를 구성할 수 있습니다. 이 구성은 제한된 사용 사례로 범위가 지정된 재사용 가능한 구성을 지원하는 기능을 제공합니다. HTTP 클라이언트 작성기는 `HttpClient`를 작성할 때 `ProxyOptions.fromConfiguration(<Configuration passed into the builder>)`에서 반환된 `ProxyOptions`를 사용합니다.

다음 예에서는 `Configuration` 개체에 설정된 `http.proxy*` 구성을 사용하여 Fiddler를 프록시로 인증하는 프록시를 사용합니다.

```java
Configuration configuration = new Configuration()
    .put("java.net.useSystemProxies", "true")
    .put("http.proxyHost", "localhost")
    .put("http.proxyPort", "8888")
    .put("http.proxyUser", "1")
    .put("http.proxyPassword", "1");

HttpClient nettyHttpClient = new NettyAsyncHttpClientBuilder()
    .configuration(configuration)
    .build();

HttpClient okhttpHttpClient = new OkHttpAsyncHttpClientBuilder()
    .configuration(configuration)
    .build();
```

### <a name="use-an-explicit-proxy"></a>명시적 프록시 사용

Java 클라이언트 라이브러리는 프록시 구성에 사용되는 Azure 클라이언트 라이브러리 유형 역할을 하는 `ProxyOptions` 클래스와 함께 제공됩니다. 프록시 요청, 프록시 주소, 프록시 인증 자격 증명 및 프록시를 사용하지 않는 호스트를 보내는 데 사용되는 네트워크 프로토콜을 사용하여 `ProxyOptions`를 구성할 수 있습니다. 프록시 네트워크 프로토콜 및 프록시 주소만 필요합니다. 인증 자격 증명을 사용하는 경우 사용자 이름과 암호를 모두 설정해야 합니다.

다음 예에서는 기본 Fiddler 주소(`localhost:8888`)에 대한 요청을 프록시하는 간단한 `ProxyOptions` 인스턴스를 만듭니다.

```java
ProxyOptions proxyOptions = new ProxyOptions(ProxyOptions.Type.HTTP, new InetSocketAddress("localhost", 8888));
```

다음 예에서는 프록시 인증을 요구하는 Fiddler 인스턴스에 요청을 프록시하는 인증된 `ProxyOptions`를 만듭니다.

```java
// Fiddler uses username "1" and password "1" with basic authentication as its proxy authentication requirement.
ProxyOptions proxyOptions = new ProxyOptions(ProxyOptions.Type.HTTP, new InetSocketAddress("localhost", 8888))
    .setCredentials("1", "1");
```

`ProxyOptions`로 HTTP 클라이언트 작성기를 직접 구성하여 사용할 명시적 프록시를 나타낼 수 있습니다. 이 구성은 프록시를 제공하는 가장 세부적인 방법이며, 일반적으로 프록시 사용 요구 사항을 업데이트하도록 변경할 수 있는 `Configuration`을 전달하는 것만큼 유연하지는 않습니다.

다음 예에서는 `ProxyOptions`를 사용하여 Fiddler를 프록시로 사용합니다.

```java
ProxyOptions proxyOptions = new ProxyOptions(ProxyOptions.Type.HTTP, new InetSocketAddress("localhost", 8888));

HttpClient nettyHttpClient = new NettyAsyncHttpClientBuilder()
    .proxy(proxyOptions)
    .build();

HttpClient okhttpHttpClient = new OkHttpAsyncHttpClientBuilder()
    .proxy(proxyOptions)
    .build();
```

## <a name="next-steps"></a>다음 단계

Java용 Azure SDK의 프록시 구성에 익숙해지면 애플리케이션 내에서 흐름을 보다 잘 이해하고 문제를 진단하는 데 도움이 되는 [Java용 Azure SDK의 추적 구성](tracing.md)을 참조하세요.
