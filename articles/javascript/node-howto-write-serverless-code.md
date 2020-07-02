---
title: Azure Functions를 사용하여 서버리스 Node.js 코드 작성
description: Azure Functions를 사용하여 서버리스 코드를 만들고 배포하는 방법에 대한 지침입니다.
ms.topic: article
ms.date: 08/19/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: ae6a4cebef39976af4d9a30534d394d37d86a0c8
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85792268"
---
# <a name="use-azure-functions-to-write-serverless-nodejs-code-on-azure"></a>Azure에서 Azure Functions를 사용하여 서버리스 Node.js 코드 작성

Azure에서는 서버리스 제품을 Azure Functions라고 합니다. 서버리스 코드를 사용하면 인프라를 프로비저닝하거나 관리하지 않고도 인터넷에서 응답성이 뛰어난 주문형 엔드포인트를 만들 수 있습니다. 서버리스 코드는 다양한 이벤트에 응답하여 실행되는 스크립트 또는 다른 코드 비트로 구성됩니다. 

먼저 다음으로 바로 이동합니다.

- [Visual Studio Code를 사용하여 첫 번째 함수 만들기](/azure/azure-functions/functions-create-first-function-vs-code). 이 문서에서는 많은 세부 정보를 간소화하는 Visual Studio Code의 컨텍스트에서 Azure Functions를 소개합니다.

다음으로, 다음 문서를 검토하여 Azure Functions에서 수행할 수 있는 작업에 대한 이해를 넓힙니다.

- [Azure Functions 소개](/azure/azure-functions/functions-overview)는 서버리스 개발의 이점, 비용 및 서버리스 코드를 실행하는 데 사용할 수 있는 다양한 트리거에 대해 설명합니다.

- [Azure Functions 트리거 및 바인딩 개념](/azure/azure-functions/functions-triggers-bindings)

- [Azure Functions 개발자 가이드](/azure/azure-functions/functions-reference) 및 [Azure Functions JavaScript 개발자 가이드](/azure/azure-functions/functions-reference-node)

- 서버리스 환경에서 *상태 저장* 함수를 작성하는 데 관심이 있으면 [Durable Functions란?](/azure/azure-functions/durable/durable-functions-overview) 및 [JavaScript로 첫 번째 지속성 함수 만들기](/azure/azure-functions/durable/quickstart-js-vscode)를 검토하세요.

여기서는 서버리스 코드를 자세히 검색하는 데 도움이 되는 다양한 리소스를 이용할 수 있습니다.

- Microsoft Learn 모듈: [Azure Functions 및 SignalR Service를 사용하여 웹앱에서 자동 업데이트 사용](https://docs.microsoft.com/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr/)

- 다양한 트리거를 사용하여 서버리스 코드를 실행하는 방법에 대해 알아봅니다.

  - [타이머에 따라 코드 실행](/azure/azure-functions/functions-create-scheduled-function)
  - [Azure Blob 스토리지에서 파일을 업로드하거나 업데이트할 때 코드 실행](/azure/storage/blobs/storage-upload-process-images?tabs=nodejsv10)
  - [메시지를 Azure Queue Storage에 기록할 때 코드 실행](/azure/azure-functions/functions-create-storage-queue-triggered-function)

- [Azure Functions 및 Azure Cosmos DB를 사용하여 비정형 데이터 저장](/azure/azure-functions/functions-integrate-store-unstructured-data-cosmosdb?tabs=javascript). 다른 데이터베이스에 대한 자세한 내용은 [Node.js 코드에서 Azure 데이터베이스를 통합하는 방법](node-howto-integrate-databases.md)을 참조하세요.

- [Azure Functions를 로컬에서 코딩 및 테스트](/azure/azure-functions/functions-develop-local)

- [Azure Functions에서 코드를 테스트하기 위한 전략](/azure/azure-functions/functions-test-a-function) 및 [오류 처리](/azure/azure-functions/functions-bindings-error-pages)

- [Azure Active Directory를 사용하여 인증 구성](/azure/app-service/configure-authentication-provider-aad?toc=%2fazure%2fazure-functions%2ftoc.json)

- [Azure Pipelines를 사용하여 지속적인 배포 설정](/azure/azure-functions/functions-how-to-azure-devops)

- [Node.js + Azure Functions 샘플](/samples/browse/?languages=javascript%2Cnodejs&products=azure-functions)
