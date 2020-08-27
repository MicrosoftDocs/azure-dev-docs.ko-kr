---
title: Jenkins 및 Azure CLI를 사용하여 Azure Spring Cloud에 앱 배포
description: 지속적인 통합 및 배포 파이프라인에서 Azure CLI를 사용하여 마이크로서비스를 Azure Spring Cloud 서비스에 배포하는 방법을 알아봅니다.
keywords: Jenkins, Azure, DevOps, Azure Spring Cloud, Azure CLI
ms.topic: tutorial
ms.date: 08/10/2020
ms.custom: devx-track-jenkins,devx-track-azurecli
ms.openlocfilehash: 707af403bf789ccd85e52f7bb5880389f61e8f15
ms.sourcegitcommit: 2f832baf90c208a8a69e66badef5f126d23bbaaf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88725167"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>자습서: Jenkins 및 Azure CLI를 사용하여 Azure Spring Cloud에 앱 배포

[Azure Spring Cloud](/azure/spring-cloud/spring-cloud-overview)는 기본 제공 서비스 검색 및 구성 관리를 사용하는 완전 관리형 마이크로서비스 개발입니다. 이 서비스를 사용하면 Spring Boot 기반 마이크로서비스 애플리케이션을 Azure에 쉽게 배포할 수 있습니다. 이 자습서에서는 Jenkins에서 Azure CLI를 사용하여 Azure Spring Cloud에 대한 CI/CD(지속적인 통합 및 업데이트)를 자동화하는 방법을 보여 줍니다.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 서비스 인스턴스 프로비저닝 및 Java Spring 애플리케이션 시작
> * Jenkins 서버 준비
> * Jenkins 파이프라인의 Azure CLI를 사용하여 마이크로서비스 애플리케이션 빌드 및 배포 

