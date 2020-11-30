---
title: 자습서 - Azure Kubernetes Service에서 Azure Dev Spaces 사용
description: 지속적인 통합 파이프라인에서 Azure Dev Spaces 플러그 인을 사용하는 방법을 알아봅니다.
keywords: jenkins, Azure, DevOps, Azure Dev Spaces, AKS, Azure Kubernetes Service
ms.topic: tutorial
ms.date: 10/23/2019
ms.custom: devx-track-jenkins, devx-track-azurecli
ms.openlocfilehash: 3652d0bc1dc418c4037296fdacc3a56384b592c3
ms.sourcegitcommit: 4dac39849ba2e48034ecc91ef578d11aab796e58
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94983712"
---
# <a name="tutorial-use-azure-dev-spaces-with-azure-kubernetes-service"></a>자습서: Azure Kubernetes Service에서 Azure Dev Spaces 사용

Azure Dev Spaces를 사용하면 종속 요소를 복제하거나 모방할 필요 없이 AKS(Azure Kubernetes Service)에서 실행되는 마이크로서비스 애플리케이션을 테스트하고 반복적으로 개발할 수 있습니다. Jenkins용 Azure Dev Spaces 플러그 인을 사용하면 CI/CD(지속적인 통합 및 업데이트) 파이프라인에서 Dev Spaces를 사용할 수 있습니다.

이 자습서에서는 ACR(Azure Container Registry)도 사용합니다. ACR은 이미지를 저장하고, ACR 작업은 Docker 및 Helm 아티팩트를 빌드합니다. ACR 및 ACR 작업을 아티팩트 생성에 사용하면 Jenkins 서버에 Docker 같은 추가 소프트웨어를 설치할 필요가 없습니다.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * Azure Dev Spaces가 설정된 AKS 클러스터 만들기
> * AKS에 다중 서비스 애플리케이션 배포
> * Jenkins 서버 준비
> * Jenkins 파이프라인에서 Azure Dev Spaces 플러그 인을 사용하여 코드 변경 내용을 프로젝트에 병합하기 전에 미리 봅니다.

