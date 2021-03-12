---
title: Java용 Azure SDK 및 Logback을 사용하여 로그
description: Java용 Azure SDK와 Logback 통합 소개
author: srnagar
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: srnagar
ms.openlocfilehash: 785bb65a1a6f55314246d4c1410891717f8bbd6f
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118161"
---
# <a name="log-with-the-azure-sdk-for-java-and-logback"></a>Java용 Azure SDK 및 Logback을 사용하여 로그

이 문서에서는 Java용 Azure SDK를 사용하는 애플리케이션에 Logback을 사용하여 로깅을 추가하는 방법을 소개합니다. [Java용 Azure SDK에서 로깅 구성](logging-overview.md)에 설명된 것처럼 모든 Azure 클라이언트 라이브러리는 [SLF4J](http://www.slf4j.org/)를 통해 로깅하므로 [Logback](http://logback.qos.ch/)과 같은 로깅 프레임워크를 사용할 수 있습니다.

Logback 로깅을 사용하도록 설정하려면 다음 두 가지를 수행해야 합니다.

1. Logback 라이브러리를 종속성으로 포함합니다.
2. */src/main/resources* 프로젝트 디렉터리에 *logback.xml* 이라는 파일을 만듭니다.

Logback 구성과 관련된 자세한 내용은 Logback 설명서의 [Logback 구성](http://logback.qos.ch/manual/configuration.html)을 참조하세요.

## <a name="add-the-maven-dependency"></a>Maven 종속성 추가

Maven 종속성을 추가하려면 프로젝트의 *pom.xml* 파일에 다음 XML을 포함합니다. *1.2.3* 버전 번호를 [Logback 클래식 모듈 페이지](https://mvnrepository.com/artifact/ch.qos.logback/logback-classic)에 표시된 최신 릴리스 버전 번호로 바꿉니다.

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

## <a name="add-logbackxml-to-your-project"></a>프로젝트에 logback.xml 추가

[Logback](https://logback.qos.ch/manual/introduction.html)은 주요 로깅 프레임워크 중 하나입니다. Logback 로깅을 사용하도록 구성하려면 프로젝트의 *./src/main/resources* 디렉터리에 *logback.xml* 이라는 파일을 만듭니다. 이 파일에는 로깅 요구를 사용자 지정할 수 있는 로깅 구성이 포함됩니다. *logback.xml* 구성에 대한 자세한 내용은 Logback 설명서의 [Logback 구성](https://logback.qos.ch/manual/configuration.html)을 참조하세요.

### <a name="console-logging"></a>콘솔 로깅

다음 예와 같이 콘솔에 로그하도록 Logback 구성을 만들 수 있습니다. 이 예는 INFO 수준 이상의 모든 로깅 이벤트가 발생하는 위치에 관계없이 기록하도록 구성되었습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <layout class="ch.qos.logback.classic.PatternLayout">
      <Pattern>
        %black(%d{ISO8601}) %highlight(%-5level) [%blue(%t)] %blue(%logger{100}): %msg%n%throwable
      </Pattern>
    </layout>
  </appender>

  <root level="INFO">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```

### <a name="log-azure-core-errors"></a>Azure 핵심 오류 로그

아래 예제 구성은 이전 구성과 비슷하지만, 모든 `com.azure.core` 패키지 클래스(하위 패키지 포함)에서 로깅이 제공되는 수준을 낮춥니다. 이러한 방식으로 정보 수준 이상의 모든 항목이 로깅되며, 오류 수준 이상만 로깅되는 `com.azure.core`는 예외입니다. 예를 들어 `com.azure.core`의 코드가 너무 복잡한 경우 이 방법을 사용할 수 있습니다. 이러한 종류의 구성은 두 가지 방법으로 모두 사용할 수 있습니다. 예를 들어 `com.azure.core`의 클래스에서 더 많은 디버그 정보를 가져오려면 이 설정을 DEBUG로 변경하면 됩니다.

특정 클래스 또는 특정 패키지의 로깅을 세밀하게 제어할 수 있습니다. 아래와 같이 `com.azure.core`는 모든 핵심 클래스의 출력을 제어하지만, `com.azure.security.keyvault` 또는 이와 동등한 기능을 같은 방법으로 사용하여 실행 중인 애플리케이션의 컨텍스트에서 가장 많은 정보를 제공하는 상황에 적합하도록 출력을 제어할 수 있습니다.

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

### <a name="log-to-a-file-with-log-rotation-enabled"></a>로그 순환을 사용하는 파일에 로그

위의 예는 콘솔에 로깅하는데, 콘솔은 일반적으로 로그에 권장되는 위치가 아닙니다. 콘솔 대신 매시간 롤오버하고 gzip 형식으로 보관하는 파일에 로그하도록 다음 구성을 사용하세요.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <property name="LOGS" value="./logs" />
  <appender name="RollingFile" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>${LOGS}/spring-boot-logger.log</file>
    <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
      <Pattern>%d %p %C{1.} [%t] %m%n</Pattern>
    </encoder>

    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
      <!-- rollover hourly and gzip logs -->
      <fileNamePattern>${LOGS}/archived/spring-boot-logger-%d{yyyy-MM-dd-HH}.log.gz</fileNamePattern>
    </rollingPolicy>
  </appender>

  <!-- LOG everything at INFO level -->
  <root level="INFO">
    <appender-ref ref="RollingFile" />
  </root>
</configuration>
```

### <a name="spring-applications"></a>Spring 애플리케이션

Spring 프레임워크는 로깅 구성을 비롯한 다양한 구성을 위한 Spring *application.properties* 파일을 읽어서 작동합니다. 하지만 원하는 파일에서 Logback 구성을 읽도록 Spring 애플리케이션을 구성할 수 있습니다. 이렇게 하려면 다음 줄을 Spring */src/main/resources/application.properties* 파일에 추가하여 *logback.xml* 구성 파일을 가리키도록 `logging.config` 속성을 구성합니다.

```properties
logging.config=classpath:logback.xml
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Logback 구성 및 Java용 Azure SDK에서 Logback을 로깅에 사용하도록 설정하는 방법을 알아보았습니다. Java용 Azure SDK는 모든 SLF4J 로깅 프레임워크에서 작동하므로 [SLF4J 사용자 설명서](http://www.slf4j.org/manual.html)에서 자세한 내용을 검토하는 것이 좋습니다. Logback을 사용하는 경우 웹 사이트에서 방대한 분량의 구성 지침을 찾을 수 있습니다. 자세한 내용은 Logback 설명서의 [Logback 구성](http://logback.qos.ch/manual/configuration.html)을 참조하세요.

로깅을 익힌 후에는 Azure가 [Spring](../spring-framework/spring-boot-starters-for-azure.md) 및 [MicroProfile](../eclipse-microprofile/index.yml)과 같은 프레임워크에서 제공하는 통합을 살펴보는 것이 좋습니다.