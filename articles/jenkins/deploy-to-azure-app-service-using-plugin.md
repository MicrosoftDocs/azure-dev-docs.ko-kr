---
title: 자습서 - Jenkins 플러그 인을 사용하여 Azure App Service에 배포
description: Azure App Service Jenkins 플러그 인을 사용하여 Jenkins에서 Azure에 Java 웹앱을 배포하는 방법을 알아봅니다.
keywords: Jenkins, Azure, DevOps, App Service
ms.topic: tutorial
ms.date: 08/10/2018
ms.custom: devx-track-jenkins
ms.openlocfilehash: 1be8e126a5fee2dd83682ece559998a45645ac36
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831689"
---
# <a name="tutorial-deploy-to-azure-app-service-using-the-jenkins-plugin"></a>자습서: Jenkins 플러그 인을 사용하여 Azure App Service에 배포

Azure에 Java 웹앱을 배포하려면 [Jenkins 파이프라인](deploy-to-azure-app-service-using-azure-cli.md)의 Azure CLI를 사용하거나 [Azure App Service Jenkins 플러그 인](https://plugins.jenkins.io/azure-app-service)을 사용할 수 있습니다. Jenkins 플러그인 버전 1.0은 Azure App Service의 Web Apps 기능을 사용하여 지속적인 배포를 지원합니다.

- 파일 업로드
- Linux의 Web Apps용 Docker.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 파일 업로드를 통해 Web Apps를 배포하도록 Jenkins 구성
> * Web App for Containers를 배포하도록 Jenkins 구성.

## <a name="prerequisites"></a>전제 조건

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

**Jenkins** - [Linux VM에 Jenkins 설치](configure-on-linux-vm.md)

## <a name="create-and-configure-a-jenkins-instance"></a>Jenkins 인스턴스 만들기 및 구성

Jenkins 설치에서 다음 플러그인이 설치되어 있는지 확인합니다.

- [Jenkins Git 클라이언트 플러그 인](https://plugins.jenkins.io/git-client) 버전 2.4.6
- [Docker Commons 플러그 인](https://plugins.jenkins.io/docker-commons) 버전 1.4.0
- [Azure 자격 증명](https://plugins.jenkins.io/azure-credentials) 버전 1.2
- [Azure App Service](https://plugins.jenkins.io/azure-app-service) 버전 0.1

Jenkins 플러그 인을 사용하여 Web Apps에 지원되는 언어(예: C#, PHP, Python, Java, Node.js)로 웹앱을 배포할 수 있습니다. 이 자습서에서는 [Azure용 간단한 Java 웹앱](https://github.com/azure-devops/javawebappsample)을 사용합니다. 자신의 GitHub 계정에 리포지토리를 분기하려면 GitHub 인터페이스의 오른쪽 위 모서리에 있는 **분기** 단추를 선택합니다.  

Java 프로젝트를 빌드하려면 Java JDK 및 Maven이 필요합니다. 지속적인 통합을 위해 에이전트를 사용하는 경우 Jenkins 마스터 또는 VM 에이전트에 구성 요소를 설치하십시오. Java SE 애플리케이션을 배포하는 경우 빌드 서버에서 ZIP도 필요합니다.

구성 요소를 설치하려면 SSH로 Jenkins 인스턴스에 로그인하고 다음 명령을 실행합니다.

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Web App for Containers에 배포하려면 Jenkins 마스터 또는 빌드에 사용되는 VM 에이전트에 Docker를 설치합니다. 지침은 [Ubuntu에 Docker 설치](https://docs.docker.com/engine/installation/linux/ubuntu/)를 참조하세요.

## <a name="add-an-azure-service-principal-to-the-jenkins-credentials"></a><a name="service-principal"></a> Jenkins 자격 증명에 Azure 서비스 주체 추가

Azure에 배포하려면 Azure 서비스 주체가 필요합니다. 


1. Azure 서비스 주체를 만들려면 [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) 또는 [Azure Portal](/azure/azure-resource-manager/resource-group-create-service-principal-portal)을 사용합니다.

1. Jenkins 대시보드에서 **자격 증명** > **시스템**을 선택합니다. 그런 다음 **전역 자격 증명(제한 없음)** 을 선택합니다.

1. Microsoft Azure 서비스 주체를 추가하려면 **자격 증명 추가**를 선택합니다. **구독 ID**, **클라이언트 ID**, **클라이언트 암호** 및 **OAuth 2.0 토큰 엔드포인트** 필드에 대한 값을 제공합니다. **ID** 필드를 **mySp**로 설정합니다. 이 ID는 이 문서의 후속 단계에서 사용됩니다.


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>파일을 업로드하여 Web Apps를 배포하도록 Jenkins 구성

Web Apps에 프로젝트를 배포하려면 파일을 업로드하여 빌드 아티팩트를 업로드할 수 있습니다. Azure App Service는 여러 배포 옵션을 지원합니다. Azure App Service Jenkins 플러그 인은 배포를 간소화하며, 파일 형식에 따라 다른 배포 옵션을 제공합니다. 

* Java EE 애플리케이션의 경우 [WAR 배포](/azure/app-service/deploy-zip#deploy-war-file)가 사용됩니다.
* Java SE 애플리케이션의 경우 [ZIP 배포](/azure/app-service/deploy-zip#deploy-zip-file)가 사용됩니다.
* 다른 언어의 경우 [Git 배포](/azure/app-service/deploy-local-git)가 사용됩니다.

Jenkins에서 작업을 설정하기 전에 Java 앱을 실행하려면 웹앱과 Azure App Service 계획이 필요합니다.

1. `az appservice plan create` [Azure CLI 명령](/cli/azure/appservice/plan#az-appservice-plan-create)을 사용하여 **무료** 가격 책정 계층으로 Azure App Service 계획을 만듭니다. App Service 계획은 앱을 호스트하는 데 사용되는 물리적 리소스를 정의합니다. App Service 계획에 할당된 모든 애플리케이션은 이러한 리소스를 공유합니다. 공유 리소스를 사용하면 여러 앱을 호스트하는 경우 비용을 절약할 수 있습니다.

1. 웹앱을 만듭니다. [Azure Portal](/azure/app-service/configure-common) 또는 다음 `az` Azure CLI 명령을 사용할 수 있습니다.

    ```azurecli
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
1. 앱에 필요한 Java 런타임 구성을 설정합니다. 다음 Azure CLI 명령은 최근 JDK 8 및 [Apache Tomcat](https://tomcat.apache.org/) 버전 8.0에서 실행되도록 웹앱을 구성합니다.

    ```azurecli
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Jenkins 작업 설정

1. Jenkins 대시보드에 새 **프리스타일** 프로젝트를 만듭니다.

1. [Azure용 간단한 Java 웹앱](https://github.com/azure-devops/javawebappsample)의 로컬 분기를 사용하도록 **소스 코드 관리** 필드를 구성합니다. **리포지토리 URL** 값을 제공합니다. 예: http:\//github.com/&lt;your_ID>/javawebappsample

1. **셸 실행** 명령을 추가하여 Maven을 사용하여 프로젝트를 빌드하는 단계를 추가합니다. 이 예제의 경우 대상 폴더의 \*.war 파일의 이름을 **ROOT.war**로 바꾸기 위한 추가 명령이 필요합니다.   

    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

1. **Azure 웹앱 게시**를 선택하여 빌드 후 작업을 추가합니다.

1. **mySp**를 Azure 서비스 주체로 제공합니다. 이 주체는 이전 단계에서 [Azure 자격 증명](#service-principal)으로 저장되었습니다.

1. **앱 구성** 섹션에서 구독의 리소스 그룹 및 웹앱을 선택합니다. Jenkins 플러그 인은 웹앱이 Windows 기반인지 또는 Linux 기반인지를 자동으로 감지합니다. Windows 웹앱의 경우 **파일 게시** 옵션이 표시됩니다.

1. 배포할 파일을 입력합니다. 예를 들어, Java를 사용하는 경우 WAR 패키지를 지정합니다. 선택적인 **소스 디렉터리** 및 **대상 디렉터리** 매개 변수를 사용하여 파일 업로드에 사용할 소스 및 대상 폴더를 지정합니다. Azure의 Java 웹앱은 Tomcat 서버에서 실행됩니다. 따라서 Java의 경우 WAR 패키지를 webapps 폴더에 업로드합니다. 예를 들어 **소스 디렉터리** 값을 **target**으로 설정하고 **대상 디렉터리** 값을 **webapps**로 설정합니다.

1. 프로덕션 이외의 슬롯에 배포하려는 경우 **슬롯** 이름도 설정할 수 있습니다.

1. 프로젝트를 저장하고 빌드합니다. 빌드가 완료되면 웹앱이 Azure에 배포됩니다.

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Jenkins 파이프라인을 사용하여 파일을 업로드해 Web Apps 배포

Azure App Service Jenkins 플러그 인은 파이프 라인을 지원합니다. GitHub 리포지토리에서 다음 샘플을 참조할 수 있습니다.

1. GitHub 인터페이스에서 **Jenkinsfile_ftp_plugin** 파일을 엽니다. 파일을 편집하려면 연필 아이콘을 선택합니다. 줄 11 및 12에서 각각 웹앱에 대한 **resourceGroup** 및 **webAppName** 정의를 업데이트합니다.

    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

1. 줄 14에서 **withCredentials** 정의를 Jenkins 인스턴스의 자격 증명 ID로 설정합니다.

    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Jenkins 파이프라인 만들기

1. 웹 브라우저에서 Jenkins를 엽니다. **새 항목**을 선택합니다.

1. 작업 이름을 제공하고 **파이프라인**을 선택합니다. **확인**을 선택합니다.

1. **파이프라인** 탭을 선택합니다.

1. **정의** 값에 **SCM의 파이프라인 스크립트**를 선택합니다.

1. **SCM** 값에 **Git**를 선택합니다. 분기 리포지토리의 GitHub URL을 입력합니다. 예: https://&lt;your_forked_repo>.git.

1. **스크립트 경로** 값을 **Jenkinsfile_ftp_plugin**으로 업데이트합니다.

1. **저장**을 선택하고 작업을 실행합니다.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Web App for Containers를 배포하도록 Jenkins 구성

Linux의 Web Apps는 Docker를 사용하여 배포를 지원합니다. Docker를 사용하여 웹앱을 배포하려면 서비스 런타임에 웹앱을 Docker 이미지로 패키지화하는 Dockerfile을 제공해야 합니다. 그러면 Jenkins 플러그 인이 이미지를 빌드하고 Docker 레지스트리에 푸시하고 이미지를 웹앱에 배포합니다.

Linux에서 Web Apps는 Git 및 파일 업로드와 같은 일반적인 배포 방법도 지원하지만 기본 제공되는 언어(.NET Core, Node.js, PHP 및 Ruby)에 한합니다. 다른 언어의 경우 애플리케이션 코드와 서비스 런타임을 함께 Docker 이미지로 패키징하고 Docker를 사용하여 배포해야 합니다.

Jenkins에서 작업을 설정하기 전에 Linux에 웹앱이 필요합니다. 프라이빗 Docker 컨테이너 이미지를 저장하고 관리하기 위해 컨테이너 레지스트리도 필요합니다. DockerHub를 사용하여 컨테이너 레지스트리를 만들 수 있습니다. 이 예제에서는 Azure Container Registry를 사용합니다.

* [Linux에서 웹앱을 만듭니다](/azure/app-service/containers/quickstart-nodejs).
* Azure Container Registry는 오픈 소스 Docker Registry 버전 2.0에 기반하여 관리되는 [Docker Registry](https://docs.docker.com/registry/) 서비스입니다. [Azure Container Registry를 만듭니다](/azure/container-registry/container-registry-get-started-azure-cli). DockerHub를 사용할 수도 있습니다.

### <a name="set-up-the-jenkins-job-for-docker"></a>Docker용 Jenkins 작업 설정

1. Jenkins 대시보드에 새 **프리스타일** 프로젝트를 만듭니다.

1. [Azure용 간단한 Java 웹앱](https://github.com/azure-devops/javawebappsample)의 로컬 분기를 사용하도록 **소스 코드 관리** 필드를 구성합니다. **리포지토리 URL** 값을 제공합니다. 예: http:\//github.com/&lt;your_ID>/javawebappsample

1. **셸 실행** 명령을 추가하여 Maven을 사용하여 프로젝트를 빌드하는 단계를 추가합니다. 명령에 다음 줄을 포함합니다.

    ```bash
    mvn clean package
    ```

1. **Azure 웹앱 게시**를 선택하여 빌드 후 작업을 추가합니다.

1. **mySp**를 Azure 서비스 주체로 제공합니다. 이 주체는 이전 단계에서 [Azure 자격 증명](#service-principal)으로 저장되었습니다.

1. **앱 구성** 섹션에서 구독의 리소스 그룹 및 Linux 웹앱을 선택합니다.

1. **Docker를 통해 게시**를 선택합니다.

1. **Dockerfile** 경로 값을 입력합니다. 기본값/Dockerfile을 유지할 수 있습니다. **Docker 레지스트리 URL** 값의 경우 Azure Container Registry를 사용한다면 https://&lt;yourRegistry>.azurecr.io 형식을 사용하여 URL을 제공합니다. DockerHub를 사용하는 경우 값을 비워 둡니다.

1. **레지스트리 자격 증명** 값에는 컨테이너 레지스트리에 대한 자격 증명을 추가합니다. Azure CLI에서 다음 명령을 실행하여 사용자 ID와 암호를 가져올 수 있습니다. 첫 번째 명령은 관리자 계정을 활성화합니다.

    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

1. **고급** 탭의 Docker 이미지 이름 및 태그 값은 선택 사항입니다. 기본적으로 이미지 이름의 값은 Azure Portal에서(**Docker 컨테이너** 설정에) 구성한 이미지 이름에서 가져옵니다. 태그는 $BUILD_NUMBER에서 생성됩니다. Azure Portal에 이미지 이름을 지정하거나 **고급** 탭의 **Docker 이미지** 값을 입력합니다. 이 예제의 경우 **Docker 이미지** 값을 &lt;your_Registry>.azurecr.io/calculator로 설정하고 **Docker 이미지 태그** 값을 비워 둡니다.

1. 기본 제공 Docker 이미지 설정을 사용할 경우 배포가 실패합니다. Azure Portal의 **Docker 컨테이너** 설정에서 사용자 지정 이미지를 사용하도록 Docker 구성을 변경합니다. 기본 제공 이미지의 경우 파일 업로드 방법을 사용하여 배포합니다.

1. 파일 업로드 방법과 비슷한 방법으로, **프로덕션** 이외의 다른 **슬롯** 이름을 선택할 수 있습니다.

1. 프로젝트를 만들고 빌드합니다. 컨테이너 이미지가 레지스트리로 푸시되고 웹앱이 배포됩니다.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Jenkins 파이프라인을 사용하여 Web App for Containers 배포

1. GitHub 인터페이스에서 **Jenkinsfile_container_plugin** 파일을 엽니다. 파일을 편집하려면 연필 아이콘을 선택합니다. 줄 11 및 12에서 각각 웹앱에 대한 **resourceGroup** 및 **webAppName** 정의를 업데이트합니다.

    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

1. 줄 13을 컨테이너 레지스트리 서버로 변경합니다.

    ```java
    def registryServer = '<registryURL>'
    ```

1. 줄 16을 Jenkins 인스턴스의 자격 증명 ID를 사용하도록 변경합니다.

    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Jenkins 파이프라인 만들기    

1. 웹 브라우저에서 Jenkins를 엽니다. **새 항목**을 선택합니다.

1. 작업 이름을 제공하고 **파이프라인**을 선택합니다. **확인**을 선택합니다.

1. **파이프라인** 탭을 선택합니다.

1. **정의** 값에 **SCM의 파이프라인 스크립트**를 선택합니다.

1. **SCM** 값에 **Git**를 선택합니다. 분기 리포지토리의 GitHub URL을 입력합니다. 예: `https://&lt;your_forked_repo>.git.`

1. **스크립트 경로** 값을 **Jenkinsfile_container_plugin**으로 업데이트합니다.

1. **저장**을 선택하고 작업을 실행합니다.

## <a name="verify-your-web-app"></a>웹앱 확인

1. WAR 파일이 웹앱에 성공적으로 배포되었는지 확인하려면 웹 브라우저를 엽니다. `http://&lt;your_app_name>.azurewebsites.net/api/calculator/ping`로 이동합니다. `&lt;your_app_name>`을 웹앱 이름으로 바꿉니다. 다음 메시지가 표시됩니다.

    ```output
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

1. `http://&lt;your_app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>`로 이동합니다. `&lt;x> and &lt;y>`을 임의의 숫자로 바꿔 x + y의 합을 구합니다. 계산기에 합계가 표시됩니다.

    ![계산기: 추가](./media/deploy-to-azure-app-service-using-azure-cli/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>Linux의 Azure App Service

1. 웹앱을 확인하려면 Azure CLI에서 다음 명령을 실행합니다.
    
    ```azurecli
    az acr repository list -n <myRegistry> -o json
    ```
    
    다음 메시지가 표시됩니다.
    
    ```output
    ["calculator"]
    ```
    
1. `http://&lt;your_app_name>.azurewebsites.net/api/calculator/ping`로 이동합니다. `&lt;your_app_name>`을 웹앱 이름으로 바꿉니다. 다음 메시지가 표시됩니다.

    ```output
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

1. `http://&lt;your_app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>`로 이동합니다. `&lt;x> and &lt;y>`을 임의의 숫자로 바꿔 x + y의 합을 구합니다.
    
## <a name="troubleshooting-the-jenkins-plugin"></a>Jenkins 플러그 인 문제 해결

Jenkins 플러그 인에서 버그가 발생하면 [Jenkins JIRA](https://issues.jenkins-ci.org/)에서 특정 구성 요소에 대한 문제를 제출해 주세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure의 Jenkins](./index.yml)