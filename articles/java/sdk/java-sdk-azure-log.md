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
ms.openlocfilehash: 927f20601ded9a0ea6b2793ef1b0c8e1b5e6ac19
ms.sourcegitcommit: ae2fa266a36958c04625bb0ab212e6f2db98e026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96857771"
---
# <a name="configure-logging-with-the-azure-sdk-for-java"></a>Java용 Azure SDK를 사용한 로깅 구성

이 문서에서는 Java용 [Azure SDK](https://azure.microsoft.com/downloads/)의 로깅 구성 예제를 제공합니다. 구성 옵션(예: 로그 수준 또는 클래스별 사용자 지정 로깅 설정)에 대한 자세한 내용은 선택한 로깅 프레임워크에 대한 설명서를 참조하세요.

Java용 Azure SDK 클라이언트 라이브러리는 SLF4J([Simple Logging Facade for Java](https://www.slf4j.org/))를 사용합니다. SLF4J를 사용하면 애플리케이션을 배포할 때 호출되는 기본 로깅 프레임워크를 사용할 수 있습니다. 클라이언트 작성기에서 [HttpLogOptions](/java/api/com.azure.core.http.policy.httplogoptions?view=azure-java-stable)를 설정하는 기능을 제공하는 경우 로그를 출력하려면 [HttpLogDetailLevel](/java/api/com.azure.core.http.policy.httplogdetaillevel?view=azure-java-stable) 및 허용되는 모든 헤더와 쿼리 매개 변수도 지정해야 합니다.

> [!NOTE]
> 이 문서는 Azure SDK 클라이언트 라이브러리의 최신 버전에 적용됩니다. 라이브러리가 지원되는지 확인하려면 [Azure SDK 최신 릴리스](https://azure.github.io/azure-sdk/releases/latest/java.html) 목록을 참조하세요. 애플리케이션에서 이전 버전의 Azure SDK 클라이언트 라이브러리를 사용하는 경우 해당 서비스 설명서의 특정 지침을 참조하세요.

## <a name="declare-a-logging-framework"></a>로깅 프레임워크 선언

이러한 로거를 구현하기 전에 프로젝트에서 관련 프레임워크를 종속성으로 선언해야 합니다. 자세한 내용은 [SLF4J 사용자 설명서](https://www.slf4j.org/manual.html#projectDep)를 참조하세요.

다음 섹션에서는 일반적인 로깅 프레임워크에 대한 구성 예제를 제공합니다.

## <a name="use-log4j"></a>Log4j 사용

다음 예제에서는 Log4j 로깅 프레임워크에 대한 구성을 보여 줍니다. 자세한 내용은 [Log4j 설명서](https://logging.apache.org/log4j/1.2/)를 참조하세요.

**Maven 종속성을 추가하여 Log4j 사용**

프로젝트의 *pom.xml* 파일에 다음을 추가합니다.

```xml
<!-- https://mvnrepository.com/artifact/org.slf4j/slf4j-log4j12 -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>[1.0,)</version> <!-- Version number 1.0 and above -->
</dependency>
```

**properties 파일을 사용하여 Log4j 사용**

*log4j.properties* 파일을 프로젝트의 *./src/main/resource* 디렉터리에 만들고, 다음 내용을 추가합니다.

```properties
log4j.rootLogger=INFO, A1
log4j.appender.A1=org.apache.log4j.ConsoleAppender
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%m%n
log4j.logger.com.azure.core=ERROR
```

**XML 파일을 사용하여 Log4j 사용**

*log4j.xml* 파일을 프로젝트의 *./src/main/resource* 디렉터리에 만들고, 다음 내용을 추가합니다.

```xml
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration debug="true" xmlns:log4j='http://jakarta.apache.org/log4j/'>

    <appender name="console" class="org.apache.log4j.ConsoleAppender">
        <param name="Target" value="System.out"/>
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%m%n" />
        </layout>
    </appender>
    <logger name="com.azure.core">
        <level value="ERROR" />
        <appender-ref ref="console" />
    </logger>

    <root>
        <level value="info" />
        <appender-ref ref="console" />
    </root>

</log4j:configuration>
```

## <a name="use-log4j-2"></a>Log4j 2 사용

다음 예제에서는 Log4j 2 로깅 프레임워크에 대한 구성을 보여 줍니다. 자세한 내용은 [Log4j 2 설명서](https://logging.apache.org/log4j/2.x/manual/configuration.html)를 참조하세요.

**Maven 종속성을 추가하여 Log4j 2 사용**

프로젝트의 *pom.xml* 파일에 다음을 추가합니다.

```
<!-- https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-slf4j-impl -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>[2.0,)</version> <!-- Version number 2.0 and above -->
</dependency>
```

**properties 파일을 사용하여 Log4j 2 사용**

*log4j2.properties* 파일을 프로젝트의 *./src/main/resource* 디렉터리에 만들고, 다음 내용을 추가합니다.

```properties
appender.console.type = Console
appender.console.name = STDOUT
appender.console.layout.type = PatternLayout
appender.console.layout.pattern = %msg%n
logger.app.name=com.azure.core
logger.app.level=ERROR

rootLogger.level = info
rootLogger.appenderRefs = stdout
rootLogger.appenderRef.stdout.ref = STDOUT
```

**XML 파일을 사용하여 Log4j 2 사용**

*log4j2.xml* 파일을 프로젝트의 *./src/main/resource* 디렉터리에 만들고, 다음 내용을 추가합니다.

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

## <a name="use-logback"></a>Logback 사용

다음 예제에서는 Logback 로깅 프레임워크에 대한 기본 구성을 보여 줍니다. 자세한 내용은 [Logback 설명서](https://logback.qos.ch/manual/configuration.html)를 참조하세요.

**Maven 종속성을 추가하여 Logback 사용**

프로젝트의 *pom.xml* 파일에 다음을 추가합니다.

```
<!-- https://mvnrepository.com/artifact/ch.qos.logback/logback-classic -->
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>[0.2.5,)</version> <!-- Version number 0.2.5 and above -->
</dependency>
```

**XML 파일을 사용하여 Logback 사용**

*logback.xml* 파일을 프로젝트의 *./src/main/resource* 디렉터리에 만들고, 다음 내용을 추가합니다.

```xml
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

## <a name="use-logback-in-a-spring-boot-application"></a>Spring Boot 애플리케이션에서 Logback 사용

다음 예제에서는 Spring에서 Logback을 사용하기 위한 몇 가지 구성을 보여 줍니다. 일반적으로 로깅 구성이 프로젝트의 *./src/main/resources* 디렉터리에 있는 *logback.xml* 파일에 추가됩니다. Spring은 이 파일에서 로깅을 포함한 다양한 구성을 검토합니다. 자세한 내용은 [Logback 설명서](https://logback.qos.ch/manual/configuration.html)를 참조하세요.

모든 파일에서 Logback 구성을 읽도록 애플리케이션을 구성할 수 있습니다. *logback.xml* 파일을 Spring 애플리케이션에 연결하려면  *application.properties* 파일을 프로젝트의 *./src/main/resources* 디렉터리에 만들고, 다음 내용을 추가합니다.

```properties
logging.config=classpath:logback.xml
```

콘솔에 로깅하기 위한 Logback 구성을 만들려면 *logback.xml* 파일에 다음을 추가합니다.

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

매시간 롤오버되고 gzip 형식으로 보관되는 파일에 대한 로깅을 구성하려면 *logback.xml* 파일에 다음을 추가합니다.

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

## <a name="configure-fallback-logging-for-temporary-debugging"></a>임시 디버깅을 위한 대체 로깅 구성

SLF4J 로거를 사용하여 애플리케이션을 다시 배포할 수 없는 시나리오에서는 Azure Core 1.3.0 이상에서 Java용 Azure 클라이언트 라이브러리에 기본 제공되는 대체 로거를 사용할 수 있습니다. 이 로거를 사용하도록 설정하려면 먼저 SLF4J 로거가 없음을 확인한 다음(이 로거가 우선하므로), `AZURE_LOG_LEVEL` 환경 변수를 설정해야 합니다. 환경 변수를 설정한 후에는 애플리케이션을 다시 시작하여 로그 생성을 시작해야 합니다.

다음 표에서는 이 환경 변수에 허용되는 값을 보여줍니다.

|로그 수준   |허용되는 환경 변수 값   |
|----------|-----------|
|VERBOSE   |"verbose", "debug"     |
|INFORMATIONAL|"info", "information", "informational"  |
|WARNING     |"warn", "warning"       |
|오류    |"err", "error"  |

## <a name="setting-an-httplogdetaillevel"></a>HttpLogDetailLevel 설정
어떤 로깅 메커니즘을 사용하든, 클라이언트 작성기에서 [HttpLogOptions](/java/api/com.azure.core.http.policy.httplogoptions?view=azure-java-stable)를 설정하는 기능을 제공하는 경우 로그를 출력하도록 이러한 옵션을 추가로 구성해야 합니다. 기록할 정보를 나타내도록 [HttpLogDetailLevel](/java/api/com.azure.core.http.policy.httplogdetaillevel?view=azure-java-stable)을 지정해야 합니다.  기본값은 `NONE`이므로 이 값을 지정하지 않으면 로깅 프레임워크 또는 대체 로깅이 올바르게 구성되어도 로그가 출력되지 않습니다. 보안상의 이유로 헤더 및 쿼리 매개 변수는 기본적으로 수정되므로, 인쇄해도 안전한 헤더 및 쿼리 매개 변수를 나타내는 `Set<String>`도 로그 옵션으로 제공되어야 합니다. 이러한 값은 아래와 같이 구성할 수 있습니다. 로깅은 본문 내용과 헤더 값을 모두 인쇄하도록 설정되었으며, `"foo"` 키에 해당하는 사용자 지정 메타데이터 값을 제외한 모든 헤더 값이 수정됩니다. 그리고 존재할 가능성이 있는 서명된 sas 버전을 나타내는 sas 토큰 쿼리 매개 변수 `"sv"`를 제외한 모든 쿼리 매개 변수가 수정됩니다. 

```
new BlobClientBuilder().endpoint(<endpoint>)
            .httpLogOptions(new HttpLogOptions()
                .setLogLevel(HttpLogDetailLevel.BODY_AND_HEADERS)
                .setAllowedHeaderNames(Set.of("x-ms-meta-foo"))
                .setAllowedQueryParamNames(Set.of("sv")))
            .buildClient();
```
> [!NOTE]
> 이 예제에서는 Storage 클라이언트 작성기를 사용하지만 `HttpLogOptions`를 허용하는 원칙이 모든 작성기에 적용됩니다. 또한 이 예제에서는 클라이언트의 전체 구성을 보여주지 않고 로깅 구성만 보여줍니다. 클라이언트 구성에 대한 자세한 내용은 해당 작성기의 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure App Service에서 앱에 대한 진단 로깅 사용](/azure/app-service/troubleshoot-diagnostic-logs) 
- [Azure 보안 로깅 및 감사 옵션 검토](/azure/security/fundamentals/log-audit)
- [Azure 플랫폼 로그를 사용하는 방법 알아보기](/azure/azure-monitor/platform/platform-logs-overview)
