---
title: 자습서 - Jenkins 및 Azure CLI를 사용하여 Azure App Service에 배포
description: Azure CLI를 사용하여 Jenkins 파이프라인을 통해 Azure에 Java 웹앱을 배포하는 방법을 알아봅니다.
keywords: Jenkins, Azure, DevOps, App Service, CLI
ms.topic: tutorial
ms.date: 11/10/2020
ms.custom: devx-track-jenkins, devx-track-azurecli
ms.openlocfilehash: 88db3702221b8e18acff71514fcddc9ff7e875b0
ms.sourcegitcommit: 4dac39849ba2e48034ecc91ef578d11aab796e58
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94983642"
---
# <a name="tutorial-deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>자습서: Jenkins 및 Azure CLI를 사용해 Azure App Service에 배포

Java 웹앱을 Azure에 배포하려면 [Jenkins 파이프라인](https://jenkins.io/doc/book/pipeline/)에서 Azure CLI를 사용할 수 있습니다. 이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * Jenkins VM 만들기
> * Jenkins 구성
> * Azure에서 웹앱 만들기
> * GitHub 리포지토리 준비
> * Jenkins 파이프라인 만들기
> * 파이프라인 실행 및 웹앱 확인

## <a name="create-and-configure-jenkins-instance"></a>Jenkins 인스턴스 만들기 및 구성

Jenkins 마스터가 아직 없는 경우 [Linux VM에 Jenkins를 설치합니다](configure-on-linux-vm.md).

Azure 자격 증명 플러그 인을 사용하면 Microsoft Azure 서비스 주체 자격 증명을 Jenkins에 저장할 수 있습니다. 버전 1.2에서는 Jenkins 파이프라인이 Azure 자격 증명을 얻을 수 있도록 지원이 추가되었습니다. 

버전 1.2 이상이 있는지 확인합니다.

* Jenkins 대시보드 내에서 **Jenkins 관리->플러그 인 관리자->** 를 클릭하고 **Azure 자격 증명** 을 검색합니다. 
* 버전이 1.2보다 이전이면 플러그 인을 업데이트합니다.

Java JDK 및 Maven도 Jenkins 마스터에 필요합니다. 설치하려면 SSH를 사용하여 Jenkins 마스터에 로그인하고 다음 명령을 실행합니다.

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-a-jenkins-credential"></a>Jenkins 자격 증명에 Azure 서비스 주체 추가

Azure CLI를 실행하려면 Azure 자격 증명이 필요합니다.

* Jenkins 대시보드 내에서 **자격 증명->시스템->** 을 클릭합니다. **전역 자격 증명(제한 없음)** 을 클릭합니다.
* **자격 증명 추가** 를 클릭한 다음 구독 ID, 클라이언트 ID, 클라이언트 암호 및 OAuth 2.0 토큰 엔드포인트를 입력하여 [Microsoft Azure 서비스 주체](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%252fazure%252fazure-resource-manager%252ftoc.json)를 추가합니다. 이후 단계에서 사용할 ID를 제공합니다.

![자격 증명 추가](./media/deploy-to-azure-app-service-using-azure-cli/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Java 웹앱을 배포하기 위한 Azure App Service 만들기

[az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) CLI 명령을 사용하여 **무료** 가격 책정 계층과 함께 Azure App Service 계획을 만듭니다. App Service 계획은 앱을 호스트하는 데 사용되는 실제 리소스를 정의합니다. App Service 계획에 할당된 모든 애플리케이션은 이들 리소스를 공유하므로 여러 앱을 호스팅할 때 비용을 절감할 수 있습니다. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

계획이 준비되면 Azure CLI는 다음 예와 비슷한 출력이 표시됩니다.

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Azure 웹앱 만들기

 [az webapp create](/cli/azure/webapp#az-webapp-create) CLI 명령을 사용하여 `myAppServicePlan` App Service 계획에서 웹앱 정의를 만듭니다. 웹앱 정의는 애플리케이션에 액세스하는 URL을 제공하고 Azure에 코드를 배포하는 몇 가지 옵션을 구성합니다. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

`<app_name>` 자리 표시자를 고유한 앱 이름으로 대체합니다. 이 고유한 이름은 웹앱에 대한 기본 도메인 이름의 일부이므로 이름은 Azure에 있는 모든 앱에서 고유해야 합니다. 사용자에게 노출하기 전에 웹앱에 사용자 지정 도메인 이름 항목을 매핑할 수 있습니다.

웹앱 정의가 준비되면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다. 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Java 구성

[az appservice web config update](/cli/azure/webapp/config) 명령으로 앱에 필요한 Java 런타임 구성을 설정합니다.

다음 명령은 최신 Java 8 JDK 및 [Apache Tomcat](https://tomcat.apache.org/) 8.0에서 실행되도록 웹앱을 구성합니다.

```azurecli
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>GitHub 리포지토리 준비

1. [Azure용 간단한 Java 웹앱](https://github.com/azure-devops/javawebappsample) 리포지토리를 만듭니다. 사용자 고유의 GitHub 계정에 리포지토리를 분기하려면 오른쪽 위 모서리에 있는 **분기** 단추를 클릭합니다.

1. GitHub 웹 UI에서 **Jenkinsfile** 파일을 엽니다. 연필 아이콘을 클릭하여 이 파일을 편집하고 줄 20과 21에서 각각 웹앱의 리소스 그룹 및 이름을 업데이트합니다.

    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<app_name>'
    ```
    
1. 줄 23을 변경하여 Jenkins 인스턴스의 자격 증명 ID를 업데이트합니다.

    ```java
    withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
    ```
    
## <a name="create-jenkins-pipeline"></a>Jenkins 파이프라인 만들기

웹 브라우저에서 Jenkins를 열고 **새 항목** 을 클릭합니다.

1. 작업의 이름을 입력합니다.
1. **파이프라인** 을 선택합니다. 
1. **확인** 을 선택합니다.
1. **파이프라인** 을 선택합니다.
1. **정의** 에서 **SCM의 파이프라인 스크립트** 를 선택합니다.
1. **SCM** 에서 **Git** 을 선택합니다.
1. 포크된 리포지토리에 대한 GitHub URL(`https:\<your forked repo\>.git`)을 입력합니다.
1. **저장** 을 선택합니다.

## <a name="test-your-pipeline"></a>파이프라인 테스트

1. 만든 파이프라인으로 이동합니다.
1. **지금 빌드** 를 클릭합니다.
1. 빌드가 완료되면 **콘솔 출력** 을 선택하여 빌드 세부 정보를 확인합니다.

## <a name="verify-your-web-app"></a>웹앱 확인

다음을 수행하여 WAR 파일이 웹앱에 성공적으로 배포되었는지 확인합니다. 

1. 웹 브라우저를 엽니다.

1. `http://&lt;app_name>.azurewebsites.net/api/calculator/ping`으로 이동

1. 다음과 비슷한 텍스트가 표시됩니다.

    ```output
    Welcome to Java Web App!!! This is updated!
    Today's date
    ```

1. http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>(&lt;x> 및 &lt;y>를 임의의 숫자로 대체)로 이동하여 x와 y의 합계를 구합니다.

    ![계산기: 추가](./media/deploy-to-azure-app-service-using-azure-cli/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Linux에서 Azure Web App에 배포

Jenkins 파이프라인에서 Azure CLI를 사용한 후에는 Linux의 Azure Web App에 배포하도록 스크립트를 수정합니다. Linux의 Web Apps는 Docker를 지원합니다. 이와 같이 서비스 런타임을 사용하여 웹앱을 Docker 이미지로 패키지하는 Dockerfile을 제공합니다. 플러그 인에서 이미지를 빌드하고, Docker 레지스트리에 푸시하며, 이미지를 웹앱에 배포합니다.

1. [Linux에서 실행되는 Azure Web App을 만듭니다](/azure/app-service/containers/quickstart-nodejs).

1. [Docker를 Jenkins에 설치합니다](https://docs.docker.com/engine/installation/linux/ubuntu/).

1. [Azure Portal에서 Container Registry를 만듭니다](/azure/container-registry/container-registry-get-started-azure-cli).

1. 포크한 동일한 [간단한 Azure용 Java 웹앱](https://github.com/azure-devops/javawebappsample) 리포지토리에서 **Jenkinsfile2** 파일을 다음과 같이 편집합니다.

    1. 리소스 그룹, 웹앱 및 ACR의 이름으로 업데이트합니다(자리 표시자를 사용자의 값으로 바꿈).

        ```bash
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    1. `<azsrvprincipal\>`을 사용자의 자격 증명 ID로 업데이트합니다.

        ```bash
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

1. `Jenkinsfile2`를 사용하여 Windows에서 Azure 웹앱에 배포할 때와 같은 방법으로 새 Jenkins 파이프라인을 만듭니다.

1. 새 작업을 실행합니다.

1. 확인하려면 Azure CLI에서 다음 명령을 실행합니다.

    ```azurecli
    az acr repository list -n <myRegistry> -o json
    ```

    다음과 유사한 결과가 표시됩니다.
    
    ```output
    [
    "calculator"
    ]
    ```
    
1. `http://<app_name>.azurewebsites.net/api/calculator/ping`으로 이동합니다(자리 표시자 바꿈). 다음과 비슷한 결과가 표시됩니다. 

    ```output
    Welcome to Java Web App!!! This is updated!
    Today's date
    ```

1. `http://<app_name>.azurewebsites.net/api/calculator/add?x=<x>&y=<y>`로 이동합니다(자리 표시자 바꿈). `x` 및 `y`에 지정한 값이 합산되어 표시됩니다.
    
## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure의 Jenkins](/azure/jenkins)