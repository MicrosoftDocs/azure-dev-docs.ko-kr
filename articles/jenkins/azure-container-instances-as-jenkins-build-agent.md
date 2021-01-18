---
title: 자습서 - Azure Container Instances를 Jenkins 빌드 에이전트로 사용
description: Azure Container Instances에서 빌드 작업을 실행하도록 Jenkins 서버를 구성하는 방법을 알아봅니다.
keywords: Jenkins, Azure, DevOps, Container Instances, 빌드 에이전트
ms.topic: article
ms.date: 01/08/2021
ms.custom: devx-track-jenkins,devx-track-azurecli
ms.openlocfilehash: 678f1e19895f43e519bccaea4cdc9d796f91da91
ms.sourcegitcommit: 347bfa3b6c34579c567d1324efc63c1d6672a75b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109214"
---
# <a name="tutorial-use-azure-container-instances-as-a-jenkins-build-agent"></a>자습서: Azure Container Instances를 Jenkins 빌드 에이전트로 사용

[!INCLUDE [jenkins-integration-with-azure.md](includes/jenkins-integration-with-azure.md)]

ACI(Azure Container Instances)는 컨테이너화된 워크로드를 실행하기 위한 격리된 주문형, 버스터블 환경을 제공합니다. 이러한 특성 때문에 ACI는 Jenkins 빌드 작업을 대규모로 실행할 수 있는 우수한 플랫폼을 만들어 줍니다. 이 문서에서는 ACI를 배포하고 Jenkins 컨트롤러용 영구 빌드 에이전트로 추가하는 방법을 보여 줍니다.

Azure Container Instances에 대한 자세한 내용은 [Azure Container Instances 정보](/azure/container-instances/container-instances-overview)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- **Jenkins 서버**: Jenkins 서버가 설치되어 있지 않다면 [Azure에서 Jenkins 서버를 만듭니다](./configure-on-linux-vm.md).

## <a name="prepare-the-jenkins-controller"></a>Jenkins 컨트롤러 준비

1. Jenkins 포털로 이동합니다.

1. 메뉴에서 **Jenkins 관리** 를 선택합니다.

1. **시스템 구성** 에서 **시스템 구성** 을 선택합니다.

1. **Jenkins URL** 이 Jenkins 설치의 HTTP 주소(`http://<your_host>.<your_domain>:8080/`)로 설정되었는지 확인합니다.

1. 메뉴에서 **Jenkins 관리** 를 선택합니다.

1. **보안** 에서 **글로벌 보안 구성** 을 선택합니다.

1. **에이전트** 에서 **고정** 포트를 지정하고 사용자 환경에 적절한 포트 번호를 입력합니다.

    구성 예제:  ![TCP 포트 구성](./media/azure-container-instances-as-jenkins-build-agent/agent-port.png)

1. **저장** 을 선택합니다.

## <a name="create-jenkins-work-agent"></a>Jenkins 작업 에이전트 만들기

1. Jenkins 포털로 이동합니다.

1. 메뉴에서 **Jenkins 관리** 를 선택합니다.

1. **시스템 구성** 에서 **노드 및 클라우드 관리** 를 선택합니다.

1. 메뉴에서 **새 노드** 를 선택합니다.

1. **노드 이름** 에 값을 입력합니다.

1. **영구 에이전트** 를 선택합니다.

1. **확인** 을 선택합니다.

1. **원격 루트 디렉터리** 에 값을 입력합니다. 예를 들어 `/home/jenkins/work`

1. 필요할 경우 레이블을 입력합니다. 레이블은 여러 에이전트를 하나의 논리 그룹으로 그룹화하는 데 사용됩니다. 레이블의 예로 Linux 에이전트를 그룹화하는 `linux`가 있습니다.

1. **시작 방법** 을 **마스터에 연결하여 에이전트 시작** 으로 설정합니다.

1. 모든 필수 필드를 지정하거나 입력했는지 확인합니다.

    ![Jenkins 에이전트 구성 예](./media/azure-container-instances-as-jenkins-build-agent/agent-config.png)

1. **저장** 을 선택합니다.

1. 에이전트 상태 페이지에 `JENKINS_SECRET` 및 `AGENT_NAME`이 표시되어야 합니다. 다음 스크린샷은 값을 식별하는 방법을 보여 줍니다. 두 값은 모두 Azure Container Instance를 만들 때 필요합니다.

    ![성공적으로 생성되면 빌드 에이전트 비밀이 표시됩니다.](./media/azure-container-instances-as-jenkins-build-agent/jenkins-secret.png)

