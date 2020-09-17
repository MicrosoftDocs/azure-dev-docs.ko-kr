---
title: Azure Container Registry의 Spring Boot 앱 배포
titleSuffix: Azure App Service
description: 이 자습서는 Maven 플러그 인을 사용하여 Azure App Service에 Azure Container Registry의 Spring Boot 애플리케이션을 배포하는 단계를 설명합니다.
services: container-registry
documentationcenter: java
ms.date: 12/19/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 3bd3eab54fa231fc06805cd44b61fcb121a4ad31
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90681768"
---
# <a name="use-maven-for-azure-web-apps-to-deploy-a-spring-boot-app-in-azure-container-registry-to-azure-app-service"></a>Azure Web Apps의 Maven을 사용하여 Azure App Service에 Azure Container Registry의 Spring Boot 앱 배포

이 문서에서는 Azure Container Registry에 샘플 [Spring Boot] 애플리케이션을 배포하고 Azure Web Apps용 Maven 플러그인을 사용하여 Azure App Service에 애플리케이션을 배포하는 방법을 보여줍니다.

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
   https://github.com/spring-guides/gs-spring-boot-docker.git
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

1. 다음 메시지가 표시되어야 합니다. **Hello Docker World**

   ![로컬로 샘플 앱 찾아보기][SB01]

> [!NOTE]
>
> Docker를 로컬에서 사용하는 경우 포트 2375의 localhost에 연결할 수 없다는 오류가 표시될 수 있습니다. 이 경우 TLS 없이 로컬에서 Docker를 사용하도록 설정해야 할 수 있습니다. 이렇게 하려면 Docker 설정을 열고 **TLS 없이 TCP://localhost:2375에 데몬 노출** 옵션을 선택합니다.
>
> ![로컬 TCP 포트 2375에서 Docker 데몬 노출][TL01]

## <a name="create-an-azure-service-principal"></a>Azure 서비스 주체 만들기

이 섹션에서는 컨테이너를 Azure에 배포할 때 Maven 플러그 인에서 사용하는 Azure 서비스 주체를 만듭니다.

1. 명령 프롬프트를 엽니다.

2. Azure CLI를 사용하여 Azure 계정에 로그인합니다.
   ```azurecli
   az login
   ```
   지시에 따라 로그인 프로세스를 완료합니다.

3. Azure 서비스 주체 만들기
   ```azurecli
   az ad sp create-for-rbac --name <ServicePrincipalName>
   ```
인증 매개 변수가 없으면 임의로 만든 암호를 통한 암호 기반 인증이 사용됩니다.

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

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Container Registry 만들기

1. 명령 프롬프트를 엽니다.

1. Azure 계정에 로그인합니다.
   ```azurecli
   az login
   ```

1. 이 문서에서 사용할 Azure 리소스에 대한 리소스 그룹을 만듭니다.
   ```azurecli
   az group create --name=wingtiptoysresources --location=westus
   ```
   이 예제에서 `wingtiptoysresources`를 리소스 그룹의 고유 이름으로 바꿉니다.

1. Spring Boot 앱의 리소스 그룹에서 프라이빗 Azure Container Registry를 만듭니다. 
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoysresources --location westus --name wingtiptoysregistry --sku Basic
   ```
   이 예제에서 `wingtiptoysregistry`를 컨테이너 레지스트리의 고유 이름으로 바꿉니다.

1. 컨테이너 레지스트리에 대한 암호를 검색합니다.
   ```azurecli
   az acr credential show --name wingtiptoysregistry --resource-group wingtiptoysresources "passwords[0].value"
   ```
   Azure는 암호로 응답합니다. 예:
   ```json
   {
      "name": "password",
      "value": "xxxxxxxxxx"
   }
   ```

## <a name="add-your-azure-container-registry-and-azure-service-principal-to-your-maven-settings"></a>Maven 설정에 Azure Container Registry 및 Azure 서비스 주체 추가

1. 텍스트 편집기에서 Maven `settings.xml` 파일을 엽니다. 이 파일은 다음 예제와 같은 경로에 있을 수 있습니다.
   * `/etc/maven/settings.xml`
   * `%ProgramFiles%\apache-maven\3.5.0\conf\settings.xml`
   * `$HOME/.m2/settings.xml`

2. 이 문서의 이전 섹션에서 *settings.xml* 파일의 `<servers>` 컬렉션에 Azure Container Registry 액세스 설정을 추가합니다. 예:

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>xxxxxxxxxx</password>
      </server>
   </servers>
   ```
   위치:

   |   요소    |                                 Description                                  |
   |--------------|------------------------------------------------------------------------------|
   |    `<id>`    |         프라이빗 Azure Container Registry 이름을 포함합니다.          |
   | `<username>` |         프라이빗 Azure Container Registry 이름을 포함합니다.          |
   | `<password>` | 이 문서의 이전 섹션에서 검색된 암호를 포함합니다. |


