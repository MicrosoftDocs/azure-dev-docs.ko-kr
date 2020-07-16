---
title: Java용 Azure SDK를 사용한 로깅 구성
description: Java용 Azure SDK 클라이언트 라이브러리의 로깅 프레임워크를 구성하는 방법 알아보기
keywords: Azure, Java, SDK, 로깅
author: bmitchell287
ms.author: brendm
ms.date: 03/25/2020
ms.topic: article
ms.service: multiple
ms.custom: devx-track-java
ms.openlocfilehash: 5dbe0235143621587b111f4537a49b36f88115f1
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379447"
---
# <a name="configure-logging-with-the-azure-sdk-for-java"></a>Java용 Azure SDK를 사용한 로깅 구성

이 문서에서는 Java용 [Azure SDK](https://azure.microsoft.com/downloads/)의 로깅 구성 예제를 제공합니다. 구성 옵션(예: 로그 수준 또는 클래스별 사용자 지정 로깅 설정)에 대한 자세한 내용은 선택한 로깅 프레임워크에 대한 설명서를 참조하세요.

Java용 Azure SDK 클라이언트 라이브러리는 SLF4J([Simple Logging Facade for Java](https://www.slf4j.org/))를 사용합니다. SLF4J를 사용하면 애플리케이션을 배포할 때 호출되는 기본 로깅 프레임워크를 사용할 수 있습니다.

> [!NOTE]
> 이 문서는 Azure SDK 클라이언트 라이브러리의 최신 버전에 적용됩니다. 라이브러리가 지원되는지 확인하려면 [Azure SDK 최신 릴리스](https://azure.github.io/azure-sdk/releases/latest/java.html) 목록을 참조하세요. 애플리케이션에서 이전 버전의 Azure SDK 클라이언트 라이브러리를 사용하는 경우 해당 서비스 설명서의 특정 지침을 참조하세요.

## <a name="declare-a-logging-framework"></a>로깅 프레임워크 선언

이러한 로거를 구현하기 전에 프로젝트에서 관련 프레임워크를 종속성으로 선언해야 합니다. 자세한 내용은 [SLF4J 사용 설명서](http://www.slf4j.org/manual.html#projectDep)를 참조하세요.

## <a name="configure-log4j-or-log4j-2"></a>Log4j 또는 Log4j 2 구성

속성 파일 또는 XML 파일에서 Log4j 및 Log4j 2 로깅을 구성할 수 있습니다. Log4j 및 Log4j 2 로깅에 대한 자세한 내용은 [Apache Log4j 2 매뉴얼](https://logging.apache.org/log4j/2.x/manual/configuration.html)을 참조하세요.

### <a name="use-a-properties-file"></a>properties 파일 사용

프로젝트의 *./src/main/resource* 디렉터리에서 *log4j.properties* 또는 *log4j2.properties*(Logj4 2)라는 새 파일을 만듭니다. 시작하려면 다음 예제를 사용하세요.

Log4j 예제:

```properties
log4j.rootLogger=INFO, A1
log4j.appender.A1=org.apache.log4j.ConsoleAppender
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%m%n
log4j.logger.com.azure.core=ERROR
```

Log4j2 예제:

```properties
appender.console.type = Console
appender.console.name = LogToConsole
appender.console.layout.type = PatternLayout
appender.console.layout.pattern = %msg%n
logger.app.name=com.azure.core
logger.app.level=ERROR
```

### <a name="use-an-xml-file"></a>XML 파일 사용

또는 XML 파일을 사용하여 Log4j 및 Log4j2를 구성할 수 있습니다. 프로젝트의 *./src/main/resource* 디렉터리에서 *log4j.xml* 또는 *log4j2.xml*(Logj4 2)이라는 새 파일을 만듭니다. 시작하려면 다음 예제를 사용하세요.

Log4j 예제:

```xml
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration debug="true" xmlns:log4j='http://jakarta.apache.org/log4j/'>

  <appender name="console" class="org.apache.log4j.ConsoleAppender">
    <param name="Target" value="System.out"/>
    <layout class="org.apache.log4j.PatternLayout">
    <param name="ConversionPattern" value="%m%n" />
    </layout>
  </appender>
  <logger name="com.azure.core" additivity="true">
    <level value="ERROR" />
    <appender-ref ref="console" />
  </logger>

  <root>
    <priority value ="info"></priority>
    <appender-ref ref="console"></appender>
  </root>

</log4j:configuration>
```

Log4j2 예제:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="INFO">
    <Appenders>
        <Console name="console" target="SYSTEM_OUT">
            <PatternLayout
                pattern="%msg%n" />
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.azure.core" level="error" additivity="true">
            <appender-ref ref="console" />
        </Logger>
        <Root level="info" additivity="false">
            <appender-ref ref="console" />
        </Root>
     </Loggers>
</Configuration>
```

## <a name="configure-logback"></a>Logback 구성

[Logback](https://logback.qos.ch/manual/introduction.html)은 인기 있는 로깅 프레임워크 중 하나이며 SLF4J의 네이티브 구현입니다. Logback을 구성하려면 프로젝트의 *./src/main/resources* 디렉터리에 *logback.xml*이라는 XML 파일을 만드세요. 구성 옵션에 대한 자세한 내용은 [Logback 프로젝트 웹 사이트](https://logback.qos.ch/manual/configuration.html)에서 확인할 수 있습니다.

Logback 구성의 예는 다음과 같습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%message%n</pattern>
    </encoder>
  </appender>

  <logger name="com.azure.core" level="ERROR" />

  <root level="INFO">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```

콘솔에 로깅하는 경우의 간단한 Logback 구성은 다음과 같습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <appender name="Console"
    class="ch.qos.logback.core.ConsoleAppender">
    <layout class="ch.qos.logback.classic.PatternLayout">
      <Pattern>
        %black(%d{ISO8601}) %highlight(%-5level) [%blue(%t)] %blue(%logger{100}): %msg%n%throwable
      </Pattern>
    </layout>
  </appender>

  <root level="INFO">
    <appender-ref ref="Console" />
  </root>
</configuration>
```

한 시간마다 롤오버되고 GZIP 파일 형식으로 보관되는 파일에 로깅하는 구성은 다음과 같습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <property name="LOGS" value="./logs" />
  <appender name="RollingFile" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>${LOGS}/spring-boot-logger.log</file>
    <encoder
      class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
      <Pattern>%d %p %C{1.} [%t] %m%n</Pattern>
    </encoder>

    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
      <!-- rollover hourly and gzip logs -->
      <fileNamePattern>${LOGS}/archived/spring-boot-logger-%d{yyyy-MM-dd-HH}.log.gz</fileNamePattern>
    </rollingPolicy>
  </appender>

  <!-- LOG everything at INFO level -->
  <root level="info">
    <appender-ref ref="RollingFile" />
  </root>
</configuration>
```

### <a name="configure-logback-for-a-spring-boot-application"></a>Spring Boot 애플리케이션의 Logback 구성

Spring은 *./src/main/resources* 디렉터리에 있는 *application.properties* 파일에서 로깅을 포함한 프로젝트 구성을 찾습니다. *application.properties* 파일에서 *logback.xml*을 Spring Boot 애플리케이션에 연결하는 다음 줄을 추가하세요.

```properties
logging.config=classpath:logback.xml
```

## <a name="configure-fallback-logging-for-temporary-debugging"></a>임시 디버깅을 위한 대체 로깅 구성

SLF4J 로거를 사용하여 애플리케이션을 다시 배포할 수 없는 시나리오에서는 Azure Core 1.3.0 이상에서 Java용 Azure 클라이언트 라이브러리에 기본 제공되는 대체 로거를 사용할 수 있습니다. 이 로거를 사용하도록 설정하려면 먼저 SLF4J 로거가 없음을 확인한 다음(이 로거가 우선하므로), `AZURE_LOG_LEVEL` 환경 변수를 설정해야 합니다. 환경 변수를 설정한 후에는 애플리케이션을 다시 시작하여 로그 생성을 시작해야 합니다.

다음 표에서는 이 환경 변수에 허용되는 값을 보여줍니다.

|로그 수준   |허용되는 환경 변수 값   |
|----------|-----------|
|VERBOSE   |"verbose", "debug"     |
|INFORMATIONAL|"info", "information", "informational"  |
|WARNING     |"warn", "warning"       |
|오류    |"err", "error"  |

## <a name="next-steps"></a>다음 단계

- [Azure App Service에서 앱에 대한 진단 로깅 사용](/azure/app-service/troubleshoot-diagnostic-logs) 
- [Azure 보안 로깅 및 감사 옵션 검토](/azure/security/fundamentals/log-audit)
- [Azure 플랫폼 로그를 사용하는 방법 알아보기](/azure/azure-monitor/platform/platform-logs-overview)
