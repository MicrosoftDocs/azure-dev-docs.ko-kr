---
title: Java 및 Log4j용 Azure SDK를 사용하여 로그
description: log4j와 Java용 Azure SDK 통합 개요
author: srnagar
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: srnagar
ms.openlocfilehash: 0de76274b7f33f724c339eb0137a89d74f3e8678
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118475"
---
# <a name="log-with-the-azure-sdk-for-java-and-log4j"></a>Java 및 Log4j용 Azure SDK를 사용하여 로그

이 문서에서는 Java용 Azure SDK를 사용하는 애플리케이션에 Log4j를 사용하여 로깅을 추가하는 방법에 대한 개요를 제공합니다. [Java용 Azure SDK에서 로깅 구성](logging-overview.md)에 설명된 것처럼 모든 Azure 클라이언트 라이브러리는 [SLF4J](http://www.slf4j.org/)를 통해 로그하므로 [log4j](https://logging.apache.org/log4j/2.x/)와 같은 로깅 프레임워크를 사용할 수 있습니다.

이 문서에서는 Log4J 2.x 릴리스를 사용하는 지침을 제공하지만 Log4J 1.x는 Java용 Azure SDK에서 동일하게 지원됩니다. log4j 로깅을 사용하도록 설정하려면 다음 두 가지를 수행해야 합니다.

1. log4j 라이브러리를 종속성으로 포함합니다.
2. */src/main/resources* 프로젝트 디렉터리 아래에 구성 파일(*log4j2.xml* 또는 *log4j2.xml*)을 만듭니다.

log4j 구성에 대한 자세한 내용은 [Log4j 2 시작](https://logging.apache.org/log4j/2.x/manual/index.html)을 참조하세요.

## <a name="add-the-maven-dependency"></a>Maven 종속성 추가

Maven 종속성을 추가하려면 프로젝트의 *pom.xml* 파일에 다음 XML을 포함합니다. *2.14.0* 버전 번호를 [Apache Log4j SLF4J Binding 페이지](https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-slf4j-impl)에 표시된 최신 릴리스 버전 번호로 바꿉니다.

```xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>2.14.0</version>
</dependency>
```

## <a name="configuring-log4j"></a>Log4j 구성

Log4j를 구성하는 일반적인 방법은 외부 속성 파일 또는 외부 XML 파일을 사용하는 두 가지가 있습니다. 이러한 접근 방식은 아래에 설명되어 있습니다.

### <a name="using-a-property-file"></a>속성 파일 사용

프로젝트의 */src/main/resource* 디렉터리에 *log4j2.properties* 라는 플랫 속성 파일을 배치할 수 있습니다. 이 파일은 다음 형식을 사용해야 합니다.

```properties
appender.console.type = Console
appender.console.name = STDOUT
appender.console.layout.type = PatternLayout
appender.console.layout.pattern = %msg%n

logger.app.name = com.azure.core
logger.app.level = ERROR

rootLogger.level = info
rootLogger.appenderRefs = stdout
rootLogger.appenderRef.stdout.ref = STDOUT
```

### <a name="using-an-xml-file"></a>XML 파일 사용

프로젝트의 */src/main/resource* 디렉터리에 *log4j2.xml* 이라는 XML 파일을 배치할 수 있습니다. 이 파일은 다음 형식을 사용해야 합니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="INFO">
    <Appenders>
        <Console name="console" target="SYSTEM_OUT">
            <PatternLayout pattern="%msg%n" />
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

## <a name="next-steps"></a>다음 단계

이 문서에서는 Log4j의 구성과 Java용 Azure SDK가 로깅에 사용하도록 설정하는 방법을 설명합니다. Java용 Azure SDK는 모든 SLF4J 로깅 프레임워크에서 작동하므로 자세한 내용은 [SLF4J 사용자 설명서](http://www.slf4j.org/manual.html)를 검토하는 것이 좋습니다. Log4j를 사용하는 경우 웹 사이트에도 많은 양의 구성 지침이 제공됩니다. 자세한 내용은 [Log4j 2 시작](https://logging.apache.org/log4j/2.x/manual/index.html)을 참조하세요.

로깅을 익힌 후에는 Azure가 [Spring](../spring-framework/spring-boot-starters-for-azure.md) 및 [MicroProfile](../eclipse-microprofile/index.yml)과 같은 프레임워크에서 제공하는 통합을 살펴보는 것이 좋습니다.