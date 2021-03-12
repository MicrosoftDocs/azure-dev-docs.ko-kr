---
title: 인증 및 권한 부여 - JavaScript - Azure
description: Azure를 사용하여 ID, 인증 및 사용자가 포함된 JavaScript 앱을 개발하는 방법을 이해합니다.
ms.topic: conceptual
ms.date: 12/09/2020
ms.custom: devx-track-js
ms.openlocfilehash: 68f53743d001b44aa1495ece36d0c2764ec749d4
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118465"
---
# <a name="identity-authentication-and-users"></a>ID, 인증 및 사용자

인증 및 권한 부여는 웹 애플리케이션에 대한 광범위한 주제로, 특정 프로그래매틱 작업 및 애플리케이션과 사용자의 상호 작용으로 축소할 수 있습니다. 이 문서에서는 JavaScript 개발자가 일반적으로 이해해야 하는 주요 개념을 중점적으로 설명합니다. 

## <a name="authentication-with-azure"></a>Azure를 사용하여 인증

인증은 특정 ID가 개체에 대한 액세스 권한을 얻을 수 있는 권한입니다. 

일반적으로 Azure의 JavaScript 개발자에게 이는 다음과 같은 권한을 의미합니다.

* 프로그램에서 Azure 리소스에 액세스할 수 있도록 허용
* 사용자가 일반적으로 로그인한 후 앱에 액세스할 수 있도록 허용

|필수|관점|설명|
|--|--|--|
|예|개발자|애플리케이션 코드는 Azure 리소스에 액세스하는 데 필요한 자격 증명을 Azure에 전달해야 합니다.|
|아니요|사용자|애플리케이션 사용자의 경우 인증이 익명으로 이루어지거나 사용자 계정이 필요할 수 있습니다. 이 제한된 액세스는 Microsoft를 비롯한 모든 일반적인 인증 공급자를 사용할 수 있으며, 개발자가 고유한 사용자 인증 계층을 구축할 수도 있습니다.|

## <a name="authentication-for-developers-to-azure-services"></a>Azure 서비스에 대한 개발자 인증

Azure에 대한 프로그래매틱 방식의 인증에는 코드에서 사용하는 정확한 서비스에 대한 유효한 자격 증명이 필요합니다. 서비스의 빠른 시작 설명서를 읽고 서비스에 필요한 자격 증명 형식을 이해해야 합니다. 

### <a name="local-developer-environment-for-authenticating-to-azure"></a>Azure에 인증하기 위한 로컬 개발자 환경

서비스에 연결하는 방법을 이해한 후에는 서비스 주체를 만들고 서비스 주체를 개발 머신의 환경 변수로 설정해야 합니다. 이 단계에서는 Azure와의 직접 상호 작용을 통해 개인 계정을 제거하고, 소스 코드에 자격 증명을 체크 인하여 개인 계정이 손상될 위험을 없앱니다. 

### <a name="cloud-apps-authenticating-to-azure"></a>Azure에 인증하는 클라우드 앱

Azure 호스팅 서비스는 클라우드 기반 앱에 환경 변수 및 비밀을 포함하는 [애플리케이션 설정](../how-to/configure-web-app-settings.md)에 대한 액세스를 제공합니다. 웹앱에 다른 보안 계층을 추가하려면 Azure [키 자격 증명 모음](/azure/key-vault)에 비밀을 저장하고 호스팅 앱에서 이러한 비밀에 프로그래매틱 방식으로 액세스합니다. 

## <a name="modern-programmatic-authentication-with-azureidentity"></a>@azure/identity를 사용한 최신 프로그래매틱 방식의 인증

