---
title: 자습서 - Azure에서 실행 중인 VM을 사용하여 Jenkins 배포 크기 조정
description: Azure 가상 머신을 사용하여 추가 용량을 Jenkins 파이프라인에 추가하는 방법 알아보기
keywords: Jenkins, Azure, DevOps, 가상 머신, 에이전트
ms.topic: tutorial
ms.date: 01/08/2021
ms.custom: devx-track-jenkins,devx-track-jenkins
ms.openlocfilehash: 599fe9875d1f9ffd107cca80f9445bba18100f42
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561039"
---
# <a name="tutorial-scale-jenkins-deployments-with-vm-running-in-azure"></a>자습서: Azure에서 실행 중인 VM을 사용하여 Jenkins 배포 크기 조정

[!INCLUDE [jenkins-integration-with-azure.md](includes/jenkins-integration-with-azure.md)]

이 자습서에서는 Azure에서 Linux 가상 머신을 만들고 해당 VM을 Jenkins에 작업 노드로 추가하는 방법을 보여 줍니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 에이전트 머신 만들기
> * Jenkins에 에이전트 추가
> * 새 Jenkins 프리스타일 작업 만들기
> * Azure VM 에이전트에서 작업 실행

## <a name="prerequisites"></a>필수 구성 요소

- **Jenkins 설치**: Jenkins 설치에 대한 액세스 권한이 없는 경우 [Azure CLI를 사용하여 Jenkins를 구성](configure-on-linux-vm.md)합니다.

## <a name="configure-agent-virtual-machine"></a>에이전트 가상 머신 구성