## <a name="create-azure-container-instance-with-cli"></a>CLI를 사용하여 Azure Container Instance 만들기

1. [az group create](/cli/azure/group?#az_group_create)를 사용하여 Azure 리소스 그룹을 만듭니다.

      ```azurecli
      az group create --name my-resourcegroup --location westus
      ```

1. [az container create](https://docs.microsoft.com/cli/azure/container#az_container_create)를 사용하여 Azure Container Instance를 만듭니다. 자리 표시자를 작업 에이전트를 만들 때 얻은 값으로 바꿉니다.

    ```azurecli
    az container create \
      --name my-dock \
      --resource-group my-resourcegroup \
      --ip-address Public --image jenkins/inbound-agent:latest \
      --os-type linux \
      --ports 80 \
      --command-line "jenkins-agent -url http://jenkinsserver:port <JENKINS_SECRET> <AGENT_NAME>"
    ```

    컨테이너가 시작되면 Jenkins 컨트롤러 서버에 자동으로 연결됩니다.

    ![에이전트가 시작됨](./media/azure-container-instances-as-jenkins-build-agent/agent-start.png)

## <a name="create-a-build-job"></a>빌드 작업 만들기

이제 Jenkins 빌드 작업을 만들어서 Azure Container Instance에서 Jenkins 빌드를 보여줍니다.

1. **새 항목** 을 선택하고, 빌드 프로젝트의 이름을 지정하고(예: **aci-demo**), **프리스타일 프로젝트** 를 선택하고, **확인** 을 선택합니다.

   ![빌드 작업 이름 상자와 프로젝트 형식 목록](./media/azure-container-instances-as-jenkins-build-agent/jenkins-new-job.png)

2. **일반** 에서 **이 프로젝트를 실행할 수 있는 위치 제한** 을 선택합니다. 레이블 식에 **linux** 를 입력합니다. 이렇게 구성하면 이 빌드 작업이 ACI 클라우드에서 실행됩니다.

   ![구성 세부 정보가 제공되는 "일반" 탭](./media/azure-container-instances-as-jenkins-build-agent/jenkins-job-01.png)

3. **빌드** 아래에서 **빌드 단계 추가** 를 선택하고 **셸 실행** 을 선택합니다. `echo "aci-demo"`를 명령으로 입력합니다.

   ![빌드 단계에 대한 선택 영역이 있는 "빌드" 탭](./media/azure-container-instances-as-jenkins-build-agent/jenkins-job-02.png)

5. **저장** 을 선택합니다.

## <a name="run-the-build-job"></a>빌드 작업 실행

빌드 작업을 테스트하고 빌드 플랫폼으로 Azure Container Instances를 관찰하려면 수동으로 빌드를 시작합니다.

1. **지금 빌드** 를 선택하여 빌드 작업을 시작합니다. 작업이 시작될 때까지 몇 분 정도 걸립니다. 다음 이미지와 비슷한 상태가 표시됩니다.

   ![작업 상태가 포함된 "빌드 기록" 정보](./media/azure-container-instances-as-jenkins-build-agent/jenkins-job-status.png)

2. 작업이 실행되는 동안 Azure Portal을 열고 Jenkins 리소스 그룹을 살펴봅니다. 컨테이너 인스턴스가 만들어진 것을 볼 수 있습니다. Jenkins 작업이 이 인스턴스 내부에서 실행되고 있습니다.

   ![리소스 그룹의 컨테이너 인스턴스](./media/azure-container-instances-as-jenkins-build-agent/jenkins-aci.png)

3. Jenkins가 구성된 Jenkins 실행기 수(기본값 2)보다 많은 작업을 실행하면 여러 컨테이너 인스턴스가 만들어집니다.

   ![새로 만들어진 컨테이너 인스턴스](./media/azure-container-instances-as-jenkins-build-agent/jenkins-aci-multi.png)

4. 모든 빌드 작업이 완료되면 컨테이너 인스턴스가 제거됩니다.

   ![컨테이너 인스턴스가 제거된 리소스 그룹](./media/azure-container-instances-as-jenkins-build-agent/jenkins-aci-none.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure App Service에 CI/CD](/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service)