---
title: 자습서 - GitHub 및 Docker를 사용하여 Jenkins 파이프라인 만들기
description: 각 코드 커밋마다 GitHub에서 가져오고 앱을 실행하기 위해 새 Docker 컨테이너를 빌드하는 Azure에서 Jenkins 가상 머신을 만드는 방법을 알아봅니다.
keywords: Jenkins, Azure, DevOps, 파이프라인, CI/CD, Docker
ms.topic: tutorial
ms.date: 03/27/2017
ms.custom: devx-track-jenkins, devx-track-azurecli
ms.openlocfilehash: eb4c12fe249b485941221d382ab0090f7aa88227
ms.sourcegitcommit: 1ddcb0f24d2ae3d1f813ec0f4369865a1c6ef322
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92689050"
---
# <a name="tutorial-create-a-jenkins-pipeline-using-github-and-docker"></a>자습서: GitHub 및 Docker를 사용하여 Jenkins 파이프라인 만들기

애플리케이션 개발의 빌드 및 테스트 단계를 자동화하려면 CI/CD(지속적인 통합 및 배포) 파이프라인을 사용할 수 있습니다. 이 자습서에서는 Azure VM에서 CI/CD 파이프라인을 만들며 다음 방법이 포함됩니다.

> [!div class="checklist"]
> * Jenkins VM 만들기
> * Jenkins 설치 및 구성
> * GitHub 및 Jenkins 간 웹후크 통합 만들기
> * GitHub 커밋에서 Jenkins 빌드 작업 만들기 및 트리거
> * 앱에 대한 Docker 이미지 만들기
> * 새 Docker 이미지를 빌드한 GitHub 커밋 및 앱을 실행하는 업데이트 확인

이 자습서에서는 지속적으로 최신 버전으로 업데이트되는 [Azure Cloud Shell](/azure/cloud-shell/overview) 내의 CLI를 사용합니다. Cloud Shell을 열려면 코드 블록 상단에서 **사용해 보세요** 를 선택합니다.

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.0.30 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-jenkins-instance"></a>Jenkins 인스턴스 만들기
[처음 부팅 시 Linux 가상 머신을 사용자 지정하는 방법](/azure/virtual-machines/linux/tutorial-automate-vm-deployment)에 대한 이전 자습서에서 cloud-init를 사용하여 VM 사용자 지정을 자동화하는 방법을 배웠습니다. 이 자습서는 cloud-init 파일을 사용하여 VM에 Jenkins 및 Docker를 설치합니다. 널리 사용되는 오픈 소스 자동화 서버인 Jenkins는 Azure와 원활하게 통합되어 CI(지속적인 통합) 및 CD(지속적인 업데이트)를 지원합니다. Jenkins 사용 방법에 대한 자세한 자습서는 [Jenkins Azure Hub](/azure/jenkins/)를 참조하세요.

현재 셸에서 *cloud-init-jenkins.txt* 라는 파일을 만들고 다음 구성을 붙여넣습니다. 예를 들어 로컬 컴퓨터에 없는 Cloud Shell에서 파일을 만듭니다. `sensible-editor cloud-init-jenkins.txt`를 입력하여 파일을 만들고 사용할 수 있는 편집기의 목록을 봅니다. 전체 cloud-init 파일, 특히 첫 줄이 올바르게 복사되었는지 확인합니다.

```yaml
#cloud-config
package_upgrade: true
write_files:
  - path: /etc/systemd/system/docker.service.d/docker.conf
    content: |
      [Service]
        ExecStart=
        ExecStart=/usr/bin/dockerd
  - path: /etc/docker/daemon.json
    content: |
      {
        "hosts": ["fd://","tcp://127.0.0.1:2375"]
      }
runcmd:
  - apt install openjdk-8-jre-headless -y
  - wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
  - sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  - apt-get update && apt-get install jenkins -y
  - curl -sSL https://get.docker.com/ | sh
  - usermod -aG docker azureuser
  - usermod -aG docker jenkins
  - service jenkins restart
```