최신 Azure SDK 라이브러리는 [@azure/identity](https://www.npmjs.com/package/@azure/identity) npm 패키지를 통해 Azure 서비스에 프로그래매틱 방식으로 인증하는 데 서비스 주체를 사용합니다. 이 인증은 프로세스를 간소화하며 [최신 Azure SDK 패키지](https://www.npmjs.com/package/@azure/identity#client-libraries-supporting-authentication-with-azure-identity)에서 사용할 수 있습니다. 

```javascript
// The default credential first checks environment variables for configuration.
// If environment configuration is incomplete, it will try managed identity.

// Azure service to use
const { KeyClient } = require("@azure/keyvault-keys");

// Azure authentication library to access Azure service
const { DefaultAzureCredential } = require("@azure/identity");

// Azure SDK clients accept the credential as a parameter
const credential = new DefaultAzureCredential();

// Create authenticated client
const client = new KeyClient(vaultUrl, credential);

// Use service from authenticated client
const getResult = await client.getKey("MyKeyName");
```

## <a name="classic-programmatic-authentication"></a>일반적인 프로그래매틱 방식의 인증

유지 관리되는 대부분의 다른 Azure SDK 라이브러리의 경우 다음 패키지 중 하나를 사용합니다. 

* [@azure/ms-rest-js](https://www.npmjs.com/package/@azure/ms-rest-js) - 브라우저 및 Node.js 환경에서 작업
* [@azure/ms-rest-nodeauth](https://www.npmjs.com/package/@azure/ms-rest-nodeauth) - 대화형, 서비스 주체 및 사용자/암호를 비롯한 여러 가지 인증 메커니즘 제공
* [@azure/ms-rest-browserauth](https://www.npmjs.com/package/@azure/ms-rest-browserauth) - Azure AD 앱 필요

다음 예제에서는 서비스 제공 키 및 엔드포인트를 사용하여 인증하는 방법을 보여 줍니다.

```javascript
// Azure service to use
const { QnAMakerRuntimeClient } = require("@azure/cognitiveservices-qnamaker-runtime");

// Azure authentication library to access Azure service
const { CognitiveServicesCredentials } = require("@azure/ms-rest-azure-js");  
 
// QnA Maker runtime credentials
const QNAMAKER_KEY = process.env["QNAMAKER_KEY"];
const QNAMAKER_ENDPOINT = process.env["QNAMAKER_ENDPOINT"];
const KNOWLEDGEBASE_ID = process.env["QNAMAKER_KNOWLEDGE_BASE_ID"];

const cognitiveServicesCredentials = new CognitiveServicesCredentials(QNAMAKER_KEY);
const client = new QnAMakerRuntimeClient(cognitiveServicesCredentials, QNAMAKER_ENDPOINT);
const customHeaders = { Authorization: `EndpointKey ${QNAMAKER_KEY}` };

// A question you'd like to get a response for, from the knowledge base. For example
const question = "How are you?";

// Maximum number of answer to retreive
const top = 1;

// Find only answers that contain these metadata
const strictFilters = [{ name: "editorial", value: "chitchat" }];

client.runtime.generateAnswer( 
        KNOWLEDGEBASE_ID,
        { question, top, strictFilters },
        { customHeaders }
).then(result =>{
    console.log(JSON.stringify(result));

    // Sample Result
    // {
    //   answers: [
    //     {
    //       questions: [
    //         "How are you?",
    //         "How is your tuesday?"
    //       ],
    //       answer:
    //         ""I'm doing great, thanks for asking!",
    //       score: 100,
    //       id: 90,
    //       source:
    //         "qna_chitchat_Friendly.tsv",
    //       metadata: [{ name: "editorial", value: "chitchat" }],
    //       context: { isContextOnly: false, prompts: [] }
    //     }
    //   ],
    //   debugInfo: null,
    //   activeLearningEnabled: false
    // }

});

```

## <a name="user-authentication-with-an-app-registration"></a>앱 등록을 사용한 사용자 인증

MSAL(Microsoft 인증 라이브러리)은 사용자 인증 웹 개발에 권장되는 라이브러리입니다. 이 라이브러리는 여러 [언어 및 프레임워크](/azure/active-directory/develop/msal-overview#languages-and-frameworks)에서 사용할 수 있습니다.

MSAL을 사용하려면 웹앱을 Microsoft에 [앱으로 등록](/azure/active-directory/develop/quickstart-register-app)해야 합니다. 앱 등록에는 사용자 범위 권한 및 리디렉션 URL과 같은 일반적인 인증 정보가 포함됩니다. 

이 [MSAL 빠른 시작](/azure/active-directory/develop/quickstart-v2-javascript)에서 샘플 프로젝트를 사용하여 자세히 알아보세요.

사용자는 앱에 로그인할 때 앱에 권한을 부여합니다. 이 권한은 사용자와 함께 저장되며, 사용자가 관리하고 해지할 수 있습니다.

* 소비자 앱 권한 관리 - [https://account.live.com/consent/manage](https://account.live.com/consent/manage)
* Active Directory 앱 권한 관리 - [https://myapplications.microsoft.com/](https://myapplications.microsoft.com/)

## <a name="next-steps"></a>다음 단계

* [Azure App Service 구성](../how-to/configure-web-app-settings.md)