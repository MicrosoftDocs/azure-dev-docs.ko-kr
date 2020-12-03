---
title: Spring Cloud Azure Stream Binder를 Azure Service Bus에 사용하는 방법
description: 이 문서에서는 Spring Cloud Stream Binder를 사용하여 Azure Service Bus에서 메시지를 보내고 받는 방법을 설명합니다.
author: seanli1988
manager: kyliel
ms.author: seal
ms.date: 10/10/2020
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 170d3727b661f18252edb39396739e2791101534
ms.sourcegitcommit: 709fa38a137b30184a7397e0bfa348822f3ea0a7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96441799"
---
# <a name="how-to-use-spring-cloud-azure-stream-binder-for-azure-service-bus"></a>Spring Cloud Azure Stream Binder를 Azure Service Bus에 사용하는 방법

[!INCLUDE [spring-boot-20-note.md](includes/spring-boot-20-note.md)]

이 문서에서는 Spring Cloud Stream Binder를 사용하여 Service Bus `queues` 및 `topics`에서 메시지를 보내고 받는 방법을 보여 줍니다.

Azure는 [AMQP 1.0](http://www.amqp.org/)("고급 메시지 큐 프로토콜 1.0") 표준을 기반으로 하는 [Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview)("Azure Service Bus")라는 비동기 메시징 플랫폼을 제공합니다. Service Bus는 지원되는 Azure 플랫폼 범위에서 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에는 다음 필수 구성 요소가 필요합니다.

1. Azure 구독: Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)을 활성화하거나 [체험 계정](https://azure.microsoft.com/free/)에 등록할 수 있습니다.

1. 지원되는 JDK(Java Development Kit) 버전 8 이상. Azure에서 개발하는 경우 사용할 수 있는 JDK에 대한 자세한 내용은 <https://aka.ms/azure-jdks>를 참조하세요.

1. [Apache Maven](http://maven.apache.org/) 버전 3.2 이상.

1. 이미 구성된 Service Bus 큐 또는 토픽이 있는 경우 Service Bus 네임스페이스에서 다음 요구 사항을 충족하는지 확인합니다.

    1. 모든 네트워크에서 액세스 허용
    1. Standard 이상
    1. 큐 및 토픽에 대한 읽기/쓰기 액세스 권한이 있는 액세스 정책 사용

1. 구성된 Service Bus 큐 또는 토픽이 없는 경우 Azure Portal을 사용하여 [Service Bus 큐를 만들거나](/azure/service-bus-messaging/service-bus-quickstart-portal)[Service Bus 토픽을 만듭니다](/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal). 네임스페이스가 이전 단계에서 지정된 요구 사항을 충족하는지 확인합니다. 또한 이 자습서의 테스트 앱에 필요한 네임스페이스의 연결 문자열을 적어둡니다.

1. Spring Boot 애플리케이션이 없는 경우 [Spring Initializer](https://start.spring.io/)를 사용하여 **Maven** 프로젝트를 만듭니다. **Maven 프로젝트** 를 선택하고, **종속성** 아래에서 **웹** 종속성을 추가하고, **8** Java 버전을 선택합니다.


## <a name="use-the-spring-cloud-stream-binder-starter"></a>Spring Cloud Stream Binder 스타터 사용

1. 앱의 부모 디렉터리에서 *pom.xml* 파일을 찾습니다. 예를 들어 다음과 같습니다.

    `C:\SpringBoot\servicebus\pom.xml`

    또는

    `/users/example/home/servicebus/pom.xml`

1. 텍스트 편집기에서 *pom.xml* 파일을 엽니다.

1. Service Bus 큐 또는 토픽을 사용하는지 여부에 따라 다음 코드 블록을 **&lt;dependencies>** 요소 아래에 추가합니다.

    **Azure Service Bus 큐**

    ```xml
    <dependency>
        <groupId>com.azure.spring</groupId>
        <artifactId>azure-spring-cloud-stream-binder-servicebus-queue</artifactId>
        <version>2.0.0-beta.1</version> <!-- {x-version-update;com.azure.spring:azure-spring-cloud-stream-binder-servicebus-queue;current} -->
    </dependency>
    ```

    **Service Bus 토픽**

    ```xml
    <dependency>
        <groupId>com.azure.spring</groupId>
        <artifactId>azure-spring-cloud-stream-binder-servicebus-topic</artifactId>
        <version>2.0.0-beta.1</version> <!-- {x-version-update;com.azure.spring:azure-spring-cloud-stream-binder-servicebus-topic;current} -->
    </dependency>
    ```


1. *pom.xml* 파일을 저장하고 닫습니다.

## <a name="configure-the-app-for-your-service-bus"></a>Service Bus용 앱 구성

연결 문자열 또는 자격 증명 파일을 기반으로 하여 앱을 구성할 수 있습니다. 이 자습서에서는 연결 문자열을 사용합니다. 자격 증명 파일을 사용하는 방법에 대한 자세한 내용은 [Service Bus 큐용 Spring Cloud Azure Stream Binder 코드 샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-cloud-sample-servicebus-queue-binder
) 및 [Service Bus 토픽용 Spring Cloud Azure Stream Binder 코드 샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-cloud-stream-binder-servicebus-topic)을 참조하세요.

1. 앱의 *리소스* 디렉터리에서 *application.properties* 파일을 찾습니다.

   `C:\SpringBoot\servicebus\src\main\resources\application.properties`

   또는

   `/users/example/home/servicebus/src/main/resources/application.properties`

1. 텍스트 편집기에서 *application.properties* 파일을 엽니다.

1. Service Bus 큐 또는 토픽을 사용하는지 여부에 따라 적절한 코드를 *application.properties* 파일의 끝에 추가합니다. [필드 설명 표](#fd)를 사용하여 샘플 값을 Service Bus에 적절한 속성으로 바꿉니다.

    **Service Bus 큐**

    ```yaml
    spring.cloud.azure.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.cloud.stream.bindings.input.destination=examplequeue
    spring.cloud.stream.bindings.output.destination=examplequeue
    spring.cloud.stream.servicebus.queue.bindings.input.consumer.checkpoint-mode=MANUAL
    ```

    **Service Bus 토픽**

    ```yaml
    spring.cloud.azure.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.cloud.stream.bindings.input.destination=exampletopic
    spring.cloud.stream.bindings.input.group=examplesubscription
    spring.cloud.stream.bindings.output.destination=exampletopic
    spring.cloud.stream.servicebus.topic.bindings.input.consumer.checkpoint-mode=MANUAL
    ```

    **<a name="fd">필드 설명</a>**

    |                                        필드                                   |                                                                                   Description                                                                                    |
    |--------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    |               `spring.cloud.azure.servicebus.connection-string`                |                                        Azure Portal의 Service Bus 네임스페이스에서 가져온 연결 문자열을 지정합니다.                                   |
    |               `spring.cloud.stream.bindings.input.destination`                 |                            이 자습서에서 사용한 Service Bus 큐 또는 Service Bus 토픽을 지정합니다.                         |
    |                  `spring.cloud.stream.bindings.input.group`                    |                                            Service Bus 토픽을 사용한 경우 토픽 구독을 지정합니다.                                |
    |               `spring.cloud.stream.bindings.output.destination`                |                               입력 대상에 사용한 것과 동일한 값을 지정합니다.                        |
    | `spring.cloud.stream.servicebus.queue.bindings.input.consumer.checkpoint-mode` |                                                       `MANUAL`을 지정합니다.                                                   |
    | `spring.cloud.stream.servicebus.topic.bindings.input.consumer.checkpoint-mode` |                                                       `MANUAL`을 지정합니다.                                                   |

1. *application.properties* 파일을 저장하고 닫습니다.

## <a name="implement-basic-service-bus-functionality"></a>기본 Service Bus 기능 구현

이 섹션에서는 메시지를 Service Bus에 보내는 데 필요한 Java 클래스를 만듭니다.

### <a name="modify-the-main-application-class"></a>기본 애플리케이션 클래스 수정

1. 앱의 패키지 디렉터리에서 기본 애플리케이션 Java 파일을 찾습니다. 예:

    `C:\SpringBoot\servicebus\src\main\java\com\example\ServiceBusBinderApplication.java`

   또는

   `/users/example/home/servicebus/src/main/java/com/example/ServiceBusBinderApplication.java`

1. 텍스트 편집기에서 기본 애플리케이션 Java 파일을 엽니다.

1. 파일에 다음 코드를 추가합니다.

    ```java
    package com.example;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    public class ServiceBusBinderApplication {

        public static void main(String[] args) {
            SpringApplication.run(ServiceBusBinderApplication.class, args);
        }
    }
    ```

1. 파일을 저장하고 닫습니다.

### <a name="create-a-new-class-for-the-source-connector"></a>원본 커넥터에 대한 새 클래스 만들기

1. 텍스트 편집기를 사용하여 *StreamBinderSource.java* 라는 Java 파일을 앱의 패키지 디렉터리에 만듭니다.

1. 다음 코드를 새 파일에 추가합니다.

    ```java
    package com.example;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.cloud.stream.annotation.EnableBinding;
    import org.springframework.cloud.stream.messaging.Source;
    import org.springframework.messaging.support.GenericMessage;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.RequestParam;
    import org.springframework.web.bind.annotation.RestController;

    @EnableBinding(Source.class)
    @RestController
    public class StreamBinderSource {

        @Autowired
        private Source source;

        @PostMapping("/messages")
        public String postMessage(@RequestParam String message) {
            this.source.output().send(new GenericMessage<>(message));
            return message;
        }
    }
    ```

1. *StreamBinderSources.java* 파일을 저장하고 닫습니다.

### <a name="create-a-new-class-for-the-sink-connector"></a>싱크 커넥터에 대한 새 클래스 만들기

1. 텍스트 편집기를 사용하여 *StreamBinderSink.java* 라는 Java 파일을 앱의 패키지 디렉터리에 만듭니다.

1. 다음 코드 줄을 새 파일에 추가합니다.

    ```java
    package com.example;

    import com.microsoft.azure.spring.integration.core.AzureHeaders;
    import com.microsoft.azure.spring.integration.core.api.Checkpointer;
    import org.springframework.cloud.stream.annotation.EnableBinding;
    import org.springframework.cloud.stream.annotation.StreamListener;
    import org.springframework.cloud.stream.messaging.Sink;
    import org.springframework.messaging.handler.annotation.Header;

    @EnableBinding(Sink.class)
    public class StreamBinderSink {

        @StreamListener(Sink.INPUT)
        public void handleMessage(String message, @Header(AzureHeaders.CHECKPOINTER) Checkpointer checkpointer) {
            System.out.println(String.format("New message received: '%s'", message));
            checkpointer.success().handle((r, ex) -> {
                if (ex == null) {
                    System.out.println(String.format("Message '%s' successfully checkpointed", message));
                }
                return null;
            });
        }
    }
    ```

1. *StreamBinderSink.java* 파일을 저장하고 닫습니다.

## <a name="build-and-test-your-application"></a>애플리케이션 빌드 및 테스트

1. 명령 프롬프트를 엽니다.

1. 디렉터리를 *pom.xml* 파일의 위치로 변경합니다. 예를 들어 다음과 같습니다.

    `cd C:\SpringBoot\servicebus`

    또는

    `cd /users/example/home/servicebus`

2. Maven을 사용하여 Spring Boot 애플리케이션을 빌드하고 실행합니다.

    ```shell
    mvn clean spring-boot:run
    ```

3. 애플리케이션이 실행되면 *curl* 을 사용하여 애플리케이션을 테스트할 수 있습니다.

    ```shell
    curl -X POST localhost:8080/messages?message=hello
    ```

    애플리케이션 로그에 "hello"라는 메시지가 게시됩니다.

    ```shell
    New message received: 'hello'
    Message 'hello' successfully checkpointed
    ```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 예기치 않은 요금이 청구되지 않도록 [Azure Portal](https://portal.azure.com/)을 사용하여 이 문서에서 만든 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure의 Spring](/java/azure/spring-framework)