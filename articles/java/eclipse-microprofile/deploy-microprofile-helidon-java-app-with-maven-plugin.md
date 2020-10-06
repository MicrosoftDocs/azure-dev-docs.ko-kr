---
title: Maven을 사용하여 Azure App Service에 Helidon Web App 배포
description: Azure Web App용 Maven 플러그 인을 사용하여 App Service on Linux에 Helidon 앱을 배포하는 방법을 알아봅니다.
services: app-service
documentationcenter: java
ms.date: 06/10/2020
ms.service: app-service
ms.topic: article
ms.custom: ''
ms.openlocfilehash: d41d2b00a36439f0368ca3aa295aa4e13e66846b
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401904"
---
# <a name="deploy-a-helidon-web-app-to-azure-app-service-with-maven"></a>Maven을 사용하여 Azure App Service에 Helidon Web App 배포

이 빠른 시작에서는 [Azure App Service Web Apps용 Maven 플러그 인](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)을 사용하여 Helidon 애플리케이션을 [Azure App Service on Linux](/azure/app-service/containers/)에 배포합니다. 앱의 의존성, 런타임 및 구성을 배포 가능한 단일 아티팩트에 통합하려면 [Tomcat 및 WAR 파일](/azure/app-service/containers/quickstart-java)에 대해 Java SE 배포를 선택하는 것이 좋습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

