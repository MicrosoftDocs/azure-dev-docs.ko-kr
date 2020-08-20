---
title: Visual Studio Code의 컨테이너 레지스트리 사용
description: 자습서 2부, 컨테이너 레지스트리 사용
ms.topic: conceptual
ms.date: 09/20/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: c0eaa345af7d439cda47672f597ce1d82e78a355
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88218426"
---
# <a name="use-a-container-registry"></a>컨테이너 레지스트리 사용

[이전 단계: 소개 및 필수 구성 요소](tutorial-vscode-docker-node-01.md)

이 단계에서는 앱 이미지에 적합한 컨테이너 레지스트리를 설정합니다. 그러면 Azure App Service와 같은 컨테이너 지원 호스팅 서비스가 레지스트리에서 이미지를 가져올 수 있습니다.

이 자습서에서는 이미지용 프라이빗, 보안, 호스팅 레지스트리인 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)를 사용합니다. 단, 여기에 표시된 도구와 프로세스는 [Docker Hub](https://hub.docker.com/)와 같은 다른 레지스트리에서도 작동합니다.

## <a name="create-an-azure-container-registry"></a>Azure Container Registry 만들기

1. Visual Studio Code에서 **F1** 키를 선택하여 명령 팔레트를 엽니다.

1. 검색 상자에 **레지스트리**를 입력합니다. 결과에서 **Azure Container Registry: 레지스트리 만들기**를 선택합니다.

   ![Visual Studio Code의 Docker 탐색기](media/deploy-containers/docker-create-registry.jpg)

1. 다음 값을 입력하거나 선택합니다.

    - **레지스트리 이름**에 Azure에서 고유하며 영숫자가 5~50자 포함된 이름을 입력합니다.
    - **SKU**에서 **기본**을 선택합니다.
    - **리소스 그룹**에 구독 내에서 고유한 값을 입력합니다.
    - **위치**에서 가까운 지역을 선택합니다.

    Visual Studio Code에서 Azure에 레지스트리를 만드는 프로세스가 시작됩니다. 완료되면 다음과 같은 알림이 표시됩니다. 이 알림은 레지스트리가 성공적으로 만들어진 것을 알려줍니다.

   ![레지스트리가 만들어졌다고 알려주는 Visual Studio Code의 확인 메시지](media/deploy-containers/registry-created.jpg)

1. **Docker** 탐색기를 엽니다. 방금 설정한 레지스트리 엔드포인트가 **레지스트리** 아래에 표시되는지 확인합니다.

   ![Docker 탐색기에 레지스트리가 나타나는지 확인](media/deploy-containers/docker-explorer-registry.jpg)

## <a name="sign-in-to-azure-container-registry"></a>Azure Container Registry에 로그인

Docker 확장에서 Azure 레지스트리를 볼 수는 있지만, Container Registry에 로그인할 때까지 이미지를 푸시할 수 없습니다.

1. Visual Studio에서 **Ctrl**+ **`** 을 선택하여 통합 터미널을 엽니다.

1. 다음 Azure CLI 명령을 실행하여 Container Registry에 로그인합니다. `<your-registry-name>`을 직접 만든 레지스트리 이름으로 바꿉니다.

    ```bash
    az acr login --name <your-registry-name>
    ```

> [!div class="nextstepaction"]
> [레지스트리를 만들었습니다.](tutorial-vscode-docker-node-03.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=create-registry)
 