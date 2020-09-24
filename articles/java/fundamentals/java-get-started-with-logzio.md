---
title: Azure에서 실행되는 Java 앱용 Logz.io 시작
description: 이 자습서에서는 Azure에서 실행되는 Java 앱용 Logz.io를 통합하고 구성하는 방법을 보여 줍니다.
author: jdubois
manager: bborges
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: judubois
ms.custom: devx-track-java
ms.openlocfilehash: 89dc2a40e39a417d33e59b22f28202c61f036c79
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831489"
---
# <a name="tutorial-getting-started-with-monitoring-and-logging-using-logzio-for-java-apps-running-on-azure"></a>자습서: Azure에서 실행되는 Java 앱용 Logz.io를 사용하여 모니터링 및 로깅 시작

이 자습서에서는 수집 및 분석을 위해 로그를 [Logz.io](https://logz.io/) 서비스에 보내도록 클래식 Java 애플리케이션을 구성하는 방법을 보여 줍니다. Logz.io는 Elasticsearch/Logstash/Kibana(ELK) 및 Grafana를 기반으로 하는 전체 모니터링 솔루션을 제공합니다.

이 자습서에서는 Log4J 또는 Logback을 사용하고 있다고 가정합니다. 이러한 두 라이브러리는 Java에서 로깅하는 데 가장 널리 사용되므로 자습서가 Azure에서 실행되는 대부분의 애플리케이션에서 작동합니다. 이미 탄력적 스택을 사용하여 Java 애플리케이션을 모니터링하고 있는 경우 이 자습서에서는 Logz.io 엔드포인트를 대상으로 지정하도록 다시 구성하는 방법을 보여 줍니다.

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * 로그를 기존 Java 애플리케이션에서 Logz.io로 보냅니다.
> * 진단 로그 및 메트릭을 Azure 서비스에서 Logz.io로 보냅니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Java Developer Kit](./java-jdk-long-term-support.md) 버전 8 이상
* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/logz.logzio-elk-as-a-service-pro)의 Logz.io 계정
* Log4J 또는 Logback을 사용하는 기존 Java 애플리케이션

## <a name="send-java-application-logs-to-logzio"></a>Logz.io에 Java 애플리케이션 로그 보내기

먼저, Logz.io 계정에 액세스할 수 있는 토큰을 사용하여 Java 애플리케이션을 구성하는 방법에 대해 알아봅니다.

### <a name="get-your-logzio-access-token"></a>Logz.io 액세스 토큰 가져오기

토큰을 가져오려면 Logz.io 계정에 로그인하고, 오른쪽 모서리에서 톱니바퀴 아이콘을 선택한 다음, **설정 > 일반**을 차례로 선택합니다. 나중에 사용할 수 있도록 계정 설정에 표시된 액세스 토큰을 복사하세요.

### <a name="install-and-configure-the-logzio-library-for-log4j-or-logback"></a>Log4J 또는 Logback용 Logz.io 라이브러리 설치 및 구성

Logz.io Java 라이브러리는 Maven Central에서 사용할 수 있으므로 앱 구성에 종속성으로 추가할 수 있습니다. Maven Central에서 버전 번호를 확인하고, 다음 구성 설정에서 최신 버전을 사용합니다.

Maven을 사용하는 경우 다음 종속성을 `pom.xml` 파일에 추가합니다.

**Log4J:**

```xml
<dependency>
    <groupId>io.logz.log4j2</groupId>
    <artifactId>logzio-log4j2-appender</artifactId>
    <version>1.0.11</version>
</dependency>
```

**Logback:**

```xml
<dependency>
    <groupId>io.logz.logback</groupId>
    <artifactId>logzio-logback-appender</artifactId>
    <version>1.0.22</version>
</dependency>
```

Gradle을 사용하는 경우 다음 종속성을 빌드 스크립트에 추가합니다.

**Log4J:**

```
implementation 'io.logz.log4j:logzio-log4j-appender:1.0.11'
```

**Logback:**

```
implementation 'io.logz.logback:logzio-logback-appender:1.0.22'
```

다음으로, Log4J 또는 Logback 구성 파일을 업데이트합니다.

**Log4J:**

```xml
<Appenders>
    <LogzioAppender name="Logzio">
        <logzioToken><your-logz-io-token></logzioToken>
        <logzioType>java-application</logzioType>
        <logzioUrl>https://<your-logz-io-listener-host>:8071</logzioUrl>
    </LogzioAppender>
</Appenders>

<Loggers>
    <Root level="info">
        <AppenderRef ref="Logzio"/>
    </Root>
</Loggers>
```

**Logback:**

