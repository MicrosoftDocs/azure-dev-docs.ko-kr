---
title: Node.js용 Azure 관리 모듈을 사용하여 인증
description: 서비스 사용자를 통해 Node.js용 Azure 관리 모듈에 인증합니다.
ms.topic: how-to
ms.date: 09/29/2020
ms.custom: devx-track-js
ms.openlocfilehash: 2d7b4047226b28ab71597e523243adf7fc0d4c0d
ms.sourcegitcommit: 0b1c751c5a4a837977fec1c777bca5ad15cf2fc7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91621669"
---
# <a name="authenticate-with-the-azure-management-modules-for-javascript"></a>JavaScript용 Azure 관리 모듈을 사용하여 인증

모든 [SDK 클라이언트 라이브러리](azure-sdk-library-package-index.md)에는 인스턴스화될 때 `credentials` 개체를 통한 인증이 필요합니다. 필요한 자격 증명을 인증하고 만드는 방법에는 여러 가지가 있습니다.

필요한 자격 증명을 만드는 일반적인 방법은 다음과 같습니다.

- **서비스 주체** 인증은 _권장 방법_입니다. [Azure 서비스 주체를 생성](node-sdk-azure-authenticate-principal.md)하는 방법을 알아봅니다. 
- **대화형 로그인**은 가장 쉬운 인증 방법이지만 사용자 계정 및 브라우저로 로그인해야 합니다.
- 사용자 이름과 암호를 사용한 **기본** 인증. 이는 안전을 위한 최소한의 방법입니다. 

## <a name="samples"></a>샘플

|인증 패키지|샘플 인증 스크립트|
|--|--|
|[@azure/ms-rest-nodeauth](https://www.npmjs.com/package/@azure/ms-rest-nodeauth) <br>(권장)|[인증서가 있는 서비스 주체](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/authFileWithSpCert.ts)<br>[파일의 서비스 주체](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/authFileWithSpSecret.ts)<br>[대화형](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/interactivePersonalAccount.ts)<br>[기본](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/usernamePassword.ts)|
|[@azure/ms-rest-browserauth](https://www.npmjs.com/package/@azure/ms-rest-browserauth)<br>(권장)|[팝업을 사용한 인증(create-react-app)](https://github.com/Azure/ms-rest-browserauth/tree/master/samples/authentication-with-popup)<br>[팝업 없이 반응](https://github.com/Azure/ms-rest-browserauth/tree/master/samples/react-app)<br>[로그인 단추를 사용하는 HTML](https://github.com/Azure/ms-rest-browserauth/tree/master/samples/vanilla)|
|[ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure)|[서비스 주체](https://github.com/Azure/azure-sdk-for-node/blob/master/Documentation/Authentication.md#service-principal-authentication)<br>[대화형](https://github.com/Azure/azure-sdk-for-node/blob/master/Documentation/Authentication.md#interactive-login)<br>[기본](https://github.com/Azure/azure-sdk-for-node/blob/master/Documentation/Authentication.md#basic-authentication)|

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="next-steps"></a>다음 단계   

* [Visual Studio Code에서 Azure에 정적 웹 사이트 배포](tutorial-vscode-static-website-node-01.md)