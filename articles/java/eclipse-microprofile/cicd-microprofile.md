---
title: Azure Pipelines을 사용하는 MicroProfile 앱의 CI/CD
description: Azure Pipelines를 사용하여 Azure Web App for Containers 인스턴스에 MicroProfile 앱을 배포하도록 CI/CD 릴리스 주기를 설정하는 방법을 알아봅니다.
services: devops
documentationcenter: MicroProfile
author: ruyakubu
manager: brunoborges
ms.author: ruyakubu
ms.date: 07/31/2019
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.custom: devx-track-java
ms.openlocfilehash: 7ce992ca063daf0430ad3fc2d6882b820c2a2315
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379917"
---
# <a name="cicd-for-microprofile-apps-using-azure-pipelines"></a>Azure Pipelines을 사용하는 MicroProfile 앱의 CI/CD

이 자습서에서는 Azure Web App for Containers에 [MicroProfile](http://microprofile.io) Java EE 애플리케이션을 배포하도록 Azure Pipelines CI/CD(지속적인 통합 및 지속적인 배포) 릴리스 주기를 간편하게 설정하는 방법을 보여줍니다. 이 자습서의 MicroProfile 앱은 [Payara Micro](https://www.payara.fish/payara_micro) 기본 이미지를 사용하여 WAR 파일을 만듭니다. 

```Dockerfile
FROM payara/micro:5.182
COPY target/*.war $DEPLOY_DIR/ROOT.war
EXPOSE 8080
```
Docker 이미지를 만들고 컨테이너 이미지를 ACR(Azure Container Registry)에 푸시하여 Azure Pipelines 컨테이너화 프로세스를 시작합니다. Azure Pipelines 릴리스 파이프라인을 만들고 컨테이너 이미지를 웹앱에 배포하여 프로세스를 완료합니다.

## <a name="prerequisites"></a>사전 요구 사항

1. [Azure Portal](https://portal.azure.com)에서 [Azure Container Registry](https://azure.microsoft.com/services/container-registry)를 만듭니다.
   
1. Azure Portal에서 [Azure Web App for Containers](https://azure.microsoft.com/services/app-service/containers/)를 만듭니다. **OS**로 **Linux**를 선택하고, **컨테이너 구성**에서 **빠른 시작**을 **이미지 원본**으로 선택합니다.  
   
1. [https://github.com/Azure-Samples/microprofile-hello-azure](https://github.com/Azure-Samples/microprofile-hello-azure)에서 샘플 GitHub 리포지토리의 복제 URL을 복사하여 저장합니다.
   
1. [Azure DevOps](https://dev.azure.com) 조직에 등록 또는 로그인하고, 새 [프로젝트](/vsts/organizations/projects/create-project)를 만듭니다. 
   
1. 다음과 같이 샘플 GitHub 리포지토리를 Azure Repos로 가져옵니다.
   
   1. Azure DevOps 프로젝트 페이지의 왼쪽 탐색 영역에서 **Repos**를 선택합니다.
   1. **또는 리포지토리 가져오기**에서 **가져오기**를 선택합니다. 
   1. 앞에서 저장한 Git 복제 URL을 **복제 URL**에 입력하고 **가져오기**를 선택합니다.
  
## <a name="create-a-build-pipeline"></a>빌드 파이프라인 만들기

Azure Pipelines의 지속적인 통합 빌드 파이프라인은 Java EE 원본 앱에 커밋이 있을 때마다 모든 빌드 작업을 자동으로 실행합니다. 이 예제의 Azure Pipelines는 Maven을 사용하여 Java MicroProfile 프로젝트를 빌드합니다.

1. Azure DevOps 프로젝트 페이지의 왼쪽 탐색 영역에서 **파이프라인** > **빌드**를 선택합니다. 
   
1. **새 파이프라인**을 선택합니다.
   
1. **클래식 편집기를 사용하여 YAML 없이 파이프라인 만들기**를 선택합니다. 
   
1. 프로젝트 이름과 가져온 GitHub 리포지토리가 필드에 표시되는지 확인하고, **계속**을 선택합니다.
   
1. 템플릿 목록에서 **Maven**을 선택한 다음, **적용**을 선택합니다.
   
1. 오른쪽 창에서 **에이전트 풀** 드롭다운에 **호스트된 Ubuntu 1604**가 표시되는지 확인합니다.
   
   > [!NOTE]
   > Azure Pipelines는 이 설정을 통해 어떤 빌드 서버를 사용해야 하는지 알 수 있습니다.  사용자 지정된 프라이빗 빌드 서버를 사용할 수도 있습니다.
   
1. 지속적인 통합을 위한 파이프라인을 구성하려면 왼쪽 창에서 **트리거** 탭을 선택한 다음, **지속적인 통합 사용** 옆의 확인란을 선택합니다.  
   
1. 페이지 맨 위에서 **저장 및 큐에 넣기** 옆에 있는 드롭다운을 선택하고, **저장**을 선택합니다. 

   ![지속적인 통합 사용](media/cicd-microprofile/continuous-integration.png)

## <a name="create-a-docker-build-image"></a>Docker 빌드 이미지 만들기

Azure Pipelines는 Payara Micro의 기본 이미지에 Dockerfile을 사용하여 Docker 이미지를 만듭니다.  

1. **작업** 탭을 선택한 다음, **에이전트 작업 1** 옆에 있는 더하기 기호 **+** 를 선택하여 작업을 추가합니다.
   
   ![새 작업 추가](media/cicd-microprofile/add-task.png)
   
1. 오른쪽 창의 템플릿 목록에서 **Docker**를 선한 다음, **추가**를 선택합니다. 
   
1. 왼쪽 창에서 **buildAndPush**를 선택하고, 오른쪽 창의 **표시 이름** 필드에 설명을 입력합니다.
   
1. **컨테이너 리포지토리** 아래에서 **컨테이너 레지스트리** 필드 옆에 있는 **새로 만들기**를 선택합니다. 
   
1. 다음과 같이 **Docker 레지스트리 서비스 연결 추가** 대화 상자를 작성합니다.
   
   |필드|값|
   |---|---|
   |**레지스트리 유형**|**Azure Container Registry**를 선택합니다.|
   |**연결 이름**|연결의 이름을 입력합니다.|
   |**Azure 구독**|드롭다운 목록에서 Azure 구독을 선택하고, 필요에 따라 **권한 부여**를 선택합니다.|
   |**Azure 컨테이너 레지스트리**|드롭다운에서 Azure Container Registry 이름을 선택합니다.| 
   
1. **확인**을 선택합니다.
   
   ![Docker 레지스트리 서비스 연결 추가](media/cicd-microprofile/dockerconnection.png)
   
   > [!NOTE]
   > Docker 허브 또는 다른 레지스트리를 사용하는 경우 **레지스트리 유형** 옆에 있는 **Azure Container Registry** 대신 **Docker Hub** 또는 **기타**를 선택합니다. 그리고 컨테이너 레지스트리의 자격 증명 및 연결 정보를 입력합니다.
   
1. **명령** 아래의 **명령** 드롭다운에서 **빌드**를 선택합니다.
   
1. **Dockerfile** 옆에 있는 줄임표 **...** 를 선택하고, GitHub 리포지토리에서 **Dockerfile**을 찾아 선택한 다음, **확인**을 선택합니다. 
   
   ![Dockerfile 선택](media/cicd-microprofile/selectdockerfile.png)
   
1. **태그** 아래에서 새 줄에 *최신*을 입력합니다. 
   
1. 페이지 맨 위에서 **저장 및 큐에 넣기** 옆에 있는 드롭다운을 선택하고, **저장**을 선택합니다. 

## <a name="push-the-docker-image-to-acr"></a>ACR에 Docker 이미지 푸시

Azure Pipelines는 Docker 이미지를 Azure Container Registry에 푸시하고, 이 이미지를 사용하여 MicroProfile API 앱을 컨테이너화된 Java 웹앱으로 실행합니다.

1. Azure Pipelines에서 Docker를 사용 중이므로, [Docker 빌드 이미지 만들기](#create-a-docker-build-image)의 단계를 반복하여 또 다른 Docker 템플릿을 만듭니다. 이번에는 **명령** 드롭다운에서 **푸시**를 선택합니다.
   
1. **저장 및 큐에 넣기** 옆에 있는 드롭다운을 선택하고, **저장 및 큐에 넣기**를 선택합니다. 
   
1. **파이프라인 실행** 팝업의 **에이전트 풀** 아래에서 **호스트된 Ubuntu 1604**가 선택되었는지 확인하고, **저장 및 실행**을 선택합니다. 
   
1. 빌드가 완료되면 **빌드** 페이지의 하이퍼링크를 선택하여 빌드가 성공했는지 확인하고 다른 세부 정보를 볼 수 있습니다.
   
   ![빌드 하이퍼링크 선택](media/cicd-microprofile/checkbuild.png)

## <a name="create-a-release-pipeline"></a>릴리스 파이프라인 만들기

Azure Pipelines 지속적인 릴리스 파이프라인은 빌드가 성공하는 즉시 Azure 같은 대상 환경에 대한 배포를 자동으로 트리거합니다. 개발, 테스트, 스테이징 또는 프로덕션 환경에 대 한 릴리스 파이프라인을 만들 수 있습니다.

1. Azure DevOps 프로젝트 페이지의 왼쪽 탐색 영역에서 **파이프라인** > **릴리스**를 선택합니다. 
   
1. **새 파이프라인**을 선택합니다.
   
1. 템플릿 목록에서 **Azure App Service에 Java 앱 배포**를 선택한 다음, **적용**을 선택합니다. 
   
   ![Azure App Service에 Java 앱 배포 템플릿 선택](media/cicd-microprofile/selectreleasetemplate.png)
   
1. 팝업 창에서 **스테이지 1**을 *개발*, *테스트*, *스테이징* 또는 *프로덕션* 같은 스테이지 이름으로 변경한 다음, 창을 닫습니다. 
   
1. 왼쪽 창의 **아티팩트** 아래에서 **추가**를 선택하여 빌드 파이프라인의 아티팩트를 릴리스 파이프라인에 연결합니다. 
   
1. 오른쪽 창의 **소스(빌드 파이프라인)** 아래에 있는 드롭다운에서 해당 빌드 파이프라인을 선택한 다음, **추가**를 선택합니다.
   
   ![빌드 아티팩트 추가](media/cicd-microprofile/addbuildartifact.png)
   
1. **프로덕션** 스테이지에서 하이퍼링크를 **스테이지 작업 보기**로 선택합니다.
   
   ![스테이지 이름 선택](media/cicd-microprofile/viewstagetasks.png)
   
1. 오른쪽 창에서 다음과 같이 양식을 작성합니다.
   
   |필드|값|
   |---|---|
   |**Azure 구독**|드롭다운에서 해당 Azure 구독을 선택합니다.|
   |**앱 유형**|드롭다운에서 **Web App for Containers(Linux)** 를 선택합니다.|
   |**앱 서비스 이름**|드롭다운에서 ACR 인스턴스를 선택합니다.|
   |**레지스트리 또는 네임스페이스**|필드에 ACR 이름을 입력합니다. 예를 들어 *mymicroprofileregistry.azure.io*를 입력합니다.
   |**리포지토리**|Docker 이미지를 포함하고 있는 리포지토리를 입력합니다.| 
   
   ![스테이지 작업 구성](media/cicd-microprofile/configurestage.png)
   
1. 왼쪽 창에서 **Azure App Service에 War 배포**를 선택하고, 오른쪽 창에서 **태그** 필드에 *최신*을 입력합니다. 
   
1. 왼쪽 창에서 **에이전트에서 실행**을 선택하고, 오른쪽 창의 **에이전트 풀** 드롭다운에서 **호스트된 Ubuntu 1604**를 선택합니다. 

## <a name="set-up-environment-variables"></a>환경 변수 설정

배포하는 동안 컨테이너 레지스트리에 연결할 환경 변수를 추가하고 정의합니다.

1. **변수** 탭을 선택한 다음, **추가**를 선택하여 컨테이너 레지스트리 URL, 사용자 이름 및 암호에 대한 다음 변수를 추가합니다. 
   
   |속성|값|
   |---|---|
   |*registry.url*|컨테이너 레지스트리 URL을 입력합니다. 예: *https:\//mymicroprofileregistry.azure.io*|
   |*registry.username*|레지스트리의 사용자 이름을 입력합니다.|
   |*registry.password*|레지스트리의 암호를 입력합니다. 보안을 위해 자물쇠 아이콘을 클릭하여 암호를 숨깁니다.|
   
   ![변수 추가](media/cicd-microprofile/addvariables.png)
   
1. **작업** 탭의 왼쪽 창에서 **Azure App Service에 War 배포**를 선택합니다. 
   
1. 오른쪽 창에서 **애플리케이션 및 구성 설정**을 확장한 다음, **앱 설정** 필드 옆에 있는 줄임표 **...** 를 선택합니다.
   
1. **앱 설정** 팝업에서 **추가**를 선택하여 앱 설정 변수를 정의하고 할당합니다.
   
   |속성|값|
   |---|---|
   |*DOCKER_REGISTRY_SERVER_URL*|*$(registry.url)*|
   |*DOCKER_REGISTRY_SERVER_USERNAME*|*$(registry.username)*|
   |*DOCKER_REGISTRY_SERVER_PASSWORD*|*$(registry.password)*|
   
1. **확인**을 선택합니다.
   
   ![변수 추가 및 설정](media/cicd-microprofile/appsettings.png)
   
## <a name="set-up-continuous-deployment"></a>연속 배포 설정 

지속적인 배포를 사용하려면 다음을 수행합니다. 

1. **파이프라인** 탭의 **아티팩트** 아래에서 빌드 아티팩트의 번개 아이콘을 선택합니다. 
   
1. 오른쪽 창에서 **지속적인 배포 트리거**를 **사용**으로 설정합니다.
   
1. 오른쪽 위에서 **저장**을 선택한 다음, 다시 **저장**을 선택합니다. 
   
   ![지속적인 배포 트리거 사용](media/cicd-microprofile/setcontinuousdeployment.png)
   
## <a name="deploy-the-java-app"></a>Java 앱 배포

CI/CD를 사용하도록 설정했으므로, 소스 코드를 수정하면 빌드 및 릴리스가 자동으로 생성되고 실행됩니다. 다음과 같이 릴리스를 수동으로 만들고 실행할 수도 있습니다.

1. 릴리스 파이프라인 페이지의 오른쪽 위에서 **릴리스 만들기**를 선택합니다.
   
1. **새 릴리스 만들기** 페이지의 **트리거에 대한 스테이지가 자동에서 수동으로 변경됩니다** 아래에서 스테이지 이름을 선택합니다. 
   
1. **만들기**를 선택합니다. 
   
1. 릴리스 이름을 선택하고, 스테이지를 마우스로 가리키거나 선택한 다음, **배포**를 선택합니다. 
   
## <a name="test-the-java-web-app"></a>Java 웹앱 테스트

배포가 성공적으로 완료되면 웹앱을 테스트합니다. 

1. Azure Portal에서 웹앱 URL을 복사합니다.
   
   ![Azure Portal의 App Service 앱](media/cicd-microprofile/portalurl.png)
   
1. 웹 브라우저에서 URL을 입력하여 앱을 실행합니다. 웹 페이지에 **Hello Azure!** 가 나타납니다.
   
   ![Java 웹앱 페이지](media/cicd-microprofile/webapp.png)

