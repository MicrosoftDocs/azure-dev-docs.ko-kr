---
title: Java를 사용하여 웹앱 배포 구성 | Microsoft Docs
description: Java용 Azure SDK를 사용하여 Git 또는 FTP Azure App Service 배포를 구성하는 Java 샘플 코드
author: rloutlaw
ms.assetid: 833e9c78-1e50-4c23-a611-f73a2f0c2983
ms.topic: article
ms.date: 03/30/2017
ms.custom: devx-track-java
ms.openlocfilehash: edc981189a6697ea487e44e9619b30391bc5d197
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831779"
---
# <a name="configure-azure-app-service-deployment-sources-from-your-java-applications"></a>Java 애플리케이션에서 Azure App Service 배포 원본 구성

[이 샘플 ](https://github.com/Azure-Samples/compute-java-create-virtual-machines-across-regions-in-parallel)에서는 각각 서로 다른 배포 원본을 사용하는 단일 [ Azure App Service ](/azure/app-service/) 계획에서 네 가지 애플리케이션에 코드를 배포합니다.

## <a name="run-the-sample"></a>샘플 실행

[인증 파일](/azure/java/java-sdk-azure-authenticate#mgmt-file)을 만들고 컴퓨터에서 파일의 전체 경로가 포함된 `AZURE_AUTH_LOCATION` 환경 변수를 설정합니다. 다음을 실행합니다.

```
git clone https://github.com/Azure-Samples/app-service-java-configure-deployment-sources-for-web-apps.git
cd app-service-java-configure-deployment-sources-for-web-apps
mvn clean compile exec:java
```

[GitHub에서 전체 샘플 코드(영문)](https://github.com/Azure-Samples/app-service-java-configure-deployment-sources-for-web-apps/blob/master/src/main/java/com/microsoft/azure/management/appservice/samples/ManageWebAppSourceControl.java)를 봅니다.

## <a name="authenticate-with-azure"></a>Azure를 사용하여 인증

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="create-a-app-service-app-running-apache-tomcat"></a>Apache Tomcat을 실행하는 App Service 앱 만들기

```java
// create a new Standard app service plan and create a single Java 8/Tomcat 8 app in it
WebApp app1 = azure.webApps().define(app1Name)
             .withNewResourceGroup(rgName)
             .withNewAppServicePlan(planName)
             .withRegion(Region.US_WEST)
             .withPricingTier(AppServicePricingTier.STANDARD_S1)
             .withJavaVersion(JavaVersion.JAVA_8_NEWEST)
             .withWebContainer(WebContainer.TOMCAT_8_0_NEWEST)
             .create();
```

`withJavaVersion()` 및 `withWebContainer()`는 Tomcat 8을 사용하여 HTTP 요청을 처리하도록 App Service를 구성합니다.

## <a name="deploy-a-java-application-using-ftp"></a>FTP를 사용하여 Java 애플리케이션 배포
```java
// pass the PublishingProfile that contains FTP information to a helper method 
uploadFileToFtp(app1.getPublishingProfile(), "helloworld.war", 
      ManageWebAppSourceControl.class.getResourceAsStream("/helloworld.war"));

// Use the FTP classes in the Apache Commons library to connect to Azure using 
// the information from the PublishingProfile
private static void uploadFileToFtp(PublishingProfile profile, String fileName, InputStream file) throws Exception {
        FTPClient ftpClient = new FTPClient();
        String[] ftpUrlSegments = profile.ftpUrl().split("/", 2);
        String server = ftpUrlSegments[0];
        // Tomcat will deploy WAR files uploaded to this directory.
        String path = "./site/wwwroot/webapps"; 

        // FTP the build WAR to Azure
        ftpClient.connect(server);
        ftpClient.login(profile.ftpUsername(), profile.ftpPassword());
        ftpClient.setFileType(FTP.BINARY_FILE_TYPE);
        ftpClient.changeWorkingDirectory(path);
        ftpClient.storeFile(fileName, file);
        ftpClient.disconnect();
}
```

이 코드는 WAR 파일을 `/site/wwwroot/webapps` 디렉터리에 업로드합니다. Tomcat은 기본적으로 App Service에서 이 디렉터리에 배치된 WAR 파일을 배포합니다.

## <a name="deploy-a-java-application-from-a-local-git-repo"></a>로컬 Git 리포지토리에서 Java 애플리케이션 배포

```java
// get the publishing profile from the App Service webapp
PublishingProfile profile = app2.getPublishingProfile();

// create a new Git repo in the sample directory under src/main/resources 
Git git = Git
    .init()
    .setDirectory(new File(ManageWebAppSourceControl.class.getResource("/azure-samples-appservice-helloworld/").getPath()))
    .call();
git.add().addFilepattern(".").call();
// add the files in the sample app to an initial commit
git.commit().setMessage("Initial commit").call(); 

// push the commit using the Azure Git remote URL and credentials in the publishing profile
PushCommand command = git.push();
command.setRemote(profile.gitUrl()); 
command.setCredentialsProvider(new UsernamePasswordCredentialsProvider(profile.gitUsername(), profile.gitPassword()));
command.setRefSpecs(new RefSpec("master:master")); 
command.setForce(true);
command.call();
```      

이 코드는 [JGit](https://eclipse.org/jgit/) 라이브러리를 사용하여 `src/main/resources/azure-samples-appservice-helloworld` 폴더에 새 Git 리포지토리를 만듭니다. 그런 다음 샘플에서 폴더의 모든 파일을 초기 커밋에 추가하고 웹앱의 `PublishingProfile`에 있는 Git 배포 정보를 사용하여 커밋을 Azure로 푸시합니다. 

>[!NOTE]
> 리포지토리에 있는 파일의 레이아웃은 Azure App Service에서 `/site/wwwroot/` 디렉터리 아래에 배포된 파일을 원하는 방식과 정확히 일치해야 합니다.

## <a name="deploy-an-application-from-a-public-git-repo"></a>공용 Git 리포지토리에서 애플리케이션 배포

```java
// deploy a .NET sample app from a public GitHub repo into a new webapp
WebApp app3 = azure.webApps().define(app3Name)
                .withNewResourceGroup(rgName)
                .withExistingAppServicePlan(plan)
                .defineSourceControl()
                .withPublicGitRepository(
                   "https://github.com/Azure-Samples/app-service-web-dotnet-get-started")
                .withBranch("master")
                .attach()
                .create();
 ```

 App Service 런타임에서 리포지토리의 `master` 분기에 있는 최신 코드를 사용하여 .NET 프로젝트를 자동으로 빌드하고 배포합니다.

## <a name="continuous-deployment-from-a-github-repo"></a>GitHub 리포지토리에서 지속적인 배포

```java
// deploy the application whenever you push a new commit or merge a pull request into your master branch
WebApp app4 = azure.webApps()
                    .define(app4Name)
                    .withExistingResourceGroup(rgName)
                    .withExistingAppServicePlan(plan)
                    // Uncomment the following lines to turn on continuous deployment scenario
                    //.defineSourceControl()
                    //    .withContinuouslyIntegratedGitHubRepository("username", "reponame")
                    //    .withBranch("master")
                    //    .withGitHubAccessToken("YOUR GITHUB PERSONAL TOKEN")
                    //    .attach()
                    .create();
```  

`username` 및 `reponame` 값은 GitHub에서 사용되는 값입니다. 리포지토리 읽기 권한이 있는 [GitHub 개인용 액세스 토큰을 만들어](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)`withGitHubAccessToken`에 전달합니다. 


## <a name="sample-explanation"></a>샘플 설명

샘플에서는 새로 만든 [표준](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview) App Service 계획에서 실행되는 Java 8 및 Tomcat 8을 사용하여 첫 번째 애플리케이션을 만듭니다. 그런 다음 코드에서 `PublishingProfile` 개체의 정보를 사용하여 WAR 파일을 FTP로 보내고, Tomcat에서 이를 배포합니다.

두 번째 애플리케이션은 첫 번째 애플리케이션과 동일한 계획에서 사용되며 Java 8/Tomcat 8 애플리케이션으로도 구성됩니다. JGit 라이브러리에서 App Service에 매핑되는 디렉터리 구조에 압축되지 않은 Java 웹 애플리케이션을 포함하는 폴더에 새 Git 리포지토리를 만듭니다. 새 커밋에서 새 Git 리포지토리에 폴더의 파일들을 추가하고, Git에서는 웹앱의 `PublishingProfile`에서 제공하는 원격 URL과 사용자 이름/암호를 사용하여 커밋을 Azure로 푸시합니다.

세 번째 애플리케이션은 Java 및 Tomcat용으로 구성되지 않습니다. 대신 공용 GitHub 리포지토리의 .NET 샘플은 원본에서 직접 배포됩니다.

네 번째 애플리케이션은 변경 내용을 푸시하거나 GitHub 리포지토리의 마스터 분기로 끌어오기 요청을 병합할 때마다 마스터 분기에 코드를 배포합니다.

| 샘플에 사용되는 클래스 | 메모
|-------|-------|
| [WebApp](/java/api/com.microsoft.azure.management.appservice.webapp) | `azure.webApps().define()....create()` 흐름 체인에서 만들어집니다. App Service 웹앱과 앱에 필요한 모든 리소스를 만듭니다. 대부분의 메서드는 구성 세부 정보에 대한 개체를 쿼리하지만, `restart()`와 같은 동사 메서드는 웹앱의 상태를 변경합니다.
| [WebContainer](/java/api/com.microsoft.azure.management.appservice.webcontainer) | Java 웹 컨테이너를 실행하는 WebApp을 정의할 때 `withWebContainer()`에 대한 매개 변수로 사용되는 고정 공용 필드가 있는 클래스입니다. Jetty 및 Tomcat 버전을 모두 선택할 수 있습니다.
| [PublishingProfile](/java/api/com.microsoft.azure.management.appservice.publishingprofile) | `getPublishingProfile()` 메서드를 사용하여 WebApp 개체를 통해 얻습니다. 배포 사용자 이름 및 암호(Azure 계정 또는 서비스 사용자 자격 증명과는 별개임)를 포함한 FTP 및 Git 배포 정보가 포함되어 있습니다.
| [AppServicePlan](/java/api/com.microsoft.azure.management.appservice.appserviceplan) | `azure.appServices().appServicePlans().getByResourceGroup()`에서 반환됩니다. 메서드는 계획에서 실행되는 웹앱의 용량, 계층 및 수를 확인하는 데 사용할 수 있습니다.
| [AppServicePricingTier](/java/api/com.microsoft.azure.management.appservice.PricingTier) | App Service 계층을 나타내는 정적 공용 필드가 있는 클래스입니다. `withPricingTier()`를 사용하여 앱을 만드는 동안 또는 `azure.appServices().appServicePlans().define()`을 통해 계획을 정의할 때 직접 계획 계층 인라인을 정의하는 데 사용됩니다.
| [JavaVersion](/java/api/com.microsoft.azure.management.appservice.javaversion) | App Service에서 지원하는 Java 버전을 나타내는 정적 공용 필드가 있는 클래스입니다. 새 웹앱을 만들 때 `define()...create()` 체인에서 `withJavaVersion()`과 함께 사용됩니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next-steps](includes/java-next-steps.md)]