이 자습서에서는 핵심 Azure 서비스, AKS, ACR, Azure Dev Spaces, Jenkins [파이프라인](https://jenkins.io/doc/book/pipeline/) 및 플러그 인, GitHub에 대해 중간 수준의 지식을 갖고 있다고 가정합니다. kubectl 및 Helm 같은 지원 도구에 대한 기본 지식이 있으면 도움이 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

* GitHub 계정. GitHub 계정이 없는 경우 시작하기 전에 [체험 계정](https://github.com/)을 만듭니다.

* [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) 확장 프로그램이 설치된 [Visual Studio Code](https://code.visualstudio.com/download)

* [Azure CLI](/cli/azure/install-azure-cli) 버전 2.0.43 이상

* Jenkins 서버. Jenkins가 없는 경우 이 [빠른 시작](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.production-jenkins)의 단계에 따라 Azure에 [Jenkins](/azure/jenkins/install-jenkins-solution-template)를 배포합니다. 

* 이 자습서의 뒷부분에 설명된 것처럼, Jenkins 서버에 Helm과 kubectl이 모두 설치되어 있고 Jenkins 계정에 사용할 수 있어야 합니다.

* VS Code, VS Code Terminal 또는 WSL, Bash. 


## <a name="create-azure-resources"></a>Azure 리소스 만들기

이 섹션에서는 다음과 같은 Azure 리소스를 만듭니다.

* 이 자습서에 필요한 모든 Azure 리소스를 포함하는 리소스 그룹입니다.
* [AKS(Azure Kubernetes Service)](/azure/aks/) 클러스터
* ACR 작업을 사용하여 Docker 이미지를 빌드하고 저장할 [ACR(Azure Container Registry)](/azure/container-registry/)

1. 리소스 그룹을 만듭니다.

    ```azurecli
    az group create --name MyResourceGroup --location westus2
    ```

2. AKS 클러스터를 만듭니다. [Dev Spaces를 지원하는 Azure 지역](/azure/dev-spaces/about#supported-regions-and-configurations)에 AKS 클러스터를 만듭니다.

    ```azurecli
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Dev Spaces를 사용하도록 AKS를 구성합니다.

    ```azurecli
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    이 단계에서는 `azds` CLI 확장을 설치합니다.

4. 컨테이너 레지스트리를 만듭니다.

    ```azurecli
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>AKS 클러스터에 샘플 앱 배포

이 섹션에서는 개발 공간을 설정하고 이전 섹션에서 만든 AKS 클러스터에 샘플 애플리케이션을 배포합니다. 이 애플리케이션은 *webfrontend* 및 *mywebapi* 로 구성됩니다. 두 구성 요소 모두 개발 공간에 배포됩니다. 이 자습서의 뒷부분에서 mywebapi에 대한 끌어오기 요청을 제출하여 jenkins에서 CI 파이프라인을 트리거하겠습니다.

Azure Dev Spaces 사용 방법 및 Azure Dev Spaces로 다중 서비스 개발을 사용하는 방법에 대한 자세한 내용은 [Azure Dev Spaces에서 Java를 사용하여 시작](/azure/dev-spaces/get-started-java) 및 [Azure Dev Spaces로 다중 서비스 개발](/azure/dev-spaces/multi-service-java)을 참조하세요. 두 자습서는 여기에 없는 추가 배경 정보를 제공합니다.

1. GitHub에서 https://github.com/Azure/dev-spaces 리포지토리를 다운로드합니다.

2. VS Code에서 `samples/java/getting-started/webfrontend` 폴더를 엽니다. (디버그 자산을 추가하거나 프로젝트를 복원하라는 모든 기본 프롬프트를 무시할 수 있습니다.)

3. `/src/main/java/com/ms/sample/webfrontend/Application.java`를 다음과 같이 업데이트합니다.

    ```java
    package com.ms.sample.webfrontend;

    import java.io.*;
    import java.net.*;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.web.bind.annotation.*;

    @SpringBootApplication
    @RestController
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

        @RequestMapping(value = "/greeting", produces = "text/plain")
        public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
            URLConnection conn = new URL("http://mywebapi/").openConnection();
            conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
            {
                return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
            }
        }
    }
    ```

4. **보기**, **터미널** 을 차례로 클릭하여 VS Code에서 통합 터미널을 엽니다.

5. `azds prep` 명령을 실행하여 개발 공간에서 실행할 애플리케이션을 준비합니다. 애플리케이션을 올바르게 준비하려면 이 명령을 반드시 `dev-spaces/samples/java/getting-started/webfrontend`에서 실행해야 합니다.

    ```bash
    azds prep --public
    ```

    Dev Spaces CLI의 `azds prep` 명령은 기본 설정으로 Docker 및 Kubernetes 자산을 생성합니다. 이러한 파일은 프로젝트의 수명 동안 지속되며, 사용자 지정할 수 있습니다.

    * `./Dockerfile` 및 `./Dockerfile.develop`는 앱의 컨테이너 이미지 및 원본 코드가 빌드되는 방법을 설명하고 컨테이너 내에서 실행됩니다.
    * `./charts/webfrontend` 아래의 [Helm 차트](https://helm.sh/docs/topics/charts/)는 Kubernetes에 컨테이너를 배포하는 방법을 설명합니다.
    * `./azds.yaml`은 Azure Dev Spaces 구성 파일입니다.

    자세한 내용은 [Azure Dev Spaces 작동 및 구성 방법](/azure/dev-spaces/how-dev-spaces-works)을 참조하세요.

6. `azds up` 명령을 사용하여 AKS에서 애플리케이션을 빌드하고 실행합니다.

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>콘솔 출력에서 `up` 명령으로 생성된 URL에 대한 정보를 검색합니다. 다음과 같은 형식입니다.

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    브라우저 창에서 이 URL을 열면 웹앱이 보입니다. 컨테이너가 실행될 때 `stdout` 및 `stderr` 출력이 터미널 창으로 스트리밍됩니다.

8. 다음으로, *mywebapi* 를 설정하고 배포합니다.

    1. 디렉터리를 `dev-spaces/samples/java/getting-started/mywebapi`로 변경합니다.

    2. 다음을 실행합니다.

        ```bash
        azds prep
        ```

    3. 다음을 실행합니다.

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Jenkins 서버 준비

이 섹션에서는 샘플 CI 파이프라인을 실행하도록 Jenkins 서버를 준비합니다.

* 플러그 인 설치
* Helm 및 Kubernetes CLI 설치
* 자격 증명 추가

### <a name="install-plug-ins"></a>플러그 인 설치

1. Jenkins 서버에 로그인합니다. **Jenkins 관리 > 플러그 인 관리** 를 선택합니다.
2. **사용 가능** 탭에서 다음 플러그 인을 선택합니다.
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces)
    * [Azure Container Registry 작업](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [환경 인젝터](https://plugins.jenkins.io/envinject)
    * [GitHub 통합](https://plugins.jenkins.io/github-pullrequest)

    이러한 플러그 인이 목록에 표시되지 않으면 **설치됨** 탭을 확인하여 플러그 인이 이미 설치되어 있는지 확인합니다.

3. 플러그 인을 설치하려면 **지금 다운로드 및 다시 시작한 후 설치** 를 선택합니다.

4. Jenkins 서버를 다시 시작하여 설치를 완료합니다.

### <a name="install-helm-and-kubectl"></a>Helm 및 kubectl 설치

샘플 파이프라인은 Helm 및 kubectl을 사용하여 개발 공간에 배포합니다. Jenkins를 설치하면 *jenkins* 라는 관리자 계정이 생성됩니다. Helm와 kubectl 둘 다 jenkins 사용자가 액세스할 수 있어야 합니다.

1. SSH를 통해 Jenkins 마스터에 연결합니다. 

2. 다음과 같이 `jenkins` 사용자로 전환합니다.
    ```bash
    sudo su jenkins
    ```

3. Helm CLI를 설치합니다. 자세한 내용은 [Helm 설치](https://helm.sh/docs/using_helm/#installing-helm)를 참조하세요.

4. kubectl을 설치합니다. 자세한 내용은 [**az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm)를 참조하세요.

### <a name="add-credentials-to-jenkins"></a>Jenkins에 자격 증명 추가

1. Jenkins에서 Azure 서비스 주체는 Azure 리소스를 인증하고 액세스해야 합니다. 서비스 주체를 만드는 방법은 Azure App Service에 배포 자습서의 [서비스 주체 만들기](deploy-from-github-to-azure-app-service.md#create-service-principal) 섹션을 참조하세요. 다음 단계를 완료하려면 필요하므로 `create-for-rbac`의 출력 복사본을 꼭 저장해야 합니다. 출력은 다음과 같이 표시됩니다.

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. 이전 단계의 서비스 주체 정보를 사용하여 *Microsoft Azure 서비스 주체* 자격 증명 유형을 Jenkins에 추가합니다. 아래 스크린샷의 이름은 `create-for-rbac`의 출력과 일치합니다.

    **ID** 필드는 서비스 주체의 Jenkins 자격 증명 이름입니다. 이 예제에서는 `displayName` 값을 사용하지만(이 예제에서는 `xxxxxxxjenkinssp`), 원하는 텍스트를 사용해도 됩니다. 이 자격 증명 이름은 다음 섹션에 나오는 AZURE_CRED_ID 환경 변수의 값입니다.

    ![Jenkins에 서비스 주체 자격 증명 추가](media/azure-dev-spaces-and-aks/add-service-principal-credentials.png)

    **설명** 은 선택 사항입니다. 자세한 지침은 Azure App Service에 배포 자습서의 [Jenkins에 서비스 주체 추가](deploy-from-github-to-azure-app-service.md#add-service-principal-to-jenkins) 섹션을 참조하세요. 



3. ACR 자격 증명을 표시하려면 다음 명령을 실행합니다.

    ```azurecli
    az acr credential show -n <yourRegistryName>
    ```

    JSON 출력의 복사본을 만듭니다. 다음과 비슷한 형식입니다.

    ```json
    {
      "passwords": [
        {
          "name": "password",
          "value": "vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz"
        },
        {
          "name": "password2",
          "value": "zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz"
        }
      ],
      "username": "acr01"
    }
    ```

4. *사용자 이름 및 암호* 자격 증명 유형을 Jenkins에 추가합니다. **username** 은 이전 단계의 사용자 이름이며, 이 예제에서는 `acr01`입니다. **password** 는 첫 번째 암호의 값이며, 이 예제에서는 `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`입니다. 이 자격 증명의 **ID** 는 ACR_CRED_ID의 값입니다.

5. AKS 자격 증명을 설정합니다. *Kubernetes 구성(kubeconfig)* 자격 증명 유형을 Jenkins에 추가합니다("직접 입력" 옵션 사용). AKS 클러스터에 대한 액세스 자격 증명을 얻으려면 다음 명령을 실행합니다.

    ```azurecli
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   이 자격 증명의 **ID** 는 다음 섹션에 나오는 KUBE_CONFIG_ID의 값입니다.

## <a name="create-a-pipeline"></a>파이프라인 만들기

예제 파이프라인에서 선택한 시나리오는 다음과 같은 실제 패턴을 기반으로 합니다. 끌어오기 요청은 제안된 변경 내용을 빌드한 후 테스트 및 검토를 위해 Azure 개발 공간에 배포하는 CI 파이프라인을 트리거합니다. 검토 결과에 따라 변경 내용이 병합되어 AKS에 배포되거나 삭제됩니다. 마지막으로, 개발자 공간이 제거됩니다.

Jenkins 파이프라인 구성과 Jenkinsfile은 CI 파이프라인의 단계를 정의합니다. 다음 순서도는 Jenkinsfile에 정의된 파이프라인 단계 및 결정 지점을 보여줍니다.

![Jenkins 파이프라인 흐름](media/azure-dev-spaces-and-aks/jenkins-pipeline-flow.png)

1. [https://github.com/azure-devops/mywebapi](https://github.com/azure-devops/mywebapi)에서 수정된 *mywebapi* 프로젝트 버전을 다운로드합니다. 이 프로젝트는 *Jenkinsfile*, *Dockerfiles* 및 Helm 차트를 포함하여 파이프라인을 만드는 데 필요한 여러 파일을 포함하고 있습니다.

2. Jenkins에 로그인합니다. 왼쪽 메뉴에서 **항목 추가** 를 선택합니다.

3. **파이프라인** 을 선택한 다음, **항목 이름 입력** 상자에 이름을 입력합니다. **확인** 을 선택하면 파이프라인 구성 화면이 자동으로 열립니다.

4. **일반** 탭에서 **Prepare an environment for the run**(실행 환경 준비)을 선택합니다. 

5. **Keep Jenkins Environment Variables**(Jenkins 환경 변수 유지) 및 **Keep Jenkins Build Variables**(Jenkins 빌드 변수 유지)를 선택합니다.

6. **속성 콘텐츠** 상자에 다음 환경 변수를 입력합니다.

    ```
    AZURE_CRED_ID=[your credential ID of service principal]
    RES_GROUP=[your resource group of the function app]
    ACR_RES_GROUP=[your ACR resource group]
    ACR_NAME=[your ACR name]
    ACR_REGISTRY=[your ACR registry url, without http schema]
    ACR_CRED_ID=[your credential id of your ACR account]
    AKS_RES_GROUP=[your AKS resource group]
    AKS_NAME=[your AKS name]
    IMAGE_NAME=[name of Docker image you will push to ACR, without registry prefix]
    KUBE_CONFIG_ID=[your kubeconfig id]
    PARENT_DEV_SPACE=[shared dev space name]
    TEST_ENDPOINT=[your web frontend end point for testing. Should be webfrontend.XXXXXXXXXXXXXXXXXXX.xxxxxx.aksapp.io]
    ```

    이전 섹션에서 지정한 샘플 값을 사용하면 다음과 비슷한 환경 변수 목록이 표시됩니다.

    ![Jenkins 파이프라인 환경 변수](media/azure-dev-spaces-and-aks/jenkins-pipeline-environment.png)

7. **파이프라인 > 정의** 에서 **SCM의 파이프라인 스크립트** 를 선택합니다.
8. **SCM** 에서 **Git** 를 선택한 다음, 리포지토리 URL을 입력합니다.
9. **분기 지정자** 에 `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`를 입력합니다.
10. SCM 리포지토리 URL 및 스크립트 경로 "Jenkinsfile"을 입력합니다.
11. **경량 체크 아웃** 이 선택됩니다.

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>파이프라인을 트리거하는 끌어오기 요청 만들기

이 섹션의 3단계를 완료하려면 Jenkinsfile 부분을 주석 처리해야 합니다. 그렇지 않으면 새 버전과 기존 버전을 나란히 놓고 보려고 할 때 404 오류가 발생합니다. 기본적으로 PR을 병합하기로 선택하면 mywebapi의 이전 공유 버전이 삭제되고 새 버전으로 대체됩니다. Jenkinsfile을 다음과 같이 변경하고 1단계를 완료합니다.

```Groovy
    if (userInput == true) {
        stage('deploy') {
            // Apply the deployment to shared namespace in AKS using acsDeploy, Helm or kubectl   
        }
        
        stage('Verify') {
            // verify the staging environment is working properly
        }
        
        /* Comment the cleanup stage to allow side by side comparison with the new child dev space

        stage('cleanup') {
            devSpacesCleanup aksName: env.AKS_NAME, 
                azureCredentialsId: env.AZURE_CRED_ID, 
                devSpaceName: devSpaceNamespace, 
                kubeConfigId: env.KUBE_CONFIG_ID, 
                resourceGroupName: env.AKS_RES_GROUP,
                helmReleaseName: releaseName
        }
        */

    } else {
        // Send a notification
    }
```

1. `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`를 변경하고 끌어오기 요청을 만듭니다. 다음은 그 예입니다. 

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Jenkins에 로그인하고 파이프라인 이름을 선택한 다음, **지금 작성** 을 선택합니다. 

    Jenkins 파이프라인을 자동으로 트리거하도록 *webhook* 를 설정할 수도 있습니다. 끌어오기 요청을 입력하면 GitHub가 Jenkins에 POST를 발급하고 파이프라인을 트리거합니다. webhook 설정에 대한 자세한 내용은 [GitHub에 Jenkins 연결](deploy-from-github-to-azure-app-service.md#connect-jenkins-to-github)을 참조하세요.

3. 현재 공유 버전과 변경 내용 비교:

    1. 브라우저를 열고 공유 버전 `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`로 이동합니다. TEST_ENDPOINT에는 URL이 포함됩니다.

    2. 다른 탭을 열고 PR 개발 공간 URL을 입력합니다. `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`와 유사합니다. Jenkins 작업의 **빌드 기록 > <build#> > 콘솔 출력** 에서 링크를 찾을 수 있습니다. 페이지에서 `aksapp`을 검색하거나, 접두사만 보려면 `azdsprefix`를 검색합니다.

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>자식 개발 공간의 URL 작성

끌어오기 요청을 제출하면 Jenkins는 팀의 공유 개발 공간에 따라 자식 개발 공간을 만들고, 해당 자식 개발 공간의 끌어오기 요청에 있는 코드를 실행합니다. 자식 개발 공간의 URL은 `http://$env.azdsprefix.<test_endpoint>` 형식입니다. 

**$env.azdsprefix** 는 Azure Dev Spaces 플러그 인의 파이프라인 실행 중에 **devSpacesCreate** 를 통해 설정됩니다.

```Groovy
stage('create dev space') {
    devSpacesCreate aksName: env.AKS_NAME,
        azureCredentialsId: env.AZURE_CRED_ID,
        kubeconfigId: env.KUBE_CONFIG_ID,
        resourceGroupName: env.AKS_RES_GROUP,
        sharedSpaceName: env.PARENT_DEV_SPACE,
        spaceName: devSpaceNamespace
}
```

`test_endpoint`는 이전에 [AKS 클러스터에 샘플 앱 배포 7단계](#test_endpoint)에서 `azds up`을 사용하여 배포한 webfrontend 앱의 URL입니다. `$env.TEST_ENDPOINT`의 값은 파이프라인 구성에서 설정합니다. 

다음 코드 조각은 `smoketest` 단계에서 자식 개발 공간 URL을 사용하는 방법을 보여줍니다. 이 코드는 다음과 같이 자식 개발 공간 TEST_ENDPOINT를 사용할 수 있는지 확인하고, 사용할 수 있다면 인사말 텍스트를 stdout에 다운로드합니다.

```Groovy
stage('smoketest') {
    // CI testing against http://$env.azdsprefix.$env.TEST_ENDPOINT" 
    SLEEP_TIME = 30
    SITE_UP = false
    for (int i = 0; i < 10; i++) {
        sh "sleep ${SLEEP_TIME}"
        code = "0"
        try {
            code = sh returnStdout: true, script: "curl -sL -w '%{http_code}' 'http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting' -o /dev/null"
        } catch (Exception e){
            // ignore
        }
        if (code == "200") {
            sh "curl http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting"
            SITE_UP = true
            break
        }
    }
    if(!SITE_UP) {
        echo "The site has not been up after five minutes"
    }
}
```

## <a name="clean-up-resources"></a>리소스 정리

샘플 애플리케이션 사용을 마쳤으면 리소스 그룹을 삭제하여 Azure 리소스를 정리합니다.

```azurecli
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure에서 Jenkins를 사용하는 CI/CD](deploy-from-github-to-aks.md)
