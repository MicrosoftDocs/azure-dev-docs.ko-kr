---
title: Spring Boot Starter를 Azure Service Bus JMS에 사용하는 방법
description: 이 문서에서는 Spring JMS Starter를 사용하여 Azure Service Bus에서 메시지를 보내고 받는 방법을 설명합니다.
author: seanli1988
manager: kyliel
ms.author: seal
ms.date: 08/21/2019
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 1d849ed17a2201be1595b6bc80e613691ac778c8
ms.sourcegitcommit: e1175aa94709b14b283645986a34a385999fb3f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93192455"
---
# <a name="how-to-use-the-spring-boot-starter-for-azure-service-bus-jms"></a>Spring Boot Starter를 Azure Service Bus JMS에 사용하는 방법

[!INCLUDE [spring-boot-20-note.md](includes/spring-boot-20-note.md)]

Azure는 [AMQP 1.0](http://www.amqp.org/)("고급 메시지 큐 프로토콜 1.0") 표준을 기반으로 하는 [Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview)("Azure Service Bus")라는 비동기 메시징 플랫폼을 제공합니다. Service Bus는 지원되는 Azure 플랫폼 범위에서 사용할 수 있습니다.

Azure Service Bus JMS용 Spring Boot Starter는 Spring과 Service Bus를 통합합니다.

이 문서에서는 Azure Service Bus JMS용 Spring Boot Starter를 사용하여 Service Bus `queues` 및 `topics`에서 메시지를 보내고 받는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에는 다음 필수 구성 요소가 필요합니다.

1. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)을 활성화하거나 [무료 계정](https://azure.microsoft.com/free/)에 등록할 수 있습니다.

1. 지원되는 JDK(Java Development Kit) 버전 8 이상. Azure에서 개발하는 경우 사용할 수 있는 JDK에 대한 자세한 내용은 <https://aka.ms/azure-jdks>를 참조하세요.

1. [Apache Maven](http://maven.apache.org/) 버전 3.2 이상.

1. 이미 구성된 Service Bus 큐 또는 토픽이 있는 경우 Service Bus 네임스페이스에서 다음 요구 사항을 충족하는지 확인합니다.

    1. 모든 네트워크에서 액세스 허용
    1. 프리미엄 이상
    1. 큐 및 토픽에 대한 읽기/쓰기 액세스 권한이 있는 액세스 정책 사용

1. 구성된 Service Bus 큐 또는 토픽이 없는 경우 Azure Portal을 사용하여 [Service Bus 큐를 만들거나](/azure/service-bus-messaging/service-bus-quickstart-portal)[Service Bus 토픽을 만듭니다](/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal). 네임스페이스가 이전 단계에서 지정된 요구 사항을 충족하는지 확인합니다. 또한 이 자습서의 테스트 앱에 필요한 네임스페이스의 연결 문자열을 적어둡니다.

1. Spring Boot 애플리케이션이 없는 경우 [Spring Initializer를 사용하여 **Maven** 프로젝트를 만듭니다](https://start.spring.io/). **Maven Project** (Maven 프로젝트)를 선택하고, **Dependencies** (종속성) 아래에서 **Web** (웹) 종속성을 추가해야 합니다.

## <a name="use-the-azure-service-bus-jms-starter"></a>Azure Service Bus JMS 스타터 사용

1. 앱의 부모 디렉터리에서 *pom.xml* 파일을 찾습니다. 예를 들어 다음과 같습니다.

    `C:\SpringBoot\servicebus\pom.xml`

    또는

    `/users/example/home/servicebus/pom.xml`

1. 텍스트 편집기에서 *pom.xml* 파일을 엽니다.

1. Spring Boot Azure Service Bus JMS 스타터를 `<dependencies>` 목록에 추가합니다.

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms-spring-boot-starter</artifactId>
        <version>2.1.7</version>
    </dependency>
    ```

    ![dependency 섹션을 pom.xml 파일에 추가합니다.](media/configure-spring-boot-starter-java-app-with-azure-service-bus/add-dependency-section-new.png)

1. *pom.xml* 파일을 저장하고 닫습니다.

## <a name="configure-the-app-for-your-service-bus"></a>Service Bus용 앱 구성 

이 섹션에서는 Service Bus 큐 또는 토픽을 사용하도록 앱을 구성하는 방법을 보여 줍니다.

### <a name="use-a-service-bus-queue"></a>Service Bus 큐 사용

1. 앱의 *리소스* 디렉터리에서 *application.properties* 파일을 찾습니다.

    `C:\SpringBoot\servicebus\application.properties`

    또는

    `/users/example/home/servicebus/application.properties`

1. 텍스트 편집기에서 *application.properties* 파일을 엽니다.

1. 다음 코드를 *application.properties* 파일의 끝에 추가합니다. 자리 표시자 값을 적절한 서비스 버스 값으로 바꿉니다. 값을 따옴표로 묶지 마세요.

    ```yml
    spring.jms.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.jms.servicebus.idle-timeout=<IdleTimeout>
    ```

    **필드 설명**

    | 필드                                     | Description                                                                                     |
    |-------------------------------------------|-------------------------------------------------------------------------------------------------|
    | `spring.jms.servicebus.connection-string` | Azure Portal의 Service Bus 네임스페이스에서 가져온 연결 문자열을 지정합니다. |
    | `spring.jms.servicebus.idle-timeout`      | 유휴 시간 제한을 밀리초 단위로 지정합니다. 이 자습서의 추천 값은 1,800,000입니다.   |

1. *application.properties* 파일을 저장하고 닫습니다.

### <a name="use-service-bus-topic"></a>Service Bus 토픽 사용

1. 앱의 *리소스* 디렉터리에서 *application.properties* 파일을 찾습니다.

    `C:\SpringBoot\servicebus\application.properties`

    또는

    `/users/example/home/servicebus/application.properties`

1. 텍스트 편집기에서 *application.properties* 파일을 엽니다.

1. 다음 코드를 *application.properties* 파일의 끝에 추가합니다. 자리 표시자 값을 적절한 서비스 버스 값으로 바꿉니다. 값을 따옴표로 묶지 마세요.

    ```yml
    spring.jms.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.jms.servicebus.topic-client-id=<ServiceBusSubscriptionID>
    spring.jms.servicebus.idle-timeout=<IdleTimeout>
    ```

    **필드 설명**

    | 필드                                     | Description                                                                                       |
    |-------------------------------------------|---------------------------------------------------------------------------------------------------|
    | `spring.jms.servicebus.connection-string` | Azure Portal의 Service Bus 네임스페이스에서 가져온 연결 문자열을 지정합니다.   |
    | `spring.jms.servicebus.topic-client-id`   | JMS 클라이언트 ID를 지정합니다. 이 ID는 Azure Portal에서 Service Bus 구독 ID입니다.                | 
    | `spring.jms.servicebus.idle-timeout`      | 유휴 시간 제한을 밀리초 단위로 지정합니다. 이 자습서의 추천 값은 1,800,000입니다.     |

1. *application.properties* 파일을 저장하고 닫습니다.

## <a name="implement-basic-service-bus-functionality"></a>기본 Service Bus 기능 구현

이 섹션에서는 Service Bus 큐 또는 토픽에 메시지를 보내고, 해당 큐 또는 토픽 구독에서 메시지를 받는 데 필요한 Java 클래스를 만듭니다.

### <a name="modify-the-main-application-class"></a>기본 애플리케이션 클래스 수정

1. 앱의 패키지 디렉터리에서 기본 애플리케이션 Java 파일을 찾습니다. 예:

    `C:\SpringBoot\servicebus\src\main\java\com\wingtiptoys\servicebus\ServiceBusJmsStarterApplication.java`

    또는

    `/users/example/home/servicebus/src/main/java/com/wingtiptoys/servicebus/ServiceBusJmsStarterApplication.java`

1. 텍스트 편집기에서 기본 애플리케이션 Java 파일을 엽니다.

1. 파일에 다음 코드를 추가합니다.

   ```java
    package com.wingtiptoys.servicebus;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    public class ServiceBusJmsStarterApplication {

        public static void main(String[] args) {
            SpringApplication.run(ServiceBusJmsStarterApplication.class, args);
        }
    }
    ```

1. 파일을 저장하고 닫습니다.

### <a name="define-a-test-java-class"></a>테스트 Java 클래스 정의

1. 텍스트 편집기를 사용하여 *User.java* 라는 Java 파일을 앱의 패키지 디렉터리에 만듭니다.

1. 사용자 이름을 저장하고 검색하는 일반 사용자 클래스를 정의합니다.

    ```java
    package com.wingtiptoys.servicebus;

    import java.io.Serializable;

    // Define a generic User class.
    public class User implements Serializable {

        private static final long serialVersionUID = -295422703255886286L;

        private String name;

        public User() {
        }

        public User(String name) {
            setName(name);
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

    }
    ```

    `Serializable`은 Spring 프레임워크에서 `JmsTemplate`의 `send` 메서드를 사용하도록 구현되었습니다. 그렇지 않으면 사용자 지정 `MessageConverter` 빈을 정의하여 json에서 콘텐츠를 텍스트 형식으로 직렬화해야 합니다. `MessageConverter`에 대한 자세한 내용은 공식 [Spring JMS 스타터 프로젝트](https://spring.io/guides/gs/messaging-jms/)를 참조하세요.

1. *User.java* 파일을 저장하고 닫습니다.

### <a name="create-a-new-class-for-the-message-send-controller"></a>메시지 송신 컨트롤러에 대한 새 클래스 만들기

1. 텍스트 편집기를 사용하여 *SendController.java* 라는 Java 파일을 앱의 패키지 디렉터리에 만듭니다.

1. 다음 코드를 새 파일에 추가합니다.

    ```java
    package com.wingtiptoys.servicebus;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.jms.core.JmsTemplate;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.RequestParam;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class SendController {

        private static final String DESTINATION_NAME = "<DestinationName>";

        private static final Logger logger = LoggerFactory.getLogger(SendController.class);

        @Autowired
        private JmsTemplate jmsTemplate;

        @PostMapping("/messages")
        public String postMessage(@RequestParam String message) {
            logger.info("Sending message");
            jmsTemplate.convertAndSend(DESTINATION_NAME, new User(message));
            return message;
        }
    }
    ```

    > [!NOTE]
    > `<DestinationName>`을 Service Bus 네임스페이스에 구성된 사용자 고유의 큐 이름 또는 토픽 이름으로 바꿉니다.

1. *SendController.java* 를 저장하고 닫습니다.

### <a name="create-a-class-for-the-message-receive-controller"></a>메시지 수신 컨트롤러에 대한 클래스 만들기

#### <a name="receive-messages-from-a-service-bus-queue"></a>Service Bus 큐에서 메시지 받기

1. 텍스트 편집기를 사용하여 *QueueReceiveController.java* 라는 Java 파일을 앱의 패키지 디렉터리에 만듭니다.

1. 다음 코드를 새 파일에 추가합니다.

    ```java
    package com.wingtiptoys.servicebus;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.jms.annotation.JmsListener;
    import org.springframework.stereotype.Component;

    @Component
    public class QueueReceiveController {

        private static final String QUEUE_NAME = "<ServiceBusQueueName>";

        private final Logger logger = LoggerFactory.getLogger(QueueReceiveController.class);

        @JmsListener(destination = QUEUE_NAME, containerFactory = "jmsListenerContainerFactory")
        public void receiveMessage(User user) {
            logger.info("Received message: {}", user.getName());
        }
    }
    ```

    > [!NOTE]
    > `<ServiceBusQueueName>`을 Service Bus 네임스페이스에 구성된 사용자 고유의 큐 이름으로 바꿉니다.

1. *QueueReceiveController.java* 파일을 저장하고 닫습니다.

#### <a name="receive-messages-from-a-service-bus-subscription"></a>Service Bus 구독에서 메시지 받기

1. 텍스트 편집기를 사용하여 *TopicReceiveController.java* 라는 Java 파일을 앱의 패키지 디렉터리에 만듭니다. 

1. 다음 코드를 새 파일에 추가합니다. `<ServiceBusTopicName>` 자리 표시자를 Service Bus 네임스페이스에 구성된 사용자 고유의 토픽 이름으로 바꿉니다. `<ServiceBusSubscriptionName>` 자리 표시자를 Service Bus 토픽에 대한 사용자 고유의 구독 이름으로 바꿉니다.

    ```java
    package com.wingtiptoys.servicebus;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.jms.annotation.JmsListener;
    import org.springframework.stereotype.Component;

    @Component
    public class TopicReceiveController {

        private static final String TOPIC_NAME = "<ServiceBusTopicName>";

        private static final String SUBSCRIPTION_NAME = "<ServiceBusSubscriptionName>";

        private final Logger logger = LoggerFactory.getLogger(TopicReceiveController.class);

        @JmsListener(destination = TOPIC_NAME, containerFactory = "topicJmsListenerContainerFactory",
                subscription = SUBSCRIPTION_NAME)
        public void receiveMessage(User user) {
            logger.info("Received message: {}", user.getName());
        }
    }
    ```

1. *TopicReceiveController.java* 파일을 저장하고 닫습니다.

## <a name="build-and-test-your-application"></a>애플리케이션 빌드 및 테스트

1. 명령 프롬프트를 열고, 디렉터리를 *pom.xml* 의 위치로 변경합니다. 예를 들어 다음과 같습니다.

    `cd C:\SpringBoot\servicebus`

    또는

    `cd cd /users/example/home/servicebus`

1. Maven을 사용하여 Spring Boot 애플리케이션을 빌드하고 실행합니다.

    ```shell
    mvn clean spring-boot:run
    ```

1. 애플리케이션이 실행되면 *curl* 을 사용하여 애플리케이션을 테스트할 수 있습니다.

    ```shell
    curl -X POST localhost:8080/messages?message=hello
    ```

    애플리케이션 로그에 "Sending message"(메시지를 보내는 중) 및 "hello"가 게시됩니다.

    ```shell
    [nio-8080-exec-1] com.wingtiptoys.servicebus.SendController : Sending message
    [enerContainer-1] com.wingtiptoys.servicebus.ReceiveController : Received message: hello
    ```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 예기치 않은 요금이 청구되지 않도록 [Azure Portal](https://portal.azure.com/)을 사용하여 이 문서에서 만든 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Service Bus 및 AMQP 1.0과 함께 JMS API를 사용하는 방법](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp)