VM을 만들려면 먼저 [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroupJenkins* 라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroupJenkins --location eastus
```

이제 [az vm create](/cli/azure/vm)로 VM을 만듭니다. `--custom-data` 매개 변수를 사용하여 cloud-init 구성 파일을 전달합니다. 현재 작업 디렉터리 외부에 파일을 저장한 경우 *cloud-init-jenkins.txt* 에 전체 경로를 제공합니다.

```azurecli-interactive 
az vm create --resource-group myResourceGroupJenkins \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
```

VM을 만들고 구성하는 데 몇 분 정도 걸립니다.

웹 트래픽이 VM에 연결되도록 허용하려면 [az vm open-port](/cli/azure/vm)를 사용하여 샘플 앱을 실행하는 데 사용되는 Jenkins 트래픽에 대한 포트 *8080* 및 Node.js 앱에 대한 포트 *1337* 을 엽니다.

```azurecli-interactive 
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 8080 --priority 1001
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 1337 --priority 1002
```


## <a name="configure-jenkins"></a>Jenkins 구성
Jenkins 인스턴스에 액세스하려면 VM의 공용 IP 주소를 가져옵니다.

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

보안을 위해 VM에서 텍스트 파일로 저장된 초기 관리자 암호를 입력하여 Jenkins 설치를 시작해야 합니다. 이전 단계에서 가져온 공용 IP 주소를 사용하여 VM에 SSH를 수행합니다.

```bash
ssh azureuser@<publicIps>
```

`service` 명령을 사용하여 Jenkins가 실행되고 있는지 확인합니다.

```bash
$ service jenkins status
● jenkins.service - LSB: Start Jenkins at boot time
   Loaded: loaded (/etc/init.d/jenkins; generated)
   Active: active (exited) since Tue 2019-02-12 16:16:11 UTC; 55s ago
     Docs: man:systemd-sysv-generator(8)
    Tasks: 0 (limit: 4103)
   CGroup: /system.slice/jenkins.service

Feb 12 16:16:10 myVM systemd[1]: Starting LSB: Start Jenkins at boot time...
...
```

Jenkins 설치를 위해 `initialAdminPassword`를 보고 복사합니다.

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

파일을 아직 사용할 수 없는 경우 cloud-init이 Jenkins 및 Docker 설치를 완료할 때까지 몇 분 더 기다립니다.

이제 웹 브라우저를 열고 `http://<publicIps>:8080`으로 이동합니다. 다음과 같이 초기 Jenkins 설치를 완료합니다.

- **설치할 플러그 인 선택** 을 선택합니다.
- 상단의 텍스트 상자에서 *GitHub* 를 검색합니다. *GitHub* 에 대한 확인란을 선택한 다음, **설치** 를 선택합니다.
- 첫 번째 관리 사용자를 만듭니다. **admin** 과 같은 사용자 이름을 입력한 다음, 고유하고 안전한 암호를 입력합니다. 마지막으로, 전체 이름 및 이메일 주소를 입력합니다.
- **저장 및 끝내기** 를 선택합니다.
- Jenkins가 준비되면 **Jenkins를 사용하여 시작** 을 선택합니다.
  - Jenkins를 사용하여 시작할 때 웹 브라우저에 빈 페이지가 표시되는 경우 Jenkins 서비스를 다시 시작합니다. SSH 세션에서 `sudo service jenkins restart`를 입력한 다음, 웹 브라우저를 새로 고칩니다.
- 필요한 경우 만든 사용자 이름과 암호를 사용하여 Jenkins에 로그인합니다.


## <a name="create-github-webhook"></a>GitHub 웹후크 만들기
GitHub를 통해 통합을 구성하려면 Azure 샘플 리포지토리에서 [Node.js 헬로 월드 샘플 앱](https://github.com/Azure-Samples/nodejs-docs-hello-world)을 엽니다. 사용자 고유의 GitHub 계정에 리포지토리를 분기하려면 오른쪽 위 모서리에 있는 **분기** 단추를 선택합니다.

만든 분기 내부에 웹후크를 만듭니다.

- **설정** 을 선택한 다음, 왼쪽에 있는 **웹후크** 를 선택합니다.
- **Add webhook** (웹후크 추가)를 선택한 다음, 필터 상자에서 *Jenkins* 를 입력합니다.
- **Payload URL** (페이로드 URL)에 대해 `http://<publicIps>:8080/github-webhook/`를 입력합니다. 후행 슬래시(/)를 포함해야 합니다.
- **Content type** (콘텐츠 형식)에 대해 *application/x-www-form-urlencoded* 를 선택합니다.
- **Which events would you like to trigger this webhook?** (이 웹후크가 트리거되도록 하려는 이벤트는 무엇입니까?)에 대해 *Just the push event* (푸시 이벤트만)를 선택합니다.
- **Active** (활성)를 선택합니다.
- **Add webhook** 를 클릭합니다.

![분기된 리포지토리에 GitHub 웹후크 추가](media/pipeline-with-github-and-docker/github-webhook.png)


## <a name="create-jenkins-job"></a>Jenkins 작업 만들기
코드 커밋과 같은 GitHub의 이벤트에 대해 Jenkins가 응답하도록 하려면 Jenkins 작업을 만듭니다. 사용자 고유의 GitHub 포크에 대한 URL을 사용합니다.

Jenkins 웹 사이트에서 홈 페이지에서 **새 작업 만들기** 를 선택합니다.

- *HelloWorld* 를 작업 이름으로 입력합니다. **프리스타일 프로젝트** 를 선택한 다음 **확인** 을 선택합니다.
- **일반** 섹션에서 **GitHub 프로젝트** 를 선택하고 `https://github.com/cynthn/nodejs-docs-hello-world`와 같은 포크된 리포지토리 URL을 입력합니다.
- **소스 코드 관리** 섹션에서 **Git** 을 선택하고 `https://github.com/cynthn/nodejs-docs-hello-world.git`과 같은 포크된 리포지토리 *.git* URL을 입력합니다.
- **트리거 빌드** 에서 **GITscm 폴링에 대한 GitHub 후크 트리거** 를 선택합니다.
- **빌드** 섹션 아래에서 **빌드 단계 추가** 를 선택합니다. **셸 실행** 을 선택한 다음 명령 창에 `echo "Test"` 명령을 입력합니다.
- 작업 창 맨 아래에서 **저장** 을 선택합니다.


## <a name="test-github-integration"></a>GitHub 통합 테스트
Jenkins와 GitHub의 통합을 테스트하려면 사용자 분기의 변경 내용을 커밋합니다. 

GitHub 웹 UI로 돌아가서 분기된 리포지토리를 선택한 다음, **index.js** 파일을 선택합니다. 연필 아이콘을 선택하여 이 파일을 편집하면 6번 줄은 다음과 같습니다.

```javascript
response.end("Hello World!");
```

변경 사항을 커밋하려면 맨 아래쪽에 **변경 내용 커밋** 단추를 선택합니다.

Jenkins에서 작업 페이지의 왼쪽 아래 모서리에 있는 **기록 빌드** 섹션에서 새 빌드가 시작됩니다. 빌드 번호 링크를 선택하고 왼쪽에 있는 **콘솔 출력** 을 선택합니다. 코드가 GitHub에서 로드되면 Jenkins가 수행하는 단계와 콘솔에 메시지 `Test`을 출력하는 빌드 작업을 확인할 수 있습니다. GitHub에서 커밋이 수행될 때마다 웹후크는 Jenkins에 도달하며, 이 방법으로 새 빌드를 트리거합니다.


## <a name="define-docker-build-image"></a>Docker 빌드 이미지 정의
GitHub 커밋에 기반하여 실행되는 Node.js 앱을 확인하려면 Docker 이미지를 빌드하여 앱을 실행합니다. 이미지는 앱을 실행하는 컨테이너를 구성하는 방법을 정의하는 Dockerfile에서 빌드됩니다. 

SSH 연결부터 VM까지 이전 단계에서 만든 작업 이후에 명명된 Jenkins 작업 영역 디렉터리를 변경합니다. 이 예제에서는 *HelloWorld* 라고 명명되었습니다.

```bash
cd /var/lib/jenkins/workspace/HelloWorld
```

`sudo sensible-editor Dockerfile`을 사용하여 이 작업 영역 디렉터리에 파일을 만들고 다음 콘텐츠를 붙여넣습니다. 전체 Dockerfile, 특히 첫 줄이 올바르게 복사되었는지 확인합니다.

```yaml
FROM node:alpine

EXPOSE 1337

WORKDIR /var/www
COPY package.json /var/www/
RUN npm install
COPY index.js /var/www/
```

이 Dockerfile은 Alpine Linux를 사용하는 기본 Node.js 이미지를 사용하며, 헬로 월드 앱이 실행되는 포트 1337을 노출한 다음, 앱 파일을 복사하고 초기화합니다.


## <a name="create-jenkins-build-rules"></a>Jenkins 빌드 규칙 만들기
이전 단계에서 콘솔에 메시지를 출력하는 기본 Jenkins 빌드 규칙을 만들었습니다. Dockerfile을 사용하고 앱을 실행하는 빌드 단계를 만들겠습니다.

Jenkins 인스턴스로 돌아가서 이전 단계에서 만든 작업을 선택합니다. 왼쪽에 있는 **구성** 을 선택하고 **빌드** 섹션까지 아래로 스크롤합니다.

- 기존 `echo "Test"` 빌드 단계를 제거합니다. 기존 빌드 단계 상자의 오른쪽 위 모서리에 있는 빨간색 십자가를 선택합니다.
- **빌드 단계 추가** 를 선택한 다음, **셸 실행** 을 선택합니다.
- **명령** 상자에서 다음 Docker 명령을 입력한 다음 **저장** 을 선택합니다.

  ```bash
  docker build --tag helloworld:$BUILD_NUMBER .
  docker stop helloworld && docker rm helloworld
  docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
  ```

Docker 빌드 단계는 이미지를 만들고, 이미지에 대한 기록을 유지할 수 있도록 하는 Jenkins 빌드 번호로 태그를 만듭니다. 앱을 실행하는 기존 컨테이너가 모두 중지되었다가 제거됩니다. 새 컨테이너는 이미지를 사용하여 시작되었다가 GitHub의 최신 커밋에 따라 Node.js 앱을 실행합니다.


## <a name="test-your-pipeline"></a>파이프라인 테스트
전체 파이프라인의 실제 동작을 확인하려면 분기된 GitHub 리포지토리에서 다시 *index.js* 파일을 편집하고 **변경 내용 커밋** 을 선택합니다. GitHub에 대한 웹후크에 기반하는 Jenkins에서 새 작업이 시작됩니다. Docker 이미지를 만들고 새 컨테이너에서 앱을 시작하는 데는 몇 초가 걸립니다.

필요한 경우 다시 VM의 공용 IP 주소를 가져옵니다.

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

웹 브라우저를 열고 `http://<publicIps>:1337`을 입력합니다. Node.js 앱이 표시되고 다음과 같이 GitHub 분기에서 최신 커밋 내용을 반영합니다.

![Node.js 앱 실행](media/pipeline-with-github-and-docker/running-nodejs-app.png)

이제 GitHub의 *index.js* 파일을 다시 편집하고 변경 내용을 커밋합니다. Jenkins에서 작업이 완료되는 동안 몇 초 대기한 다음, 웹 브라우저를 새로 고쳐서 다음과 같이 새 컨테이너에서 실행되는 앱의 업데이트된 버전을 확인합니다.

![다른 GitHub 커밋 후 Node.js 앱 실행](media/pipeline-with-github-and-docker/another-running-nodejs-app.png)


## <a name="next-steps"></a>다음 단계
이 자습서에서는 각 코드 커밋에서 Jenkins 빌드 작업을 실행한 다음 앱을 테스트하는 Docker 컨테이너를 배포하도록 GitHub를 구성하였습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Jenkins VM 만들기
> * Jenkins 설치 및 구성
> * GitHub 및 Jenkins 간 웹후크 통합 만들기
> * GitHub 커밋에서 Jenkins 빌드 작업 만들기 및 트리거
> * 앱에 대한 Docker 이미지 만들기
> * 새 Docker 이미지를 빌드한 GitHub 커밋 및 앱을 실행하는 업데이트 확인

Azure DevOps Services와 Jenkins를 통합하는 방법에 대한 자세한 내용을 보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [Jenkins 및 Azure DevOps Services를 사용하여 앱 배포](deploy-to-linux-vm-using-azure-devops-services.md)
