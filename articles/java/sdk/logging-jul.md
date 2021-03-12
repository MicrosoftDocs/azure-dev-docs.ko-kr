---
title: Java용 Azure SDK 및 java.util.logging을 사용하여 로그
description: java.util.logging과 Java용 Azure SDK 통합 개요
author: srnagar
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: srnagar
ms.openlocfilehash: ca3a431debec21bad2099371e711c1df73d9b344
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118585"
---
# <a name="log-with-the-azure-sdk-for-java-and-javautillogging"></a>Java용 Azure SDK 및 java.util.logging을 사용하여 로그

이 문서에서는 Java용 Azure SDK를 사용하는 애플리케이션에 java.util.logging을 사용하여 로깅을 추가하는 방법에 대한 개요를 제공합니다. java.util.logging 프레임워크는 JDK의 일부입니다. [Java용 Azure SDK에서 로깅 구성](logging-overview.md)에 설명된 것처럼 모든 Azure 클라이언트 라이브러리는 [SLF4J](http://www.slf4j.org/)를 통해 로그하므로 [java.util.logging](https://docs.oracle.com/javase/8/docs/api/java/util/logging/Logger.html)과 같은 로깅 프레임워크를 사용할 수 있습니다.

java.util.logging을 사용하도록 설정하려면 다음 두 가지를 수행해야 합니다.

1. java.util.logging용 SLF4J 어댑터를 종속성으로 포함합니다.
2. */src/main/resources* 프로젝트 디렉터리에서 *logging.properties* 라는 파일을 만듭니다.

로거 구성과 관련된 자세한 내용은 Oracle 설명서의 [로깅 출력 구성](https://docs.oracle.com/cd/E23549_01/doc.1111/e14568/handler.htm)을 참조하세요.

## <a name="add-the-maven-dependency"></a>Maven 종속성 추가

Maven 종속성을 추가하려면 프로젝트의 *pom.xml* 파일에 다음 XML을 포함합니다. *1.7.30* 버전 번호를 [SLF4J JDK14 Binding 페이지](https://mvnrepository.com/artifact/org.slf4j/slf4j-jdk14)에 표시된 최신 릴리스 버전 번호로 바꿉니다.

```xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-jdk14</artifactId>
    <version>1.7.30</version> <!-- replace this version with the latest available version on Maven central -->
</dependency>
```

## <a name="add-loggingproperties-to-your-project"></a>프로젝트에 logging.properties 추가

`java.util.logging`을 사용하여 로그하려면 프로젝트의 *./src/main/resources* 디렉터리에서 *logging.properties* 라는 파일을 만듭니다. 이 파일에는 로깅 요구를 사용자 지정할 수 있는 로깅 구성이 포함됩니다. 자세한 내용은 [Java 로깅: 구성](http://tutorials.jenkov.com/java-logging/configuration.html)을 참조하세요.

*logging.properties* 이외의 다른 파일 이름을 사용하려면 `java.util.logging.config.file` 시스템 속성을 설정하면 됩니다. 이 속성은 로거 인스턴스를 만들기 전에 설정해야 합니다.

### <a name="console-logging"></a>콘솔 로깅

다음 예제와 같이 콘솔에 기록할 구성을 만들 수 있습니다. 이 예제는 INFO 수준 이상의 모든 로깅 이벤트가 발생하는 위치에 관계없이 기록하도록 구성됩니다.

```properties
handlers = java.util.logging.ConsoleHandler
.level = INFO

java.util.logging.ConsoleHandler.level = INFO
java.util.logging.ConsoleHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.SimpleFormatter.format=[%1$tF %1$tT] [%4$s] %5$s %n
```

### <a name="log-to-a-file"></a>파일에 기록

이전 예제에서는 일반적으로 로그의 기본 위치가 아닌 콘솔에 기록합니다. 대신 파일에 대한 로깅을 구성하려면 다음 구성을 사용합니다.

```properties
handlers = java.util.logging.FileHandler
.level = INFO

java.util.logging.FileHandler.pattern = %h/myapplication.log
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.level = INFO
```

이 코드는 홈 디렉터리(`%h`)에 *myapplication.log* 라는 파일을 만듭니다. 이 로거는 특정 기간이 지나면 자동 파일 회전을 지원하지 않습니다. 이 기능이 필요한 경우 로그 파일 회전을 관리하는 스케줄러를 작성해야 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 `java.util.logging`의 구성과 Java용 Azure SDK가 로깅에 사용하도록 설정하는 방법을 설명합니다. Java용 Azure SDK는 모든 SLF4J 로깅 프레임워크에서 작동하므로 자세한 내용은 [SLF4J 사용자 설명서](http://www.slf4j.org/manual.html)를 검토하는 것이 좋습니다.

로깅을 익힌 후에는 Azure가 [Spring](../spring-framework/spring-boot-starters-for-azure.md) 및 [MicroProfile](../eclipse-microprofile/index.yml)과 같은 프레임워크에서 제공하는 통합을 살펴보는 것이 좋습니다.