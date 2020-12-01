---
title: 소개 및 필수 구성 요소
description: GitHub 작업을 사용하여 로컬로 React 클라이언트 애플리케이션을 빌드하고 Azure Static Web App에 배포합니다.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 5d70e14a9a5247f99c8b6e033af0a225b27da30b
ms.sourcegitcommit: 291768a67862336267c67819e913c16710e3875e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95820680"
---
# <a name="1-build-and-deploy-a-static-web-app-to-azure"></a>1. Static 웹앱을 빌드하여 Azure에 배포

이 자습서에서는 GitHub 작업을 사용하여 로컬로 React 클라이언트 애플리케이션을 빌드하고 Azure Static Web App에 배포합니다. 

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

- [Azure CLI를 설치](/cli/azure/install-azure-cli)하거나 [Azure Cloud Shell](https://shell.azure.com.) 사용
- [Node.js 및 npm](https://nodejs.org/en/download) - 로컬 컴퓨터에 설치
- [Visual Studio Code](https://code.visualstudio.com/) - 로컬 컴퓨터에 설치 
    - [Azure Static Web Apps(미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) - React 앱을 Azure Static 웹앱에 배포하는 데 사용됩니다.
- [Git](https://git-scm.com/downloads) - GitHub에 푸시하는 데 사용됩니다. 푸시하면 GitHub 작업이 활성화됩니다.
- [GitHub 계정](https://github.com/join) - 분기하고 리포지토리로 푸시하는 데 사용됩니다.

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [React Cognitive Services Image Analyzer 앱을 다운로드하여 로컬로 실행](run-the-react-cognitive-services-image-analyzer-app-locally.md) 