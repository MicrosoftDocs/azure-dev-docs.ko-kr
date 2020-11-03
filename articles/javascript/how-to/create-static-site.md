---
title: Node.js, API 및 태그를 사용하여 Azure에 정적 사이트 빌드
description: Azure를 사용하여 JAMstack 앱(JavaScript, API 및 태그)을 빌드하는 방법
ms.topic: how-to
ms.date: 08/20/2019
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: b126cb60341733451be555fa7759773aac99f6ce
ms.sourcegitcommit: c3a1c9051b89870f6bfdb3176463564963b97ba4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92437286"
---
# <a name="build-jamstack-static-site-web-apps-on-azure-with-nodejs"></a>Node.js를 사용하여 Azure에서 JAMstack(정적 사이트) 웹앱 빌드

정적 페이지로 제공되는 *JavaScript* 프런트 엔드, *API* (서버리스 코드로 작성된 타사 또는 사용자 지정 API) 및 템플릿 기반 *태그* (HTML 및 CSS)의 조합을 사용하여 뛰어난 웹앱을 생산적으로 빌드하고 유지 관리할 수 있습니다. JAMstack이라고도 하는 이 조합을 사용하면 웹 페이지를 제공하기 위해 복잡한 백 엔드 코드를 작성할 필요가 없습니다. 대신 시스템에서 정적 페이지(HTML, CSS 및 JavaScript)만 제공하며, 이러한 페이지는 서버 쪽 작업을 위해 API를 호출합니다. 자동 크기 조정 서버리스 기술을 사용하여 이러한 API를 작성할 수 있으므로 일반적인 상시(always-on) 서버 또는 웹 호스트를 사용할 때 발생하는 비용과 보안 문제를 완전히 방지할 수 있습니다. (자세한 내용은 [jamstack.org](https://jamstack.org/)를 참조하세요.)

Azure에서 정적/JAMstack 사이트를 구현하려면 다음과 같은 다양한 도구와 서비스를 사용합니다.

- 필요에 따라 데이터베이스를 구성합니다.
- Azure Functions에서 서버리스 API 코드를 구현합니다. 이러한 API는 일반적으로 데이터베이스를 사용합니다.
- 프런트 엔드 개발(예: Angular)에 사용할 라이브러리를 선택합니다. 그런 다음, 이러한 정적 HTML, CSS 및 JavaScript 파일을 기본 제공 웹 서버를 제공하는 Azure Blob Storage에 업로드합니다.
- 모든 트래픽이 하나의 URL 도메인을 통과하도록 역방향 프록시를 만듭니다.

//build 2019 세션인 [JavaScript, Visual Studio Code 및 Azure를 사용한 생산적인 프런트 엔드 개발](https://azure.microsoft.com/resources/videos/build-2019-productive-front-end-development-with-javascript-visual-studio-code-and-azure/)을 사용하여 프로세스 데모를 볼 수 있습니다.

> [!VIDEO https://medius.studios.ms/Embed/Video-nc/B19-BRK3021?latestplayer=true]

단계별 자습서는 [Azure에 정적 웹 사이트 배포](../tutorial-vscode-static-website-node-01.md)에서 찾을 수 있습니다.

다음 문서에서도 자세한 내용을 추가적으로 설명합니다.

- **데이터베이스** : [Node.js 앱에서 Azure 데이터베이스를 통합하는 방법](integrate-database.md)에서 설명하는 Azure의 다른 데이터베이스 서비스와 같이 원하는 모든 데이터베이스를 사용할 수 있습니다.
  
- **서버리스 API** :

  - [Visual Studio Code에서 Azure Functions 배포](../tutorial-vscode-serverless-node-01.md)로 시작합니다. 여기에서는 Visual Studio Code의 컨텍스트에서 Azure Functions를 소개하여 많은 세부 정보를 간소화합니다.
  - 문서가 완료되면 HTTP 엔드포인트와 동일한 함수의 이름이 지정된 하위 폴더가 포함된 Azure Functions 프로젝트(폴더)가 있습니다. 이 함수 폴더에는 코드가 포함된 *index.js* 파일이 포함되어 있습니다.
  - 필요에 따라 해당 함수를 수정하고, 더 많은 함수를 프로젝트에 추가한 다음, 공개적으로 사용할 수 있는 Azure에 다시 배포할 수 있습니다.
  - 서버리스 개발에 대한 추가 리소스는 [Azure에서 서버리스 Node.js 코드를 작성하는 방법](develop-serverless-apps.md)을 참조하세요.

- **Azure Storage에 프런트 엔드 배포** : API를 사용하면 이제 원하는 프레임워크를 사용하여 이러한 API를 사용하는 프런트 엔드 코드를 작성할 수 있습니다. 준비가 되면 [자습서: Blob Storage에서 정적 웹 사이트 호스트](/azure/storage/blobs/storage-blob-static-website-host) 문서에 따라 해당 파일을 Azure에 업로드하고 정적 웹 사이트 호스팅을 설정합니다.

- **역방향 프록시 만들기** : [Azure Functions 프록시 사용](/azure/azure-functions/functions-proxies)에서 설명하는 역방향 프록시를 사용하면 특정 요청을 다른 URL로 쉽게 보낼 수 있습니다. 이 경우 프런트 엔드 파일에 대한 요청을 Azure Storage URL로 보내려고 합니다. 여기서는 해당 파일 및 API 요청을 Azure Functions URL에 배포했습니다.

  - 이러한 프록시를 만들려면 Functions 프로젝트의 *proxies.json* 파일을 편집하여 아래와 같이 `<storage_url>` 및 `<api_url>`을 사용자의 URL로 바꿉니다.
  
    ```json
    {
      "$schema": "http://json.schemastore.org/proxies",
      "proxies": {
        "Static frontend on Azure Storage": {
          "matchCondition": {
            "route": "/{*restOfPath}"
          },
          "backendUri": "<storage_url>/{restOfPath}"
        },
        "Azure Functions API": {
          "matchCondition": {
            "route": "/api/{*restOfPath}"
          },
          "backendUri": "<api_url>/api/{restOfPath}"
        }
      }
    }
    ```
