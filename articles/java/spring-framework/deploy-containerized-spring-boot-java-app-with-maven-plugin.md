---
title: Maven을 사용하여 Azure에 Spring Boot 앱 배포
description: Azure Web Apps의 Maven 플러그 인을 사용하여 Spring Boot 앱을 Azure에 배포하는 방법을 알아봅니다.
services: app-service
documentationcenter: java
ms.date: 12/19/2018
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: seo-java-july2019, seo-java-august2019
ms.openlocfilehash: b926b0bd1cb401f12f927abfea112e6004988812
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "81668399"
---
# <a name="use-maven-for-azure-web-apps-to-deploy-a-containerized-spring-boot-app-to-azure"></a>Azure Web Apps의 Maven을 사용하여 Azure에 컨테이너화된 Spring Boot 앱 배포

이 문서에서는 [Azure Web Apps용 Maven 플러그인](https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin)을 사용하여 Azure App Services에 Docker 컨테이너의 샘플 Spring Boot 애플리케이션을 배포하는 방법을 보여 줍니다.

> [!NOTE]
> 
> [Apache Maven](https://maven.apache.org/)에서 Azure Web Apps용 Maven 플러그인은 Maven 프로젝트에 Azure App Service의 원활한 통합을 제공하고 Azure App Service에 웹앱을 배포하는 개발자를 위한 프로세스를 간소화합니다.
> 
> Azure Web Apps의 Maven 플러그 인은 현재 미리 보기로 사용할 수 있습니다. 지금은 FTP 게시만 지원되지만 향후에 기능이 추가될 계획입니다.
> 

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
>

## <a name="clone-the-sample-spring-boot-on-docker-web-app"></a>Docker 웹앱에 샘플 Spring Boot 복제

이 섹션에서는 컨테이너화된 Spring Boot 애플리케이션을 복제하고 로컬로 테스트합니다.

1. 명령 프롬프트 또는 터미널 창을 열고 Spring Boot 애플리케이션을 저장할 로컬 디렉터리를 만들고 해당 디렉터리로 변경합니다. 예:
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   -- 또는 --
   ```shell
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. [Spring Boot on Docker 시작] 샘플 프로젝트를 방금 만든 디렉터리에 복제합니다. 예:
   ```shell
   git clone https://github.com/spring-guides/gs-spring-boot-docker
   ```

1. 디렉터리를 완료된 프로젝트로 변경합니다. 예:
   ```shell
   cd gs-spring-boot-docker/complete
   ```

1. Maven을 사용하여 JAR 파일을 빌드합니다. 예:
   ```shell
   mvn clean package
   ```

1. 웹앱을 만들면 Maven을 사용하여 웹앱을 시작합니다. 예:
   ```shell
   mvn spring-boot:run
   ```

1. 웹 브라우저를 사용하여 로컬로 이동하여 웹앱을 테스트합니다. 예를 들어, curl을 사용할 수 있는 경우 다음 명령을 사용할 수 있습니다.
   ```shell
   curl http://localhost:8080
   ```

1. **Hello Docker World** 메시지가 표시됩니다.

## <a name="create-an-azure-service-principal"></a>Azure 서비스 주체 만들기

이 섹션에서는 컨테이너를 Azure에 배포할 때 Maven 플러그 인에서 사용하는 Azure 서비스 주체를 만듭니다.

1. 명령 프롬프트를 엽니다.

2. Azure CLI를 사용하여 Azure 계정에 로그인합니다.
   ```shell
   az login
   ```
   지시에 따라 로그인 프로세스를 완료합니다.

3. Azure 서비스 주체 만들기
   ```shell
   az ad sp create-for-rbac --name "uuuuuuuu" --password "pppppppp"
   ```
   위치:

   | 매개 변수  |                    Description                     |
   |------------|----------------------------------------------------|
   | `uuuuuuuu` | 서비스 주체에 대한 사용자 이름을 지정합니다. |
   | `pppppppp` | 서비스 주체에 대한 암호를 지정합니다.  |


4. Azure는 다음 예제와 유사한 JSON로 응답합니다.
   ```json
   {
      "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "displayName": "uuuuuuuu",
      "name": "http://uuuuuuuu",
      "password": "pppppppp",
      "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
   }
   ```

   > [!NOTE]
   >
   > 컨테이너를 Azure에 배포하도록 Maven 플러그 인을 구성하는 경우 이 JSON 응답의 값을 사용합니다. `aaaaaaaa`, `uuuuuuuu`, `pppppppp` 및 `tttttttt`는 다음 섹션에서 Maven `settings.xml` 파일을 구성할 때 이러한 값을 해당 요소에 쉽게 매핑할 수 있도록 이 예제에서 사용되는 자리 표시자 값입니다.
   >
   >

## <a name="configure-maven-to-use-your-azure-service-principal"></a>Azure 서비스 주체를 사용하도록 Maven 구성

이 섹션에서는 Maven에서 컨테이너를 Azure에 배포할 때 사용할 인증을 구성하기 위해 Azure 서비스 주체의 값을 사용합니다.

1. 텍스트 편집기에서 Maven `settings.xml` 파일을 엽니다. 이 파일은 다음 예제와 같은 경로에 있을 수 있습니다.
   * `/etc/maven/settings.xml`
   * `%ProgramFiles%\apache-maven\3.5.0\conf\settings.xml`
   * `$HOME/.m2/settings.xml`

2. *settings.xml* 파일의 `<servers>` 컬렉션에 이 자습서의 이전 섹션에 있는 Azure 서비스 주체 설정을 추가합니다. 예:

   ```xml
   <servers>
      <server>
        <id>azure-auth</id>
         <configuration>
            <client>aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa</client>
            <tenant>tttttttt-tttt-tttt-tttt-tttttttttttt</tenant>
            <key>pppppppp</key>
            <environment>AZURE</environment>
         </configuration>
      </server>
   </servers>
   ```
   위치:

   |     요소     |                                                                                   Description                                                                                   |
   |-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |     `<id>`      |                                Azure에 웹앱을 배포할 때 Maven을 사용하여 보안 설정을 조회하는 고유한 이름을 지정합니다.                                |
   |   `<client>`    |                                                             서비스 사용자의 `appId` 값을 포함합니다.                                                             |
   |   `<tenant>`    |                                                            서비스 사용자의 `tenant` 값을 포함합니다.                                                             |
   |     `<key>`     |                                                           서비스 사용자의 `password` 값을 포함합니다.                                                            |
   | `<environment>` | 대상 Azure 클라우드 환경을 정의합니다. 이 예에서는 `AZURE`입니다. (환경의 전체 목록은 [Azure Web Apps의 Maven 플러그 인] 설명서에서 제공됩니다.) |


3. *settings.xml* 파일을 저장하고 닫습니다.

## <a name="optional-deploy-your-local-docker-file-to-docker-hub"></a>선택 사항: Docker 허브에 로컬 Docker 파일 배포

Docker 계정이 있는 경우 Docker 컨테이너 이미지를 로컬에서 빌드하고 Docker 허브에 푸시할 수 있습니다. 이렇게 하려면 다음 단계를 수행합니다.

1. 텍스트 편집기에서 Spring Boot 애플리케이션에 대한 `pom.xml` 파일을 엽니다.

1. `<containerSettings>` 요소의 `<imageName>` 자식 요소를 찾습니다.

1. Docker 계정 이름으로 `${docker.image.prefix}` 값을 업데이트합니다.
   ```xml
   <containerSettings>
      <imageName>mydockeraccountname/${project.artifactId}</imageName>
   </containerSettings>
   ```

1. 다음 배포 방법 중 하나를 선택합니다.

   * Maven에서 로컬로 컨테이너 이미지를 빌드하고 Docker를 사용하여 컨테이너를 Docker 허브에 푸시합니다.
      ```shell
      mvn clean package docker:build
      docker push
      ```

   * [Maven의 Docker 플러그 인]이 설치되어 있는 경우 `-DpushImage` 매개 변수를 사용하여 컨테이너 이미지를 Docker 허브에 자동으로 빌드할 수 있습니다.
      ```shell
      mvn clean package docker:build -DpushImage
      ```

## <a name="optional-customize-your-pomxml-before-deploying-your-container-to-azure"></a>선택 사항: 컨테이너를 Azure에 배포하기 전에 pom.xml 사용자 지정

텍스트 편집기에서 Spring Boot 애플리케이션에 대한 `pom.xml` 파일을 연 다음, `azure-webapp-maven-plugin`에 대한 `<plugin>` 요소를 찾습니다. 이 요소는 다음 예제와 유사합니다.

   ```xml
   <plugin>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-webapp-maven-plugin</artifactId>
      <version>0.1.3</version>
      <configuration>
         <authentication>
            <serverId>azure-auth</serverId>
         </authentication>
         <resourceGroup>maven-plugin</resourceGroup>
         <appName>maven-linux-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <containerSettings>
            <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         </containerSettings>
         <appSettings>
            <property>
               <name>PORT</name>
               <value>8080</value>
            </property>
         </appSettings>
      </configuration>
   </plugin>
   ```

Maven 플러그 인에 대해 수정할 수 있는 여러 값이 있으며 이러한 각 요소에 대한 자세한 설명을 [Azure Web Apps의 Maven 플러그 인] 설명서에서 사용할 수 있습니다. 즉, 이 문서에서 강조 표시된 값은 여러 개입니다.

| 요소 | Description |
|---|---|
| `<version>` | [Azure Web Apps의 Maven 플러그 인] 버전을 지정합니다. [Maven 중앙 리포지토리](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-webapp-maven-plugin%22)에 나열된 버전을 검사하여 최신 버전을 사용하고 있는지 확인해야 합니다. |
| `<authentication>` | Azure에 대한 인증 정보를 지정합니다. 이 예제에서는 `azure-auth`이 포함된 `<serverId>` 요소를 포함합니다. Maven에서는 해당 값을 사용하여 이 문서의 이전 섹션에 정의된 Maven *settings.xml* 파일에서 Azure 서비스 주체 값을 조회합니다. |
| `<resourceGroup>` | 대상 리소스 그룹, 즉, 이 예에서 `maven-plugin`을 지정합니다. 리소스 그룹이 아직 존재하지 않는 경우 배포 중에 만들어집니다. |
| `<appName>` | 웹앱에 대한 대상 이름을 지정합니다. 이 예제에서는 대상 이름은 `maven-linux-app-${maven.build.timestamp}`이며 이 예제에서 충돌을 피하기 위해 여기에 `${maven.build.timestamp}` 접미사가 추가됩니다. (타임스탬프는 선택 사항입니다. 앱 이름에 대한 고유한 문자열을 지정할 수 있습니다.) |
| `<region>` | 대상 지역을 지정합니다. 이 예제에서는 `westus`입니다. (전체 목록은 [Azure Web Apps의 Maven 플러그 인] 설명서에서 제공됩니다.) |
| `<appSettings>` | Maven에 대한 고유한 설정을 지정하여 Azure에 웹앱을 배포할 때 사용합니다. 이 예제에서 `<property>` 요소는 앱에 대한 포트를 지정하는 자식 요소의 이름/값 쌍을 포함합니다. |

> [!NOTE]
>
> 이 예제에서 기본값의 포트를 변경하는 경우 포트 번호를 변경하도록 설정해야 합니다.
>

## <a name="build-and-deploy-your-container-to-azure"></a>Azure에 컨테이너 빌드 및 배포

이 문서의 이전 섹션에서 설정을 모두 구성했으면 컨테이너를 Azure에 배포할 준비가 되었습니다. 이렇게 하려면 다음 단계를 수행합니다.

1. *pom.xml* 파일을 변경한 경우 이전에 사용하던 명령 프롬프트 또는 터미널 창에서 Maven를 사용하여 JAR 파일을 다시 작성합니다. 예:
   ```shell
   mvn clean package
   ```

1. Maven을 사용하여 Azure에 웹앱을 배포합니다. 예:
   ```shell
   mvn azure-webapp:deploy
   ```

Maven은 Azure에 웹앱을 배포합니다. 웹앱이 아직 없는 경우 생성됩니다.

> [!NOTE]
>
> *pom.xml* 파일의 `<region>` 요소에서 지정한 지역이 배포를 시작할 때 서버를 충분히 사용할 수 없는 경우 다음 예제와 비슷한 오류가 표시될 수 있습니다.
>
> ```xml
> [INFO] Start deploying to Web App maven-linux-app-20170804...
> [INFO] ------------------------------------------------------------------------
> [INFO] BUILD FAILURE
> [INFO] ------------------------------------------------------------------------
> [INFO] Total time: 31.059 s
> [INFO] Finished at: 2017-08-04T12:15:47-07:00
> [INFO] Final Memory: 51M/279M
> [INFO] ------------------------------------------------------------------------
> [ERROR] Failed to execute goal com.microsoft.azure:azure-webapp-maven-plugin:0.1.3:deploy (default-cli) on project gs-spring-boot-docker: null: MojoExecutionException: CloudException: OnError while emitting onNext value: retrofit2.Response.class
> ```
>
> 이 경우에 다른 지역을 지정하고 Maven 명령을 다시 실행하여 애플리케이션을 배포할 수 있습니다.
>
>

웹을 배포하면 [Azure Portal]을 사용하여 작업을 관리할 수 있습니다.

* 웹앱은 **App Services**에 나열됩니다.

   ![Azure Portal App Services에 나열된 웹앱][AP01]

* 웹앱의 URL은 웹앱의 **개요**에 나열됩니다.

   ![웹앱의 URL 확인][AP02]

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

이 문서에서 설명하는 다양한 기술에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Web Apps의 Maven 플러그 인]

* [Azure CLI에서 Azure에 로그인](/azure/xplat-cli-connect)

* [Azure Web Apps의 Maven 플러그 인을 사용하여 Spring Boot 앱을 Azure App Service에 배포하는 방법](deploy-spring-boot-java-app-with-maven-plugin.md)

* [Azure CLI 2.0을 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Maven 설정 참조](https://maven.apache.org/settings.html)

* [Maven의 Docker 플러그 인]

Java와 함께 Azure를 사용하는 방법에 관한 자세한 정보는 [Java 개발자를 위한 Azure]와 [Azure DevOps 및 Java 사용하기]를 참조하세요.

<!-- URL List -->

[Azure CLI(명령줄 인터페이스)]: /cli/azure/overview
[Java 개발자를 위한 Azure]: /azure/developer/java/
[Azure Portal]: https://portal.azure.com/
[Docker]: https://www.docker.com/
[Maven의 Docker 플러그 인]: https://github.com/spotify/docker-maven-plugin
[체험판 Azure 계정]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Azure DevOps 및 Java 사용하기]: /azure/devops/
[Maven]: https://maven.apache.org/
[MSDN 구독자 혜택]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: https://projects.spring.io/spring-boot/
[Spring Boot on Docker 시작]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/
[Azure Web Apps의 Maven 플러그 인]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin

[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->

[AP01]: media/deploy-containerized-spring-boot-java-app-with-maven-plugin/AP01.png
[AP02]: media/deploy-containerized-spring-boot-java-app-with-maven-plugin/AP02.png