>[!Note]
> Azure Spring Cloud는 현재 퍼블릭 미리 보기로 제공됩니다. 퍼블릭 미리 보기 제품을 통해 고객은 공식 릴리스 전에 새로운 기능을 시험해 볼 수 있습니다.  퍼블릭 미리 보기 기능 및 서비스는 프로덕션 용도로 사용되지 않습니다.  미리 보기 동안 제공되는 지원에 대한 자세한 내용은 [FAQ](https://azure.microsoft.com/support/faq/)를 검토하거나 [지원 요청](/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조하여 자세히 알아보세요.

## <a name="prerequisites"></a>전제 조건

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

**Jenkins**: [Linux VM에 Jenkins 설치](configure-on-linux-vm.md)

**GitHub 계정**: GitHub 계정이 없는 경우 시작하기 전에 [체험 계정](https://github.com/)을 만듭니다.

## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>서비스 인스턴스 프로비저닝 및 Java Spring 애플리케이션 시작

[Piggy Metrics](https://github.com/Azure-Samples/piggymetrics)를 Microsoft 서비스 애플리케이션 샘플로 사용하고, [빠른 시작: Azure CLI를 사용하여 Java Spring 애플리케이션 시작](/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)의 동일한 단계에 따라 서비스 인스턴스를 프로비저닝하고 애플리케이션을 설정합니다. 동일한 프로세스를 이미 수행한 경우 다음 섹션으로 건너뛸 수 있습니다. 그렇지 않으면 Azure CLI 명령이 다음에 포함됩니다. 추가 배경 정보를 얻으려면 [빠른 시작: Azure CLI를 사용하여 Java Spring 애플리케이션 시작](/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)을 참조하세요.

로컬 머신은 Jenkins 빌드 서버와 동일한 필수 구성 요소를 충족해야 합니다. 마이크로서비스 애플리케이션을 빌드하고 배포하려면 다음이 설치되어 있어야 합니다.
    * [Git](https://git-scm.com/)
    * [JDK 8](/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 이상](https://maven.apache.org/download.cgi)
    * [Azure CLI 설치](/cli/azure/install-azure-cli?view=azure-cli-latest), 2.0.67 버전 이상

1. Azure Spring Cloud 확장을 설치합니다.

    ```Azure CLI
    az extension add --name spring-cloud
    ```

1. Azure Spring Cloud 서비스를 포함할 리소스 그룹을 만듭니다.

    ```Azure CLI
    az group create --location eastus --name <resource group name>
    ```

1. Azure Spring Cloud의 인스턴스를 프로비저닝합니다.

    ```Azure CLI
    az spring-cloud create -n <service name> -g <resource group name>
    ```

1. [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics)를 사용자 고유의 GitHub 계정에 포크합니다. 로컬 머신에서 리포지토리를 `source-code`이라는 디렉터리에 복제합니다.

    ```bash
    mkdir source-code
    git clone https://github.com/<your GitHub id>/piggymetrics
    ```

1. 구성 서버를 설정합니다. &lt;GitHub ID&gt;를 올바른 값으로 바꿔야 합니다.

    ```Azure CLI
    az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

1. 프로젝트를 빌드합니다.

    ```bash
    cd piggymetrics
    mvn clean package -D skipTests
    ```

1. **gateway**, **auth-service** 및 **account-service**라는 3개의 마이크로서비스를 만듭니다.

    ```Azure CLI
    az spring-cloud app create --n gateway -s <service name> -g <resource group name>
    az spring-cloud app create --n auth-service -s <service name> -g <resource group name>
    az spring-cloud app create --n account-service -s <service name> -g <resource group name>
    ```

1. 애플리케이션을 배포합니다.

    ```Azure CLI
    az spring-cloud app deploy -n gateway -s <service name> -g <resource group name> --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service -s <service name> -g <resource group name> --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service -s <service name> -g <resource group name> --jar-path ./auth-service/target/auth-service.jar
    ```

1. 퍼블릭 엔드포인트를 게이트웨이에 할당합니다.

    ```Azure CLI
    az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

1. 애플리케이션이 실행되고 있는지 확인할 수 있도록 게이트웨이 애플리케이션을 쿼리하여 URL을 가져옵니다.

    ```Azure CLI
    az spring-cloud app show --name gateway | grep url
    ```
    
 1. 이전 명령에서 제공하는 URL로 이동하여 PiggyMetrics 애플리케이션을 실행합니다.

## <a name="prepare-jenkins-server"></a>Jenkins 서버 준비

이 섹션에서는 Jenkins 서버에서 테스트에 적합한 빌드를 실행하도록 준비합니다. 그러나 보안에 영향을 주므로 [Azure VM Agent](https://plugins.jenkins.io/azure-vm-agents) 또는 [Azure Container Agent](https://plugins.jenkins.io/azure-container-agents)를 통해 Azure에서 에이전트를 스핀업하여 빌드를 실행합니다. 자세한 내용은 [마스터 빌드의 보안 영향](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master)에 관한 Jenkins 문서를 참조하세요.

### <a name="install-plug-ins"></a>플러그 인 설치

1. Jenkins 서버에 로그인합니다. **Jenkins 관리 > 플러그 인 관리**를 선택합니다.

1. **사용 가능** 탭에서 다음 플러그 인을 선택합니다.

    * [GitHub 통합](https://plugins.jenkins.io/github-pullrequest)
    * [Azure 자격 증명](https://plugins.jenkins.io/azure-credentials)

    이러한 플러그 인이 목록에 표시되지 않으면 **설치됨** 탭을 확인하여 플러그 인이 이미 설치되어 있는지 확인합니다.

1. 플러그 인을 설치하려면 **지금 다운로드 및 다시 시작한 후 설치**를 선택합니다.

1. Jenkins 서버를 다시 시작하여 설치를 완료합니다.

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>Jenkins 자격 증명 저장소에 Azure 서비스 주체 자격 증명 추가

1. Azure에 배포하려면 Azure 서비스 주체가 필요합니다. 자세한 내용은 'Azure App Service에 배포' 자습서의  [서비스 주체 만들기](deploy-from-github-to-azure-app-service.md#create-service-principal) 섹션을 참조하세요. `az ad sp create-for-rbac`의 출력은 다음과 같습니다.

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

1. Jenkins 대시보드에서 **자격 증명** > **시스템**을 선택합니다. 그런 다음 **전역 자격 증명(제한 없음)** 을 선택합니다.

1. **자격 증명 추가**를 선택합니다.

1. 종류로 **Microsoft Azure 서비스 주체**를 선택합니다.

1. 다음 필드에 대한 값을 입력합니다.

    - **구독 ID**: Azure 구독 ID입니다.
    - **클라이언트 ID**: 서비스 주체 앱 ID
    - **클라이언트 비밀**: 서비스 주체 암호
    - **테넌트 ID**: Microsoft 계정 테넌트 ID
    - **Azure 환경**: 사용자 환경에 적합한 값을 선택합니다. 예를 들어 Azure Global의 경우 **Azure**를 사용하세요.
    - **ID**: `azure_service_principal`로 설정합니다. 이 ID는 이 문서의 이후 단계에서 사용됩니다.
    - **설명**: 선택 사항이지만 권장합니다.
    
### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>Maven 및 Az CLI spring-cloud 확장 설치

샘플 파이프라인은 Maven을 사용하여 빌드하고, Az CLI를 사용하여 서비스 인스턴스에 배포합니다. Jenkins를 설치하면 *jenkins*라는 관리자 계정이 생성됩니다. *jenkins* 사용자에게 spring-cloud 확장을 실행할 수 있는 권한이 있는지 확인합니다.

1. SSH를 통해 Jenkins 마스터에 연결합니다.

1. Maven 설치

    ```bash
    sudo apt-get install maven
    ```

1. `az version`을 입력하여 Azure CLI가 설치되었는지 확인합니다. Azure CLI가 설치되어 있지 않으면 [Azure CLI](/cli/azure/install-azure-cli) 설치를 참조하세요.

1. 다음과 같이 `jenkins` 사용자로 전환합니다.

    ```bash
    sudo su jenkins
    ```

1. **spring-cloud** 확장을 추가합니다.

    ```bash
    az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>Jenkinsfile 만들기

1. 사용자 고유의 리포지토리(https://github.com/&lt ;your GitHub id&gt; /piggymetrics)에서 **Jenkinsfile**을 루트에 만듭니다.

1. 파일을 다음과 같이 업데이트합니다. **\<resource group name>** 및 **\<service name>** 의 값을 바꿔야 합니다. Jenkins에서 자격 증명을 추가할 때 다른 값을 사용하는 경우 **azure_service_principal**을 올바른 ID로 바꿉니다.

   ```groovy
       node {
         stage('init') {
           checkout scm
         }
         stage('build') {
           sh 'mvn clean package'
         }
         stage('deploy') {
           withCredentials([azureServicePrincipal('azure_service_principal')]) {
             // login to Azure
             sh '''
               az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
               az account set -s $AZURE_SUBSCRIPTION_ID
             '''  
             // Set default resource group name and service name. Replace <resource group name> and <service name> with the right values
             sh 'az configure --defaults group=<resource group name>'
             sh 'az configure --defaults spring-cloud=<service name>'
             // Deploy applications
             sh 'az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar'
             sh 'az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar'
             sh 'az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar'
             sh 'az logout'
           }
         }
       }
   ```

1. 변경 내용을 저장하고 커밋합니다.

## <a name="create-the-job"></a>작업 만들기

1. Jenkins 대시보드에서 **새 항목**을 클릭합니다.

1. 작업 이름으로 *Deploy-PiggyMetrics*를 제공하고, **파이프라인**을 선택합니다. 확인을 클릭합니다.

1. **파이프라인** 탭을 클릭합니다.

1. **정의**에서 **SCM의 파이프라인 스크립트**를 선택합니다.

1. **SCM**에서 **Git**을 선택합니다.

1. 포크된 리포지토리( **https://github.com/&lt ;your GitHub id&gt; /piggymetrics.git**)에 대한 GitHub URL을 입력합니다.

1. **분기 지정자('any'의 경우 검은색)** 가 * **/Azure**인지 확인합니다.

1. **스크립트 경로**를 **Jenkinsfile**로 유지합니다.

1. 페이지 맨 아래에 있는 **저장**

## <a name="validate-and-run-the-job"></a>작업 유효성 검사 및 실행

작업을 실행하기 전에 로그인 입력 상자의 텍스트를 **로그인 ID 입력**으로 업데이트해 보겠습니다.

1. 사용자 고유의 리포지토리에 있는 **/gateway/src/main/resources/static/** 에서 `index.html`을 엽니다.

1. "로그인 입력"을 검색하여 "로그인 ID 입력"으로 업데이트합니다.

    ```HTML
    <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

1. 변경 내용 커밋

1. Jenkins에서 작업을 수동으로 실행합니다. Jenkins 대시보드에서 *Deploy-PiggyMetrics* 작업을 클릭한 다음, **지금 빌드**를 클릭합니다.

작업이 완료되면 **게이트웨이** 애플리케이션의 공용 IP로 이동하여 애플리케이션이 업데이트되었는지 확인합니다. 

![업데이트된 Piggy Metrics](./media/deploy-to-azure-spring-cloud-using-azure-cli/piggymetrics.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 이 문서에서 만든 리소스를 삭제합니다.

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure의 Jenkins](/azure/jenkins/)