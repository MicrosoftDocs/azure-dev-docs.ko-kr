---
title: Azure Event Hub를 사용하여 Spring Cloud 스트림 바인더 애플리케이션을 만드는 방법
description: Azure Event Hub와 함께 Spring Boot Initializer로 만든 Java 기반 Spring Cloud Stream Binder애플리케이션을 구성하는 방법을 알아보세요.
services: event-hubs
documentationcenter: java
ms.date: 12/19/2018
ms.service: event-hubs
ms.tgt_pltfrm: na
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: ff068c48a36aa746de1dac23861d453e0c6ff512
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379197"
---
# <a name="how-to-create-a-spring-cloud-stream-binder-application-with-azure-event-hubs"></a>Azure Event Hub를 사용하여 Spring Cloud 스트림 바인더 애플리케이션을 만드는 방법

이 문서는 Azure Event Hub와 함께 Spring Boot Initializer로 만든 Java 기반 Spring Cloud Stream Binder애플리케이션을 구성하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 수행하기 위해 다음 필수 구성 요소가 필요합니다.

* Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택]을 활성화하거나 [체험판 Azure 계정]에 등록할 수 있습니다.
* 지원되는 JDK(Java Development Kit) Azure에서 개발하는 경우 사용할 수 있는 JDK에 대한 자세한 내용은 <https://aka.ms/azure-jdks>를 참조하세요.
* [Apache Maven](http://maven.apache.org/), 버전 3.0 이상

> [!IMPORTANT]
>
> 이 문서의 단계를 완료하려면 Spring Boot 버전 2.0 이상이 필요합니다.
>

## <a name="create-an-azure-event-hub-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Event Hub 만들기

다음 절차에서는 Azure 이벤트 허브를 만듭니다.

### <a name="create-an-azure-event-hub-namespace"></a>Event Hub 네임스페이스 만들기

1. <https://portal.azure.com/>에서 Azure Portal을 찾아 로그인합니다.

1. **+ 리소스 만들기**를 클릭한 다음 *Event Hubs**를 검색합니다.

1. **만들기**를 클릭합니다.

   ![Event Hub 네임스페이스 만들기][IMG01]

1. **네임스페이스 만들기** 페이지에서 다음 정보를 입력합니다.

   * 이벤트 허브 네임스페이스에 대한 URI의 일부가 되는 고유한 **이름**을 입력합니다. 예: **wingtiptoys**를 **이름**에 입력한 경우 URI는 *wingtiptoys.servicebus.windows.net*입니다.
   * 가격 책정 계층.
   * 네임스페이스에 사용하려는 **구독**을 선택합니다.
   * 네임스페이스에 새 **리소스 그룹**을 만들지 아니면 기존 리소스 그룹을 선택할지를 지정합니다.
   * 이벤트 허브 네임 스페이스에 대한 **위치**를 지정합니다.
   * 네임스페이스에 **처리량 단위**를 지정할 수도 있습니다.

   ![Azure Event Hub 네임스페이스 옵션을 지정합니다.][IMG02]

1. 위에 열거된 이러한 옵션을 지정한 경우 **만들기**를 클릭하여 네임스페이스를 만듭니다.

## <a name="create-an-azure-event-hub-in-your-namespace"></a>네임스페이스에 Event Hub 만들기

네임스페이스가 배포되면 네임스페이스에서 이벤트 허브를 만들 수 있습니다.

1. 이전 단계에서 만든 네임스페이스로 이동합니다.

1. 상단 메뉴 모음에서 **+ 이벤트 허브**를 클릭합니다.

1. 이벤트 허브 이름을 지정합니다.

1. **만들기**를 클릭합니다.

   ![이벤트 허브 만들기][IMG05]

### <a name="create-an-azure-storage-account-for-your-event-hub-checkpoints"></a>이벤트 허브 검사점에 대한 Azure Storage 계정 만들기

다음 절차에서는 이벤트 허브 검사점에 대한 스토리지 계정을 만듭니다.

1. <https://portal.azure.com/>에서 Azure Portal로 이동합니다.

1. **+만들기**를 클릭한 다음, **스토리지**를 클릭한 후 **스토리지 계정**을 클릭합니다.

1. **스토리지 계정 만들기** 페이지에서 다음 정보를 입력합니다.

   * 스토리지 계정에 사용하려는 **구독**을 선택합니다.
   * 스토리지 계정에 새 **리소스 그룹**을 만들지 아니면 기존 리소스 그룹을 선택할지를 지정합니다.
   * 스토리지 계정의 고유한 **이름**을 입력합니다.
   * 스토리지 계정의 **위치**를 지정합니다.

   ![Azure Storage 계정 옵션을 지정합니다.][IMG08]

1. 위에 나열된 옵션을 지정한 경우 **검토 + 만들기**를 클릭하여 스토리지 계정을 만듭니다.

1. 사양을 검토하고 **만들기**를 클릭합니다.  배포에는 몇 분 정도 걸릴 수 있습니다.

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Spring Initializr를 사용하여 간단한 Spring Boot 애플리케이션 만들기

다음 절차에서는 Spring Boot 애플리케이션을 만듭니다.

1. [https://www.microsoft.com]\(<https://start.spring.io/>) 로 이동합니다.

1. 다음 옵션을 지정합니다.

   * **Java**를 사용하는 **Maven** 프로젝트를 생성합니다.
   * 2\.0 이상의 **Spring Boot** 버전을 지정합니다.
   * 애플리케이션에 대한 **그룹** 및 **아티팩트** 이름을 지정합니다.
   * **Web** 종속성 추가

      ![기본 Spring Initializr 옵션][SI01]

   > [!NOTE]
   >
   > Spring Initializr는 **그룹** 및 **아티팩트** 이름을 사용하여 패키지 이름을 만듭니다(예: *com.wingtiptoys.eventhub*).
   >

1. 위에 열거된 이러한 옵션을 지정한 경우 **프로젝트 만들기**를 클릭합니다.

1. 메시지가 표시되면 로컬 컴퓨터의 경로에 프로젝트를 다운로드합니다.

1. 로컬 시스템에서 파일의 압축을 푼 후에 단순한 Spring Boot 애플리케이션을 편집할 준비를 합니다.

## <a name="configure-your-spring-boot-app-to-use-the-azure-event-hub-starter"></a>Azure Event Hub starter를 사용하도록 Spring Boot 앱 구성

1. 앱의 루트 디렉터리에서 *pom.xml* 파일을 찾습니다. 예:

   `C:\SpringBoot\eventhub\pom.xml`

   또는

   `/users/example/home/eventhub/pom.xml`

1. 텍스트 편집기에서 *pom.xml* 파일을 열고 `<dependencies>` 목록에 Spring Cloud Azure Event Hub Stream Binder starter를 추가합니다.

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>spring-cloud-azure-eventhubs-stream-binder</artifactId>
      <version>1.1.0.RC2</version>
   </dependency>
   ```

1. *pom.xml* 파일을 저장하고 닫습니다.

## <a name="create-an-azure-credential-file"></a>Azure 자격 증명 파일 만들기

1. 명령 프롬프트를 엽니다.

1. Spring Boot 앱의 *리소스* 디렉터리로 이동합니다. 예:

   ```shell
   cd C:\SpringBoot\eventhub\src\main\resources
   ```

   또는

   ```shell
   cd /users/example/home/eventhub/src/main/resources
   ```

1. Azure 계정 로그인:

   ```azurecli
   az login
   ```

1. 구독 나열:

   ```azurecli
   az account list
   ```
   Azure가 구독 목록을 반환하며 사용하려는 구독의 GUID를 복사해야 합니다. 예를 들어 다음과 같습니다.

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "11111111-1111-1111-1111-111111111111",
       "isDefault": true,
       "name": "Converted Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "22222222-2222-2222-2222-222222222222",
       "user": {
         "name": "gena.soto@wingtiptoys.com",
         "type": "user"
       }
     }
   ]
   ```
   
1. Azure에 사용하려는 구독에 대한 GUID를 지정합니다. 예:

   ```azurecli
   az account set -s 11111111-1111-1111-1111-111111111111
   ```

1. Azure 자격 증명 파일 만들기

   ```azurecli
   az ad sp create-for-rbac --sdk-auth > my.azureauth
   ```

   이 명령은 *my.azureauth* 파일을 *리소스* 디렉터리에 다음 예제와 유사하게 만듭니다.

   ```json
   {
     "clientId": "33333333-3333-3333-3333-333333333333",
     "clientSecret": "44444444-4444-4444-4444-444444444444",
     "subscriptionId": "11111111-1111-1111-1111-111111111111",
     "tenantId": "22222222-2222-2222-2222-222222222222",
     "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
     "resourceManagerEndpointUrl": "https://management.azure.com/",
     "activeDirectoryGraphResourceId": "https://graph.windows.net/",
     "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
     "galleryEndpointUrl": "https://gallery.azure.com/",
     "managementEndpointUrl": "https://management.core.windows.net/"
   }
   ```

## <a name="configure-your-spring-boot-app-to-use-your-azure-event-hub"></a>Azure Event Hub를 사용하도록 Spring Boot 앱 구성

1. 앱의 *리소스* 디렉터리에서 *application.properties* 파일을 찾습니다.

   `C:\SpringBoot\eventhub\src\main\resources\application.properties`

   또는

   `/users/example/home/eventhub/src/main/resources/application.properties`

2. 텍스트 편집기에서 *application.properties* 파일을 찾고 다음 줄을 추가하고 샘플 값을 이벤트 허브의 적절한 속성으로 바꿉니다.

   ```yaml
   spring.cloud.azure.credential-file-path=my.azureauth
   spring.cloud.azure.resource-group=wingtiptoysresources
   spring.cloud.azure.region=West US
   spring.cloud.azure.eventhub.namespace=wingtiptoysnamespace
   spring.cloud.azure.eventhub.checkpoint-storage-account=wingtiptoysstorage
   spring.cloud.stream.bindings.input.destination=wingtiptoyshub
   spring.cloud.stream.bindings.input.group=$Default
   spring.cloud.stream.eventhub.bindings.input.consumer.checkpoint-mode=MANUAL
   spring.cloud.stream.bindings.output.destination=wingtiptoyshub
   ```
   위치:

   |                          필드                           |                                                                                   Description                                                                                    |
   |----------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |        `spring.cloud.azure.credential-file-path`         |                                                    이 자습서의 앞부분에서 만든 Azure 자격 증명 파일을 지정합니다.                                                    |
   |           `spring.cloud.azure.resource-group`            |                                                      Azure 이벤트 허브를 포함하는 Azure 리소스 그룹을 지정합니다.                                                      |
   |               `spring.cloud.azure.region`                |                                           Azure 이벤트 허브를 만들 때 지정한 지리적 영역을 지정합니다.                                            |
   |         `spring.cloud.azure.eventhub.namespace`          |                                          Azure 이벤트 허브 네임스페이스를 만들 때 지정한 고유 이름을 지정합니다.                                           |
   | `spring.cloud.azure.eventhub.checkpoint-storage-account` |                                                    이 자습서의 앞부분에서 만든 Azure Storage 계정을 지정합니다.                                                    |
   |     `spring.cloud.stream.bindings.input.destination`     |                            입력 대상 Azure Event Hub를 지정합니다.이 자습서에서는 이 자습서의 앞부분에서 만든 허브를 사용합니다.                            |
   |       `spring.cloud.stream.bindings.input.group `        | Azure Event Hub에서 소비자 그룹을 지정합니다. Azure Event Hub를 만들 때 생성된 기본 소비자 그룹을 사용하려면 '$Default'로 설정할 수 있습니다. |
   |    `spring.cloud.stream.bindings.output.destination`     |                               출력 대상 Azure Event Hub를 지정합니다.이 자습서에서는 입력 대상과 동일합니다.                               |


3. *application.properties* 파일을 저장하고 닫습니다.

## <a name="add-sample-code-to-implement-basic-event-hub-functionality"></a>기본 이벤트 허브 기능을 구현하는 샘플 코드 추가

이 섹션에서는 이벤트를 이벤트 허브에 보내는 데 필요한 Java 클래스를 만듭니다.

### <a name="modify-the-main-application-class"></a>기본 애플리케이션 클래스 수정

1. 앱의 패키지 디렉터리에서 기본 애플리케이션 Java 파일을 찾습니다. 예:

   `C:\SpringBoot\eventhub\src\main\java\com\wingtiptoys\eventhub\EventhubApplication.java`

   또는

   `/users/example/home/eventhub/src/main/java/com/wingtiptoys/eventhub/EventhubApplication.java`

1. 텍스트 편집기에서 애플리케이션 Java 파일을 열고 다음 줄을 파일에 추가합니다.

   ```java
   package com.wingtiptoys.eventhub;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;

   @SpringBootApplication
   public class EventhubApplication {
      public static void main(String[] args) {
         SpringApplication.run(EventhubApplication.class, args);
      }
   }
   ```

1. 기본 애플리케이션 Java 파일을 저장하고 닫습니다.

### <a name="create-a-new-class-for-the-source-connector"></a>원본 커넥터에 대한 새 클래스 만들기

1. 앱의 패키지 디렉터리에 *EventhubSource.java*라는 새 Java 파일을 만듭니다. 그리고 파일 텍스트 편집기에서 해당 파일을 열고 다음 줄을 추가합니다.

   ```java
   package com.wingtiptoys.eventhub;

   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.cloud.stream.annotation.EnableBinding;
   import org.springframework.cloud.stream.messaging.Source;
   import org.springframework.messaging.support.GenericMessage;
   import org.springframework.web.bind.annotation.PostMapping;
   import org.springframework.web.bind.annotation.RequestBody;
   import org.springframework.web.bind.annotation.RestController;

   @EnableBinding(Source.class)
   @RestController
   public class EventhubSource {

      @Autowired
      private Source source;

      @PostMapping("/messages")
      public String postMessage(@RequestBody String message) {
         this.source.output().send(new GenericMessage<>(message));
         return message;
      }
   }
   ```
1. *EventhubSource.java* 파일을 저장 후 닫습니다.

### <a name="create-a-new-class-for-the-sink-connector"></a>싱크 커넥터에 대한 새 클래스 만들기

1. 앱의 패키지 디렉터리에 *EventhubSink.java*라는 새 Java 파일을 만듭니다. 그리고 파일 텍스트 편집기에서 해당 파일을 열고 다음 줄을 추가합니다.

   ```java
   package com.wingtiptoys.eventhub;

   import com.microsoft.azure.spring.integration.core.AzureHeaders;
   import com.microsoft.azure.spring.integration.core.api.Checkpointer;
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;
   import org.springframework.cloud.stream.annotation.EnableBinding;
   import org.springframework.cloud.stream.annotation.StreamListener;
   import org.springframework.cloud.stream.messaging.Sink;
   import org.springframework.messaging.handler.annotation.Header;

   @EnableBinding(Sink.class)
   public class EventhubSink {

      private static final Logger LOGGER = LoggerFactory.getLogger(EventhubSink.class);

      @StreamListener(Sink.INPUT)
      public void handleMessage(String message, @Header(AzureHeaders.CHECKPOINTER) Checkpointer checkpointer) {
         LOGGER.info("New message received: '{}'", message);
         checkpointer.success().handle((r, ex) -> {
            if (ex == null) {
               LOGGER.info("Message '{}' successfully checkpointed", message);
            }
            return null;
         });
      }
   }
   ```

1. *EventhubSink.java* 파일을 저장 후 닫습니다.

## <a name="build-and-test-your-application"></a>애플리케이션 빌드 및 테스트

애플리케이션을 빌드하고 테스트하려면 다음 절차를 수행합니다.

1. 명령 프롬프트를 열고 디렉터리를 *pom.xml* 파일이 위치한 폴더로 변경합니다. 예:

   `cd C:\SpringBoot\eventhub`

   또는

   `cd /users/example/home/eventhub`

1. Maven을 사용하여 Spring Boot 애플리케이션을 빌드하고 실행합니다. 예:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. 애플리케이션이 실행되면, 애플리케이션을 테스트하기 위해 *curl*을 사용할 수 있습니다. 예:

   ```shell
   curl -X POST -H "Content-Type: text/plain" -d "hello" http://localhost:8080/messages
   ```
   애플리케이션 로그에 "hello"가 표시됩니다. 다음은 그 예입니다.

   ```shell
   [Thread-13] INFO com.wingtiptoys.eventhub.EventhubSink - New message received: 'hello'
   [pool-10-thread-7] INFO com.wingtiptoys.eventhub.EventhubSink - Message 'hello' successfully checkpointed
   ```

## <a name="next-steps"></a>다음 단계

Spring과 Azure에 대한 자세한 사항은 Azure의 Spring 설명서 센터를 참조합니다.

> [!div class="nextstepaction"]
> [Azure의 Spring](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>추가 리소스

Event Hub 스트림 바인더에 대한 Azure 지원에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Event Hubs 정의](/azure/event-hubs/event-hubs-about)

* [Azure Portal을 사용하여 Event Hubs 네임스페이스 및 이벤트 허브 만들기](/azure/event-hubs/event-hubs-create)

* [Azure Event Hub를 사용하여 Apache Kafka에 대한 Spring Boot Starter를 사용하는 방법](configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub.md)

Java와 함께 Azure를 사용하는 방법에 관한 자세한 정보는 [Java 개발자를 위한 Azure]와 [Azure DevOps 및 Java 사용하기]를 참조하세요.

**[Spring Framework]** 는 Java 개발자가 엔터프라이즈 수준의 애플리케이션을 만드는 데 도움이 되는 오픈 소스 솔루션입니다. 해당 플랫폼을 기반으로 하여 빌드되는 인기 있는 프로젝트 중 하나가 [Spring Boot]입니다. 이 프로젝트는 독립 실행형 Java 애플리케이션을 만드는 간단한 방법을 제공합니다. Spring Boot을 시작하는 개발자를 도우려면 <https://github.com/spring-guides/>에서 몇 가지 샘플 Spring Boot 패키지를 사용할 있습니다. 기본 Spring Boot 프로젝트 목록에서 선택하는 것 외에도 **[Spring Initializr]** 를 통해 사용자 지정 Spring Boot 애플리케이션을 만들기 시작하는 개발자에게 도움을 줍니다.

<!-- URL List -->

[체험판 Azure 계정]: https://azure.microsoft.com/pricing/free-trial/
[Java 개발자를 위한 Azure]: /azure/developer/java/
[Azure DevOps 및 Java 사용하기]: /azure/devops/
[MSDN 구독자 혜택]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[IMG01]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-01.png
[IMG02]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-02.png
[IMG03]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-03.png
[IMG04]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-04.png
[IMG05]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-05.png
[IMG06]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-06.png
[IMG07]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-07.png
[IMG08]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-08.png

[SI01]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-project-01.png
[SI02]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-project-02.png
[SI03]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-project-03.png
