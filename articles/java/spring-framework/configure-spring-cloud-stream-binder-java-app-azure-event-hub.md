---
title: Azure Event Hub를 사용하여 Spring Cloud 스트림 바인더 애플리케이션을 만드는 방법
description: Azure Event Hub와 함께 Spring Boot Initializer로 만든 Java 기반 Spring Cloud Stream Binder애플리케이션을 구성하는 방법을 알아보세요.
services: event-hubs
documentationcenter: java
ms.date: 02/08/2021
ms.service: event-hubs
ms.tgt_pltfrm: na
ms.topic: article
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: d0c87ce32caddc0100b91abd800a18179ba4101e
ms.sourcegitcommit: bccbab4883e6b6b4926fc194c35ad948b11ccc3f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99822723"
---
# <a name="how-to-create-a-spring-cloud-stream-binder-application-with-azure-event-hubs"></a>Azure Event Hub를 사용하여 Spring Cloud 스트림 바인더 애플리케이션을 만드는 방법

이 문서는 Azure Event Hub와 함께 Spring Boot Initializer로 만든 Java 기반 Spring Cloud Stream Binder애플리케이션을 구성하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택]을 활성화하거나 [체험판 Azure 계정]에 등록할 수 있습니다.
* 지원되는 JDK(Java Development Kit) Azure에서 개발하는 경우 사용할 수 있는 JDK에 대한 자세한 내용은 <https://aka.ms/azure-jdks>를 참조하세요.
* [Apache Maven](http://maven.apache.org/), 버전 3.0 이상

> [!IMPORTANT]
> 이 문서의 단계를 완료하려면 Spring Boot 2.2 또는 2.3 버전이 필요합니다.

## <a name="create-an-azure-event-hub-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Event Hub 만들기

다음 절차에서는 Azure 이벤트 허브를 만듭니다.

### <a name="create-an-azure-event-hub-namespace"></a>Event Hub 네임스페이스 만들기

1. <https://portal.azure.com/>에서 Azure Portal을 찾아 로그인합니다.

1. **+ 리소스 만들기** 를 선택한 다음, *Event Hubs* 를 검색합니다.

1. **만들기** 를 선택합니다.

   >[!div class="mx-imgBorder"]
   >![Event Hub 네임스페이스 만들기][IMG01]

1. **네임스페이스 만들기** 페이지에서 다음 정보를 입력합니다.

   * 네임스페이스에 사용하려는 **구독** 을 선택합니다.
   * 네임스페이스에 새 **리소스 그룹** 을 만들지 아니면 기존 리소스 그룹을 선택할지를 지정합니다.
   * 이벤트 허브 네임스페이스에 대한 URI의 일부가 될 고유한 **네임스페이스 이름** 을 입력합니다. 예: *wingtiptoys-space* 를 **네임스페이스 이름** 에 입력한 경우 URI는 `wingtiptoys-space.servicebus.windows.net`입니다.
   * 이벤트 허브 네임 스페이스에 대한 **위치** 를 지정합니다.
   * 가격 책정 계층.
   * 네임스페이스에 **처리량 단위** 를 지정할 수도 있습니다.
   
   >[!div class="mx-imgBorder"]
   >![Azure Event Hub 네임스페이스 옵션을 지정합니다.][IMG02]

1. 위에 나열된 옵션을 지정한 경우 **검토 + 만들기** 를 선택하고, 사양을 검토하고, **만들기** 를 선택하여 네임스페이스를 만듭니다.

## <a name="create-an-azure-event-hub-in-your-namespace"></a>네임스페이스에 Event Hub 만들기

네임스페이스가 배포되면 **리소스로 이동** 을 선택하여 **Event Hubs 네임스페이스** 페이지를 엽니다. 여기서 네임스페이스에 이벤트 허브를 만들 수 있습니다.

1. 이전 섹션에서 만든 네임스페이스로 이동합니다.

1. 상단 메뉴 모음에서 **+ Event Hubs** 를 선택합니다.

1. 이벤트 허브 이름을 지정합니다.

1. **만들기** 를 선택합니다.

   >[!div class="mx-imgBorder"]
   >![이벤트 허브 만들기][IMG05]

### <a name="create-an-azure-storage-account-for-your-event-hub-checkpoints"></a>이벤트 허브 검사점에 대한 Azure Storage 계정 만들기

다음 절차에서는 이벤트 허브 검사점에 대한 스토리지 계정을 만듭니다.

1. <https://portal.azure.com/>에서 Azure Portal로 이동합니다.

1. **+리소스 만들기** 를 선택하고, **스토리지** 를 선택한 다음, **스토리지 계정** 을 선택합니다.

1. **스토리지 계정 만들기** 페이지에서 다음 정보를 입력합니다.

   * 스토리지 계정에 사용하려는 **구독** 을 선택합니다.
   * 스토리지 계정에 새 **리소스 그룹** 을 만들지 아니면 기존 리소스 그룹을 선택할지를 지정합니다.
   * 스토리지 계정의 고유한 **이름** 을 입력합니다.
   * 스토리지 계정의 **위치** 를 지정합니다.

   >[!div class="mx-imgBorder"]
   >![Azure Storage 계정 옵션을 지정합니다.][IMG08]

1. 위에 나열된 옵션을 지정한 경우 **검토 + 만들기** 를 선택하여 스토리지 계정을 만듭니다.

1. 사양을 검토하고 **만들기** 를 선택합니다.  배포에는 몇 분 정도 걸릴 수 있습니다.

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Spring Initializr를 사용하여 간단한 Spring Boot 애플리케이션 만들기

다음 절차에서는 Spring Boot 애플리케이션을 만듭니다.

1. [https://www.microsoft.com]\(<https://start.spring.io/>) 로 이동합니다.

1. 다음 옵션을 지정합니다.

   * **Java** 를 사용하는 **Maven** 프로젝트를 생성합니다.
   * **2.3** 과 동일한 **Spring Boot** 버전을 지정합니다.
   * 애플리케이션에 대한 **그룹** 및 **아티팩트** 이름을 지정합니다.
   * Java 버전에 대해 **8** 을 선택합니다.
   * *Web* 종속성 추가

   >[!div class="mx-imgBorder"]
   >![기본 Spring Initializr 옵션][SI01]

   > [!NOTE]
   > Spring Initializr는 **그룹** 및 **아티팩트** 이름을 사용하여 패키지 이름을 만듭니다(예: *com.contoso.eventhubs.sample*).

1. 위에 나열된 옵션이 지정되면 **생성** 을 선택합니다.

1. 메시지가 표시되면 로컬 컴퓨터의 경로에 프로젝트를 다운로드합니다.

1. 로컬 시스템에서 파일의 압축을 푼 후에 단순한 Spring Boot 애플리케이션을 편집할 준비를 합니다.

## <a name="configure-your-spring-boot-app-to-use-the-azure-event-hub-starter"></a>Azure Event Hub starter를 사용하도록 Spring Boot 앱 구성

1. 앱의 루트 디렉터리에서 *pom.xml* 파일을 찾습니다. 예:

   *C:\SpringBoot\eventhubs-sample\pom.xml*

   또는

   */users/example/home/eventhubs-sample/pom.xml*

1. 텍스트 편집기에서 *pom.xml* 파일을 열고 `<dependencies>` 목록에 Spring Cloud Azure Event Hub Stream Binder starter를 추가합니다.

   ```xml
   <dependency>
     <groupId>com.azure.spring</groupId>
     <artifactId>azure-spring-cloud-stream-binder-eventhubs</artifactId>
     <version>2.1.0</version>
   </dependency>
   ```

1. JDK 버전 9 이상을 사용하는 경우 다음 종속성을 추가합니다.

   ```xml
   <dependency>
       <groupId>javax.xml.bind</groupId>
       <artifactId>jaxb-api</artifactId>
       <version>2.3.1</version>
   </dependency>
   <dependency>
       <groupId>org.glassfish.jaxb</groupId>
       <artifactId>jaxb-runtime</artifactId>
       <version>2.3.1</version>
       <scope>runtime</scope>
   </dependency>
   ```

1. *pom.xml* 파일을 저장하고 닫습니다.

## <a name="configure-your-spring-boot-app-to-use-your-azure-event-hub"></a>Azure Event Hub를 사용하도록 Spring Boot 앱 구성

1. 다음과 같은 앱의 *리소스* 디렉터리에서 *application.yaml* 파일을 찾습니다.

   *C:\SpringBoot\eventhubs-sample\src\main\resources\application.yaml*

   또는

   */users/example/home/eventhubs-sample/src/main/resources/application.yaml*

2. 텍스트 편집기에서 *application.yaml* 파일을 열고, 다음 줄을 추가하고, 샘플 값을 이벤트 허브의 적절한 속성으로 바꿉니다.

   ```yaml
    spring:
      cloud:
        azure:
          eventhub:
            connection-string: [eventhub-namespace-connection-string]
            checkpoint-storage-account: wingtiptoysstorage
            checkpoint-access-key: [checkpoint-access-key]
            checkpoint-container: wingtiptoyscontainer
            
        stream:
          bindings:
            consume-in-0:
              destination: wingtiptoyshub
              group: $Default
            supply-out-0:
              destination: wingtiptoyshub
   
          eventhub:
            bindings:
              consume-in-0:
                consumer:
                  checkpoint-mode: MANUAL
          function:
            definition: consume;supply;
          poller:
            initial-delay: 0
            fixed-delay: 1000
   ```

   위치:

   |                          필드                           |                                                                                   Description                                                                                    |
   |----------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |               `spring.cloud.azure.eventhub.connection-string`                |                                        Azure Portal의 이벤트 허브 네임스페이스에서 가져온 연결 문자열을 지정합니다.                                   |
   |               `spring.cloud.azure.function.definition`                |                                        바인딩에서 노출하는 외부 대상에 바인딩할 기능 빈을 지정합니다.                                   |
   |               `spring.cloud.azure.poller.fixed-delay`                |                                        기본 폴러의 고정 지연 시간을 밀리초 단위로 지정합니다(기본값은 1000L).                                   |
   |               `spring.cloud.azure.poller.initial-delay`                |                                       정기 트리거의 초기 지연 시간을 지정합니다(기본값은 0).                                   |
   |               `spring.cloud.stream.bindings.consume-in-0.destination`                 |                            이 자습서에서 사용한 이벤트 허브를 지정합니다.                         |
   |               `spring.cloud.stream.bindings.consume-in-0.group`                    |                               Event Hubs 인스턴스의 소비자 그룹을 지정합니다.                                |
   |               `spring.cloud.stream.bindings.supply-out-0.destination`                |                             이 자습서에서 사용한 것과 동일한 이벤트 허브를 지정합니다.                        |
   | `spring.cloud.stream.eventhub.bindings.consume-in-0.consumer.checkpoint-mode` |                                                       `MANUAL`을 지정합니다.                                                   |
   |               `spring.cloud.stream.eventhub.checkpoint-access-key` |                                                      스토리지 계정의 액세스 키를 지정합니다.                                                   |
   |               `spring.cloud.stream.eventhub.checkpoint-container` |                                                       스토리지 계정의 컨테이너를 지정합니다.                                                   |
   |               `spring.cloud.stream.eventhub.checkpoint-storage-account` |                                                 이 자습서에서 만든 스토리지 계정을 지정합니다.                                               |

3. *application.yaml* 파일을 저장하고 닫습니다.

## <a name="add-sample-code-to-implement-basic-event-hub-functionality"></a>기본 이벤트 허브 기능을 구현하는 샘플 코드 추가

이 섹션에서는 이벤트를 이벤트 허브에 보내는 데 필요한 Java 클래스를 만듭니다.

### <a name="modify-the-main-application-class"></a>기본 애플리케이션 클래스 수정

1. 앱의 패키지 디렉터리에서 기본 애플리케이션 Java 파일을 찾습니다. 예:

   *C:\SpringBoot\eventhubs-sample\src\main\java\com\contoso\eventhubs\sample\EventhubSampleApplication.java*

   또는

   */users/example/home/eventhubs-sample/src/main/java/com/contoso/eventhubs/sample/EventhubSampleApplication.java*

1. 텍스트 편집기에서 애플리케이션 Java 파일을 열고 다음 줄을 파일에 추가합니다.

   ```java
    package com.contoso.eventhubs.sample;
    
    import com.azure.spring.integration.core.api.reactor.Checkpointer;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.context.annotation.Bean;
    import org.springframework.messaging.Message;
    
    import java.util.function.Consumer;
    
    import static com.azure.spring.integration.core.AzureHeaders.CHECKPOINTER;
    
    @SpringBootApplication
    public class EventhubSampleApplication {
    
        public static final Logger LOGGER = LoggerFactory.getLogger(EventhubSampleApplication.class);
    
        public static void main(String[] args) {
            SpringApplication.run(EventhubSampleApplication.class, args);
        }
    
        @Bean
        public Consumer<Message<String>> consume() {
            return message -> {
                Checkpointer checkpointer = (Checkpointer) message.getHeaders().get(CHECKPOINTER);
                LOGGER.info("New message received: '{}'", message);
                checkpointer.success()
                            .doOnSuccess(success -> LOGGER.info("Message '{}' successfully checkpointed", message))
                            .doOnError(error -> LOGGER.error("Exception: {}", error.getMessage()))
                            .subscribe();
            };
        }
    
    }
   ```

1. 기본 애플리케이션 Java 파일을 저장하고 닫습니다.

### <a name="create-a-new-configuration-class"></a>새 구성 클래스 만들기

1. 앱의 패키지 디렉터리에 *EventProducerConfiguration.java* 라는 새 Java 파일을 만든 다음, 파일 텍스트 편집기에서 해당 파일을 열고 다음 줄을 추가합니다.

    ```java
    package com.contoso.eventhubs.sample;
    
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.messaging.Message;
    import reactor.core.publisher.EmitterProcessor;
    import reactor.core.publisher.Flux;
    
    import java.util.function.Supplier;
    
    @Configuration
    public class EventProducerConfiguration {
    
        private static final Logger LOGGER = LoggerFactory.getLogger(EventProducerConfiguration.class);
    
        @Bean
        public EmitterProcessor<Message<String>> emitter() {
            return EmitterProcessor.create();
        }
    
        @Bean
        public Supplier<Flux<Message<String>>> supply(EmitterProcessor<Message<String>> emitter) {
            return () -> Flux.from(emitter)
                             .doOnNext(m -> LOGGER.info("Manually sending message {}", m))
                             .doOnError(t -> LOGGER.error("Error encountered", t));
        }
    }
    ```
1. *EventProducerConfiguration.java* 파일을 저장하고 닫습니다.

### <a name="create-a-new-controller-class"></a>새 컨트롤러 클래스 만들기

1. 앱의 패키지 디렉터리에 *EventProducerController.java* 라는 새 Java 파일을 만든 다음, 파일 텍스트 편집기에서 해당 파일을 열고 다음 줄을 추가합니다.

   ```java
   package com.contoso.eventhubs.sample;
   
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.http.ResponseEntity;
   import org.springframework.messaging.Message;
   import org.springframework.messaging.support.MessageBuilder;
   import org.springframework.web.bind.annotation.PostMapping;
   import org.springframework.web.bind.annotation.RequestBody;
   import org.springframework.web.bind.annotation.RestController;
   import reactor.core.publisher.EmitterProcessor;
   
   @RestController
   public class EventProducerController {
   
       public static final Logger LOGGER = LoggerFactory.getLogger(EventProducerController.class);
   
       @Autowired
       private EmitterProcessor<Message<String>> emitterProcessor;
   
       @PostMapping("/messages")
       public ResponseEntity<String> sendMessage(@RequestBody String message) {
           LOGGER.info("Going to add message {} to emitter", message);
           emitterProcessor.onNext(MessageBuilder.withPayload(message).build());
           return ResponseEntity.ok("Sent!");
       }
   }
   ```

1. *EventProducerController.java* 파일을 저장하고 닫습니다.

## <a name="build-and-test-your-application"></a>애플리케이션 빌드 및 테스트

애플리케이션을 빌드하고 테스트하려면 다음 절차를 수행합니다.

1. 명령 프롬프트를 열고 디렉터리를 *pom.xml* 파일이 위치한 폴더로 변경합니다. 예:

   ```cmd
    cd C:\SpringBoot\eventhubs-sample
   ```
   또는

   ```bash
   cd /users/example/home/eventhubs-sample
   ```

1. Maven을 사용하여 Spring Boot 애플리케이션을 빌드하고 실행합니다. 예:

   ```bash
   mvn clean package -Dmaven.test.skip=true
   mvn spring-boot:run
   ```

1. 애플리케이션이 실행되면, 애플리케이션을 테스트하기 위해 `curl`을 사용할 수 있습니다. 예:

   ```bash
   curl -X POST -H "Content-Type: text/plain" -d "hello" http://localhost:8080/messages
   ```
   애플리케이션 로그에 "hello"가 표시됩니다. 예를 들면 다음과 같습니다.

   ```output
   2020-09-11 15:11:12.138  INFO 7616 --- [      elastic-4] c.contoso.eventhubs.sample.EventhubSampleApplication  : New message received: 'hello'
   2020-09-11 15:11:12.406  INFO 7616 --- [ctor-http-nio-1] c.contoso.eventhubs.sample.EventhubSampleApplication  : Message 'hello' successfully checkpointed
   ```

## <a name="next-steps"></a>다음 단계

Spring과 Azure에 대한 자세한 사항은 Azure의 Spring 설명서 센터를 참조합니다.

> [!div class="nextstepaction"]
> [Azure의 Spring](./index.yml)

### <a name="additional-resources"></a>추가 리소스

Event Hub 스트림 바인더에 대한 Azure 지원에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Event Hubs란?](/azure/event-hubs/event-hubs-about)

* [Azure Portal을 사용하여 Event Hubs 네임스페이스 및 이벤트 허브 만들기](/azure/event-hubs/event-hubs-create)

* [Azure Event Hub를 사용하여 Apache Kafka에 대한 Spring Boot Starter를 사용하는 방법](configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub.md)

Java와 함께 Azure를 사용하는 방법에 관한 자세한 정보는 [Java 개발자를 위한 Azure]와 [Azure DevOps 및 Java 사용하기]를 참조하세요.

**[Spring Framework]** 는 Java 개발자가 엔터프라이즈 수준의 애플리케이션을 만드는 데 도움이 되는 오픈 소스 솔루션입니다. 해당 플랫폼을 기반으로 하여 빌드되는 인기 있는 프로젝트 중 하나가 [Spring Boot]입니다. 이 프로젝트는 독립 실행형 Java 애플리케이션을 만드는 간단한 방법을 제공합니다. Spring Boot을 시작하는 개발자를 도우려면 <https://github.com/spring-guides/>에서 몇 가지 샘플 Spring Boot 패키지를 사용할 있습니다. 기본 Spring Boot 프로젝트 목록에서 선택하는 것 외에도 **[Spring Initializr]** 를 통해 사용자 지정 Spring Boot 애플리케이션을 만들기 시작하는 개발자에게 도움을 줍니다.

<!-- URL List -->

[체험판 Azure 계정]: https://azure.microsoft.com/pricing/free-trial/
[Java 개발자를 위한 Azure]: ../index.yml
[Azure DevOps 및 Java 사용하기]: /azure/devops/
[MSDN 구독자 혜택]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[IMG01]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-01.png
[IMG02]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-02.png
[IMG05]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-05.png
[IMG08]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-08.png
[SI01]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-project-01.png