```xml
<configuration>
    <!-- Use shutdownHook so that we can close gracefully and finish the log drain -->
    <shutdownHook class="ch.qos.logback.core.hook.DelayingShutdownHook"/>
    <appender name="LogzioLogbackAppender" class="io.logz.logback.LogzioLogbackAppender">
        <token><your-logz-io-token></token>
        <logzioUrl>https://<your-logz-io-listener-host>:8071</logzioUrl>
        <logzioType>java-application</logzioType>
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>INFO</level>
        </filter>
    </appender>

    <root level="debug">
        <appender-ref ref="LogzioLogbackAppender"/>
    </root>
</configuration>
```

`<your-logz-io-token>` 자리 표시자를 액세스 토큰으로 바꾸고 `<your-logz-io-listener-host>` 자리 표시자를 해당 영역의 수신기 호스트(예: listener.logz.io)로 바꿉니다. 계정 영역을 찾는 방법에 대한 자세한 내용은 [계정 영역](https://docs.logz.io/user-guide/accounts/account-region.html)을 참조하세요.

`logzioType` 요소는 서로 다른 문서를 구분하는 데 사용되는 Elasticsearch의 논리 필드를 참조합니다. Logz.io를 최대한 활용하려면 이 매개 변수를 올바르게 구성해야 합니다.

Logz.io "Type"은 로그 형식(예: Apache, NGinx, MySQL)이며, 원본(예: server1, server2, server3)이 아닙니다. 이 자습서에서는 Java 애플리케이션을 구성하므로 `java-application` 형식을 호출하고 있으며, 이러한 애플리케이션의 형식은 모두 동일해야 합니다.

고급 사용에서는 Java 애플리케이션을 다양한 형식으로 그룹화할 수 있으며, 각 형식에는 고유한 특정 로그 형식이 사용됩니다(Log4J 및 Logback으로 구성 가능). 예를 들어 "spring-boot-monolith" 형식과 "spring-boot-microservice" 형식을 사용할 수 있습니다.

### <a name="test-your-configuration-and-log-analysis-on-logzio"></a>Logz.io에서 구성 및 로그 분석 테스트

Logz.io 라이브러리가 구성되면 애플리케이션에서 로그를 직접 보내야 합니다. 모든 구성이 제대로 작동하는지 테스트하려면 Logz.io 콘솔로 이동하여 **Live tail**(라이브 비상 로그) 탭을 선택한 다음, **run**(실행)을 선택합니다. 연결이 작동하고 있음을 알려주는 다음과 비슷한 메시지가 표시됩니다.

```output
Requesting Live Tail access...
Access granted. Opening connection...
Connected. Tailing...
````

다음으로, 애플리케이션을 시작하거나 이를 사용하여 일부 로그를 생성합니다. 로그는 화면에 직접 표시됩니다. 예를 들어 Spring Boot 애플리케이션의 첫 번째 시작 메시지는 다음과 같습니다.

```output
2019-09-19 12:54:40.685Z Starting JavaApp on javaapp-default-9-5cfcb8797f-dfp46 with PID 1 (/workspace/BOOT-INF/classes started by cnb in /workspace)
2019-09-19 12:54:40.686Z The following profiles are active: prod
2019-09-19 12:54:42.052Z Bootstrapping Spring Data repositories in DEFAULT mode.
2019-09-19 12:54:42.169Z Finished Spring Data repository scanning in 103ms. Found 6 repository interfaces.
2019-09-19 12:54:43.426Z Bean 'spring.task.execution-org.springframework.boot.autoconfigure.task.TaskExecutionProperties' of type [org.springframework.boot.autoconfigure.task.TaskExecutionProperties] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
```

이제 Logz.io에서 로그를 처리했으므로 모든 플랫폼의 서비스를 사용할 수 있습니다.

## <a name="send-azure-services-data-to-logzio"></a>Logz.io에 Azure 서비스 데이터 보내기

다음으로, 로그와 메트릭을 Azure 리소스에서 Logz.io로 보내는 방법에 대해 알아봅니다.

### <a name="deploy-the-template"></a>템플릿 배포

첫 번째 단계는 Logz.io - Azure 통합 템플릿을 배포하는 것입니다. 통합은 파이프라인의 모든 필수 구성 요소를 설정하는 미리 만들어진 Azure 배포 템플릿을 기반으로 합니다. 템플릿은 하나의 Event Hub 네임스페이스, 하나의 Event Hub, 두 개의 스토리지 Blob 및 필요한 모든 올바른 권한과 연결을 만듭니다. 자동 배포에서 설정한 리소스는 단일 Azure 지역에 대한 데이터를 수집하고, 해당 데이터를 Logz.io에 제공할 수 있습니다.

[리포지토리의 추가 정보에 있는 첫 번째 단계](https://github.com/logzio/logzio-azure-serverless)에 표시된 **Azure에 배포** 단추를 찾습니다.

**Azure에 배포**가 선택되면 Azure Portal의 **사용자 지정 배포** 페이지가 미리 채워진 필드 목록과 함께 표시됩니다.

대부분의 필드는 있는 그대로 둘 수 있지만, 다음 설정은 입력해야 합니다.

* **리소스 그룹**: 기존 그룹을 선택하거나 새 그룹을 만듭니다.
* **Logzio 로그/메트릭 호스트**: Logz.io 수신기의 URL을 입력합니다. 이 URL이 무엇인지 확실하지 않으면 로그인 URL을 확인합니다. app.logz.io인 경우 listener.logz.io를 사용합니다(기본 설정). app-eu.logz.io인 경우 listener-eu.logz.io를 사용합니다.
* **Logzio 로그/메트릭 토큰**: Azure 로그 또는 메트릭을 제공하려는 Logz.io 계정의 토큰을 입력합니다. 이 토큰은 Logz.io UI의 계정 페이지에서 찾을 수 있습니다.

페이지 아래쪽의 약관에 동의하고, **구매**를 선택합니다. 그러면 Azure에서 템플릿을 배포하며, 1~2분 정도 걸릴 수 있습니다. 결국에는 포털의 위쪽에 "배포 성공" 메시지가 표시됩니다.

정의된 리소스 그룹을 방문하여 배포된 리소스를 검토할 수 있습니다.

데이터를 Azure Blob Storage에 백업하도록 logzio-azure-serverless를 구성하는 방법을 알아보려면 [Azure 활동 로그 제공](https://docs.logz.io/shipping/log-sources/azure-activity-logs.html)을 참조하세요.

### <a name="stream-azure-logs-and-metrics-to-logzio"></a>Logz.io로 Azure 로그 및 메트릭 스트림

이제 통합 템플릿을 배포했으므로 진단 데이터를 방금 배포한 Event Hub로 스트림하도록 Azure를 구성해야 합니다. 데이터가 Event Hub에 제공되면 함수 앱에서 해당 데이터를 Logz.io로 전달합니다.

1. 검색 창에서 "진단"을 입력한 다음, **진단 설정**을 선택합니다.

2. 리소스 목록에서 리소스를 선택한 다음, **진단 설정**을 선택하여 해당 리소스에 대한 **진단 설정** 패널을 엽니다.

    ![진단 설정 패널](media/java-get-started-with-logzio/diagnostics-settings.png)

3. **이름**에서 진단 설정 이름을 지정합니다.

4. **이벤트 허브로의 스트림**을 선택한 다음, **구성**을 선택하여 **Event Hub 선택** 패널을 엽니다.

5. Event Hub를 선택합니다.

    * **이벤트 허브 네임스페이스 선택**: **Logzio**로 시작하는 네임스페이스(예: `LogzioNS6nvkqdcci10p`)를 선택합니다.
    * **이벤트 허브 이름 선택**: 로그에 대해 **insights-operational-logs**를 선택하고, 메트릭에 대해 **insights-operational-metrics**를 선택합니다.
    * **이벤트 허브 정책 이름 선택**: **LogzioSharedAccessKey**를 선택합니다.

6. **확인**을 선택하여 **진단 설정** 패널로 돌아갑니다.

7. [로그] 섹션에서 스트림하려는 데이터를 선택한 다음, **저장**을 선택합니다.

이제 선택한 데이터가 Event Hub로 스트림됩니다.

### <a name="visualize-your-data"></a>데이터 시각화

다음으로, 데이터를 시스템에서 Logz.io로 가져올 시간을 지정한 다음, Kibana를 엽니다. 대시보드를 채우는 데이터(_eventhub_ 형식)가 표시됩니다. 대시보드를 만드는 방법에 대한 자세한 내용은 [완전한 Kibana 대시보드 만들기](https://logz.io/blog/perfect-kibana-dashboard/)를 참조하세요.

여기서는 **Discover**(검색) 탭에서 특정 데이터를 쿼리하거나, Kibana 개체를 만들어 **Visualize**(시각화) 탭에서 데이터를 시각화할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 Azure 리소스가 완료되면 다음 명령을 사용하여 해당 리소스를 삭제할 수 있습니다.

```azurecli-interactive
az group delete --name <resource group>
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 로그와 메트릭을 Logz.io에 보내도록 Java 애플리케이션 및 Azure 서비스를 구성하는 방법을 알아보았습니다.

다음으로 Event Hub를 사용하여 애플리케이션을 모니터링하는 방법에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [외부 도구에서 사용할 수 있도록 Azure 모니터링 데이터를 Event Hub로 스트림](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs)