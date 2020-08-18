---
title: 자습서 - Jenkins를 사용하여 GitHub에서 Azure App Service로 배포
description: GitHub 및 CD(지속적인 배포)에서 Java 웹앱용 Azure App Service까지의 CI(연속 통합)를 위해 Jenkins를 구성하는 방법을 알아봅니다.
keywords: Jenkins, Azure, DevOps, App Service
ms.topic: tutorial
ms.date: 08/10/2020
ms.openlocfilehash: 3961d413a573d416777f649cef44ceccdecb0b01
ms.sourcegitcommit: e792f681ab66c54e6fd0c7f3cb71816206216d72
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88075716"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-using-jenkins"></a>자습서: Jenkins를 사용하여 GitHub에서 Azure App Service로 배포

이 자습서에는 Jenkins에서 CI(지속적인 통합) 및 CD(연속 배포)를 설정하여 GitHub의 샘플 Java 웹앱을 [Linux의 Azure App Service](/azure/app-service/containers/app-service-linux-intro)로 배포합니다. GitHub로 커밋을 푸시하여 앱을 업데이트하면 Jenkins는 앱을 자동으로 빌드한 후 Azure App Service에 다시 게시합니다. 이 자습서의 샘플 앱은 [Spring Boot](https://projects.spring.io/spring-boot/) 프레임워크를 사용하여 개발했습니다. 

![GitHub에서 Azure App Service로 배포 개요](media/deploy-from-github-to-azure-app-service/azure-continuous-integration-deployment-overview.png)

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * GitHub에서 빌드하고, Azure App Service에 배포하고, 기타 작업을 수행할 수 있도록 Jenkins 플러그 인을 설치합니다.
> * 작업 복사본이 생성되도록 샘플 GitHub 리포지토리를 포크합니다.
> * Jenkins를 GitHub에 연결합니다.
> * Jenkins가 사용자의 자격 증명을 사용하지 않고도 Azure에 액세스할 수 있도록 Azure 서비스 주체를 만듭니다.
> * Jenkins에 서비스 주체를 추가합니다.
> * GitHub에서 앱을 업데이트할 때마다 샘플 앱을 빌드 및 배포하는 Jenkins 파이프라인을 만듭니다.
> * Jenkins 파이프라인용 빌드 및 배포 파일을 만듭니다.
> * 빌드 및 배포 스크립트에서 Jenkins 파이프라인을 가리킵니다.
> * 수동 빌드를 실행하여 Azure에 샘플 앱을 배포합니다.
> * GitHub에서 앱 업데이트를 푸시하여 빌드한 후 Azure에 다시 배포하도록 Jenkins를 트리거합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음 항목이 필요합니다.

* JDX(Java 개발 키트) 및 Maven 도구를 포함하는 [Jenkins](https://jenkins.io/) 서버가 Azure Linux VM에 설치되어야 합니다.

  Jenkins 서버가 없는 경우 Azure Portal에서 다음 단계를 완료합니다. [Azure Linux VM에 Jenkins 서버 만들기](/azure/jenkins/install-jenkins-solution-template)

* 샘플 Java 웹앱에 대해 작업 복사본을 얻기 위한(포크) [GitHub](https://github.com) 계정 

* 로컬 명령줄 또는 [Azure Cloud Shell](/azure/cloud-shell/overview)에서 실행할 수 있는 [Azure CLI](/cli/azure/install-azure-cli)

## <a name="install-jenkins-plug-ins"></a>Jenkins 플러그 인 설치

1. 이 위치에서 Jenkins 웹 콘솔에 로그인합니다.

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. Jenkins 주 페이지에서 **Jenkins 관리** > **플러그 인 관리**를 선택합니다.

   ![Jenkins 플러그 인 관리](media/deploy-from-github-to-azure-app-service/manage-plug-ins-for-azure.png)

1. **사용 가능** 탭에서 다음 플러그 인을 선택합니다.

   - [Azure App Service](https://plugins.jenkins.io/azure-app-service)
   - [GitHub 분기 원본](https://plugins.jenkins.io/github-branch-source)
   - Jenkins [Environment Injector 플러그 인](https://plugins.jenkins.io/envinject)
   - [Azure 자격 증명](https://plugins.jenkins.io/azure-credentials)

   이러한 플러그 인이 나타나지 않는 경우 **설치됨** 탭을 확인하여 아직 설치되지 않았는지 확인합니다.

1. 선택한 플러그 인을 설치하려면 **지금 다운로드하고 다시 시작한 후 설치**를 선택합니다.

1. 완료한 후에 Jenkins 메뉴에서 **Jenkins 관리**를 선택하여 이후 단계를 위한 Jenkins 관리 페이지로 돌아갑니다.

## <a name="fork-sample-github-repo"></a>샘플 GitHub 리포지토리 포크

1. [Spring Boot 샘플 앱을 위해 GitHub 리포지토리에 로그인](https://github.com/spring-guides/gs-spring-boot)합니다. 

1. GitHub의 오른쪽 위 모서리에서 **포크**를 선택합니다.

   ![GitHub에서 샘플 리포지토리 포크](media/deploy-from-github-to-azure-app-service/fork-github-repo.png)

1. 지시에 따라 GitHub 계정을 선택하고 분기를 완료합니다.

다음으로 GitHub 자격 증명을 사용하여 Jenkins를 설정합니다.

## <a name="connect-jenkins-to-github"></a>GitHub에 Jenkins 연결

Jenkins가 GitHub를 모니터링하고, GitHub 포크의 웹앱으로 새 커밋이 푸시될 때 대응하도록 하려면 Jenkins에서 [GitHub 웹후크](https://developer.github.com/webhooks/)를 사용하도록 설정합니다.

> [!NOTE]
> 
> 이러한 단계는 GitHub 사용자 이름 및 암호를 사용하여 Jenkins가 GitHub에서 작동하기 위한 개인 액세스 토큰 자격 증명을 만듭니다. 
> 그러나 GitHub 계정이 다단계 인증을 사용하는 경우 GitHub에서 토큰을 만들고, 해당 토큰을 대신 사용하도록 Jenkins를 설정합니다. 
> 자세한 내용은 [Jenkins GitHub 플러그 인](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin) 설명서를 참조하세요.

1. **Jenkins 관리** 페이지에서 **시스템 구성**을 선택합니다. 

   ![Jenkins에서 시스템 구성](media/deploy-from-github-to-azure-app-service/manage-jenkins-configure-system.png)

1. **GitHub** 섹션에서 GitHub 서버에 대한 세부 정보를 제공합니다. **GitHub 서버 추가** 목록에서 **GitHub 서버**를 선택합니다. 

   ![Jenkins에 GitHub 서버 추가](media/deploy-from-github-to-azure-app-service/add-GitHub-server.png)

1. **후크 관리** 속성을 선택하지 않은 경우 이 속성을 선택합니다. 기타 설정을 지정할 수 있도록 **고급**을 선택합니다. 

   ![GitHub 서버에 대한 고급 Jenkins 설정 지정](media/deploy-from-github-to-azure-app-service/advanced-GitHub-settings.png)

1. **추가 GitHub 작업 관리** 목록에서 **로그인 및 암호를 토큰으로 변환**을 선택합니다.

   ![로그인 및 암호를 GitHub에 대한 토큰으로 변환](media/deploy-from-github-to-azure-app-service/manage-additional-actions.png)

1. GitHub 사용자 이름 및 암호를 입력할 수 있도록 **로그인 및 암호에서**를 선택합니다. 완료되면 **토큰 자격 증명 만들기**를 선택합니다. 그러면 [GitHub PAT(개인용 액세스 토큰)](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)가 만들어집니다.   

   ![로그인 및 암호에서 GitHub PAT 만들기](media/deploy-from-github-to-azure-app-service/create-github-token-credentials.png)

1. **GitHub 서버** 섹션의 **자격 증명** 목록에서 새 토큰을 선택합니다. **테스트 연결**을 선택하여 인증이 작동하는지 확인합니다.

   ![새 PAT를 사용하여 GitHub 서버에 대한 연결 확인](media/deploy-from-github-to-azure-app-service/check-github-connection.png)

다음으로, Jenkins가 Azure 리소스를 인증하고 액세스하는 데 사용하는 Azure 서비스 주체를 만듭니다.

## <a name="create-service-principal"></a>서비스 주체 만들기

이후 섹션에서는 GitHub에서 앱을 빌드하고 Azure App Service에 앱을 배포하는 Jenkins 파이프라인 작업을 만듭니다. 자격 증명을 입력하지 않고 Jenkins가 Azure에 액세스하도록 하려면 [서비스 주체](/active-directory/develop/app-objects-and-service-principals)가 필요합니다. 이 문서에서 설명하는 서비스 주체를 이미 사용할 수 있는 경우 이 섹션을 건너뛸 수 있습니다.

서비스 주체를 만들려면 Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?#az-ad-sp-create-for-rbac) 명령을 실행합니다.

```azurecli-interactive
az ad sp create-for-rbac
```

**참고**:

- 성공적으로 완료되면 `az ad sp create-for-rbac`에서 여러 값을 표시합니다. `name`, `password` 및 `tenant` 값은 다음 단계에서 사용됩니다.
- 기본적으로 서비스 주체는 Azure 계정에 대한 전체 읽기 및 쓰기 권한이 있는 **기여자** 역할을 사용하여 생성됩니다. RBAC(역할 기반 액세스 제어)와 역할에 대한 자세한 내용은 [RBAC: 기본 제공 역할](/azure/active-directory/role-based-access-built-in-roles)을 참조하세요.
- 분실한 암호는 복구할 수 없습니다. 따라서 암호를 안전한 장소에 저장해야 합니다. 암호를 잊어버린 경우 [서비스 주체 자격 증명을 다시 설정](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials)해야 합니다.


## <a name="add-service-principal-to-jenkins"></a>Jenkins에 서비스 주체 추가

1. Jenkins 주 페이지에서 **자격 증명** > **시스템**을 선택합니다. 

1. **시스템** 페이지의 **도메인**에서 **전역 자격 증명(무제한)** 을 선택합니다.

1. 왼쪽 메뉴에서 **자격 증명 추가**를 선택합니다.

1. **종류** 목록에서 **Azure 서비스 주체**를 선택합니다.

1. 이 단계에서는 표에 설명된 속성에서 서비스 주체 및 Azure 구독에 대한 정보를 제공합니다.

   ![Azure 서비스 주체 자격 증명 추가](media/deploy-from-github-to-azure-app-service/add-service-principal-credentials.png)

   | 속성 | 값 | 설명 | 
   |----------|-------|-------------| 
   | **구독 ID** | <*yourAzureSubscription-ID*> | Azure 구독에 대한 GUID 값입니다. <p>**팁**: Azure 구독 ID를 모르는 경우 명령줄 또는 Cloud Shell에서 이 Azure CLI 명령을 실행한 후 `id` GUID 값을 사용합니다. <p>`az account list` | 
   | **클라이언트 ID** | <*yourAzureServicePrincipal-ID*> | Azure 서비스 주체에 대해 이전에 생성된 `appId` GUID 값입니다. | 
   | **클라이언트 암호** | <*yourSecurePassword*> | Azure 서비스 주체에 대해 제공한 `password` 값 또는 "비밀"입니다. | 
   | **테넌트 ID** | <*yourAzureActiveDirectoryTenant-ID*> | Azure Active Directory 테넌트에 대한 `tenant` GUID 값입니다. | 
   | **ID** | <*yourAzureServicePrincipalName*> | Azure 서비스 주체에 대한 `displayName` 값입니다. | 

1. 서비스 주체가 작동하는지 확인하려면 **서비스 주체 확인**을 선택합니다. 완료되면 **확인**을 선택합니다.

다음으로, 앱을 빌드하고 배포하는 Jenkins 파이프라인을 만듭니다.

## <a name="create-jenkins-pipeline"></a>Jenkins 파이프라인 만들기

Jenkins에서 앱을 빌드하고 배포하기 위한 파이프라인 작업을 만듭니다.

1. Jenkins 홈페이지로 돌아간 후 **새 항목**을 선택합니다. 

   ![Jenkins 파이프라인 만들기](media/deploy-from-github-to-azure-app-service/jenkins-select-new-item.png)

1. 파이프라인 작업의 이름(예: "My-Java-Web-App")을 제공하고 **파이프라인**을 선택합니다. 아래쪽에서 **확인**을 선택합니다.  

   ![Jenkins 파이프라인 작업 이름 지정](media/deploy-from-github-to-azure-app-service/jenkins-select-pipeline.png)

1. 사용자 고유의 자격 증명을 사용하지 않아도 Jenkins에서 Azure에 배포할 수 있도록 서비스 주체를 사용하여 Jenkins를 설정합니다.

   1. **일반** 탭에서 **Prepare an environment for the run**(실행 환경 준비)을 선택합니다. 

   1. 표시되는 **속성 콘텐츠** 상자에서 이러한 환경 변수 및 해당 값을 추가합니다. 

      ```ini
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![실행을 위한 환경 준비 및 환경 변수 설정](media/deploy-from-github-to-azure-app-service/prepare-environment-for-jenkins-run.png)

1. 완료되면 **저장**을 선택합니다.

다음으로, Jenkins에 대한 빌드 및 배포 스크립트를 만듭니다.

## <a name="create-build-and-deployment-files"></a>빌드 및 배포 파일 만들기

이제 Jenkins에서 앱을 빌드하고 배포하는 데 사용하는 파일을 만듭니다.

1. GitHub 포크의 `src/main/resources/` 폴더에서 이름이 `web.config`이고 이 XML을 포함하는 이 앱 구성 파일을 만듭니다. 단, `$(JAR_FILE_NAME)`을 `gs-spring-boot-0.1.0.jar`로 바꿉니다.

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
      <system.webServer>
         <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
         </handlers>
         <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\${JAR_FILE_NAME}&quot;"></httpPlatform>
      </system.webServer>
   </configuration>
   ```

1. GitHub 포크의 루트 폴더에 이름이 `Jenkinsfile`이고 이 텍스트([GitHub의 원본](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se))를 포함하는 이 빌드 및 배포 스크립트를 만듭니다.

   ```groovy
   node {
      stage('init') {
         checkout scm
      }
      stage('build') {
         sh '''
            mvn clean package
            cd target
            cp ../src/main/resources/web.config web.config
            cp todo-app-java-on-azure-1.0-SNAPSHOT.jar app.jar 
            zip todo.zip app.jar web.config
         '''
      }
      stage('deploy') {
         azureWebAppPublish azureCredentialsId: env.AZURE_CRED_ID,
         resourceGroup: env.RES_GROUP, appName: env.WEB_APP, filePath: "**/todo.zip"
      }
   }
   ```

1. `web.config` 및 `Jenkinsfile`을 둘 다 GitHub 포크에 커밋하고 변경 내용을 푸시합니다.

## <a name="point-pipeline-at-script"></a>스크립트에서 파이프라인 지정

이제 Jenkins에서 사용하도록 하려는 빌드 및 배포 스크립트를 지정합니다.

1. Jenkins에서 이전에 만든 파이프라인 작업을 선택합니다. 

   ![웹앱에 대한 Jenkins 파이프라인 작업 선택](media/deploy-from-github-to-azure-app-service/select-pipeline-job.png)

1. 왼쪽 메뉴에서 **구성**을 선택합니다.

1. **파이프라인** 탭의 **정의** 목록에서 **SCM의 파이프라인 스크립트**를 선택합니다.

   1. 나타나는 **SCM** 상자에서 원본 제어로 **Git**를 선택합니다. 

   1. **리포지토리** 섹션에서 **리포지토리 URL**로 GitHub 포크의 URL을 입력합니다. 예를 들면 다음과 같습니다. 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. **자격 증명**에 대해 이전에 만든 GitHub 개인용 액세스 토큰을 선택합니다.

   1. **스크립트 경로** 상자에서 "Jenkinsfile" 스크립트의 경로를 추가합니다.

   완료되면 파이프라인 정의가 다음 예제와 비슷합니다. 

   ![스크립트에서 Jenkins 파이프라인 가리키기](media/deploy-from-github-to-azure-app-service/set-up-jenkins-github.png)

1. 완료되면 **저장**을 선택합니다.

다음으로, 앱을 빌드하고 Azure App Service에 배포합니다. 

## <a name="build-and-deploy-to-azure"></a>빌드 및 Azure에 배포

1. 명령줄 또는 Azure Cloud Shell에서 Azure CLI를 사용하여 [Linux의 Azure App Service 웹앱](/azure/app-service/containers/app-service-linux-intro)을 만듭니다. 그러면 Jenkins는 빌드가 완료된 후 웹앱을 배포합니다. 웹앱 이름이 고유해야 합니다.

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   이러한 Azure CLI 명령에 대한 자세한 내용은 다음 페이지를 참조하세요.

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. Jenkins에서 파이프라인 작업을 선택하고 **지금 빌드**를 선택합니다.

   빌드가 완료되면 Jenkins는 앱을 배포하며 이 앱은 현재 Azure의 게시 URL에 라이브되어 있습니다. 예를 들면 다음과 같습니다. 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Azure에서 배포된 앱 보기](media/deploy-from-github-to-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>변경 내용 푸시 및 재배포

1. 웹 브라우저에서 웹앱 GitHub 포크의 이 위치로 이동합니다.

   `complete/src/main/java/Hello/Application.java`
   
1. GitHub의 오른쪽 위 모서리에서 **이 파일 편집**을 선택합니다.

1. `commandLineRunner()` 메서드를 이와 같이 변경하고 리포지토리의 `master` 분기에 대한 변경 내용을 커밋합니다. `master` 분기의 이 커밋은 Jenkins에서 빌드를 시작합니다. 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. 빌드가 완료되고 Jenkins가 Azure에 다시 배포하면 업데이트가 표시되도록 앱을 새로 고칩니다.

   ![Azure에서 배포된 앱 보기](media/deploy-from-github-to-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>Jenkins 플러그 인 문제 해결

Jenkins 플러그 인에서 버그가 발생하면 [Jenkins JIRA](https://issues.jenkins-ci.org/)에서 특정 구성 요소에 대한 문제를 제출해 주세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure VM을 빌드 에이전트로 사용](/azure/jenkins/jenkins-azure-vm-agents)
