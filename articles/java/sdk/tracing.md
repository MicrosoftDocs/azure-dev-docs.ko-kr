---
title: Java용 Azure SDK에서 추적 구성
description: 추적과 관련된 Java용 Azure SDK의 개념 소개
author: samvaity
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: savaity
ms.openlocfilehash: 2dc2085ac71167cefd8fed5475dc9744cf520fea
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528589"
---
# <a name="configure-tracing-in-the-azure-sdk-for-java"></a>Java용 Azure SDK에서 추적 구성

이 문서에서는 Java용 Azure SDK를 구성하여 추적 기능을 통합하는 방법을 소개합니다.

Java용 Azure SDK는 [OpenTelemetery](https://opentelemetry.io/) 기반 [azure-core-tracing-opentelemetry plugin](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/core/azure-core-tracing-opentelemetry#azure-tracing-opentelemetry-client-library-for-java)에 대한 종속성을 포함하여 모든 클라이언트 라이브러리에서 추적을 사용할 수 있도록 지원합니다. OpenTelemetry는 클라우드 네이티브 소프트웨어에 대한 원격 분석 데이터를 생성, 캡처 및 수집할 수 있는 주요 오픈 소스 가시성 프레임워크입니다.

추적과 관련된 두 가지 주요 개념은 **범위** 와 **추적** 입니다. 범위는 추적의 단일 작업을 나타냅니다. 범위는 HTTP 요청, RPC(원격 프로시저 호출), 데이터베이스 쿼리 또는 코드에서 사용하는 경로를 나타낼 수 있습니다. 추적은 시스템을 통한 작업 경로를 표시하는 범위의 트리입니다. TraceID라는 고유한 16바이트 시퀀스로 추적을 구분할 수 있습니다. 이러한 개념 및 이러한 개념과 OpenTelemetry의 관계에 대한 자세한 내용은 [OpenTelemetry 설명서](https://opentelemetry.io/docs/)를 참조하세요.

다음과 같은 두 가지 방법으로 Java용 Azure 클라이언트 라이브러리에서 추적을 사용하도록 설정할 수 있습니다.

1. Java용 Azure SDK에 기본 제공되는 기능을 사용하도록 설정합니다.
2. In Process 에이전트가 추적 데이터를 수집하고 코드 변경 없이 제출할 수 있도록 설정합니다.

## <a name="enable-tracing-in-the-azure-sdk-for-java"></a>Java용 Azure SDK에서 추적 사용

모든 Azure Java 클라이언트 라이브러리에 추적을 사용하도록 설정하려면 `azure-core-tracing-opentelemetry` 및 `opentelemetry-sdk` 종속성을 애플리케이션에 추가합니다. 예를 들어 Maven에서는 다음 종속성을 추가합니다.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-core-tracing-opentelemetry</artifactId>
  <version>1.0.0-beta.6</version>
</dependency>

<dependency>
  <groupId>io.opentelemetry</groupId>
  <artifactId>opentelemetry-sdk</artifactId>
  <version>0.8.0</version>
</dependency>
```

이 종속성을 추가하면 추적이 사용하도록 설정되고, 모든 HTTP 요청에 추적이 포함됩니다. 현재 다음과 같은 두 가지 문제가 있습니다.

1. 들어오는 부모 범위와 통합되지 않습니다.
2. 생성된 추적을 나중에 분석하기 위해 내보낼 수 없습니다.

다음 섹션에서는 이러한 문제를 해결합니다.

### <a name="integrate-parent-spans"></a>부모 범위 통합

위에서 설명했듯이, 종속성을 포함하면 Azure 클라이언트 라이브러리 내에서 추적을 사용할 수 있습니다. 그러나 들어오는 추적 데이터와 통합되지 않습니다. 수신 요청이 Azure 클라이언트 라이브러리를 호출하는 웹 환경을 예로 들 수 있습니다. 추적을 사용하도록 설정하려면 애플리케이션에서 루트 범위를 만들어 Azure 클라이언트 라이브러리 호출에 전달하면 됩니다. 그러면 이 범위가 Azure 서비스로 나가는 적절한 요청에 캡슐화될 수 있습니다. 다음 예와 같이 모든 클라이언트 메서드에서 `Context` 매개 변수를 사용하여 이 작업을 수행할 수 있습니다.

```java
// The 'span' given in this context is the parent span key received from the incoming request.
Context traceContext = new Context(PARENT_SPAN_KEY, span);

// This example code passes a new configuration setting to a service, but also includes
// the traceContext from above, so that it may be picked up by the http transport and included as appropriate.
appConfigClient.setConfigurationSettingWithResponse(new ConfigurationSetting().setKey("hello").setValue("world"), true, traceContext);
```

부모 범위가 제공되지 않는 경우에는 모든 클라이언트 라이브러리 발신 요청을 캡슐화하는 새 부모 범위가 만들어집니다. Azure 클라이언트 라이브러리 메서드를 호출할 때마다 두 가지 범위가 만들어집니다. 하나는 클라이언트 라이브러리의 진행 상황을 추적하고, 다른 하나는 나가는 HTTP 요청 범위를 추적합니다.

#### <a name="tracer-span-attributes"></a>추적 프로그램 범위 특성

OpenTelemetry의 [의미 체계 규칙](https://github.com/open-telemetry/opentelemetry-specification/blob/e9340d74f1ba0b651b3581d6bd5df6a92b772e18/semantic-conventions.md))에 설명된 필수 표준 특성 외에도, Azure 클라이언트 라이브러리는 다음 특성을 사용하여 범위에 주석을 추가합니다.

* `az.namespace`: Azure 서비스에 매핑되는 Microsoft 리소스 공급자 [네임스페이스](/azure/azure-resource-manager/management/azure-services-resource-providers)입니다.
* `x-ms-request-id`: 요청에 대한 고유 식별자입니다.
* `span.kind`: 추적의 범위, 부모 및 자식 간의 관계를 설명합니다.
* `span.status.message`: 완료된 범위의 상태를 나타냅니다.
* `span.status.code`: 완료된 범위의 상태 코드를 나타냅니다.

수행할 작업에 대한 추가 메타데이터는 범위 이름에 캡처됩니다. HTTP 범위 이름은 URI 경로 값으로 설정되고 라이브러리 메서드 호출 범위는 `<namespace qualified type>.<method name>` 형식입니다.

예를 들어 구성 설정을 지정하는 App Configuration 클라이언트 요청(즉, `appConfigClient.setConfigurationSettingWithResponse(new ConfigurationSetting().setKey("hello").setValue("world")`)은 다음과 같은 두 범위를 초래합니다.

* `AppConfig.setKey`라는 라이브러리 메서드 범위
* `/kv/hello`라는 HTTP 발신 요청 범위

### <a name="configure-tracing-exports"></a>추적 내보내기 구성

추적 정보를 사용하려는 애플리케이션은 [Zipkin](https://zipkin.io/), [Jaeger](https://www.jaegertracing.io/) 및 [Azure Monitor](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/monitor/microsoft-opentelemetry-exporter-azuremonitor#azure-monitor-opentelemetry-exporter-client-library-for-java) 같은 분산 추적 저장소로 추적을 내보내야 합니다. 다음 예에서는 Jaeger 관련 API를 사용하여 localhost 포트 14250에서 실행되는 Jaeger 분산 추적 저장소로 추적 정보를 내보내도록 구성합니다.

```java
ManagedChannel channel = ManagedChannelBuilder.forAddress("localhost", 14250).usePlaintext().build();
JaegerGrpcSpanExporter exporter = JaegerGrpcSpanExporter.newBuilder()
    .setChannel(channel)
    .setServiceName("Sample")
    .setDeadline(0)
    .build();
TracerSdkFactory tracerSdkFactory = (TracerSdkFactory) OpenTelemetry.getTracerFactory();
tracerSdkFactory.addSpanProcessor(SimpleSpansProcessor.newBuilder(exporter).build());
```

## <a name="enable-tracing-with-the-in-process-agent"></a>In Process 에이전트로 추적 사용

대규모 분산 시스템에서는 애플리케이션을 나중에 분석하기 위해 데이터를 자동으로 수집하고 전송하는 [Azure Monitor](/azure/azure-monitor/overview) 기능인 Application Insights를 사용할 수 있습니다. 이 계측 기능은 애플리케이션을 모니터링하고, '계측 키'라고 하는 고유한 GUID를 사용하여 원격 분석 데이터를 [Azure Application Insights 리소스](/azure/azure-monitor/app/app-insights-overview)로 보냅니다.

[Java In Process 에이전트](/azure/azure-monitor/app/java-in-process-agent)를 사용하면 코드를 변경하지 않고 애플리케이션 모니터링을 사용하도록 설정할 수 있습니다. 또한 [azure-core-tracing-opentelemetry](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/core/azure-core-tracing-opentelemetry#azure-tracing-opentelemetry-client-library-for-java) 종속성을 프로젝트에 추가해야 합니다. 이 작업을 완료한 후에는 Application Insights 대시보드를 사용하여 요청을 계측하고, 성능 카운터를 수집하고, 성능 문제 및 예외를 진단하고, 애플리케이션 내에서 사용자가 수행하는 작업을 추적하는 코드를 작성할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Java용 Azure SDK의 핵심 교차 기능에 대해 알아보았으므로, 이제 [Java 및 Azure Identity로 Azure 인증](identity.md)에서 보안 애플리케이션을 만드는 방법을 알아보세요.
