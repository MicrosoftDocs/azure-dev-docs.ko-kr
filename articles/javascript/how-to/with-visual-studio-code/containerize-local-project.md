---
title: 로컬 프로젝트에서 컨테이너 만들기
description: Visual Studio Code를 사용하여 로컬 개발 프로젝트의 Dockerfile 만들기
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: devx-track-js
ms.openlocfilehash: a299e4647e3257627b62242a4ab63b16fc5590a3
ms.sourcegitcommit: 3f8aa923e4626b31cc533584fe3b66940d384351
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99230181"
---
# <a name="create-a-container-image-from-your-local-javascript-project"></a>로컬 JavaScript 프로젝트에서 컨테이너 이미지 만들기

컨테이너의 앱을 실행하면 웹앱 사용자에게 일관된 환경을 배포할 수 있습니다. Docker는 급격한 학습 곡선과 같이 빠르게 습득할 수 있으므로 일반적인 Docker 작업을 간소화하는 확장이 Visual Studio Code에서 제공됩니다.

## <a name="prepare-your-environment"></a>환경 준비 

[Docker](https://www.docker.com/)가 설치되고 실행 중이어야 합니다. 다음 명령을 사용하여 이를 확인하세요.

```bash
docker system info
```

Docker가 설치되어 실행되고 있지 않으면 이 명령은 오류를 반환합니다. 

## <a name="create-a-container"></a>컨테이너 만들기

1. Visual Studio에서 기존 JavaScript 프로젝트를 엽니다. 
1. 작업 표시줄에서 **확장** 아이콘을 선택한 후 `docker`를 검색하고 **Docker** [확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker)을 선택합니다.
1. Docker 확장을 설치하고 Visual Studio Code를 다시 로드합니다.

    ![Visual Studio Code용 Docker 확장 설치](../../media/node-howto-e2e/visual-studio-code-docker-extension.png)

    Visual Studio Code의 Docker 확장에는 기존 프로젝트를 위한 *Dockerfile* 및 *docker-compose.yml* 파일을 생성하는 명령이 포함되어 있습니다.

1. 작업 표시줄에서 Docker 아이콘을 선택한 다음, 사이드바에서 Docker 컨테이너를 봅니다.

    :::image type="content" source="../../media/howto-containerize-local-project/docker-extension-activity-bar-side-bar.png" alt-text="`git branch(git 분기)`를 검색하고 `Git: Create Branch(Git: 분기 만들기)`를 선택합니다.":::

## <a name="view-available-docker-commands"></a>사용 가능한 Docker 명령 보기

사용 가능한 Docker 명령을 보려면 명령 팔레트를 표시(**F1**)하고 `docker`를 입력합니다.

![Visual Studio Code용 Docker 확장에서 지원되는 명령 ](../../media/node-howto-e2e/visual-studio-code-available-docker-codes.png)

## <a name="create-a-dockerfile-in-your-project"></a>프로젝트에서 Dockerfile 만들기

1. **git** 과 같은 소스 제어를 사용하는 경우 다른 변경 내용이 없는지 확인합니다. 그러면 자동으로 생성되는 파일을 확인하는 데 도움이 됩니다.

1. **Docker: 프로젝트의 설정을 사용하여**  작업 영역에 docker 파일을 추가합니다. 

    이러한 설정은 Node.js 프로젝트에 일반적입니다.

    |Setting|값|
    |--|--|
    |애플리케이션 플랫폼|Node.js|
    |Package.json|package.json|
    |노출할 포트|8080 - 또는 자동으로 감지된 값|
    |선택적 docker 파일(.dockerignore) 포함 |예|

    ![Visual Studio Code에서 생성된 Dockerfile](../../media/node-howto-e2e/visual-studio-code-complete-dockerfile.png)

    Docker 명령은 즉시 사용할 수 있는 완전한 *Dockerfile* 및 Docker 작성 파일을 생성합니다.

## <a name="build-image-from-your-project"></a>프로젝트에서 이미지 빌드

1. **F1** 키를 선택하고, 명령 팔레트에서 `dockerb`를 입력한 다음, **Docker: Build Image**(Docker: 이미지 빌드) 명령을 선택합니다. 
1. 방금 생성한 *Dockerfile* 을 선택합니다. 
1. package.json에 이름 속성이 있는 경우 컨테이너의 이미지 이름으로 사용됩니다. 
    package.json이 없으면 `ALIAS/IMAGE-NAME` 형식으로 태그를 지정합니다. 여기서 ALIAS는 Docker 별칭이고, IMAGE-NAME은 프로젝트의 이미지 이름입니다. 태그의 예를 들면 `diberry/express-web-app`입니다. 
1. **Enter** 키를 선택하여 빌드되는 Docker 이미지의 출력을 표시하는 통합 터미널 창을 시작합니다.

    ![Docker 이미지 빌드 출력](../../media/node-howto-e2e/docker-build-image-output.png)

    이 명령은 `docker build` 실행 프로세스를 자동으로 실행합니다.

1. 작업 표시줄에서 Docker 아이콘을 선택한 후 **이미지** 를 선택하여 이미지 목록에서 새 이미지를 확인합니다. 
    
    목록에서 다른 새 이미지를 볼 수도 있습니다. Docker는 컨테이너의 기반이 되는 이미지를 풀다운합니다.  

## <a name="run-local-container-project"></a>로컬 컨테이너 프로젝트 실행

1. 작업 표시줄에서 Docker 아이콘을 선택합니다.
1. **이미지** 목록에서 이미지 이름을 마우스 오른쪽 단추로 클릭하고 **실행** 을 선택합니다.

    :::image type="content" source="../../media/howto-containerize-local-project/docker-extension-running-container-visual-studio-code.png" alt-text="이미지 목록에서 이미지 이름을 마우스 오른쪽 단추로 클릭하고 실행을 선택합니다.":::

## <a name="push-local-container-image-to-dockerhub"></a>로컬 컨테이너 이미지를 DockerHub로 밀어넣기

이미지로 Azure 웹앱을 만들려면 레지스트리의 이미지를 사용할 수 있어야 합니다. 이미지는 커뮤니티 레지스트리에 공개적으로 제공되거나 [Azure Container Registry](/azure/container-registry/)와 같이 인증을 통해 액세스할 수 있는 프라이빗 레지스트리에 제공됩니다. 

이미지를 푸시하려면 CLI에서 `docker login`을 실행하고 자신의 계정 자격 증명을 입력하여 DockerHub를 통해 이미 인증되었는지 확인합니다.

1. Visual Studio Code에서 F1 키를 사용하여 명령 팔레트를 엽니다.
1. `dockerpush`를 입력하고 `Docker: Push` 명령을 선택합니다. 
1. 방금 작성한 이미지 태그(예: `diberry/express-web-app`)를 선택하고 **Enter** 키를 누릅니다. 
1. 이 명령은 `docker push`의 호출을 자동화하고 통합 터미널에 출력을 표시합니다.

## <a name="push-local-container-image-to-azure-container-registry"></a>Azure Container Registry에 로컬 컨테이너 이미지 밀어넣기

[인증하고 자체 Azure Container Registry에 밀어넣기](../with-azure-cli/create-container-registry-resource.md) 단계를 읽습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Container Registry 리소스 만들기](../with-azure-cli/create-container-registry-resource.md)