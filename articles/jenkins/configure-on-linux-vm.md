---
title: 빠른 시작 - Azure CLI를 사용하여 Jenkins 구성
description: Jenkins를 Azure Linux 가상 머신에 설치하고 Java 애플리케이션 샘플을 빌드하는 방법을 알아봅니다.
keywords: Jenkins, Azure, DevOps, 포털, Linux, 가상 머신
ms.topic: quickstart
ms.date: 08/19/2020
ms.custom: devx-track-jenkins
ms.openlocfilehash: b5be59dc1ed3fab69051a8ddd23576e27c966a7b
ms.sourcegitcommit: 800c5e05ad3c0b899295d381964dd3d47436ff90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88614552"
---
# <a name="quickstart-configure-jenkins-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Jenkins 구성

이 빠른 시작은 Azure에서 사용 가능하도록 구성된 도구와 플러그 인을 사용하여 Ubuntu Linux VM에 [Jenkins](https://jenkins.io)를 설치하는 방법을 보여줍니다.

이 빠른 시작에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * Jenkins를 다운로드하고 설치하는 설치 파일 만들기
> * 리소스 그룹 만들기
> * 설치 파일을 사용하여 가상 머신 만들기
> * 가상 머신에 SSH로 연결할 수 있도록 8080 포트 열기
> * SSH를 통해 가상 머신에 연결
> * GitHub에서 샘플 Java 앱을 기반으로 샘플 Jenkins 작업 구성
> * 샘플 Jenkins 작업 빌드

## <a name="prerequisites"></a>전제 조건

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="troubleshooting"></a>문제 해결

Jenkins를 구성할 때 문제가 발생할 경우 최신 지침 및 알려진 문제에 대한 [Cloudbees Jenkins 설치 페이지](https://www.jenkins.io/doc/book/installing/)를 참조하세요.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. [Azure Cloud Shell](/azure/cloud-shell/overview)을 열고(아직 열지 않은 경우) **Bash**로 전환합니다.

1. `cloud-init-jenkins.txt`라는 파일을 만듭니다.

    ```bash
    code cloud-init-jenkins.txt
    ```

1. 다음 코드를 새 파일에 붙여넣습니다.

    ```json
    #cloud-config
    package_upgrade: true
    runcmd:
      - apt install openjdk-8-jdk -y
      - wget -qO - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
      - sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
      - apt-get update && apt-get install jenkins -y
      - service jenkins restart
    ```

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

1. [az group create](/cli/azure/group#az-group-create)를 사용하여 리소스 그룹을 만듭니다. `--location` 매개 변수를 사용자 환경에 적절한 값으로 바꿔야 할 수 있습니다.

    ```azurecli
    az group create \
    --name QuickstartJenkins-rg \
    --location eastus
    ```

1. [az vm create](/cli/azure/vm#az-vm-create)를 사용하여 가상 머신을 만듭니다.

    ```azurecli
    az vm create \
    --resource-group QuickstartJenkins-rg \
    --name QuickstartJenkins-vm \
    --image UbuntuLTS \
    --admin-username "azureuser" \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
    ```

1. [az vm list](/cli/azure/vm#az-vm-list)를 사용하여 새 가상 머신의 생성(및 상태)을 확인합니다.

    ```azurecli
    az vm list -d -o table --query "[?name=='QuickstartJenkins-vm']"
    ```

1. [az vm open](/cli/azure/vm#az-vm-open-port)을 사용하여 새 가상 머신에서 8080 포트를 엽니다.

    ```azurecli
    az vm open-port \
    --resource-group QuickstartJenkins-rg \
    --name QuickstartJenkins-vm  \
    --port 8080 --priority 1010
    ```

## <a name="configure-jenkins"></a>Jenkins 구성

1. [az vm show](/cli/azure/vm#az-vm-show)를 사용하여 샘플 가상 머신의 공용 IP 주소를 가져옵니다.

    ```azurecli
    az vm show \
    --resource-group QuickstartJenkins-rg \
    --name QuickstartJenkins-vm -d \
    --query [publicIps] \
    --output tsv
    ```

    **참고**:

    - `--query` 매개 변수는 가상 머신의 공용 IP 주소로 출력을 제한합니다.

1. 이전 단계에서 검색된 IP 주소를 사용하여 가상 머신에 SSH로 연결합니다. 연결 요청을 확인해야 합니다.

    ```azurecli
    ssh azureuser@<ip_address>
    ```

    **참고**:

    - 연결에 성공하면 Cloud Shell 프롬프트에 사용자 이름 및 가상 머신 이름(`azureuser@QuickstartJenkins-vm`)이 포함됩니다.

1. Jenkins 서비스의 상태를 가져와 Jenkins가 실행 중인지 확인합니다.

    ```bash
    service jenkins status
    ```

1. 자동 생성된 Jenkins 암호를 가져옵니다.

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

1. IP 주소를 사용하여 브라우저에서 다음 URL을 엽니다. `http://<ip_address>:8080`

1. 앞서 검색한 암호를 입력하고 **계속**을 선택합니다.

    ![Jenkins 잠금을 해제하는 초기 페이지](./media/configure-on-linux-vm/unlock-jenkins.png)

1. **설치할 플러그인 선택**을 선택합니다.

    ![선택한 플러그 인을 설치하는 옵션을 선택합니다.](./media/configure-on-linux-vm/select-plugins.png)

1. 페이지 맨 위에 있는 필터 상자에 `github`를 입력합니다. GitHub 플러그인을 선택하고 **설치**를 선택합니다.

    ![GitHub 플러그 인 설치](./media/configure-on-linux-vm/install-github-plugin.png)

1. 첫 번째 관리 사용자의 정보를 입력하고 **저장 및 계속**을 선택합니다.

    ![첫 번째 관리 사용자에 대한 정보 입력](./media/configure-on-linux-vm/create-first-user.png)

1. **인스턴스 구성** 페이지에서 **저장 및 마침**을 선택합니다.

    ![인스턴스 구성의 확인 페이지](./media/configure-on-linux-vm/instance-configuration.png)

1. **Jenkins 사용 시작**을 선택합니다.

    ![Jenkins가 준비되었습니다.](./media/configure-on-linux-vm/start-using-jenkins.png)

## <a name="create-your-first-job"></a>첫 번째 작업 만들기

1. Jenkins 홈페이지에서 **작업 만들기**를 선택합니다.

    ![Jenkins 콘솔 홈페이지](./media/configure-on-linux-vm/jenkins-home-page.png)

1. 작업 이름에 `mySampleApp`을 입력하고 **프리스타일 프로젝트**를 선택한 후 **확인**을 선택합니다.

    ![새 작업 만들기](./media/configure-on-linux-vm/new-job.png)

1. **소스 코드 관리** 탭을 선택합니다. **Git**을 사용하도록 설정하고 **리포지토리 URL** 값에 다음 URL을 입력합니다. `https://github.com/spring-guides/gs-spring-boot.git`

    ![Git 리포지토리 정의](./media/configure-on-linux-vm/source-code-management.png)

1. **빌드** 탭을 선택한 후 **빌드 단계 추가**를 선택합니다.

    ![새 빌드 단계 추가](./media/configure-on-linux-vm/add-build-step.png)

1. 드롭다운 메뉴에서 **Gradle 스크립트 호출**을 선택합니다.

    ![Gradle 스크립트 옵션 선택](./media/configure-on-linux-vm/invoke-gradle-script-option.png)

1. **Gradle 래퍼 사용**을 선택한 후 **래퍼 위치**에 `complete`를 입력하고 **작업**에 `build`를 입력합니다.

    ![Gradle 스크립트 옵션](./media/configure-on-linux-vm/gradle-script-options.png)

1. **고급**을 선택하고 **루트 빌드 스크립트** 필드에 `complete`를 입력합니다.

    ![고급 Gradle 스크립트 옵션](./media/configure-on-linux-vm/root-build-script.png)

1. 페이지의 아래쪽으로 스크롤하고 **저장**을 선택합니다.

## <a name="build-the-sample-java-app"></a>샘플 Java 앱 빌드

1. 프로젝트에 대한 홈페이지가 표시되면 **지금 빌드**를 선택하여 코드를 컴파일하고 샘플 앱을 패키징합니다.

    ![프로젝트 홈페이지](./media/configure-on-linux-vm/project-home-page.png)

1. **빌드 기록** 제목 아래의 그래픽은 작업을 빌드하는 중임을 나타냅니다.

    ![작업 빌드 진행 중](./media/configure-on-linux-vm/job-currently-building.png)

1. 빌드가 완료되면 **작업 영역** 링크를 선택합니다.

    ![작업 영역 링크 선택](./media/configure-on-linux-vm/job-workspace.png)

1. `complete/build/libs`로 이동하여 `.jar` 파일이 성공적으로 빌드되었는지 확인합니다.

    ![대상 라이브러리에서 빌드가 성공했는지 확인합니다.](./media/configure-on-linux-vm/successful-build.png)

1. 이제 Azure에서 Jenkins 서버로 고유한 프로젝트를 빌드할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure의 Jenkins](/azure/developer/jenkins)