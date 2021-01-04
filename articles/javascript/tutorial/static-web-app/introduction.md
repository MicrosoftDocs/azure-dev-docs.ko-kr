---
title: 소개 및 필수 구성 요소
description: GitHub 작업을 사용하여 로컬로 React/TypeScript 클라이언트 애플리케이션을 빌드하고 Azure Static Web App에 배포합니다.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: dd35ca0925b014d2fddeb064c8cbdbdea703697b
ms.sourcegitcommit: c8330128d5d6a71859933a890ecdf047cb950996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97522354"
---
# <a name="1-build-and-deploy-a-static-web-app-to-azure"></a>1. Static 웹앱을 빌드하여 Azure에 배포

이 자습서에서는 GitHub 작업을 사용하여 로컬로 React/TypeScript 클라이언트 애플리케이션을 빌드하고 Azure Static Web App에 배포합니다. 

React(create-react-app) 앱은 다음과 같은 기능을 제공합니다. 
* Cognitive Services Computer Vision의 Azure 키 및 엔드포인트를 찾을 수 없는 경우 메시지 표시
* Cognitive Services Computer Vision을 사용하여 이미지 분석
    * 공용 이미지 URL을 입력하거나 컬렉션의 이미지 분석
    * 분석이 완료되면
        * 이미지 표시
        * Computer Vision JSON 결과 표시 

특정 분기에 대한 푸시가 발생하면 다음과 같은 GitHub 작업이 시작됩니다.
* Computer Vision 키 및 엔드포인트의 GitHub 비밀을 빌드에 삽입
* React(create-react-app) 클라이언트 빌드
* 결과 파일을 Azure Static 웹앱 리소스로 이동

[!INCLUDE [Create or use existing Azure Subscription ](../../includes/environment-subscription-h2.md)]

## <a name="what-is-an-azure-static-web-app"></a>Azure Static 웹앱이란?

정적 웹앱을 빌드할 때 원하는 기능과 제어 수준에 따라 Azure에서 여러 가지 옵션을 선택할 수 있습니다. 이 자습서는 여러분이 호스팅 환경이 아닌 프런트 엔드 코드에 집중할 수 있도록 여러 옵션이 자동으로 선택되는 가장 쉬운 서비스를 중심으로 진행됩니다.

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
> [React Cognitive Services Image Analyzer 앱을 다운로드하여 로컬로 실행](run-the-react-cognitive-services-image-analyzer-app-locally.md) 