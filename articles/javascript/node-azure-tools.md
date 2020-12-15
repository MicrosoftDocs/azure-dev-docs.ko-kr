---
title: 도구 선택 - JavaScript - Azure
description: Azure에 Node.js 및 JavaScript 개발을 위한 개별 도구 설치
ms.topic: reference
ms.date: 12/07/2020
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: 714d096e4afde345bffa2582026f28fa2126a38c
ms.sourcegitcommit: ae2fa266a36958c04625bb0ab212e6f2db98e026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96857798"
---
# <a name="tools-for-javascript-developers-on-azure"></a>Azure의 JavaScript 개발자용 도구 

JavaScript는 여러 도구로 이루어진 에코시스템입니다. 이 문서에서는 JavaScript 개발자를 위해 Microsoft에서 만들고 유지 관리하는 도구가 사용됩니다. 이러한 도구는 새로운 Azure 서비스 및 배포/호스팅 시나리오를 개선합니다. 

이러한 도구를 사용하지 않아도 Azure를 사용할 수 있지만, 사용하면 기능과 지원의 측면에서 훨씬 좋은 환경을 만들어 줍니다. 

## <a name="azure-portal"></a>Azure portal

[Azure Portal](https://portal.azure.com/)을 통해 계정의 모든 구독과 리소스에 액세스할 수 있습니다. 

## <a name="azure-cli"></a>Azure CLI
Azure CLI는 명령줄에서 Azure 리소스를 관리하기 위해 최적화되어 있습니다. 

Azure CLI는 다음과 같은 사용 시나리오를 제공합니다.

* [로컬 설치](/cli/azure/install-az-cli2)
* [웹 셸](https://shell.azure.com/)
* [컨테이너](/cli/azure/run-azure-cli-docker)

Azure Portal을 사용하는 경우 포털의 위쪽 탐색 모음에서 Azure CLI를 사용할 수 있습니다.

:::image type="content" source="media/azure-tools/azure-portal-select-azure-cloud-shell.png" alt-text="Azure Portal을 사용하는 경우 포털의 위쪽 탐색 모음에서 Azure CLI를 사용할 수 있습니다.":::

## <a name="typescript"></a>TypeScript

[TypeScript](https://www.typescriptlang.org/download)는 다음과 같은 JavaScript 기능 및 추가 레이어를 모두 제공합니다. TypeScript의 형식 시스템. 작동 중인 기존 JavaScript 코드도 TypeScript 코드입니다. TypeScript의 주요 이점은 코드의 예기치 않은 동작을 강조 표시하여 버그 가능성을 낮추는 것입니다.

## <a name="typescript-and-the-azure-sdk-client-libraries"></a>TypeScript 및 Azure SDK 클라이언트 라이브러리

클라이언트 라이브러리가 TypeScript로 작성되기 때문에 Azure SDK 클라이언트 라이브러리 참조 설명서는 TypeScript용으로 작성되었습니다. Azure SDK 클라이언트 라이브러리를 사용하기 위해 꼭 TypeScript를 사용할 필요가 없습니다. 

[Azure SDK용 TypeScript 지침](https://azure.github.io/azure-sdk/typescript_introduction.html)에 대해 자세히 알아보세요.

## <a name="visual-studio-code"></a>Visual Studio Code

[Visual Studio Code](https://code.visualstudio.com)는 Azure용 JavaScript 개발용으로 인기 있는 IDE입니다. 인터페이스, 기능 및 확장이 함께 작동하여 개발 시간을 단축하고 개발 불만을 줄입니다. 

로컬 개발 프로젝트의 루트에 프로젝트 작업 영역을 만든 다음, 모든 관련 구성, 설정 및 확장을 추가합니다. 모든 팀원이 프로젝트에 필요한 설정 및 도구에 액세스할 수 있도록 프로젝트를 사용하여 작업 영역 파일을 체크 인합니다.

Visual Studio Code를 사용하면 다음과 같은 여러 가지 이점이 있습니다.

* Visual Studio Code에서 Azure 참조 설명서를 인라인으로 표시
* Visual Studio Code에서 명령문 완성 기능 제공
* 모호한 형식 또는 개체가 거의 없음

Visual Studio code는 [JavaScript 프로젝트 사용](https://code.visualstudio.com/docs/nodejs/working-with-javascript)에 대한 풍부한 설명서를 제공합니다. 

## <a name="visual-studio-code-extensions"></a>Visual Studio Code 확장
Visual Studio Code에서 직접 Azure 서비스를 통해 인터페이스에 다음과 같은 사용 가능한 확장을 사용합니다.

| 도구 | Description  |
|:---------:|---------|
| [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions "Azure Functions 확장에 연결") <br> [![Azure Functions 도구](media/node-azure-tools/icon-azure-functions.png)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) | 함수 만들기, 관리, 보기, 디버그 및 배포|
| [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice "Azure App Service 확장에 연결") <br> [![App Service 도구](media/node-azure-tools/icon-azure-app-service.png)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) | 사이트 및 Azure Portal 찾아보기, 새 사이트 만들기 및 슬롯에 배포 |
| [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb "Cosmos DB 확장에 연결" )  <br> [![Cosmos DB 도구](media/node-azure-tools/icon-cosmos-db.png)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)| Azure에서 전역적으로 분산된 다중 model 데이터베이스 만들기, 찾아보기 및 업데이트 |
| [Docker](https://marketplace.visualstudio.com/items?itemName=formulahendry.docker-explorer)   <br> [![Docker](media/node-azure-tools/icon-docker.png)](https://marketplace.visualstudio.com/items?itemName=formulahendry.docker-explorer)| Docker 컨테이너 및 이미지, Docker 허브 및 Azure Container Registry 관리 |

> [!div class="nextstepaction"]
> [Visual Studio Code 마켓플레이스에서 Azure 확장 기능 가져오기](https://marketplace.visualstudio.com/search?term=azure&target=VSCode&category=All%20categories&sortBy=Relevance)
