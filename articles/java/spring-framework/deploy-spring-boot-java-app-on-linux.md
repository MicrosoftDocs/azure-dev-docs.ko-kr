---
title: Azure App Service에서 Linux에 Spring Boot 웹앱 배포
description: 이 자습서에서는 Microsoft Azure에서 Linux Web App으로 Spring Boot 애플리케이션을 배포하는 단계를 설명합니다.
services: azure app service
documentationcenter: java
ms.date: 12/31/2019
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: mvc, devx-track-java
ms.openlocfilehash: f6f90deebd214d0381f3a66c6808dbc4f848aa13
ms.sourcegitcommit: 2f832baf90c208a8a69e66badef5f126d23bbaaf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88725147"
---
# <a name="deploy-a-spring-boot-application-to-linux-on-azure-app-service"></a>Azure App Service에서 Linux에 Spring Boot 애플리케이션 배포

이 자습서에서는 [Docker]를 사용하여 [Azure App Service](/azure/app-service/containers/app-service-linux-intro)에서 [Spring Boot] 애플리케이션을 컨테이너화하고 Linux 호스트로 Docker 이미지를 배포하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서의 단계를 완료하려면 다음 필수 조건이 필요합니다.

* Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택]을 활성화하거나 [체험판 Azure 계정]에 등록할 수 있습니다.
* [Azure CLI(명령줄 인터페이스)]
* 지원되는 JDK(Java Development Kit) Azure에서 개발하는 경우 사용할 수 있는 JDK에 대한 자세한 내용은 <https://aka.ms/azure-jdks>를 참조하세요.
* Apache의 [Maven] 빌드 도구(버전 3)
* [Git] 클라이언트
* [Docker] 클라이언트

> [!NOTE]
>
> 이 자습서의 가상화 요구 사항으로 인해 가상 머신에는 이 문서의 단계를 따를 수 없습니다. 따라서 가상화 기능이 사용하도록 설정된 물리적 컴퓨터를 사용해야 합니다.

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Spring Boot on Docker 시작 웹앱 만들기

다음 단계에서는 간단한 Spring Boot 웹 애플리케이션을 만들어 로컬로 테스트하는 데 필요한 단계를 안내합니다.

1. 명령 프롬프트를 열고 애플리케이션을 저장할 로컬 디렉터리를 만들고 해당 디렉터리로 변경합니다. 예를 들면 다음과 같습니다.

   ```bash
   mkdir SpringBoot
   cd SpringBoot
   ```

1. [Spring Boot on Docker 시작] 샘플 프로젝트를 방금 만든 디렉터리에 복제합니다. 예:

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. 디렉터리를 완료된 프로젝트로 변경합니다. 예:

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Maven을 사용하여 JAR 파일을 빌드합니다. 예:

   ```bash
   mvn package
   ```

1. 웹앱이 만들어지면 디렉터리를 JAR 파일이 위치한 `target` 디렉터리로 변경하고 웹앱을 시작합니다. 예:

   ```bash
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar --server.port=80
   ```

1. 웹 브라우저를 사용하여 로컬로 이동하여 웹앱을 테스트합니다. 예를 들어 curl을 사용할 수 있고 포트 80에서 실행하도록 Tomcat 서버를 구성한 경우:

   ```bash
   curl http://localhost
   ```

