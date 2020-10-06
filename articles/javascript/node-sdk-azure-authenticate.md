---
title: Node.js용 Azure 관리 모듈을 사용하여 인증
description: 서비스 사용자를 통해 Node.js용 Azure 관리 모듈에 인증합니다.
ms.topic: how-to
ms.date: 06/17/2017
ms.custom: devx-track-js
ms.openlocfilehash: 150b00c4dbb21d0514d1d7c7d34813272bbf06e1
ms.sourcegitcommit: 717e32b68fc5f4c986f16b2790f4211967c0524b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91586122"
---
# <a name="authenticate-with-the-azure-management-modules-for-javascript"></a>JavaScript용 Azure 관리 모듈을 사용하여 인증

리소스를 관리하는 데 도움이 되는 Azure 서비스에 대한 두 가지 관리 패키지 세트가 있습니다.
- Node.js용 Azure SDK
- JavaScript용 Azure SDK

Node.js용 Azure SDK는 다음과 같은 Azure 서비스에 대한 이전 관리 패키지 세트입니다. 
- Node.js에서만 사용할 수 있으며 브라우저에서는 사용할 수 없음
- 직접 작성된 형식의 선언 파일을 사용하여 JavaScript로 작성됨
- 개발이 중지되었고 더 이상 사용되지 않으며 JavaScript용 Azure SDK 패키지로 대체됨
- 패키지 이름이 `azure-arm-`으로 시작함
- Azure Active Directory를 사용하여 인증하기 위해 패키지의 클라이언트 클래스에 전달할 수 있는 자격 증명을 만들려면 [m s-rest-azure](https://www.npmjs.com/package/ms-rest-azure) 패키지가 필요함
- https://github.com/Azure/azure-sdk-for-node 리포지토리에 있음

JavaScript용 Azure SDK는 다음과 같은 Azure 서비스에 대한 최신 관리 패키지 세트입니다.
- Node.js와 브라우저 모두에서 사용 가능
- TypeScript로 작성되었으며 JavaScript 및 TypeScript 프로젝트 모두에서 사용할 수 있음
- 현재도 개발 중이며 Azure 서비스에서 리소스 관리 API를 업데이트하면 업데이트를 수신함
- 패키지 이름이 `@azure/arm-`으로 시작함
- Azure Active Directory를 사용하여 인증하기 위해 패키지의 클라이언트 클래스에 전달할 수 있는 자격 증명을 만들려면 [@azure/ms-rest-nodeauth](https://www.npmjs.com/package/@azure/ms-rest-nodeauth) 패키지가 필요함 애플리케이션이 브라우저에서 실행되는 경우 [@azure/ms-rest-browserauth](https://www.npmjs.com/package/@azure/ms-rest-browserauth)를 대신 사용하세요.
- https://github.com/Azure/azure-sdk-for-js 리포지토리에 있음

두 패키지 세트를 구분하는 쉬운 방법은 패키지 이름을 확인하는 것입니다.

모든 서비스 API에는 인스턴스화될 때 `credentials` 개체를 통한 인증이 필요합니다. Node.js용 Azure SDK와 JavaScript용 Azure SDK에서 패키지의 필수 자격 증명을 인증하고 만드는 방법에는 여러 가지가 있습니다.

몇 가지 일반적인 방법은 다음과 같습니다.

- 기본 인증(사용자 이름 및 암호 사용)
- 대화형 로그인은 가장 쉬운 인증 방법이지만 사용자 계정으로 로그인해야 합니다.
- 서비스 주체 인증. [Node.js를 사용하여 Azure 서비스 사용자 만들기](./node-sdk-azure-authenticate-principal.md) 항목에서는 서비스 주체를 만들기 위한 다양한 기술을 설명하고 있습니다. 

아래의 각 패키지에 대한 추가 정보는 자격 증명 개체를 가져올 수 있는 다양한 방법에 대한 세부 정보를 참조하세요.
- [@azure/ms-rest-nodeauth](https://www.npmjs.com/package/@azure/ms-rest-nodeauth) - Node.js에서 JavaScript용 Azure SDK의 관리 패키지를 사용하는 경우
- [@azure/ms-rest-browserauth](https://www.npmjs.com/package/@azure/ms-rest-browserauth) - 브라우저에서 JavaScript용 Azure SDK의 관리 패키지를 사용하는 경우
- [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) - 이전 Node.js용 Azure SDK의 관리 패키지를 사용하는 경우

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="next-steps"></a>다음 단계   

* [Visual Studio Code에서 Azure에 정적 웹 사이트 배포](tutorial-vscode-static-website-node-01.md)