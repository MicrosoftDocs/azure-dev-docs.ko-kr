---
title: 자습서 - Azure VM 에이전트를 사용하여 Jenkins 배포 크기 조정
description: Jenkins Azure VM 에이전트 플러그 인에서 Azure 가상 머신을 사용하여 추가 용량을 Jenkins 파이프라인에 추가하는 방법을 알아봅니다.
keywords: Jenkins, Azure, DevOps, 가상 머신, 에이전트
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: c5ea0c3782414abfda47810ba68ad1092cd7b0d5
ms.sourcegitcommit: f65561589d22b9ba2d69b290daee82eb47b0b20f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88162052"
---
# <a name="tutorial-scale-jenkins-deployments-with-azure-vm-agents"></a>자습서: Azure VM 에이전트를 사용하여 Jenkins 배포 크기 조정

이 자습서는 Jenkins [Azure VM 에이전트 플러그 인](https://plugins.jenkins.io/azure-vm-agents)을 사용하여 Azure에서 실행 중인 Linux 가상 머신을 통해 주문형 용량을 추가하는 방법을 설명합니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * Azure VM 에이전트 플러그 인 설치
> * Azure 구독에서 리소스를 만들기 위한 플러그 인 구성
> * 각 에이전트에 사용할 수 있는 컴퓨팅 리소스 설정
> * 각 에이전트에 설치된 운영 체제 및 도구 설정
> * 새 Jenkins 프리스타일 작업 만들기
> * Azure VM 에이전트에서 작업 실행

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="install-azure-vm-agents-plugin"></a>Azure VM 에이전트 플러그 인 설치

1. Jenkins 대시보드에서 **Jenkins 관리**를 선택한 다음, **플러그 인 관리**를 선택합니다.

1. **가용성** 탭을 선택한 다음, **Azure VM 에이전트**를 검색합니다. 플러그 인에 대한 항목 옆의 확인란을 선택하고 대시보드 아래 쪽에서 **다시 시작하지 않고 설치**를 선택합니다.

## <a name="configure-the-azure-vm-agents-plugin"></a>Azure VM 에이전트 플러그 인 구성

1. Jenkins 대시보드에서 **Jenkins 관리**, **시스템 관리**를 차례로 선택합니다.

1. 페이지의 아래쪽으로 스크롤하여 **새 클라우드 추가** 드롭다운이 있는 **클라우드** 섹션을 찾아 **Microsoft Azure VM 에이전트**를 선택합니다.

1. **Azure 자격 증명** 섹션에 있는 **추가** 드롭다운에서 기존 서비스 주체를 선택합니다. 아무것도 나열되지 않는 경우 다음 단계를 수행하여 Azure 계정에 대한 [서비스 주체를 만들어](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager) Jenkins 구성에 추가합니다.

    a. **Azure 자격 증명** 옆에 있는 **추가**를 선택하고 **Jenkins**를 선택합니다.
    b. **자격 증명 추가** 대화 상자에 있는 **종류** 드롭다운에서 **Microsoft Azure 서비스 주체**를 선택합니다.
    c. Azure CLI 또는 [Cloud Shell](/azure/cloud-shell/overview)에서 Active Directory Service 주체를 만듭니다.
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
    d. 서비스 주체의 자격 증명을 **자격 증명 추가** 대화 상자에 입력합니다. Azure 구독 ID를 모르는 경우 CLI에서 쿼리할 수 있습니다.
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    완료된 서비스 주체는 **구독 ID**에 `id` 필드, **클라이언트 ID**에 `appId` 값, **클라이언트 암호**에 `password` 및 **테넌트 ID**에 `tenant`을 사용해야 합니다. **추가**를 선택하여 서비스 주체를 추가한 다음, 새로 만든 자격 증명을 사용하도록 플러그 인을 구성합니다.

    ![Azure 서비스 주체 구성](./media/scale-deployments-using-vm-agents/new-service-principal.png)

    

1. **리소스 그룹 이름** 섹션에서 **새로 만들기**를 선택된 상태로 두고 `myJenkinsAgentGroup`을 입력합니다.
1. **구성 확인**을 선택하여 Azure에 연결하여 프로필 설정을 테스트합니다.
1. **적용**을 선택하여 플러그 인 구성을 업데이트합니다.

## <a name="configure-agent-resources"></a>에이전트 리소스 구성

Azure VM 에이전트를 정의하는 데 사용할 템플릿을 구성합니다. 이 템플릿은 만들 때 각 에이전트가 갖게 되는 컴퓨팅 리소스를 정의합니다.

1. **Azure Virtual Machine Template 추가** 옆에 있는 **추가**를 선택합니다.
1. **이름**에 `defaulttemplate`을 입력합니다.
1. **레이블**에 `ubuntu`를 입력합니다.
1. 콤보 상자에서 원하는 [Azure 지역](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 선택합니다.
1. **Virtual Machine 크기**에 있는 드롭다운 목록에서 [VM 크기](/azure/virtual-machines/linux/sizes)를 선택합니다. 이 자습서에는 범용 `Standard_DS1_v2` 크기가 적합합니다.   
1. **보존 시간**은 `60`으로 둡니다. 이 설정은 Jenkins가 유휴 에이전트를 할당 취소할 때까지 대기할 수 있는 시간(분)을 정의합니다. 유휴 에이전트를 자동으로 제거하지 않으려는 경우 0을 지정합니다.

   ![일반 VM 구성](./media/scale-deployments-using-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>에이전트 운영 체제 및 도구 구성

플러그 인 구성의 **이미지 구성** 섹션에서 **Ubuntu 16.04 LTS**를 선택합니다. **Git 설치(최신)**, **Maven 설치(V3.5.0)** 및 **Docker 설치** 옆에 있는 확인란을 선택하여 새로 만든 에이전트에서 이러한 도구를 설치합니다.

![VM OS 및 도구 구성](./media/scale-deployments-using-vm-agents/jenkins-os-config.png)

**관리자 자격 증명** 옆에 있는 **추가**를 선택한 다음, **Jenkins**를 선택합니다. 에이전트에 로그인하는 데 사용되는 사용자 이름 및 암호를 입력하여 Azure VM에서 관리 계정에 대한 [사용자 이름 및 암호 정책](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm)을 충족하도록 합니다.

**템플릿 확인**을 선택하여 구성을 확인한 다음, **저장**을 선택하여 변경 내용을 저장하고 Jenkins 대시보드로 돌아갑니다.

## <a name="create-a-job-in-jenkins"></a>Jenkins에서 작업 만들기

1. Jenkins 대시보드 내에서 **New Item**을 클릭합니다. 
1. 이름에 `demoproject1`을 입력하고 **프리스타일 프로젝트**를 선택한 다음, **확인**을 선택합니다.
1. **일반** 탭에서 **프로젝트를 실행할 수 있는 위치 제한**을 선택하고 **레이블 식**에 `ubuntu`를 입력합니다. 이전 단계에서 만든 클라우드 구성에 의해 레이블이 처리된다는 확인 메시지가 표시됩니다. 
   ![작업 설정](./media/scale-deployments-using-vm-agents/job-config.png)
1. **소스 코드 관리** 탭에서 **Git**을 선택하고 **리포지토리 URL** 필드에 다음 URL을 추가합니다. `https://github.com/spring-projects/spring-petclinic.git`
1. **빌드** 섹션에서 **빌드 단계 추가**, **최상위 Maven 대상 호출**을 차례로 선택합니다. **목표** 필드에 `package`를 입력합니다.
1. **저장**을 클릭하여 작업 정의를 저장합니다.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Azure VM 에이전트에서 새 작업 빌드

1. Jenkins 대시보드로 돌아갑니다.
1. 이전 단계에서 만든 작업을 선택한 다음 **지금 빌드**를 클릭합니다. 새 빌드가 대기됩니다. 에이전트 VM이 Azure 구독에 만들어져야 시작됩니다.
1. 빌드가 완료되면 **콘솔 출력**으로 이동합니다. 해당 빌드가 Azure 에이전트에서 원격으로 수행되었음이 표시됩니다.

![콘솔 출력](./media/scale-deployments-using-vm-agents/console-output.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Jenkins 플러그 인 문제 해결

Jenkins 플러그 인에서 버그가 발생하면 [Jenkins JIRA](https://issues.jenkins-ci.org/)에서 특정 구성 요소에 대한 문제를 제출해 주세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure App Service에 CI/CD](deploy-from-github-to-azure-app-service.md)