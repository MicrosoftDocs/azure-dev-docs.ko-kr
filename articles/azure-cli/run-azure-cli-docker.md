---
title: Docker 컨테이너에서 Azure CLI 실행
description: Azure CLI를 호스팅하는 Docker 컨테이너 실행 방법
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 01/29/2018
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 96a2bcf311cb504b08d44da3ea6033dbad9034b8
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82031295"
---
# <a name="run-azure-cli-in-a-docker-container"></a>Docker 컨테이너에서 Azure CLI 실행

Docker를 사용하여 Azure CLI가 사전 설치된 독립 실행형 Linux 컨테이너를 실행할 수 있습니다. Docker로 CLI를 실행하는 격리 환경을 신속하게 시작할 수 있습니다. 이미지를 배포를 위한 베이스로 사용할 수도 있습니다.

## <a name="run-in-a-docker-container"></a>Docker 컨테이너에서 실행

> [!NOTE]
> Azure CLI는 [Microsoft Container Registry](https://azure.microsoft.com/services/container-registry)로 마이그레이션되었습니다. Docker 허브의 기존 태그는 계속 지원되지만 새 릴리스는 mcr.microsoft.com/azure-cli로만 제공됩니다.

`docker run`을 사용하여 CLI를 설치합니다.

   ```bash
   docker run -it mcr.microsoft.com/azure-cli
   ```

> [!NOTE]
> 사용자 환경에서 SSH 키를 선택하려는 경우 `-v ${HOME}/.ssh:/root/.ssh`를 사용하여 SSH 키를 환경에 탑재합니다.
>
> ```bash
> docker run -it -v ${HOME}/.ssh:/root/.ssh mcr.microsoft.com/azure-cli
> ```

CLI는 `/usr/local/bin`에 있는 `az` 명령으로 이미지에 설치됩니다. 로그인하려면, [az login](/cli/azure/reference-index#az-login) 명령을 사용합니다.

[!INCLUDE [interactive-login](includes/interactive-login.md)]

다른 인증 방법에 대한 자세한 내용은 [Azure CLI로 로그인](authenticate-azure-cli.md)을 참조하세요.

## <a name="update-docker-image"></a>Docker 이미지 업데이트

Docker를 사용하여 업데이트하려면 새 이미지를 풀링하고 모든 기존 컨테이너를 다시 만들어야 합니다. 이러한 이유로 CLI를 데이터 저장소로 호스트하는 컨테이너를 사용하지 말아야 합니다.

`docker pull`을 사용하여 로컬 이미지를 업데이트합니다.

```bash
docker pull mcr.microsoft.com/azure-cli
```

## <a name="uninstall-docker-image"></a>Docker 이미지 제거

[!INCLUDE [uninstall-boilerplate.md](includes/uninstall-boilerplate.md)]

CLI 이미지를 실행하는 모든 컨테이너를 중지한 후 이미지를 제거합니다.

```bash
docker rmi mcr.microsoft.com/azure-cli
```

## <a name="next-steps"></a>다음 단계

Azure CLI를 사용할 준비가 되었으면 해당 기능 및 일반 명령을 잠시 둘러보세요.

> [!div class="nextstepaction"]
> [Azure CLI 시작](get-started-with-azure-cli.md)
