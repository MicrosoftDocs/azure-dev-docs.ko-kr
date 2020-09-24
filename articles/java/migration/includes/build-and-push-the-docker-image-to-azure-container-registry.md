---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: f2efcb620b570c57c3907ba2a682c65078e84790
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738402"
---
### <a name="build-and-push-the-docker-image-to-azure-container-registry"></a>Docker 이미지를 빌드하고 Azure Container Registry로 푸시

Dockerfile을 만든 후 Docker 이미지를 빌드하고 Azure Container Registry에 게시해야 합니다.

[WildFly 컨테이너 빠른 시작 GitHub 리포지토리](https://github.com/Azure/wildfly-container-quickstart)를 사용한 경우 이미지를 빌드하고 Azure Container Registry로 게시하는 과정은 다음 3개 명령을 호출하는 것과 같습니다.

이 예제에서 `MY_ACR` 환경 변수는 Azure Container Rregistry의 이름을 포함하고 `MY_APP_NAME` 변수는 Azure Container Rregistry에서 사용하려는 웹 애플리케이션의 이름을 포함합니다.

WAR 파일을 빌드합니다.

```shell
mvn package
```

Azure Container Registry에 로그인합니다.

```shell
az acr login -n ${MY_ACR}
```

이미지를 빌드하고 푸시합니다.

```shell
az acr build -t ${MY_ACR}.azurecr.io/${MY_APP_NAME} -f src/main/docker/Dockerfile .
```

또는 다음 명령과 같이 Docker CLI를 사용하여 먼저 이미지를 로컬로 빌드하고 테스트할 수 있습니다. 이 방법을 사용하면 ACR에 처음 배포하기 전에 이미지를 테스트하고 구체화하는 작업을 간소화할 수 있습니다. 그러나 Docker CLI를 설치하고 Docker 데몬이 실행 중인지 확인해야 합니다.

이미지를 빌드합니다.

```shell
docker build -t ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

로컬에서 이미지를 실행합니다.

```shell
docker run -it -p 8080:8080 ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

이제 `http://localhost:8080`에서 애플리케이션에 액세스할 수 있습니다.

Azure Container Registry에 로그인합니다.

```shell
az acr login -n ${MY_ACR}
```

Azure Container Registry로 이미지를 푸시합니다.

```shell
docker push ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Azure에서 컨테이너 이미지를 빌드하고 저장하는 방법에 대한 자세한 내용은 [Azure Container Registry를 사용하여 컨테이너 이미지 빌드 및 저장](/learn/modules/build-and-store-container-images/) 학습 모듈을 참조하세요.
