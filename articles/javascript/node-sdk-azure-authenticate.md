---
title: Node.js용 Azure 관리 모듈을 사용하여 인증
description: 서비스 사용자를 통해 Node.js용 Azure 관리 모듈에 인증합니다.
ms.topic: article
ms.date: 06/17/2017
ms.openlocfilehash: 76233fb6e6d15829783ff98b3af672abc7eba226
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85792283"
---
# <a name="authenticate-with-the-azure-modules-for-nodejs"></a>Node.js용 Azure 모듈을 사용하여 인증

모든 서비스 API에는 인스턴스화될 때 `credentials` 개체를 통한 인증이 필요합니다. Node.js용 Azure SDK를 통해 필요한 자격 증명을 인증하고 만드는 세 가지 방법이 있습니다. 

- 기본 인증
- 대화형 로그인
- 서비스 주체 인증

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="basic-authentication"></a>기본 인증

Azure 계정 자격 증명을 사용하여 프로그래밍 방식으로 인증하려면 `loginWithUsernamePassword` 함수를 사용합니다. 다음 JavaScript 코드 조각에서는 환경 변수로 저장된 자격 증명을 통해 기본 인증을 사용하는 방법을 보여 줍니다. 

```javascript
const Azure = require('azure');
const MsRest = require('ms-rest-azure');

MsRest.loginWithUsernamePassword(process.env.AZURE_USER, 
                                 process.env.AZURE_PASS, 
                                 (err, credentials) => {
  if (err) throw err;

  let storageClient = Azure.createARMStorageManagementClient(credentials, 
                                                             '<azure-subscription-id>');

  // ..use the client instance to manage service resources.
});
```

## <a name="interactive-login"></a>대화형 로그인

대화형 로그인은 사용자가 브라우저에서 인증할 수 있게 하는 링크와 코드를 제공합니다. 동일한 스크립트에서 여러 계정을 사용하거나 사용자가 개입하는 것이 좋을 때 이 방법을 사용합니다.

```javascript
const Azure = require('azure');
const MsRest = require('ms-rest-azure');

MsRest.interactiveLogin((err, credentials) => {
  if (err) throw err;

  let storageClient = Azure.createARMStorageManagementClient(credentials, '<azure-subscription-id>');

  // ..use the client instance to manage service resources.
});
```

## <a name="service-principal-authentication"></a>서비스 주체 인증

[대화형 로그인](#interactive-login)은 가장 쉬운 인증 방법입니다. 그러나 Node.js SDK를 사용하는 경우 계정 자격 증명을 제공하는 대신 서비스 사용자 인증을 사용하는 것이 좋습니다. [Node.js를 사용하여 Azure 서비스 사용자 만들기](./node-sdk-azure-authenticate-principal.md) 항목에서는 서비스 사용자를 만들고 사용하기 위한 다양한 기술을 설명하고 있습니다. 