1. 다음 메시지가 표시되어야 합니다. **Hello Docker World**

   ![로컬로 샘플 앱 찾아보기][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Azure Container Registry를 만들어서 프라이빗 Docker 레지스트리로 사용

다음 단계에서는 Azure Portal을 사용하여 Azure Container Registry를 만드는 방법을 설명합니다.

> [!NOTE]
>
> Azure Portal 대신 Azure CLI를 사용하려는 경우 [Azure CLI 2.0을 사용하여 프라이빗 Docker 컨테이너 레지스트리 만들기](/azure/container-registry/container-registry-get-started-azure-cli)의 단계에 따르세요.

1. [Azure Portal]을 찾아 로그인합니다.

   Azure Portal에서 사용자의 계정에 로그인했으면 [Azure Portal을 사용하여 프라이빗 Docker 컨테이너 레지스트리 만들기] 문서의 단계를 수행합니다. 편의상 다음 단계에서 다시 설명합니다.

1. **+ 새로 만들기**의 메뉴 아이콘을 클릭하고 **컨테이너**를 클릭한 다음, **Azure Container Registry**를 클릭합니다.

   ![새로운 Azure Container Registry 만들기][AR01]

1. **컨테이너 레지스트리** 만들기 페이지가 표시되면 **레지스트리 이름**, **구독**, **리소스 그룹**, **위치**를 입력합니다. 그런 다음, **만들기**를 클릭합니다.

   ![Azure Container Registry 설정 구성][AR03]

## <a name="configure-maven-to-build-image-to-your-azure-container-registry"></a>이미지를 Azure Container Registry에 빌드하도록 Maven 구성

1. Spring Boot 애플리케이션의 완성된 프로젝트 디렉터리(예: "*C:\SpringBoot\gs-spring-boot-docker\complete*" 또는 " */users/robert/SpringBoot/gs-spring-boot-docker/complete*") 텍스트 편집기를 사용하여 *pom.xml* 파일을 엽니다.

1. 최신 버전의 [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) 및 로그인 서버 값을 사용하여 *pom.xml* 파일의 `<properties>` 컬렉션을 업데이트하고 이 자습서의 이전 섹션에서 Azure Container Registry에 대한 설정에 액세스할 수 있습니다. 다음은 그 예입니다. 

   ```xml
   <properties>
      <jib-maven-plugin.version>2.2.0</jib-maven-plugin.version>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. *pom.xml* 파일의 `<plugins>` 컬렉션에 [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin)을 추가합니다.  이 예제에서는 2.2.0 버전을 사용합니다.

   `<from>/<image>`에서 기본 이미지를 지정합니다. 여기서는 `mcr.microsoft.com/java/jre:8-zulu-alpine`입니다. `<to>/<image>`에 기본 이미지로 빌드할 최종 이미지의 이름을 지정합니다.  

   인증 `{docker.image.prefix}`는 이전에 표시된 레지스트리 페이지의 **로그인 서버**입니다. `{project.artifactId}`는 프로젝트의 첫 번째 Maven 빌드에서 JAR 파일의 이름 및 버전 번호입니다.

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jre:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Spring Boot 애플리케이션의 완성된 프로젝트 디렉터리로 이동하고 다음 명령을 실행하여 애플리케이션을 다시 빌드하고 Azure Container Registry에 컨테이너를 푸시합니다.

   ```bash
   az acr login -n wingtiptoysregistry && mvn compile jib:build
   ```

> [!NOTE]
> 1. `az acr login ...` 명령은 Azure Container Registry에 로그인하려고 시도합니다. 그렇지 않으면 jib-maven-plugin에 `<username>` 및 `<password>`를 제공해야 합니다. jib의 [인증 방법](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin#authentication-methods)을 참조하세요.
> 2. 지브를 사용하여 이미지를 Azure Container Registry에 푸시하는 경우 이미지는 *Dockerfile*을 사용하지 않습니다. 자세한 내용은 [이](https://cloudplatform.googleblog.com/2018/07/introducing-jib-build-java-docker-images-better.html) 문서를 참조하세요.
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-using-your-container-image"></a>컨테이너 이미지를 사용하여 Azure App Service에서 Linux에 웹앱 만들기

1. [Azure Portal]을 찾아 로그인합니다.

2. **+ 리소스 만들기**에 대한 메뉴 아이콘, **컴퓨팅**, **Web App for Containers**를 차례로 클릭합니다.
   
   ![Azure Portal에서 새로운 웹앱 만들기][LX01]

3. **Web App on Linux** 페이지가 표시되면 다음 정보를 입력합니다.

   * 드롭다운 목록에서 **구독**을 선택합니다.

   * 기존 **리소스 그룹**을 선택하거나 새 리소스 그룹을 만들기 위해 이름을 지정합니다.

   * **앱 이름**에 고유한 이름을 입력합니다. 예: "*wingtiptoyslinux*"

   * `Docker Container`를 **게시**하도록 지정합니다.

   * **운영 체제**로 *Linux*를 선택합니다.

   * **지역**을 선택합니다.

   * **Linux 계획**을 수락하고 기존 **App Service 계획**을 선택하거나 **새로 만들기**를 클릭하여 새 App Service 계획을 만듭니다.

   * **다음: Docker**를 클릭합니다.

   ![웹앱 설정 구성][LX02]

      **웹앱** 페이지에서 **Docker**를 선택하고 다음 정보를 입력합니다.

   * **단일 컨테이너**를 선택합니다.

   * **레지스트리**: 컨테이너를 선택합니다(예: "*wingtiptoysregistry*").

   * **이미지**: 이전에 만든 이미지를 선택합니다(예: "*gs-spring-boot-docker*").

   * **태그**: 이미지에 대한 태그를 선택합니다. 예: "*latest*"

   * **시작 명령**: 이미지에 시작 명령이 이미 있으므로 공백으로 유지합니다.

   위 정보를 모두 입력한 후 **검토 + 만들기**를 클릭합니다.

   ![웹앱 설정 구성][LX02-A]

   * **검토 + 만들기**를 클릭합니다.

정보를 검토하고 **만들기**를 클릭합니다.

배포가 완료되면 **리소스로 이동**을 클릭합니다.  배포 페이지에 애플리케이션에 액세스하는 데 사용하는 URL이 표시됩니다.

   ![배포 URL 가져오기][LX02-B]

> [!NOTE]
>
> Azure에서는 인터넷 요청을 80 포트에서 실행되는 임베디드 Tomcat 서버에 자동으로 매핑합니다. 그러나 임베디드 Tomcat 서버가 8080 포트 또는 사용자 지정 포트에서 실행되도록 구성한 경우 임베디드 Tomcat 서버의 포트를 정의하는 환경 변수를 웹앱에 추가해야 합니다. 이렇게 하려면 다음 단계를 수행합니다.
>
> 1. [Azure Portal]을 찾아 로그인합니다.
>
> 2. **Web Apps**에 대한 아이콘을 클릭하고 **App Services** 페이지에서 앱을 선택합니다.
>
> 3. 왼쪽 탐색 창에서 **구성**을 클릭합니다.
>
> 4. **애플리케이션 설정** 섹션에서 **WEBSITES_PORT**라는 새 설정을 추가하고 값에 대한 사용자 지정 포트 번호를 입력합니다.
>
> 5. **확인**을 클릭합니다. 그런 다음 **Save**를 클릭합니다.
>
> ![Azure Portal에서 사용자 지정 포트 번호 저장][LX03]

<!--
##  OPTIONAL: Configure the embedded Tomcat server to run on a different port

The embedded Tomcat server in the sample Spring Boot application is configured to run on port 8080 by default. However, if you want to run the embedded Tomcat server to run on a different port, such as port 80 for local testing, you can configure the port by using the following steps.

1. Go to the *resources* directory (or create the directory if it does not exist); for example:
   ```shell
   cd src/main/resources
   ```

1. Open the *application.yml* file in a text editor if it exists, or create a new YAML file if it does not exist.

1. Modify the **server** setting so that the server runs on port 80; for example:
   ```yaml
   server:
      port: 80
   ```

1. Save and close the *application.yml* file.
-->

## <a name="next-steps"></a>다음 단계

Spring과 Azure에 대한 자세한 사항은 Azure의 Spring 설명서 센터를 참조합니다.

> [!div class="nextstepaction"]
> [Azure의 Spring](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>추가 리소스

Azure에서 Spring Boot 애플리케이션을 사용 하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure App Service에 Spring Boot 애플리케이션 배포](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)
* [Azure Container Service의 Kubernetes 클러스터에 Spring Boot 애플리케이션 배포](deploy-spring-boot-java-app-on-kubernetes.md)

Java와 함께 Azure를 사용하는 방법에 관한 자세한 정보는 [Java 개발자를 위한 Azure]와 [Azure DevOps 및 Java 사용하기]를 참조하세요.

Spring Boot on Docker 샘플 프로젝트에 대한 자세한 정보는 [Spring Boot on Docker 시작]을 참조하세요.

자체 Spring Boot 애플리케이션을 시작하는 데 도움이 필요하면 https://start.spring.io/ 에서 **Spring Initializr**를 참조하세요.

간단한 Spring Boot 애플리케이션을 만들기 시작하는 방법에 대한 자세한 내용은 https://start.spring.io/ 에서 Spring Initializr를 참조하세요.

Azure와 함께 사용자 지정 Docker 이미지를 사용하는 방법에 대한 추가 예제를 보려면 [Azure Web App on Linux에 대한 사용자 지정 Docker 이미지 사용]을 참조하세요.

<!-- URL List -->

[Azure CLI(명령줄 인터페이스)]: /cli/azure/overview
[Azure Container Service (AKS)]: https://azure.microsoft.com/services/container-service/
[Java 개발자를 위한 Azure]: /azure/developer/java/
[Azure Portal]: https://portal.azure.com/
[Azure Portal을 사용하여 프라이빗 Docker 컨테이너 레지스트리 만들기]: /azure/container-registry/container-registry-get-started-portal
[Azure Web App on Linux에 대한 사용자 지정 Docker 이미지 사용]: /azure/app-service/tutorial-custom-container
[Docker]: https://www.docker.com/
[체험판 Azure 계정]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Azure DevOps 및 Java 사용하기]: /azure/devops/java/
[Maven]: http://maven.apache.org/
[MSDN 구독자 혜택]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker 시작]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->

[SB01]: media/deploy-spring-boot-java-app-on-linux/SB01.png
[SB02]: media/deploy-spring-boot-java-app-on-linux/SB02.png
[AR01]: media/deploy-spring-boot-java-app-on-linux/AR01.png
[AR03]: media/deploy-spring-boot-java-app-on-linux/AR03.png
[LX01]: media/deploy-spring-boot-java-app-on-linux/LX01.png
[LX02]: media/deploy-spring-boot-java-app-on-linux/LX02.png
[LX02-A]: media/deploy-spring-boot-java-app-on-linux/LX02-A.png
[LX02-B]: media/deploy-spring-boot-java-app-on-linux/LX02-B.png
[LX03]: media/deploy-spring-boot-java-app-on-linux/LX03.png
