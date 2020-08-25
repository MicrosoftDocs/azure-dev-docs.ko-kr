---
title: 자습서 - Jenkins를 사용하여 GitHub에서 Azure Kubernetes Service로 배포
description: GitHub 및 CD(지속적인 배포)에서 AKS(Azure Kubernetes Service)까지의 CI(연속 통합)를 위해 Jenkins를 구성하는 방법을 알아봅니다.
keywords: Jenkins, Azure, DevOps, AKS, Azure Kubernetes Service, GitHub
ms.topic: article
ms.date: 01/09/2019
ms.custom: devx-track-jenkins
ms.openlocfilehash: c73e5c567f237c6f859b52230742ae74b87326f5
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88241005"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-using-jenkins"></a>자습서: Jenkins를 사용하여 GitHub에서 Azure Kubernetes Service로 배포

이 자습서에는 Jenkins에서 CI(지속적인 통합) 및 CD(지속적인 배포)를 설정하여 GitHub의 샘플 앱을 [AKS(Azure Kubernetes Service)](/azure/aks/intro-kubernetes)로 배포합니다. 이런 방식으로 커밋을 GitHub에 푸시하여 앱을 업데이트하는 경우 Jenkins는 자동으로 새 컨테이너 빌드를 실행하고, 컨테이너 이미지를 ACR(Azure Container Registry)에 푸시한 다음, AKS에서 앱을 실행합니다. 

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * AKS 클러스터에 Azure 투표 앱 예제를 배포합니다.
> * 기본 Jenkins 프로젝트를 만듭니다.
> * Jenkins가 ACR과 상호 작용하도록 자격 증명을 설정합니다.
> * 자동화된 빌드를 위한 Jenkins 빌드 작업 및 GitHub 웹후크를 만듭니다.
> * CI/CD 파이프라인을 테스트하여 GitHub 코드 커밋에 따라 AKS에서 애플리케이션을 업데이트합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음 항목이 필요합니다.

- Kubernetes, Git, CI/CD 및 컨테이너 이미지에 대한 기본적인 이해