1. [az group create](/cli/azure/group?#az_group_create)를 사용하여 Azure 리소스 그룹을 만듭니다.

    ```azurecli
    az group create --name <resource_group> --location <location>
    ```

1. [az vm create](/cli/azure/vm#az_vm_create)를 사용하여 가상 머신을 만듭니다.

    ```azurecli
    az vm create --resource-group <resource-group> --name <vm_name> --image UbuntuLTS --admin-username azureuser --admin-password "<password>"
    ```

    **참고**:

    - 또한 `--ssh-key-value <ssh_path>` 명령을 사용하여 SSH 키를 업로드할 수도 있습니다.

1. JDK를 설치합니다.  

    #### <a name="linux"></a>[Linux](#tab/linux)
    
    1. SSH 도구를 사용하여 가상 머신에 로그인합니다.
    
        ```bash
        ssh username@123.123.123.123
        ```
        
    1. apt를 사용하여 JDK를 설치합니다. yum 또는 pacman 같은 다른 패키지 관리자 도구를 사용하여 설치할 수도 있습니다.
    
        ```bash
        sudo apt-get install -y default-jdk
        ```
    
    1. 설치가 완료되면 `java -version`을 실행하여 Java 환경을 확인합니다. 출력에는 JDK의 다양한 부분과 관련된 버전 번호가 포함됩니다.
    
    #### <a name="windows"></a>[Windows](#tab/windows)
    
    1. SSH 도구 또는 원격 데스크톱 연결을 사용하여 가상 머신에 로그인합니다.
    
    1. 사용자 환경에 적합한 [JDK를 다운로드](https://www.oracle.com/java/technologies/javase-downloads.html)합니다.
    
    1. JDK 설치
    
## <a name="configure-jenkins-url"></a>Jenkins URL 구성

JNLP를 사용하는 경우 Jenkins URL을 구성해야 합니다.

1. 메뉴에서 **Jenkins 관리** 를 선택합니다.

1. **시스템 구성** 에서 **시스템 구성** 을 선택합니다.

1. **Jenkins URL** 이 Jenkins 설치의 HTTP 주소(`http://<your_host>.<your_domain>:8080/`)로 설정되었는지 확인합니다.

1. **저장** 을 선택합니다.

## <a name="add-agent-to-jenkins"></a>Jenkins에 에이전트 추가

1. 메뉴에서 **Jenkins 관리** 를 선택합니다.

1. **시스템 구성** 에서 **노드 및 클라우드 관리** 를 선택합니다.

1. 메뉴에서 **새 노드** 를 선택합니다.

1. **노드 이름** 에 값을 입력합니다.

1. **영구 에이전트** 를 선택합니다.

1. **확인** 을 선택합니다.

1. 다음 필드에 값을 지정합니다.

    - **이름**: 새 Jenkins 설치 내에서 에이전트를 식별하는 고유 이름을 지정합니다. 이 값은 에이전트 호스트 이름과 다를 수 있습니다. 그러나 두 값을 동일하게 설정하는 것이 편리합니다. 이름 값에는 모든 특수 문자(`?*/\%!@#$^&|<>[]:;`)를 사용할 수 있습니다.

    - **원격 루트 디렉터리**: 에이전트에는 Jenkins 전용 디렉터리가 있어야 합니다. 에이전트에 이 디렉터리의 경로를 지정합니다. `/home/azureuser/work` 또는 `c:\jenkins` 같은 절대 경로를 사용하는 것이 가장 좋습니다. 이는 에이전트 머신의 로컬 경로여야 합니다. 마스터에 이 경로를 표시할 필요는 없습니다. ./jenkins-agent 같은 상대 경로를 사용하는 경우 시작 방법에 지정된 작업 디렉터리를 기준으로 경로가 설정됩니다.

    - **레이블**: 레이블은 의미상 관련된 에이전트를 하나의 논리 그룹으로 그룹화하는 데 사용됩니다. 예를 들어 Linux의 Ubuntu 배포판을 실행하는 모든 에이전트에는 `UBUNTU` 레이블을 정의할 수 있습니다.

    - **시작 방법**: 원격 Jenkins 노드를 시작하는 방법에는 **SSH를 통해 에이전트 시작** 및 **마스터에서 명령 실행을 통해 에이전트 시작** 같은 두 가지가 있습니다.

        - **SSH를 통해 에이전트 시작**: 다음 필드에 값을 지정합니다.

            - **호스트**: VM 공용 IP 주소 또는 도메인 이름. 예를 들어 `123.123.123.123` 또는 `example.com`와 같습니다.

            - **자격 증명**: 원격 호스트에 로그인하는 데 사용할 자격 증명을 선택합니다. **추가** 단추를 선택하여 새 자격 증명을 정의한 후 생성된 새 자격 증명을 선택할 수도 있습니다.

            - **호스트 키 확인 전략**: Jenkins가 연결하는 동안 원격 호스트에서 제공하는 SSH 키를 확인하는 방법을 제어합니다.

            ![SSH를 통해 에이전트 시작이라는 시작 방법을 지정하는 노드 구성 예제](./media/scale-deployments-using-vm-agents/ssh2.png)

        - **마스터에서 명령 실행을 통해 에이전트 시작**:

            - `https://<your_jenkins_host_name>/jnlpJars/agent.jar`에서 `agent.jar`를 다운로드합니다. 예들 들어 `https://localhost:8443/jnlpJars/agent.jar`입니다.

            - `agent.jar`를 가상 머신에 업로드합니다.

            - `ssh <node_host> java -jar <remote_agentjar_path>` 명령을 사용하여 Jenkins를 시작합니다. 예들 들어 `ssh azureuser@99.99.999.9 java -jar /home/azureuser/agent.jar`입니다.

            ![마스터에서 명령 실행을 통해 에이전트 시작이라는 시작 방법을 지정하는 노드 구성 예제](./media/scale-deployments-using-vm-agents/config.png)

1. **저장** 을 선택합니다.

구성을 정의하면 Jenkins가 가상 머신을 새 작업 노드로 추가합니다.

![가상 머신을 새 작업 노드로 추가하는 예제](./media/scale-deployments-using-vm-agents/commandstart.png)

## <a name="create-a-job-in-jenkins"></a>Jenkins에서 작업 만들기

1. 메뉴에서 **새 항목** 을 선택합니다.

1. 이름으로 `demoproject1`를 입력합니다.

1. **프리스타일 프로젝트** 를 선택합니다.

1. **확인** 을 선택합니다.

1. **일반** 탭에서 **프로젝트를 실행할 수 있는 위치 제한** 을 선택하고 **레이블 식** 에 `ubuntu`를 입력합니다. 이전 단계에서 만든 클라우드 구성에 의해 레이블이 처리된다는 확인 메시지가 표시됩니다.

   ![새 Jenkins 작업 설정](./media/scale-deployments-using-vm-agents/job-config.png)

1. **소스 코드 관리** 탭에서 **Git** 을 선택하고 **리포지토리 URL** 필드에 다음 URL을 추가합니다. `https://github.com/spring-projects/spring-petclinic.git`

1. **빌드** 섹션에서 **빌드 단계 추가**, **최상위 Maven 대상 호출** 을 차례로 선택합니다. **목표** 필드에 `package`를 입력합니다.

1. **저장** 을 선택합니다.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Azure VM 에이전트에서 새 작업 빌드

1. 이전 단계에서 만든 작업을 선택합니다.

1. **지금 빌드** 를 선택합니다. 새 빌드가 큐에 추가됩니다. 에이전트 VM이 Azure 구독에 만들어져야 시작됩니다.

1. 빌드가 완료되면 **콘솔 출력** 으로 이동합니다. 해당 빌드가 Azure 에이전트에서 원격으로 수행되었음이 표시됩니다.

    ![콘솔 출력](./media/scale-deployments-using-vm-agents/console-output.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure App Service에 CI/CD](./deploy-to-azure-app-service-using-azure-cli.md)