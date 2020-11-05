---
title: Azure Functions를 사용하는 서버리스 Node.js 코드
description: Azure Functions가 제공하는 서버리스 코드 인프라로 응답성이 뛰어난 주문형 HTTP 엔드포인트를 만들 수 있습니다.
ms.topic: how-to
ms.date: 10/27/2020
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js, contperfq2
ms.openlocfilehash: eb33717761d492051737b0c4ec86a93a6e2b6256
ms.sourcegitcommit: e1175aa94709b14b283645986a34a385999fb3f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93192545"
---
# <a name="use-azure-functions-to-develop-nodejs-serverless-code"></a>Azure Functions를 사용하여 Node.js 서버리스 코드 개발

Azure Functions가 제공하는 서버리스 코드 인프라로 응답성이 뛰어난 주문형 HTTP 엔드포인트를 만들 수 있습니다. 서버리스 코드는 다양한 이벤트에 응답하여 실행되는 JavaScript 또는 TypeScript 코드로 구성됩니다. 

Functions는 웹 서비스를 기반으로 코드 또는 Docker 컨테이너 형태로 추상화되어 실행되므로 개발자가 엔드포인트의 코드에 집중할 수 있습니다. 또한 Functions를 사용하면 다른 함수를 트리거하여 함수 작업 스트림으로 기존의 호스트형 백 엔드 서버 기능을 대체할 수 있으며 해당 서버를 관리할 필요가 없습니다. 

## <a name="what-is-a-function-resource"></a>함수 리소스란 무엇인가요?

Azure Function 리소스는 단일 Azure 지리적 위치의 모든 관련 함수에 대한 논리적 단위입니다. 리소스는 단일 함수 또는 여러 함수를 포함할 수 있으며, 이러한 함수는 서로 독립적이거나 입력 또는 출력 트리거와 관련이 있을 수 있습니다. 여러 가지 일반적인 함수에서 원하는 함수를 선택하거나 직접 만들 수 있습니다.

:::image type="content" source="../media/howto-serverless/portal-screenshot-new-azure-function-type.png" alt-text="여러 가지 일반적인 함수에서 원하는 함수를 선택하거나 직접 만들 수 있습니다.":::

함수 리소스 설정에는 환경 변수, 인증, 로깅 및 CORS를 비롯한 일반적인 서버리스 구성이 포함됩니다.  

## <a name="durable-stateful-functions"></a>지속적인 상태 저장 함수 

[Durable Functions](/azure/azure-functions/durable/durable-functions-overview)는 *상태* 를 유지하거나 Azure에서 장기 실행 함수를 관리합니다. [JavaScript로 첫 번째 지속성 함수를 만드세요](/azure/azure-functions/durable/quickstart-js-vscode).

## <a name="static-web-apps-include-functions"></a>정적 웹앱에 함수 포함 

서버리스 API도 필요한 정적 프런트 엔드 클라이언트 애플리케이션(예: Angular, React 또는 Vue)을 개발하는 경우 [정적 웹앱](/azure/static-web-apps/getting-started?tabs=react)을 사용하여 두 함수를 함께 번들로 묶습니다. 

## <a name="a-simple-javascript-function-for-http-requests"></a>HTTP 요청에 대한 간단한 JavaScript 함수

함수는 요청 및 컨텍스트 정보와 함께 내보내진 비동기 함수입니다. Azure Portal의 다음과 같은 부분 스크린샷은 함수 코드를 보여 줍니다. 

:::image type="content" source="../media/howto-serverless/portal-screenshot-azure-function-http.png" alt-text="Azure Portal에서 Azure Function의 부분 스크린샷":::

## <a name="develop-functions-locally-with-visual-studio-code-and-extensions"></a>Visual Studio Code 및 확장을 사용하여 로컬로 함수 개발

Visual Studio Code를 사용하여 [첫 번째 함수](/azure/azure-functions/functions-create-first-function-vs-code)를 만듭니다. Visual Studio Code는 [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)을 사용하여 많은 세부 사항을 간소화합니다.

이 확장을 사용하면 일반적인 템플릿으로 JavaScript 및 TypeScript 함수를 만들 수 있습니다. 

Azure용 HTTP 함수의 JavaScript 예제는 다음과 같습니다. 

```nodejs
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

Azure용 HTTP 함수의 TypeScript 예제는 다음과 같습니다. 

```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };

};

export default httpTrigger;
```

## <a name="configuring-the-function"></a>함수 구성

함수는 **function.json** 파일을 사용하여 구성됩니다. 이 구성을 사용하면 함수를 트리거하는 방법("direction": in)과 함수에서 반환하는 내용("direction": out)을 구성할 수 있습니다. 또한 환경 변수 및 함수가 작동하는 데 필요한 기타 정보를 설정할 수 있습니다. [트리거와 바인딩](/azure/azure-functions/functions-triggers-bindings?tabs=javascript.md)에 대해 자세히 알아보세요. 

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

## <a name="develop-functions-remotely-using-the-azure-portal"></a>Azure Portal을 사용하여 원격으로 함수 개발

[Azure Portal을 사용하여 Azure 함수를 생성](https://ms.portal.azure.com/#create/Microsoft.FunctionApp)하는 경우 함수를 구성하고, 미리 채워진 템플릿 내에 코드를 작성하고, 함수를 테스트할 수 있습니다. 

포털은 TypeScript가 아닌 JavaScript 함수만 만듭니다. TypeScript를 사용하여 개발하려는 경우 함수를 다운로드하거나 함수 확장을 사용하여 Visual Studio Code에서 로컬로 함수를 만듭니다. 

## <a name="next-steps"></a>다음 단계

[JavaScript에 대한 Azure Functions 개발자 가이드](/azure/azure-functions/functions-reference-node)를 참고하면 시작할 때 도움이 됩니다. 

[Azure 함수 및 SignalR Service를 사용하여 웹앱에서 자동 업데이트를 사용](/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr/)하는 방법을 알아보려면 Microsoft Learn 모듈을 사용하세요.

* [타이머에 따라 코드 실행](/azure/azure-functions/functions-create-scheduled-function)
* [Azure Blob 스토리지에서 파일을 업로드하거나 업데이트할 때 코드 실행](/azure/storage/blobs/storage-upload-process-images?tabs=nodejsv10)
* [메시지를 Azure Queue Storage에 기록할 때 코드 실행](/azure/azure-functions/functions-create-storage-queue-triggered-function)
* [Azure Functions 및 Azure Cosmos DB를 사용하여 구조화되지 않은 데이터 저장](/azure/azure-functions/functions-integrate-store-unstructured-data-cosmosdb?tabs=javascript)
* [Node.js + Azure Functions 샘플](/samples/browse/?languages=javascript%2Cnodejs&products=azure-functions)