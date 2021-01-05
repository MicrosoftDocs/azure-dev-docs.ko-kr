---
title: 소개 및 필수 구성 요소
description: GitHub 작업을 사용하여 로컬로 React/TypeScript 클라이언트 애플리케이션을 빌드하고 Azure Static Web App에 배포합니다.
ms.topic: tutorial
ms.date: 12/17/2020
ms.custom: devx-track-js
ms.openlocfilehash: dfd5803fe79a0a000173d2d4e3fe52b3c4f029c5
ms.sourcegitcommit: 1c508f5ba73a12e4baeacc88ad9a8359301acb50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97687441"
---
# <a name="1-build-and-deploy-a-static-web-app-to-azure"></a>1. Static 웹앱을 빌드하여 Azure에 배포

이 자습서에서는 GitHub 작업을 사용하여 로컬로 React/TypeScript 클라이언트 애플리케이션을 빌드하고 Azure Static Web App에 배포합니다. React 앱을 사용하여 Cognitive Services Computer Vision으로 이미지를 분석할 수 있습니다.

* [**예제 코드**](https://github.com/Azure-Samples/js-e2e-client-cognitive-services)

[!INCLUDE [Create or use existing Azure Subscription ](../../includes/environment-subscription-h2.md)]

## <a name="prerequisites"></a>사전 요구 사항

- [Node.js 및 npm](https://nodejs.org/en/download) - 로컬 컴퓨터에 설치
- [Visual Studio Code](https://code.visualstudio.com/) - 로컬 컴퓨터에 설치 
    - [Azure Static Web Apps(미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) - React 앱을 Azure Static 웹앱에 배포하는 데 사용됩니다.
- [Git](https://git-scm.com/downloads) - GitHub에 푸시하는 데 사용됩니다. 푸시하면 GitHub 작업이 활성화됩니다.
- [GitHub 계정](https://github.com/join) - 분기하고 리포지토리로 푸시하는 데 사용됩니다.
- Bash 환경을 통해 [Azure Cloud Shell](/azure/cloud-shell/quickstart)을 사용합니다.

   [![Embed 시작](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell 시작")](https://shell.azure.com)   
- 원하는 경우 Azure CLI를 [설치](/cli/azure/install-azure-cli)하여 CLI 참조 명령을 실행합니다.
   - local install을 사용하는 경우 [az login](/cli/azure/reference-index#az-login) 명령을 사용하여 Azure CLI에 로그인합니다.  인증 프로세스를 완료하려면 터미널에 표시되는 단계를 수행합니다.  추가 로그인 옵션은 [Azure CLI를 사용하여 로그인](/cli/azure/authenticate-azure-cli)을 참조하세요.
  - 메시지가 표시되면 처음 사용할 때 Azure CLI 확장을 설치합니다.  확장에 대한 자세한 내용은 [Azure CLI에서 확장 사용](/cli/azure/azure-cli-extensions-overview)을 참조하세요.
  - [az version](/cli/azure/reference-index?#az_version)을 실행하여 설치된 버전과 종속 라이브러리를 찾습니다. 최신 버전으로 업그레이드하려면 [az upgrade](/cli/azure/reference-index?#az_upgrade)를 실행합니다.

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub 작업을 통해 클라이언트 앱 아키텍처 및 배포 프로세스 이해](./application-architecture.md) 