* [Azure CLI](/cli/azure/), 로컬로 또는 [Azure Cloud Shell](https://shell.azure.com)을 통해.
* 지원되는 JDK(Java Development Kit) Azure에서 개발하는 경우 사용할 수 있는 JDK에 대한 자세한 내용은 <https://aka.ms/azure-jdks>를 참조하세요.
* Apache [Maven](https://maven.apache.org/), 버전 3

## <a name="install-and-sign-in-to-azure-cli"></a>Azure CLI 설치 및 로그인

Maven 플러그 인이 Helidon 애플리케이션을 배포하도록 하는 가장 간단하고 쉬운 방법은 [ Azure CLI](/cli/azure/)를 사용하는 것입니다.

Azure CLI를 사용하여 Azure 계정에 로그인합니다.

```azurecli
az login
```

지시에 따라 로그인 프로세스를 완료합니다.

## <a name="create-sample-app-from-microprofile-starter"></a>MicroProfile Starter로 샘플 앱 만들기

이 섹션에서는 Helidon 애플리케이션을 만들고 로컬에서 테스트합니다.

1. 웹 브라우저를 열고 [MicroProfile Starter](https://start.microprofile.io/) 사이트에 액세스합니다.

   ![MicroProfile Starter](./media/helidon/microprofile-starter-helidon.png)

1. 다음과 같은 필드를 입력하거나 선택합니다.  

   |  필드  | 값  |
   | ---- | ---- |
   |  groupId  |  com.microsoft.azure.samples.helidon  |
   |  artifactId  |  helidon-hello-azure  |
   |  MicroProfile 버전  |  MP 3.2  |
   |  Java SE 버전  |  Java 11  |
   |  MicroProfile 런타임  |  Helidon  |
   |  사양에 대한 예제  |  메트릭, OpenAPI  |

1. **다운로드** 단추를 선택하여 프로젝트를 다운로드합니다.

1. 보관 파일의 압축을 풉니다. 예를 들면 다음과 같습니다.

   ```bash
   unzip helidon-hello-azure.zip
   ```

1. 또는 다음 명령을 사용하여 프로젝트를 만들 수 있습니다.

   ```bash
   mvn -U archetype:generate -DinteractiveMode=false \
       -DarchetypeGroupId=io.helidon.archetypes \
       -DarchetypeArtifactId=helidon-quickstart-se \
       -DarchetypeVersion=2.0.0 \
       -DgroupId=com.microsoft.azure.samples.helidon \
       -DartifactId=helidon-hello-azure \
       -Dpackage=com.microsoft.azure.samples.helidon
   ```

1. 디렉터리를 완료된 프로젝트로 변경합니다. 예:

   ```bash
   cd helidon-hello-azure/
   ```

1. Maven을 사용하여 JAR 파일을 빌드합니다. 예:

   ```bash
   mvn clean package
   ```

1. 웹앱을 만들면 Maven을 사용하여 웹앱을 시작합니다. 예:

   ```bash
   java -jar target/helidon-hello-azure.jar
   ```

1. 웹 브라우저를 사용하여 로컬로 이동하여 웹앱을 테스트합니다. 예를 들어, curl을 사용할 수 있는 경우 다음 명령을 사용할 수 있습니다.

   ```bash
   curl http://localhost:8080/greet
   ```

1. 다음 메시지가 표시되어야 합니다. **Hello World**

## <a name="configure-maven-plugin-for-azure-app-service"></a>Azure App Service용 Maven 플러그인 구성

이 섹션에서는 Maven이 Azure App Service on Linux에 앱을 배포할 수 있도록 Helidon 프로젝트 *pom.xml* 파일을 구성합니다.

1. 코드 편집기에서 *pom.xml* 파일을 엽니다.

2. `<build>`pom.xml*파일의* 섹션에서 `<plugins>` 태그 안에 다음 `<plugin>` 항목을 삽입하세요.

   ```xml
   <build>
     <finalName>helidon-hello-azure</finalName>
     <plugins>
       <plugin>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>azure-webapp-maven-plugin</artifactId>
         <version>1.10.0</version>
       </plugin>
     </plugins>
   </build>
   ```

3. 그런 다음, 배포를 구성하고 명령 프롬프트에서 다음 maven 명령을 실행하고 **번호**를 사용하여 프롬프트에서 다음 옵션을 선택할 수 있습니다.

   ```bash
   mvn azure-webapp:config
   ```

   옵션 매개 변수:  

   |  입력 필드  |  값 입력/선택  |
   | ---- | ---- |
   |  OS 값 정의(기본값: Linux):  | 1. linux  |
   |  javaVersion의 값 정의(기본값: Java 8):   | 1. Java 11  |
   |  확인(Y/N) | y |

   다음 명령을 사용하여 구성할 수 있습니다.

   ```bash
   mvn azure-webapp:config
   [INFO] Scanning for projects...
   [INFO]
   [INFO] ------< com.microsoft.azure.samples.helidon:helidon-hello-azure >-------
   [INFO] Building myproject 1.0-SNAPSHOT
   [INFO] --------------------------------[ jar ]---------------------------------
   [INFO] 
   [INFO] --- azure-webapp-maven-plugin:1.10.0:config (default-cli) @ helidon-hello-azure ---
   Define value for OS(Default: Linux): 
   1. linux [*]
   2. windows
   3. docker
   Enter index to use: 1
   Define value for javaVersion(Default: Java 8): 
   1. Java 11
   2. Java 8 [*]
   Enter index to use: 1
   Please confirm webapp properties
   AppName : helidon-hello-azure-1600998900939
   ResourceGroup : helidon-hello-azure-1600998900939-rg
   Region : westeurope
   PricingTier : PremiumV2_P1v2
   OS : Linux
   RuntimeStack : JAVA 11-java11
   Deploy to slot : false
   Confirm (Y/N)? : y
   [INFO] Saving configuration to pom.
   [INFO] ------------------------------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ------------------------------------------------------------------------
   [INFO] Total time:  02:44 min
   [INFO] Finished at: 2020-09-25T10:57:35+09:00
   [INFO] ------------------------------------------------------------------------
   ```

4. `PORT`, `WEBSITES_PORT` 및 `WEBSITES_CONTAINER_START_TIME_LIMIT`의 `<configuration>` 섹션에 `<appSettings>` 섹션을 추가합니다. 배포의 리소스에 `<include>/libs/*.jar</include>`를 추가합니다.

   ```xml
   <plugin>
     <groupId>com.microsoft.azure</groupId>  
     <artifactId>azure-webapp-maven-plugin</artifactId>  
     <version>1.10.0</version>  
     <configuration>
       <schemaVersion>V2</schemaVersion>
       <resourceGroup>microprofile</resourceGroup>
       <appName>helidon-hello-azure-1591663020899</appName>
       <pricingTier>P1v2</pricingTier>
       <region>japaneast</region>
       <runtime>
         <os>linux</os>
         <javaVersion>java11</javaVersion>
         <webContainer>java11</webContainer>
       </runtime>
       <appSettings>
         <property>
           <name>PORT</name>
           <value>8080</value>
         </property>
         <property>
           <name>WEBSITES_PORT</name>
           <value>8080</value>
         </property>
         <property>
           <name>WEBSITES_CONTAINER_START_TIME_LIMIT</name>
           <value>600</value>
         </property>
       </appSettings>
       <deployment>
         <resources>
           <resource>
             <directory>${project.basedir}/target</directory>
             <includes>
               <include>*.jar</include>
               <include>/libs/*.jar</include>
             </includes>
           </resource>
         </resources>
       </deployment>
     </configuration>
   ```

## <a name="deploy-the-app-to-azure"></a>Azure에 앱 배포

이 문서의 이전 섹션에서 설정을 모두 구성했으면 웹앱을 Azure에 배포할 준비가 되었습니다. 이렇게 하려면 다음 단계를 수행합니다.

1. *pom.xml* 파일을 변경한 경우 이전에 사용하던 명령 프롬프트 또는 터미널 창에서 Maven를 사용하여 JAR 파일을 다시 작성합니다. 예:

   ```bash
   mvn clean package
   ```

1. Maven을 사용하여 Azure에 웹앱을 배포합니다. 예:

   ```bash
   mvn azure-webapp:deploy
   ```

Maven은 Azure에 웹앱을 배포합니다. 웹앱이나 웹앱 플랜이 아직 없는 경우 생성됩니다. 출력에 표시된 URL에서 웹앱이 표시되기까지 몇 분 정도 걸릴 수 있습니다. 웹 브라우저에서 URL로 이동합니다.  다음과 같은 메시지가 표시되어야 합니다. **Hello World**

웹이 배포되면 [Azure Portal]을 통해 관리할 수 있습니다.

* 웹앱은 **App Services**에 나열됩니다.

   ![Azure Portal App Services에 나열된 웹앱](./media/helidon/azure-portal-app-service-screen.png)

* 웹앱에 대한 **개요**에서 `Browse` 단추를 클릭하여 웹앱에 액세스할 수 있습니다.

   ![Azure Portal App Services에서 웹앱 URL 찾기](./media/helidon/azure-portal-app-service-overview.png)

`localhost` 대신 포털의 웹앱 URL을 사용하여 이전과 동일한 cURL 명령(`/data/hello`)을 사용하여 배포가 성공적으로 완료되었는지 확인합니다. 다음 메시지가 표시되어야 합니다. **Hello World**

## <a name="confirm-the-log-stream-from-running-app-service"></a>실행 중인 App Service에서 로그 스트림 확인

실행 중인 App Service의 로그를 보거나 "추적"할 수 있습니다. 사이트 코드에서 `console.log`에 대한 모든 호출은 터미널에 표시됩니다.

```azurecli
az webapp log tail -g microprofile -n helidon-hello-azure-1600998900939
```

![로그 출력을 표시하는 터미널 창](./media/helidon/azure-cli-app-service-log-stream.png)

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

* Azure Portal의 왼쪽 메뉴에서 리소스 그룹을 선택합니다.
* **이름으로 필터링** 필드에 **microprofile**을 입력합니다. 이 자습서에서 만든 리소스 그룹에는 이 접두사가 있어야 합니다.
* 이 자습서에서 만든 리소스 그룹을 선택합니다.
* 위쪽 메뉴에서 리소스 그룹 삭제를 선택합니다.

## <a name="next-steps"></a>다음 단계

MicroProfile과 Azure에 대해 자세히 알아보려면 Azure의 MicroProfile 설명서 센터로 이동합니다.

> [!div class="nextstepaction"]
> [Azure의 MicroProfile](/azure/developer/java/eclipse-microprofile)

### <a name="additional-resources"></a>추가 리소스

이 문서에서 설명하는 다양한 기술에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Web Apps의 Maven 플러그 인]

* [Azure CLI 2.0을 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Maven 설정 참조](https://maven.apache.org/settings.html)

<!-- URL List -->

[Azure Command-Line Interface (CLI)]: /cli/azure/overview
[Azure for Java Developers]: /azure/developer/java/
[Azure Portal]: https://portal.azure.com/
[free Azure account]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Working with Azure DevOps and Java]: /azure/devops/
[Maven]: http://maven.apache.org/
[MSDN subscriber benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Azure Web Apps의 Maven 플러그 인]: /java/api/overview/azure/maven/azure-webapp-maven-plugin/readme

[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->

[AP01]: media/deploy-spring-boot-java-app-with-maven-plugin/web-app-listed-azure-portal.png
[AP02]: media/deploy-spring-boot-java-app-with-maven-plugin/determine-web-app-url.png
