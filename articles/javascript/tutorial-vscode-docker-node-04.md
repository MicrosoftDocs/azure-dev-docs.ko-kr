---
title: Visual Studio Code에서 Node.js 앱의 컨테이너 이미지 만들기
description: 'Docker 자습서 4부: Node.js 애플리케이션 이미지 만들기'
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: devx-track-js
ms.openlocfilehash: 1ca5386803a5392c3132665f48b9a965b5abfd65
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91365126"
---
# <a name="create-your-nodejs-application-image"></a>Node.js 애플리케이션 이미지 만들기

[이전 단계: 로컬 Node.js 앱 만들기 및 실행](tutorial-vscode-docker-node-03.md)

## <a name="add-docker-files"></a>Docker 파일 추가

1. Visual Studio Code에서 **명령 팔레트**(**F1**)를 열고 `add docker files to workspace`를 입력한 다음, **Docker: Add Docker files to workspace**(작업 영역에 Docker 파일 추가) 명령을 선택합니다.

1. 메시지가 표시되면 애플리케이션 유형으로 **Node.js**를 선택하고, Docker 구성 파일에 대해 **아니요**로 응답한 다음, 애플리케이션에서 수신 대기하는 포트(3000이어야 함)를 선택합니다.

1. 이 명령은 `.dockerignore` 및 Docker Compose에 대한 구성 파일과 함께 `Dockerfile`을 작성합니다.

    > [!TIP]
    > VS Code는 Docker 파일을 제대로 지원합니다. 스마트 제안, 완료 및 오류 감지와 같은 다양한 언어 기능에 대해 알아보려면 VS Code 설명서에서 [Docker 작업](https://code.visualstudio.com/docs/azure/docker)을 참조하세요.

## <a name="build-a-docker-image"></a>Docker 이미지 빌드

`Dockerfile`은 원본 파일의 위치와 컨테이너 내에서 앱을 시작하는 명령을 포함하여 앱의 환경을 설명합니다.

> [!TIP]
> 컨테이너와 이미지: 컨테이너는 이미지의 인스턴스입니다.

1. **명령 팔레트**(**F1**)를 열고 **Docker 이미지: 이미지 빌드**를 실행하여 이미지를 빌드합니다. VS Code는 현재 폴더의 Dockerfile을 사용하고 이미지에 현재 폴더와 동일한 이름을 지정합니다.

1. 완료되면 Visual Studio Code의 **터미널** 패널이 열리고 `docker build` 명령이 실행됩니다. 출력에는 앱 환경을 구성하는 각 단계나 레이어도 표시됩니다.

1. 빌드된 이미지는 **DOCKER** 탐색기의 **이미지** 아래에 나타납니다.

    ![Visual Studio Code의 Docker 이미지 목록](media/deploy-containers/image-list.png)

## <a name="push-the-image-to-a-registry"></a>이미지를 레지스트리로 푸시

1. 이미지를 레지스트리로 푸시하려면 먼저 이미지에 레지스트리 이름을 태그로 지정해야 합니다. **DOCKER** 탐색기에서 **최신** 이미지를 마우스 오른쪽 단추로 클릭합니다.

    ![Visual Studio Code의 이미지 태그 명령](media/deploy-containers/tag-command.png)

1. 다음 프롬프트에서 태그를 완료하고 **Enter** 키를 누릅니다.

    통상적으로 태그는 다음 형식을 사용하여 지정됩니다.

    `[registry or username]/[image name]:[tag]`

    Azure Container Registry를 사용하는 경우 이미지 이름은 다음과 유사합니다.

    `msdocsvscodereg.azurecr.io/myexpressapp:latest`

    Docker Hub를 사용하는 경우 Docker Hub 사용자 이름을 사용합니다. 다음은 그 예입니다. 

    `fiveisprime/myexpressapp:latest`

1. 새로 태그가 지정된 이미지는 이제 레지스트리 이름이 포함된 **이미지**의 노드에 표시됩니다. 해당 노드를 확장하고 **최신**을 마우스 오른쪽 단추로 클릭하고 **푸시**를 선택합니다.

    ![Visual Studio Code의 이미지 푸시 명령](media/deploy-containers/push-command.png)

1. **터미널** 패널에는 이 작업에 사용된 `docker push` 명령이 표시됩니다. 대상 레지스트리는 이미지 이름에 지정된 레지스트리에 의해 결정됩니다.

   > [!IMPORTANT]
   > 출력에 "인증 필요"가 표시되면 터미널에서 `az acr login --name <your registry name>`을 실행합니다.

1. 완료되면 Docker 확장 탐색기에서 **레지스트리** 노드를 확장하여 레지스트리의 이미지를 살펴봅니다.

    ![Azure Container Registry에 표시되는 푸시된 이미지](media/deploy-containers/image-in-acr.png)

> [!div class="nextstepaction"]
> [내 애플리케이션 이미지를 만들었습니다.](tutorial-vscode-docker-node-05.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=containerize-app)
