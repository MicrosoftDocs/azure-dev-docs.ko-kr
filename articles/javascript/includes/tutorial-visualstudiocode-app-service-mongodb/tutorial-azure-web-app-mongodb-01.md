---
title: tutorial-azure-web-app-mongodb-01.md 포함 파일
description: tutorial-azure-web-app-mongodb-01.md 포함 파일
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: a8c9fcca1b7374ae5122cfabb3fcbfc6ba8cdd07
ms.sourcegitcommit: 8a2a7df568c69fff2080ffab248409040efda1ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183909"
---
자습서의 이 섹션에서 이 자습서를 사용하려면 Azure 구독 및 모든 소프트웨어가 필요합니다.

## <a name="create-or-use-existing-azure-subscription"></a>기존 Azure 구독 만들기 또는 사용 

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-extension&mktingSource=vscode-tutorial-appservice-extension)

## <a name="install-software"></a>소프트웨어 설치

- [Node.js 및 npm](https://nodejs.org/en/download) - 로컬 컴퓨터에 설치된 Node.js 패키지 관리자입니다.
- [Docker](https://docs.docker.com/get-docker/) - Docker는 MongoDB를 설치하지 않고도 로컬 MongoDB 데이터베이스를 제공하는 데 사용됩니다. 
    - Docker를 사용하여 로컬 MongoDB 데이터베이스를 가져와야 하는 경우 다음 항목도 사용해야 합니다.
        -  Visual Studio [개발 컨테이너](https://code.visualstudio.com/docs/remote/containers)는 JavaScript 개발을 위한 몇 가지 공통 컨테이너를 제공합니다. 
        - [원격 컨테이너](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)
    - 이미 로컬 MongoDB가 있고 Docker를 설치하지 않으려는 경우에도 이 단계를 수행할 수 있습니다. 다음 MongoDB URL을 사용할 수 있으면 개발 컨테이너를 사용하여 로컬로 실행되는 MongoDB에 액세스하는 모든 단계에서 사용자 고유의 로컬 MongoDB를 사용하도록 용도를 변경할 수 있습니다. 
        - `mongodb://localhost:27017`
- [Visual Studio Code](https://code.visualstudio.com/)(로컬 컴퓨터에 설치됨) 
- Visual Studio Code 확장:
    - Visual Studio Code용 [Azure App Service 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)(Visual Studio Code 내에서 설치됨)
    - [Azure 데이터베이스](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)

## <a name="want-to-know-more"></a>자세히 알고 싶으세요? 

선택적 Visual Studio Code 확장:
* [VS Code용 MongoDB](https://marketplace.visualstudio.com/items?itemName=mongodb.mongodb-vscode)
* [Docker](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker)