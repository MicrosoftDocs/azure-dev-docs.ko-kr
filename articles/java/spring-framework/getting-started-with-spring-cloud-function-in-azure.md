---
title: Azure에서 Spring Cloud 함수 시작하기
description: Azure에서 Spring Cloud 함수를 사용하는 방법에 대해 알아봅니다.
documentationcenter: java
author: jdubois
manager: brborges
ms.author: judubois
ms.date: 07/17/2019
ms.service: azure-functions
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 5cfdbb3542222a7ff9547b25836237d5d0991ddb
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86378477"
---
# <a name="getting-started-with-spring-cloud-function-in-azure"></a>Azure에서 Spring Cloud 함수 시작하기

이 문서에서는 [Spring Cloud 함수](https://spring.io/projects/spring-cloud-function)를 사용하여 Java 함수를 개발하여 Azure Functions에 게시하는 방법을 안내합니다. 완료되면 함수 코드가 Azure의 [사용 플랜](/azure/azure-functions/functions-scale#consumption-plan)에서 실행되고 HTTP 요청을 사용하여 트리거될 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

Java를 사용하여 함수를 개발하려면 다음을 설치해야 합니다.

- [Java Developer Kit](https://aka.ms/azure-jdks), 버전 8
- [Apache Maven](https://maven.apache.org), 버전 3.0 이상
- [Azure CLI](/cli/azure)
- [Azure Functions Core Tools](/azure/azure-functions/functions-run-local#v2) 버전 2.7.1158 이상

> [!IMPORTANT]
> 이 퀵 스타트를 완료하려면 JAVA_HOME 환경 변수를 JDK 설치 위치로 설정해야 합니다.

## <a name="what-we-are-going-to-build"></a>빌드할 항목

Azure Functions에서 실행되고 Spring Cloud 함수로 구성된 기존 "Hello, World" 함수를 빌드할 예정입니다.

사용자 이름을 포함하는 간단한 `User` JSON 개체를 받고 해당 사용자에게 환영 메시지가 포함된 `Greeting` 개체를 다시 보냅니다.

여기에서 빌드하는 프로젝트는 [https://github.com/Azure-Samples/hello-spring-function-azure](https://github.com/Azure-Samples/hello-spring-function-azure)에서 제공되므로 이 빠른 시작에 자세히 설명된 최종 작업을 확인하려는 경우 해당 샘플 리포지토리를 직접 사용할 수 있습니다.

## <a name="create-a-new-maven-project"></a>새 Maven 프로젝트 만들기

빈 Maven 프로젝트를 만든 후 Spring Cloud 함수 및 Azure Functions로 구성합니다.

빈 폴더에 새 *pom.xml*을 만들고 [https://github.com/Azure-Samples/hello-spring-function-azure/blob/master/pom.xml](https://github.com/Azure-Samples/hello-spring-function-azure/blob/master/pom.xml)의 샘플 프로젝트에서 콘텐츠를 복사하여 붙여넣습니다.

> [!NOTE]
> 이 파일은 Spring Boot 및 Spring Cloud 함수의 Maven 종속성을 사용하며 Spring Boot 및 Azure Functions Maven 플러그인을 구성합니다.

애플리케이션의 몇 가지 속성을 사용자 지정해야 합니다.

- `<functionAppName>`은 Azure 함수의 이름입니다.
- `<functionAppRegion>`은 함수가 배포된 Azure 지역의 이름입니다.
- `<functionResourceGroup>`은 사용 중인 Azure 리소스 그룹의 이름입니다.

이러한 속성은 *pom.xml* 파일의 위쪽에서 직접 변경해야 합니다.

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <azure.functions.maven.plugin.version>1.6.0</azure.functions.maven.plugin.version>
    <functionAppName>my-spring-function</functionAppName>
    <functionAppRegion>westus</functionAppRegion>
    <stagingDirectory>${project.build.directory}/azure-functions/${functionAppName}</stagingDirectory>
    <functionResourceGroup>my-resource-group</functionResourceGroup>
    <start-class>com.example.HelloFunction</start-class>
    <spring.boot.wrapper.version>1.0.25.RELEASE</spring.boot.wrapper.version>
</properties>
```

## <a name="create-azure-configuration-files"></a>Azure 구성 파일 만들기

*src/main/azure* 폴더를 만들고 다음 Azure Functions 구성 파일을 이 폴더에 추가합니다.

*host.json*:

```json
{
  "version": "2.0",
  "functionTimeout": "00:10:00"
}
```

*local.settings.json*:

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "java",
    "AzureWebJobsDashboard": ""
  }
}
```

## <a name="create-domain-objects"></a>도메인 개체 만들기

Azure Functions는 JSON 형식의 개체를 보내고 받을 수 있습니다.
이제 도메인 모델을 나타내는 `User` 및 `Greeting` 개체를 만들어 보겠습니다.
이 빠른 시작을 사용자 지정하고 더 흥미로운 작업을 수행하려는 경우 더 많은 속성을 포함하여 복잡한 개체를 만들 수 있습니다.

*src/main/java/com/example/model* 폴더를 만들고 다음 두 파일을 추가합니다.

*User.java*:

```java
package com.example.model;

public class User {

    public User() {
    }

    public User(String name) {
        this.name = name;
    }

    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

*Greeting.java*:

```java
package com.example.model;

public class Greeting {

    public Greeting() {
    }

    public Greeting(String message) {
        this.message = message;
    }

    private String message;

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }
}
```

## <a name="create-the-spring-boot-application"></a>Spring Boot 애플리케이션 만들기

이 애플리케이션은 모든 비즈니스 논리를 관리하며 전체 Spring Boot 에코시스템에 액세스할 수 있습니다.
따라서 표준 Azure 함수에 비해 두 가지 주요 이점이 있습니다.

- Azure Functions API를 사용하지 않으므로 다른 시스템으로 쉽게 이식할 수 있습니다. 예를 들어 일반적인 Spring Boot 애플리케이션에서 다시 사용할 수 있습니다.
- Spring Boot의 모든 `@Enable` 주석을 사용하여 강력한 새 기능을 쉽게 추가할 수 있습니다.

*src/main/java/com/example* 폴더에서 일반적인 Spring Boot 애플리케이션인 다음 파일을 만듭니다.

*HelloFunction.java*:

```java
package com.example;

import com.example.model.Greeting;
import com.example.model.User;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

import java.util.function.Function;

@SpringBootApplication
public class HelloFunction {

    public static void main(String[] args) throws Exception {
        SpringApplication.run(HelloFunction.class, args);
    }

    @Bean
    public Function<User, Greeting> hello() {
        return user -> new Greeting("Welcome, " + user.getName());
    }
}
```

> [!NOTE] 
> `hello()` 함수는 매우 구체적입니다.
> 
> - 이 빠른 시작에서 사용되는 함수인 `java.util.function.Function`을 반환합니다. 비즈니스 논리를 포함하며 표준 Java API를 사용하여 한 개체를 다른 개체로 변환합니다.
> - `@Bean` 주석이 있으므로 이는 Spring Bean이며 기본적으로 이름은 메서드 중 하나인 `hello`입니다. 이 이름은 다음 섹션에서 만들 Azure Functions 이름과 일치해야 하므로 애플리케이션에서 다른 함수를 만들려는 경우에 중요합니다.

## <a name="create-the-azure-function"></a>Azure 함수 만들기

전체 Azure Functions API의 이점을 활용하기 위해 이제 특정 클래스를 코딩하려고 합니다. 이 클래스는 이전 단계에서 만든 Spring Cloud 함수에 실행을 위임하는 Azure 함수입니다.

*src/main/java/com/example* 폴더에서 다음 Azure 함수를 만듭니다.

*HelloHandler.java*:

```java
package com.example;

import com.example.model.Greeting;
import com.example.model.User;
import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.AuthorizationLevel;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.HttpTrigger;
import org.springframework.cloud.function.adapter.azure.AzureSpringBootRequestHandler;

import java.util.Optional;

public class HelloHandler extends AzureSpringBootRequestHandler<User, Greeting> {

    @FunctionName("hello")
    public HttpResponseMessage execute(
            @HttpTrigger(name = "request", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<User>> request,
            ExecutionContext context) {

        context.getLogger().info("Greeting user name: " + request.getBody().get().getName());
        return request
                .createResponseBuilder(HttpStatus.OK)
                .body(handleRequest(request.getBody().get(), context))
                .header("Content-Type", "application/json")
                .build();
    }
}
```

이 Java 클래스는 다음과 같은 흥미로운 기능을 포함하는 Azure 함수입니다.

- Azure Functions 및 Spring Cloud 함수 간의 링크를 수행하는 `AzureSpringBootRequestHandler`를 확장합니다. 이는 `execute()` 메서드에서 사용되는 `handleRequest()` 메서드를 제공합니다.
- `@FunctionName("hello")` 주석으로 정의된 함수 이름은 이전 단계인 `hello`에서 구성된 Spring Bean과 동일합니다.
- 실제 Azure 함수이므로 여기에서 전체 Azure Functions API를 사용할 수 있습니다.

## <a name="add-unit-tests"></a>단위 테스트 추가

물론 이 단계는 선택 사항이지만 훌륭한 개발자라면 애플리케이션이 제대로 작동하는지 유효성을 검사하기 위해 단위 테스트를 추가해야 합니다.

*src/test/java/com/example* 폴더를 만들고 다음 JUnit 테스트를 추가합니다.

*HelloFunctionTest.java*:

```java
package com.example;

import com.example.model.Greeting;
import com.example.model.User;
import org.junit.Test;
import org.springframework.cloud.function.adapter.azure.AzureSpringBootRequestHandler;

import static org.assertj.core.api.Assertions.assertThat;

public class HelloFunctionTest {

    @Test
    public void test() {
        Greeting result = new HelloFunction().hello().apply(new User("foo"));
        assertThat(result.getMessage()).isEqualTo("Welcome, foo");
    }

    @Test
    public void start() throws Exception {
        AzureSpringBootRequestHandler<User, Greeting> handler = new AzureSpringBootRequestHandler<>(
                HelloFunction.class);
        Greeting result = handler.handleRequest(new User("foo"), null);
        handler.close();
        assertThat(result.getMessage()).isEqualTo("Welcome, foo");
    }
}
```

이제 Maven을 사용하여 Azure 함수를 테스트할 수 있습니다.

```bash
mvn clean test
```

## <a name="run-the-function-locally"></a>로컬에서 함수 실행

Azure Functions에 애플리케이션을 배포하기 전에 먼저 로컬로 테스트하겠습니다.

우선 애플리케이션을 Jar 파일로 패키징해야 합니다.

```bash
mvn package
```

이제 애플리케이션을 패키징했으므로 `azure-functions` Maven 플러그인을 사용하여 실행할 수 있습니다.

```bash
mvn azure-functions:run
```

이제 포트 7071을 사용하여 localhost에서 Azure 함수를 사용할 수 있습니다. JSON 형식의 `User` 개체를 사용해 POST 요청을 전송하여 함수를 테스트할 수 있습니다. 예를 들어 cURL을 사용합니다.

```bash
curl http://localhost:7071/api/hello -d "{\"name\":\"Azure\"}"
```

함수는 여전히 JSON 형식으로 `Greeting` 개체를 사용하여 응답해야 합니다.

```Output
{
  "message": "Welcome, Azure"
}
```

다음은 화면 상단의 cURL 요청 및 하단의 로컬 Azure 함수의 스크린샷입니다.

 ![로컬로 실행되는 Azure 함수][RFL01]

## <a name="deploy-the-function-to-azure-functions"></a>Azure Functions에 함수 배포

이제 프로덕션에 Azure 함수를 게시할 예정입니다. *pom.xml*에 정의한 `<functionAppName>`, `<functionAppRegion>` 및 `<functionResourceGroup>` 속성은 함수를 구성하는 데 사용됩니다.

> [!NOTE]
> Maven 플러그 인은 Azure를 사용하여 인증해야 합니다. Azure CLI가 설치되어 있으면 계속하기 전에 `az login`을 사용합니다.
> 자세한 인증 옵션은 [여기](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)를 확인하세요.

Maven을 실행하여 함수를 자동으로 배포합니다.

```bash
mvn azure-functions:deploy
```

이제 [Azure Portal](https://portal.azure.com)로 이동하여 생성된 `Function App`을 찾습니다.

함수를 클릭합니다.

- 함수 개요에서 함수의 URL을 확인합니다.
- **플랫폼 기능** 탭을 선택하여 **로그 스트리밍** 서비스를 찾은 다음 이 서비스를 선택하여 실행 중인 함수를 확인합니다.

이제 이전 섹션에서와 같이 cURL을 사용하여 실행 중인 함수에 액세스합니다. `your-function-name`을 실제 함수 이름으로 바꿉니다.

```bash
curl https://your-function-name.azurewebsites.net/api/hello -d "{\"name\":\"Azure\"}"
```

이전 섹션에서와 같이 함수는 여전히 JSON 형식으로 `Greeting` 개체를 사용하여 응답해야 합니다.

```Output
{
  "message": "Welcome, Azure"
}
```

축하합니다. Azure Functions에서 Spring Cloud 함수가 실행됩니다.

<!-- IMG List -->

[RFL01]: media/getting-started-with-spring-cloud-function-in-azure/RFL01.png
