---
title: 자습서 - Jenkins 및 Azure CLI를 사용하여 Azure App Service에 배포
description: Azure CLI를 사용하여 Jenkins 파이프라인을 통해 Azure에 Java 웹앱을 배포하는 방법을 알아봅니다.
keywords: Jenkins, Azure, DevOps, App Service, CLI
ms.topic: tutorial
ms.date: 01/06/2021
ms.custom: devx-track-jenkins, devx-track-azurecli
ms.openlocfilehash: cac490a111120e7b390e26d020c5a6ad00fdd667
ms.sourcegitcommit: 347bfa3b6c34579c567d1324efc63c1d6672a75b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109066"
---
# <a name="tutorial-deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>자습서: Jenkins 및 Azure CLI를 사용해 Azure App Service에 배포

[!INCLUDE [jenkins-integration-with-azure.md](includes/jenkins-integration-with-azure.md)]

Java 웹앱을 Azure에 배포하려면 [Jenkins 파이프라인](https://jenkins.io/doc/book/pipeline/)에서 Azure CLI를 사용할 수 있습니다. 이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * Jenkins VM 만들기
> * Jenkins 구성
> * Azure에서 웹앱 만들기
> * GitHub 리포지토리 준비
> * Jenkins 파이프라인 만들기
> * 파이프라인 실행 및 웹앱 확인

## <a name="prerequisites"></a>전제 조건

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

- **Jenkins** - [Linux VM에 Jenkins 설치](configure-on-linux-vm.md)
- **Azure CLI**: Jenkins 서버에 Azure CLI(버전 2.0.67 이상)를 설치합니다.

## <a name="configure-jenkins"></a>Jenkins 구성

다음 단계에서는 Jenkins 마스터에 필요한 Java JDK 및 Maven을 설치하는 방법을 보여 줍니다.

1. SSH를 사용하여 Jenkins 마스터에 로그인합니다.

1. [apt-get 리포지토리에서 Azul Zulu JDK 다운로드 및 설치](/azure/developer/java/fundamentals/java-jdk-install#download-and-install-the-azul-zulu-jdks-from-an-apt-get-repository):

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
    sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
    sudo apt-get -q update
    sudo apt-get -y install zulu-8-azure-jdk
    ```
    
1. 다음 명령을 실행하여 Maven을 설치합니다.

    ```bash
    sudo apt-get install -y maven
    ```
    
## <a name="add-azure-service-principal-to-a-jenkins-credential"></a>Jenkins 자격 증명에 Azure 서비스 주체 추가

다음 단계에서는 Azure 자격 증명을 지정하는 방법을 보여 줍니다.

1. [자격 증명 플러그 인](https://plugins.jenkins.io/credentials/)이 설치되어 있는지 확인합니다.

1. Jenkins 대시보드 내에서 **자격 증명 -> 시스템** 을 선택합니다. 

1. **글로벌 자격 증명(제한 없음)** 을 선택합니다.

1. [Microsoft Azure 서비스 주체](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%252fazure%252fazure-resource-manager%252ftoc.json)를 추가하려면 **자격 증명 추가** 를 선택합니다. 자격 증명 종류가 **_사용자 이름 및 암호_* _인지 확인하고 다음 항목을 입력합니다.

    _ **사용자 이름**: 서비스 주체 `appId`
    * **암호**: 서비스 주체 `password`
    * **ID**: 자격 증명 식별자(예: `AzureServicePrincipal`)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Java 웹앱을 배포하기 위한 Azure App Service 만들기

[az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create)를 사용하여 **무료** 가격 책정 계층과 함께 Azure App Service 계획을 만듭니다.

```azurecli
az appservice plan create \
    --name <app_service_plan> \ 
    --resource-group <resource_group> \
    --sku FREE
```

**참고**:

- App Service 계획은 앱을 호스트하는 데 사용되는 실제 리소스를 정의합니다.
- App Service 계획에 할당된 모든 애플리케이션은 이러한 리소스를 공유합니다.
- App Service 계획을 사용하면 여러 앱을 호스팅할 때 비용을 절감할 수 있습니다.

## <a name="create-an-azure-web-app"></a>Azure 웹앱 만들기

[az webapp create](/cli/azure/webapp#az-webapp-create)를 사용하여 `myAppServicePlan` App Service 계획에서 웹앱 정의를 만듭니다.

```azurecli
az webapp create \
    --name <app_name> \ 
    --resource-group <resource_group> \
    --plan <app_service_plan>
```

**참고**:

- 웹앱 정의는 애플리케이션에 액세스하는 URL을 제공하고 Azure에 코드를 배포하는 몇 가지 옵션을 구성합니다.
- `<app_name>` 자리 표시자를 고유한 앱 이름으로 대체합니다.
- 앱 이름은 웹앱의 기본 도메인 이름의 일부입니다. 따라서 이 이름은 Azure의 모든 앱에서 고유해야 합니다.
- 사용자에게 노출하기 전에 웹앱에 사용자 지정 도메인 이름 항목을 매핑할 수 있습니다.


## <a name="configure-java"></a>Java 구성

[az appservice web config update](/cli/azure/webapp/config)를 사용하여 앱에 필요한 Java 런타임 구성을 설정합니다.

```azurecli
az webapp config set \ 
    --name <app_name> \
    --resource-group <resource_group> \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>GitHub 리포지토리 준비

1. [Azure용 간단한 Java 웹앱](https://github.com/azure-devops/javawebappsample) 리포지토리를 만듭니다.

1. 사용자 고유의 GitHub 계정에 리포지토리를 포크하려면 **포크** 단추를 선택합니다.

1. 파일 이름을 클릭하여 **Jenkinsfile** 파일을 엽니다.

1. 파일을 편집하려면 연필 아이콘을 선택합니다.

1. 구독 ID 및 테넌트 ID를 업데이트합니다.
    
    ```groovy
      withEnv(['AZURE_SUBSCRIPTION_ID=<subscription_id>',
            'AZURE_TENANT_ID=<tenant_id>']) 
    ```
    
1. 줄 22와 23에서 각각 웹앱의 리소스 그룹 및 이름을 업데이트합니다.

    ```groovy
    def resourceGroup = '<resource_group>'
    def webAppName = '<app_name>'
    ```

1. Jenkins 인스턴스의 자격 증명 ID 업데이트

    ```groovy
    withCredentials([usernamePassword(credentialsId: '<service_princial>', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
    ```
    
## <a name="create-jenkins-pipeline"></a>Jenkins 파이프라인 만들기

다음을 수행하여 Jenkins 파이프라인을 만듭니다.

1. 웹 브라우저에서 Jenkins를 엽니다.

1. **새 항목** 을 선택합니다.

1. 작업의 이름을 입력합니다.

1. **파이프라인** 을 선택합니다.

1. **확인** 을 선택합니다.

1. **파이프라인** 을 선택합니다.

1. **정의** 에서 **SCM의 파이프라인 스크립트** 를 선택합니다.

1. **SCM** 에서 **Git** 을 선택합니다.

1. 포크된 리포지토리에 대한 GitHub URL(`https:\<forked_repo\>.git`)을 입력합니다.

1. **저장** 을 선택합니다.

## <a name="test-your-pipeline"></a>파이프라인 테스트

1. 만든 파이프라인으로 이동합니다.

1. **지금 빌드** 를 선택합니다.

1. 빌드가 완료되면 **콘솔 출력** 을 선택하여 빌드 세부 정보를 확인합니다.

## <a name="verify-your-web-app"></a>웹앱 확인

다음을 수행하여 WAR 파일이 웹앱에 성공적으로 배포되었는지 확인합니다.

1. `http://&lt;app_name>.azurewebsites.net/api/calculator/ping`이라는 URL로 이동합니다.

1. 다음과 비슷한 텍스트가 표시됩니다.

    ```output
    Welcome to Java Web App!!! This is updated!
    Today's date
    ```

1. http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>라는 URL로 이동합니다(&lt;x> 및 &lt;y>는 합계할 두 값으로 대체).

    ![데모 추가를 실행하는 예제](./media/deploy-to-azure-app-service-using-azure-cli/calculator-add.png)

## <a name="deploy-to-azure-app-service-on-linux"></a>Linux의 Azure App Service에 배포

App Service는 지원되는 애플리케이션 스택에 대해 기본적으로 Linux에서 웹앱을 호스트할 수도 있습니다. 사용자 지정 Linux 컨테이너(컨테이너용 웹앱이라고도 함)를 실행할 수도 있습니다.

Azure App Service on Linux에 배포하도록 스크립트를 수정할 수 있습니다. App Service on Linux는 Docker를 지원합니다. 이와 같이 서비스 런타임을 사용하여 웹앱을 Docker 이미지로 패키지하는 Dockerfile을 제공합니다. 플러그 인에서 이미지를 빌드하고, Docker 레지스트리에 푸시하며, 이미지를 웹앱에 배포합니다.

1. [사용자 지정 컨테이너를 사용하여 사용자 지정 소프트웨어를 Azure App Service로 마이그레이션](/azure/app-service/tutorial-custom-container?pivots=container-linux#configure-app-service-to-deploy-the-image-from-the-registry)을 참조하여 Azure App Service on Linux 및 Azure Container Registry를 만듭니다.

    ```azurecli
        az group create --name myResourceGroup2 --location westus2
        az acr create --name myACRName --resource-group myResourceGroup2 --sku Basic --admin-enabled true
        az appservice plan create --name myAppServicePlan --resource-group  myResourceGroup2 --is-linux
        az webapp create --resource-group myResourceGroup2 --plan myAppServicePlan --name myApp --deployment-container-image-name myACRName.azurecr.io/calculator:latest
    ```

1. [Docker를 Jenkins에 설치합니다](https://docs.docker.com/engine/installation/linux/ubuntu/).

1. [Docker 파이프라인 플러그 인](https://plugins.jenkins.io/docker-workflow/)이 설치되어 있는지 확인합니다.

1. 포크한 동일한 [간단한 Azure용 Java 웹앱](https://github.com/azure-devops/javawebappsample) 리포지토리에서 **Jenkinsfile2** 파일을 다음과 같이 편집합니다.

    1. 구독 ID 및 테넌트 ID를 업데이트합니다.

        ```groovy
         withEnv(['AZURE_SUBSCRIPTION_ID=<mySubscriptionId>',
                'AZURE_TENANT_ID=<myTenantId>']) {
        ```

    1. 리소스 그룹, 웹앱 및 ACR의 이름으로 업데이트합니다(자리 표시자를 사용자의 값으로 바꿈).

        ```bash
        def webAppResourceGroup = '<resource_group>'
        def webAppName = '<app_name>'
        def acrName = '<registry>'
        ```

    1. `<azsrvprincipal\>`을 사용자의 자격 증명 ID로 업데이트합니다.

        ```bash
        withCredentials([usernamePassword(credentialsId: '<service_principal>', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
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