- [AKS 클러스터 자격 증명](/cli/azure/aks#az-aks-get-credentials)으로 구성된 [AKS 클러스터](/azure/aks/kubernetes-walkthrough) 및 `kubectl`

- [ACR(Azure Container Registry) 레지스트리](/azure/container-registry/container-registry-get-started-azure-cli), ACR 로그인 서버 이름 및 [ACR 레지스트리에 인증](/azure/aks/cluster-container-registry-integration)하도록 구성된 AKS 클러스터

- Azure CLI 버전 2.0.46 이상의 설치 및 구성.  `az --version`을 실행하여 버전을 찾습니다. 설치하거나 업그레이드해야 하는 경우  [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

- 개발 시스템에 [설치된 Docker](https://docs.docker.com/install/)

- GitHub 계정, [GitHub 개인용 액세스 토큰](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) 및 개발 시스템에 설치된 Git 클라이언트

- Jenkins를 배포하도록 스크립팅된 이 샘플 방법 대신 사용자 고유의 Jenkins 인스턴스를 제공하는 경우 Jenkins 인스턴스에는 [설치 및 구성된 Docker](https://docs.docker.com/install/) 및 [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)이 필요합니다.

## <a name="prepare-your-app"></a>앱 준비

이 문서에서는 하나 이상의 Pod에서 호스트되는 웹 인터페이스와 임시 데이터 스토리지를 위한 Redis를 호스트하는 두 번째 Pod를 포함하는 Azure 투표 애플리케이션 예제를 사용합니다. 자동화된 배포를 위해 Jenkins 및 AKS를 통합하기 전에 먼저 수동으로 Azure 투표 애플리케이션을 준비하고 AKS 클러스터에 배포합니다. 이 수동 배포는 애플리케이션의 버전 1이고 이를 통해 작동 중인 애플리케이션을 볼 수 있습니다.

> [!NOTE]
> Azure 투표 애플리케이션 샘플은 Linux 노드에서 실행되도록 예약된 Linux Pod를 사용합니다. 이 문서에서 설명하는 흐름은 Windows Server 노드에서 예약된 Windows Server Pod에도 적용됩니다.

애플리케이션 예제([https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis))의 다음 GitHub 리포지토리를 포크합니다. 사용자 고유의 GitHub 계정에 리포지토리를 분기하려면 오른쪽 위 모서리에 있는 **분기** 단추를 선택합니다.

포크를 개발 시스템에 복제합니다. 이 리포지토리를 복제할 때 포크 URL을 사용해야 합니다.

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

복제된 포크의 디렉터리로 변경합니다.

```console
cd azure-voting-app-redis
```

애플리케이션 예제에 필요한 컨테이너 이미지를 만들려면 `docker-compose`와 함께 *docker-compose.yaml* 파일을 사용합니다.

```console
docker-compose up -d
```

필요한 기본 이미지가 풀되고 애플리케이션 컨테이너가 빌드됩니다. 그러면 [docker images](https://docs.docker.com/engine/reference/commandline/images/) 명령을 사용하여 만들어진 이미지를 볼 수 있습니다. 3개 이미지가 다운로드되거나 생성되었는지 확인합니다. `azure-vote-front` 이미지는 애플리케이션을 포함하며 `nginx-flask` 이미지를 기준으로 사용합니다. `redis` 이미지는 Redis 인스턴스를 시작하는 데 사용됩니다.

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

*azure-vote-front* 컨테이너 이미지를 ACR에 푸시하려면 먼저 [az acr list](/cli/azure/acr#az-acr-list) 명령을 사용하여 ACR 로그인 서버를 가져옵니다. 다음 예제는 *myResourceGroup*이라는 리소스 그룹에서 레지스트리의 ACR 로그인 서버 주소를 가져옵니다.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

[docker tag](https://docs.docker.com/engine/reference/commandline/tag/) 명령을 사용하여 ACR 로그인 서버 이름 및 버전 번호(`v1`)를 사용하는 태그를 이미지에 지정합니다. 이전 단계에서 가져온 고유한 `<acrLoginServer>` 이름을 입력합니다.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

마지막으로 *azure-vote-front* 이미지를 ACR 레지스트리로 푸시합니다. 다시, `<acrLoginServer>`를 `myacrregistry.azurecr.io` 같은 고유한 ACR 레지스트리의 로그인 서버 이름으로 바꿉니다.

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>AKS에 애플리케이션 예제 배포

애플리케이션 예제를 AKS 클러스터에 배포하려면 Azure 투표 리포지토리의 루트에 있는 Kubernetes 매니페스트 파일을 사용하면 됩니다. `vi` 같은 편집기로 *azure-vote-all-in-one-redis.yaml* 매니페스트 파일을 엽니다. `microsoft`를 ACR 로그인 서버 이름으로 바꿉니다. 이 값은 매니페스트 파일의 줄 **47**에 있습니다.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

다음으로, [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) 명령을 사용하여 애플리케이션을 AKS 클러스터에 배포합니다.

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Kubernetes 부하 분산 장치 서비스는 애플리케이션을 인터넷에 노출하기 위해 만들어집니다. 이 프로세스는 몇 분 정도 걸릴 수 있습니다. 부하 분산 장치 배포에 대한 진행 상황을 모니터링하려면 `--watch` 인수가 포함된 [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 명령을 사용합니다. *EXTERNAL-IP* 주소가 *보류 중*에서 *IP 주소*로 변경되면 `Control + C`를 사용하여 kubectl 조사식 프로세스를 중지합니다.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

애플리케이션이 실제로 작동하는 모습을 보려면 웹 브라우저를 서비스의 외부 IP 주소로 엽니다. 다음 예제와 같이 Azure 투표 애플리케이션이 표시됩니다.

![AKS에서 실행 중인 Azure 투표 애플리케이션 예제](media/deploy-from-github-to-aks/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Azure VM에 Jenkins 배포

이 문서에서 사용할 Jenkins를 빠르게 배포하려면 다음 스크립트를 사용하여 Azure 가상 머신을 배포하고, 네트워크 액세스를 구성하고, Jenkins의 기본 설치를 완료하면 됩니다. Jenkins와 AKS 클러스터 간 인증을 위해 스크립트는 개발 시스템의 Kubernetes 구성 파일을 Jenkins 시스템으로 복사합니다.

> [!WARNING]
> 이 샘플 스크립트는 Azure VM에서 실행되는 Jenkins 환경을 신속하게 프로비전하기 위한 데모 용도입니다. Azure 사용자 지정 스크립트 확장을 사용하여 VM을 구성한 다음, 필요한 자격 증명을 표시할 수 있습니다. *~/.kube/config*는 Jenkins VM에 복사됩니다.

다음 명령을 실행하여 스크립트를 다운로드하고 실행합니다. 실행하기 전에 스크립트의 콘텐츠를 검토해야 합니다. [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh)

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

VM을 만들고 Docker 및 Jenkins에 필요한 구성 요소를 배포하는 데는 몇 분 정도 걸립니다. 스크립트가 완료되면 다음 예제 출력과 같이 Jenkins 서버의 주소 및 대시보드 잠금을 해제하는 키가 출력됩니다.

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

웹 브라우저를 표시된 URL로 열고 잠금 해제 키를 입력합니다. 화면에 표시되는 메시지에 따라 Jenkins 구성을 완료합니다.

- **Install suggested plugins**(제안된 플러그 인 설치)를 선택합니다.
- 첫 번째 관리 사용자를 만듭니다. *azureuser*와 같은 사용자 이름을 입력한 다음, 고유하고 안전한 암호를 입력합니다. 마지막으로, 전체 이름 및 이메일 주소를 입력합니다.
- **저장 및 끝내기**를 선택합니다.
- Jenkins가 준비되면 **Jenkins를 사용하여 시작**을 선택합니다.
    - Jenkins를 사용하여 시작할 때 웹 브라우저에 빈 페이지가 표시되는 경우 Jenkins 서비스를 다시 시작합니다. 서비스를 Jenkins 인스턴스의 공용 IP 주소에 SSH를 사용하고 `sudo service jenkins restart`를 입력합니다. 서비스가 다시 시작되면 웹 브라우저를 새로 고칩니다.
- 설치 프로세스에서 만든 사용자 이름과 암호를 사용하여 Jenkins에 로그인합니다.

## <a name="create-a-jenkins-environment-variable"></a>Jenkins 환경 변수 만들기

Jenkins 환경 변수는 ACR 로그인 서버 이름을 저장하는 데 사용됩니다. 이 변수는 Jenkins 빌드 작업 중에 참조됩니다. 이 환경 변수를 만들려면 다음 단계를 완료합니다.

- Jenkins 포털의 왼쪽에서 **Jenkins 관리** > **시스템 구성**을 선택합니다.
- **전역 속성**에서 **환경 변수**를 선택합니다. 이름이 `ACR_LOGINSERVER`인 변수와 ACR 로그인 서버 값을 추가합니다.

    ![Jenkins 환경 변수](media/deploy-from-github-to-aks/env-variables.png)

- 완료되면 Jenkins 구성 페이지 아래쪽에서 **저장**을 클릭합니다.

## <a name="create-a-jenkins-credential-for-acr"></a>ACR의 Jenkins 자격 증명 만들기

Jenkins가 업데이트된 컨테이너 이미지를 빌드한 후 ACR에 푸시할 수 있게 하려면 ACR의 자격 증명을 지정해야 합니다. 이 인증에는 Azure Active Directory 서비스 주체를 사용할 수 있습니다. 필수 구성 요소로 ACR 레지스트리에 대한 ‘읽기 권한자’ 권한이 있는 AKS 클러스터의 서비스 주체를 구성했습니다.  이러한 권한을 사용하면 AKS 클러스터가 ACR 레지스트리에서 이미지를 ‘풀’할 수 있습니다.  CI/CD 프로세스 중에 Jenkins는 애플리케이션 업데이트를 기반으로 새 컨테이너 이미지를 빌드하고 이후 해당 이미지를 ACR 레지스트리로 ‘푸시’해야 합니다.  역할 및 권한을 구분하기 위해 이제 ACR 레지스트리에 대한 *Contributor* 권한이 있는 Jenkins의 서비스 주체를 구성합니다.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>ACR를 사용할 Jenkins의 서비스 주체 만들기

먼저 [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) 명령을 사용하여 서비스 주체를 만듭니다.

```azurecli
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "626dd8ea-042d-4043-a8df-4ef56273670f",
  "displayName": "azure-cli-2018-09-28-22-19-34",
  "name": "http://azure-cli-2018-09-28-22-19-34",
  "password": "1ceb4df3-c567-4fb6-955e-f95ac9460297",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

출력에 표시된 *appId* 및 *password*를 기록해 둡니다. 이러한 값은 다음 단계에서 Jenkins에 자격 증명 리소스를 구성하는 데 사용됩니다.

[az acr show](/cli/azure/acr#az-acr-show) 명령을 사용하여 ACR 레지스트리의 리소스 ID를 가져와서 변수로 저장합니다. 리소스 그룹 이름과 ACR 이름을 입력합니다.

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

이제 역할 할당을 만들어 ACR 레지스트리에 대한 서비스 주체 *Contributor* 권한을 할당합니다. 다음 예제에서는 이전 명령의 출력에 표시된 고유한 *appId*를 입력하여 서비스 주체를 만듭니다.

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Jenkins에서 ACR 서비스 주체의 자격 증명 리소스 만들기

Azure에서 만든 역할 할당을 사용하여 이제 ACR 자격 증명을 Jenkins 자격 증명 개체에 저장합니다. 이러한 자격 증명은 Jenkins 빌드 작업 중에 참조됩니다.

다시 Jenkins 포털의 왼쪽에서 **자격 증명** > **Jenkins** > **전역 자격 증명(무제한)**  > **자격 증명 추가**를 클릭합니다.

자격 증명 종류가 **사용자 이름 및 암호**인지 확인하고 다음 항목을 입력합니다.

- **사용자 이름** - ACR 레지스트리에 인증하기 위해 만든 서비스 주체의 *appId*입니다.
- **암호** - ACR 레지스트리에 인증하기 위해 만든 서비스 주체의 *password*입니다.
- **ID** - *acr-credentials*와 같은 자격 증명 식별자입니다.

완료되면 자격 증명 양식이 다음과 같이 표시됩니다.

![서비스 주체 정보를 사용하여 Jenkins 자격 증명 개체 만들기](media/deploy-from-github-to-aks/acr-credentials.png)

**확인**을 클릭하고 Jenkins 포털로 돌아갑니다.

## <a name="create-a-jenkins-project"></a>Jenkins 프로젝트 만들기

Jenkins 포털 홈페이지의 왼쪽에 있는 **새 항목**을 선택합니다.

1. *azure-vote*를 작업 이름으로 입력합니다. **프리스타일 프로젝트**를 선택한 후 **확인**을 선택합니다.
1. **일반** 섹션 아래에서 **GitHub 프로젝트**를 선택하고, 포크된 리포지토리 URL(예: *https:\//github.com/\<your-github-account\>/azure-voting-app-redis*)을 입력합니다.
1. **원본 코드 관리** 섹션 아래에서 **Git**을 선택하고, 포크된 리포지토리 *.git* URL(예: *https:\//github.com/\<your-github-account\>/azure-voting-app-redis.git*)을 입력합니다.

1. **빌드 트리거** 섹션에서 **GITscm 폴링에 대한 GitHub 후크 트리거**를 선택합니다.
1. **빌드 환경**에서 **비밀 텍스트 또는 파일 사용**을 선택합니다.
1. **바인딩**에서 **추가** > **사용자 이름 및 암호(구분)** 를 선택합니다.
   - **사용자 이름 변수**에 `ACR_ID`를 입력하고, **암호 변수**에 `ACR_PASSWORD`를 입력합니다.

     ![Jenkins 바인딩](media/deploy-from-github-to-aks/bindings.png)

1. **셸 실행** 형식의 **빌드 단계**를 추가하고 다음 텍스트를 사용합니다. 이 스크립트는 새 컨테이너 이미지를 빌드하고 ACR 레지스트리에 푸시합니다.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. **셸 실행** 형식의 다른 **빌드 단계**를 추가하고 다음 텍스트를 사용합니다. 이 스크립트는 ACR의 새 컨테이너 이미지로 AKS의 애플리케이션 배포를 업데이트합니다.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. 완료되면 **저장**을 클릭합니다.

## <a name="test-the-jenkins-build"></a>Jenkins 빌드 테스트

GitHub 커밋을 기반으로 작업을 자동화하기 전에 먼저 수동으로 Jenkins 빌드를 테스트합니다. 이 수동 빌드는 작업이 올바르게 구성되고, 적절한 Kubernetes 인증 파일이 배치되고, ACR을 사용한 인증이 작동하는지 유효성을 검사합니다.

프로젝트의 왼쪽 메뉴에서 **지금 빌드**를 선택합니다.

![Jenkins 테스트 빌드](media/deploy-from-github-to-aks/test-build.png)

첫 번째 빌드는 Docker 이미지 레이어가 Jenkins 서버로 풀다운됨에 따라 1~2분 정도 걸립니다. 후속 빌드는 캐시된 이미지 레이어를 사용하여 빌드 시간을 개선할 수 있습니다.

빌드 프로세스 중에는 GitHub 리포지토리가 Jenkins 빌드 서버에 복제됩니다. 새 컨테이너 이미지가 빌드되어 ACR 레지스트리로 푸시됩니다. 마지막으로, AKS 클러스터에서 실행 중인 Azure 투표 애플리케이션이 새 이미지를 사용하도록 업데이트됩니다. 애플리케이션 코드에는 변경된 내용이 없으므로 웹 브라우저에서 앱 예제를 보는 경우 애플리케이션은 변경되지 않습니다.

빌드 작업이 완료되면 빌드 기록에서 **빌드 #1**을 클릭합니다. **콘솔 출력**을 선택하고 빌드 프로세스의 출력을 확인합니다. 마지막 줄에 빌드가 성공했다는 내용이 표시되어야 합니다.

## <a name="create-a-github-webhook"></a>GitHub 웹후크 만들기

수동 빌드가 완료되면 이제 GitHub를 Jenkins 빌드에 통합합니다. 웹후크를 사용하여 GitHub에서 코드를 커밋할 때마다 Jenkins 빌드 작업을 실행할 수 있습니다. GitHub 웹후크를 만들려면 다음 단계를 완료합니다.

1. 웹 브라우저에서 포크된 GitHub 리포지토리로 이동합니다.
1. **설정**을 선택한 다음, 왼쪽에 있는 **웹후크**를 선택합니다.
1. **웹후크 추가**를 선택합니다. ‘페이로드 URL’에 `http://<publicIp:8080>/github-webhook/`를 입력합니다. 여기서 `<publicIp>`는 Jenkins 서버의 IP 주소입니다.  후행 슬래시(/)를 포함해야 합니다. 콘텐츠 형식에 대한 다른 기본값을 그대로 두고 *푸시* 이벤트를 트리거합니다.
1. **웹후크 추가**를 선택합니다.

    ![Jenkins의 GitHub 웹후크 만들기](media/deploy-from-github-to-aks/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>전체 CI/CD 파이프라인 테스트

이제 전체 CI/CD 파이프라인을 테스트할 수 있습니다. GitHub에 코드 커밋을 푸시하면 다음 단계가 수행됩니다.

1. GitHub 웹후크가 Jenkins에 도달합니다.
1. Jenkins는 빌드 작업을 시작하고 GitHub의 최신 코드 커밋을 풀합니다.
1. Docker 빌드는 업데이트된 코드를 사용하여 시작되고 새 컨테이너 이미지에는 최신 빌드 번호가 태그로 지정됩니다.
1. 이 새 컨테이너 이미지는 Azure Container Registry에 푸시됩니다.
1. Azure Kubernetes Service 업데이트에 배포된 애플리케이션이 Azure Container Registry 레지스트리의 최신 컨테이너 이미지를 사용하여 업데이트됩니다.

개발 컴퓨터에서 코드 편집기를 사용하여 복제된 애플리케이션을 엽니다. */azure-vote/azure-vote* 디렉터리 아래에서 **config_file.cfg** 파일을 엽니다. 다음 예제와 같이 이 파일의 투표 값을 의미 있는 값으로 업데이트합니다.

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

업데이트되면 파일을 저장하고, 변경 내용을 커밋하고, 이를 GitHub 리포지토리의 포크에 푸시합니다. GitHub 웹후크는 Jenkins에서 새 빌드 작업을 트리거합니다. Jenkins 웹 대시보드에서 빌드 프로세스를 모니터링합니다. 최신 코드를 풀하고, 업데이트된 이미지를 만들어 푸시하고, 업데이트된 애플리케이션을 AKS에 배포하는 데 몇 초 정도 걸립니다.

빌드가 완료되면 Azure 투표 애플리케이션 예제의 웹 브라우저를 새로 고칩니다. 변경 내용이 다음 예제와 같이 표시됩니다.

![Jenkins 빌드 작업으로 업데이트된 샘플 AKS 투표](media/deploy-from-github-to-aks/azure-vote-updated.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure의 Jenkins](/azure/jenkins)