3. *settings.xml* 파일의 `<servers>` 컬렉션에 이 문서의 이전 섹션에 있는 Azure 서비스 주체 설정을 추가합니다. 예:

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


4. *settings.xml* 파일을 저장하고 닫습니다.

## <a name="build-your-docker-container-image-and-push-it-to-your-azure-container-registry"></a>Docker 컨테이너 이미지 빌드 및 Azure Container Registry에 푸시

1. Spring Boot 애플리케이션의 완료된 프로젝트 디렉터리로 이동합니다. (예: "*C:\SpringBoot\gs-spring-boot-docker\complete*" 또는 " */users/robert/SpringBoot/gs-spring-boot-docker/complete*") 텍스트 편집기를 사용하여 *pom.xml* 파일을 엽니다.

2. *pom.xml* 파일의 `<properties>` 컬렉션을 이 자습서의 이전 섹션에서 사용한 Azure Container Registry의 로그인 서버 값으로 업데이트합니다. 예:

   ```xml
   <properties>
      <azure.containerRegistry>wingtiptoysregistry</azure.containerRegistry>
      <docker.image.prefix>${azure.containerRegistry}.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <maven.build.timestamp.format>yyyyMMddHHmmssSSS</maven.build.timestamp.format>
   </properties>
   ```
   위치:

   |           요소           |                                                                       Description                                                                       |
   |-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
   | `<azure.containerRegistry>` |                                              프라이빗 Azure Container Registry의 이름을 지정합니다.                                               |
   |   `<docker.image.prefix>`   | 프라이빗 컨테이너 레지스트리의 이름에 ".azurecr.io"를 추가하여 파생되는 프라이빗 Azure Container Registry의 URL을 추가합니다. |


3. *pom.xml* 파일에서 Docker 플러그 인의 `<plugin>`이 이 자습서의 이전 단계에서 로그인 서버 주소 및 레지스트리 이름에 대한 올바른 속성을 포함하는지 확인합니다. 다음은 그 예입니다. 

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <registryUrl>https://${docker.image.prefix}</registryUrl>
         <serverId>${azure.containerRegistry}</serverId>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
      </configuration>
   </plugin>
   ```
   위치:

   |     요소     |                                       Description                                       |
   |-----------------|-----------------------------------------------------------------------------------------|
   |  `<serverId>`   |  프라이빗 Azure Container Registry의 이름을 포함하는 속성을 지정합니다.   |
   | `<registryUrl>` | 프라이빗 Azure Container Registry의 URL을 포함하는 속성을 지정합니다. |


4. Spring Boot 애플리케이션의 완성된 프로젝트 디렉터리로 이동하고 다음 명령을 실행하여 애플리케이션을 다시 빌드하고 Azure Container Registry에 컨테이너를 푸시합니다.

   ```shell
   mvn package docker:build -DpushImage 
   ```

5. 선택 사항: [Azure Portal]로 이동하여 컨테이너 레지스트리에서 **gs-spring-boot-docker**라는 Docker 컨테이너 이미지가 있는지 확인합니다.

   ![Azure Portal에서 컨테이너 확인][CR01]

## <a name="customize-your-pomxml-then-build-and-deploy-your-container-to-azure"></a>pom.xml을 사용자 지정하고 컨테이너를 Azure에 빌드하고 배포합니다.

텍스트 편집기에서 Spring Boot 애플리케이션에 대한 `pom.xml` 파일을 연 다음, `azure-webapp-maven-plugin`에 대한 `<plugin>` 요소를 찾습니다. 이 요소는 다음 예제와 유사합니다.

   ```xml
   <plugin>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-webapp-maven-plugin</artifactId>
      <version>1.10.0</version>
      <configuration>
         <authentication>
            <serverId>azure-auth</serverId>
         </authentication>
         <resourceGroup>wingtiptoysresources</resourceGroup>
         <appName>maven-linux-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <containerSettings>
            <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
            <registryUrl>https://${docker.image.prefix}</registryUrl>
            <serverId>${azure.containerRegistry}</serverId>
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
| `<resourceGroup>` | 대상 리소스 그룹, 즉, 이 예에서 `wingtiptoysresources`을 지정합니다. 리소스 그룹이 아직 존재하지 않는 경우 배포 중에 만들어집니다. |
| `<appName>` | 웹앱에 대한 대상 이름을 지정합니다. 이 예제에서는 대상 이름은 `maven-linux-app-${maven.build.timestamp}`이며 이 예제에서 충돌을 피하기 위해 여기에 `${maven.build.timestamp}` 접미사가 추가됩니다. (타임스탬프는 선택 사항입니다. 앱 이름에 대한 고유한 문자열을 지정할 수 있습니다.) |
| `<region>` | 대상 지역을 지정합니다. 이 예제에서는 `westus`입니다. (전체 목록은 [Azure Web Apps의 Maven 플러그 인] 설명서에서 제공됩니다.) |
| `<containerSettings>` | 컨테이너의 이름 및 URL을 포함하는 속성을 지정합니다. |
| `<appSettings>` | Maven에 대한 고유한 설정을 지정하여 Azure에 웹앱을 배포할 때 사용합니다. 이 예제에서 `<property>` 요소는 앱에 대한 포트를 지정하는 자식 요소의 이름/값 쌍을 포함합니다. |

> [!NOTE]
>
> 이 예제에서 기본값의 포트를 변경하는 경우 포트 번호를 변경하도록 설정해야 합니다.
>

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

## <a name="next-steps"></a>다음 단계

Spring과 Azure에 대한 자세한 사항은 Azure의 Spring 설명서 센터를 참조합니다.

> [!div class="nextstepaction"]
> [Azure의 Spring](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>추가 리소스

이 문서에서 설명하는 다양한 기술에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Web Apps의 Maven 플러그 인]

* [Azure CLI에서 Azure에 로그인](/azure/xplat-cli-connect)

* [Azure CLI 2.0을 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Maven 설정 참조](https://maven.apache.org/settings.html)

* [Maven의 Docker 플러그 인]

Java와 함께 Azure를 사용하는 방법에 관한 자세한 정보는 [Java 개발자를 위한 Azure]와 [Azure DevOps 및 Java 사용하기]를 참조하세요.

<!-- URL List -->

[Azure CLI(명령줄 인터페이스)]: /cli/azure/overview
[Azure Container Service (AKS)]: https://azure.microsoft.com/services/container-service/
[Java 개발자를 위한 Azure]: /azure/developer/java/
[Azure Portal]: https://portal.azure.com/
[Azure Web Apps의 Maven 플러그 인]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Using a custom Docker image for Azure Web App on Linux]: /azure/app-service/containers/tutorial-custom-docker-image
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

[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->

[SB01]: media/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin/browse-sample-spring-boot-app.png
[CR01]: media/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin/browse-azure-portal-docker-container.png
[AP01]: media/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin/web-app-listed-azure-portal.png
[AP02]: media/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin/determine-web-app-url.png
[TL01]: media/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin/expose-docker-daemon-tcp-port.png
