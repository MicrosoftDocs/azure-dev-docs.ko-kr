---
title: Node.js용 Azure 관리 모듈을 사용하여 인증
description: 서비스 사용자를 통해 Node.js용 Azure 관리 모듈에 인증합니다.
ms.topic: how-to
ms.date: 01/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: e5774f0453960b41679a01170882fad1d9f50bad
ms.sourcegitcommit: b09d3aa79113af04a245b05cec2f810e43062152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99476439"
---
# <a name="authenticate-with-the-azure-management-modules-for-javascript"></a>JavaScript용 Azure 관리 모듈을 사용하여 인증

모든 [SDK 클라이언트 라이브러리](../azure-sdk-library-package-index.md)는 `credentials` 개체를 통한 인증이 필요합니다. 필요한 자격 증명을 인증하고 만드는 방법에는 여러 가지가 있습니다.

모든 소프트웨어 및 서비스와 마찬가지로 인증은 수년에 걸쳐 개선되었습니다. 해당 서비스에서 사용하는 인증 라이브러리를 알고 있어야 합니다. 

다음과 같은 인증 라이브러리가 있습니다.

* @azure/identity - 최신 인증 패키지
* @azure/ms-rest-nodeauth
* @azure/ms-rest-browserauth

이전 인증 패키지를 사용하고 있습니다. 이전 패키지를 사용 중인 경우 보다 안전하고 강력한 환경을 구축할 수 있도록 이전 인증 방법의 마이그레이션을 고려해야 합니다. 

## <a name="best-practices-with-azure-sdk-client-library-authentication"></a>Azure SDK 클라이언트 라이브러리 인증을 사용한 모범 사례

각 npm 패키지는 해당 클라이언트 라이브러리의 인증을 보여줍니다. 모든 패키지가 npm 패키지 페이지에서 동일한 인증 패키지를 사용하지 않는 한 인증 패키지와 코드를 혼합해서 사용하지 마세요. 

## <a name="azure-identity-library"></a>Azure ID 라이브러리

Azure ID 라이브러리는 Azure용 최신 인증 패키지입니다. 사용 중인 클라이언트 라이브러리의 추가 정보를 확인하여 이 새 라이브러리의 사용을 지원하는지 확인합니다.

[@azure/identity](https://www.npmjs.com/package/@azure/identity) 라이브러리는 Azure SDK용 Azure Active Directory 라이브러리 인증을 간소화합니다. 이 라이브러리는 SDK 라이브러리로 전달하여 API 요청을 인증할 수 있는 TokenCredential 구현 세트를 제공합니다. Azure Active Directory 서비스 주체 또는 관리 ID를 사용하는 토큰 인증을 지원합니다.

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { BlobServiceClient } = require("@azure/storage-blob");
 
// Enter your storage account name
const account = "<account>";
const defaultAzureCredential = new DefaultAzureCredential();
 
const blobServiceClient = new BlobServiceClient(
  `https://${account}.blob.core.windows.net`,
  defaultAzureCredential
);
```

앞의 JavaScript 예제 코드는 Azure ID 라이브러리를 사용하여 기본 Azure 자격 증명을 만든 다음, 이 자격 증명을 사용하여 Azure Storage 리소스에 액세스하는 방법을 보여줍니다.

## <a name="azure-ms-rest--libraries"></a>Azure ms-rest-* 라이브러리
최신 `@azure` 범위의 [클라이언트 라이브러리](../azure-sdk-library-package-index.md#modern-javascripttypescript-libraries)의 경우 서비스를 사용하려면 토큰이 필요합니다. 자격 증명을 반환하는 Azure SDK 클라이언트 인증 방법을 사용하여 토큰을 가져옵니다. 

```javascript
const msRestNodeAuth = require("@azure/ms-rest-nodeauth");
msRestNodeAuth.interactiveLogin().then((credential) => {
    // service code goes here
}).catch((err) => {
    // error code goes here
    console.error(err);
});
```

앞의 JavaScript 예제 코드는 대화형 로그인과 함께 최신 Azure 인증 라이브러리를 사용하여 자격 증명을 얻는 방법을 보여줍니다.

```javascript
// service code - this is an example only and not best practices for code flow
const { BlobServiceClient } = require('@azure/storage-blob');
const billingManagementClient = new billing.BillingManagementClient(credential, subscriptionId);
billingManagementClient.enrollmentAccounts.list().then((enrollmentList) => {
    console.log("The result is:");
    console.log(result);
})
```

앞의 JavaScript 예제 코드는 이 다음 코드 샘플에서 사용되는 스토리지 서비스와 같은 특정 Azure 서비스 클라이언트 라이브러리에 해당 자격 증명을 전달하는 방법을 보여줍니다. 클라이언트 라이브러리에서 해당 자격 증명을 사용하여 토큰을 생성합니다. 서비스는 토큰을 사용하여 요청에 대한 서비스 수준 인증의 유효성을 검사합니다. 

클라이언트 라이브러리는 토큰을 관리하며, 토큰을 언제 새로 고쳐야 하는지 알고 있습니다. 코드 베이스를 사용하는 개발자는 이를 관리할 필요가 없습니다.

## <a name="older-azure-sdk-client-authentication"></a>이전 Azure SDK 클라이언트 인증 

이전 Azure SDK 클라이언트는 궁극적으로 위에서 사용한 새로운 최신 인증으로 마이그레이션됩니다. 이 마이그레이션이 완료될 때까지 이전 클라이언트 라이브러리는 여러 인증 클라이언트를 사용하거나 리소스 키처럼 완전히 분리된 메커니즘을 사용하여 인증할 수 있습니다. 

이전 클라이언트 라이브러리에서 최상의 결과를 얻기 위해 다음과 같은 동작이 수행됩니다. 
* 각 npm 패키지는 해당 클라이언트 라이브러리의 인증을 보여줍니다.  
* 현재 코드가 동일한 코드 베이스에서 최신 `@azure/ms-*` 라이브러리와 이전의 인증 라이브러리를 사용하는 경우:
    * 이전의 비 Azure 범위 라이브러리가 서비스의 최신 라이브러리인지 확인하세요. 이 내용은 서비스 설명서에 나와 있습니다. 
    * 최신 인증 라이브러리와 이전의 인증 라이브러리를 함께 사용해야 하는 경우, 코드 베이스의 모든 애플리케이션 논리와 일치하도록 이전 라이브러리에 대한 자격 증명 만료를 제공하고 새로 고쳐야 할 수 있습니다. 

## <a name="authentication-with-azure-services-while-developing"></a>개발 중에 Azure 서비스를 사용하여 인증

개발 중에 필요한 자격 증명을 만드는 일반적인 방법은 다음과 같습니다.

| Azure 인증 유형|용도|
|--|--|
|**서비스 주체**|이 인증은 _권장하는 방법_ 입니다. [Azure 서비스 주체를 생성](node-sdk-azure-authenticate-principal.md)하는 방법을 알아봅니다. 서비스 주체를 사용하면 개인 Azure 계정과 별개인 Azure에 연결할 수 있습니다. 임시 계정일 수도 있고 장기간 동안 활성 상태를 유지하면서 개인 계정을 대신하는 계정일 수도 있습니다.|
| **대화형**| Azure 서비스를 시도할 때 가장 쉽게 인증하는 방법입니다. 브라우저를 통해 개인 계정으로 로그인해야 합니다. |
|**기본**|이 인증을 사용하려면 개인 사용자 이름 및 암호를 입력해야 합니다. 가장 안전하지 않은 방법이므로 권장하지 않습니다.| 

## <a name="authentication-with-azure-services-and-production-code"></a>Azure 서비스 및 프로덕션 코드를 사용하여 인증

프로덕션 코드에서 필요한 자격 증명을 만드는 일반적인 방법은 다음과 같습니다.

|Azure 인증 유형|용도|
|--|--|
|**MSI(관리되는 서비스 ID)**|[MSI 인증](/azure/active-directory/managed-identities-azure-resources/overview)은 프로덕션 시나리오에 가장 적합합니다. 로컬 개발 환경에서는 사용하지 않습니다. MSI를 지원하는 [서비스](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)에 사용됩니다.|
|**인증서**|[포털](/azure/cloud-services/cloud-services-configure-ssl-certificate-portal)을 사용하여 Azure에 [인증서](/azure/cloud-services/cloud-services-certs-create)를 업로드해야 합니다.|

## <a name="javascript-authentication-samples-for-azure"></a>Azure용 JavaScript 인증 샘플

|인증 패키지|샘플 인증 스크립트|
|--|--|
|[@azure/ms-rest-nodeauth](https://www.npmjs.com/package/@azure/ms-rest-nodeauth) <br>(권장)|[인증서가 있는 서비스 주체](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/authFileWithSpCert.ts)<br>[파일의 서비스 주체](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/authFileWithSpSecret.ts)<br>[대화형](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/interactivePersonalAccount.ts)<br>[기본](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/usernamePassword.ts)|
|[@azure/ms-rest-browserauth](https://www.npmjs.com/package/@azure/ms-rest-browserauth)<br>(권장)|[팝업을 사용한 인증(create-react-app)](https://github.com/Azure/ms-rest-browserauth/tree/master/samples/authentication-with-popup)<br>[팝업 없이 반응](https://github.com/Azure/ms-rest-browserauth/tree/master/samples/react-app)<br>[로그인 단추를 사용하는 HTML](https://github.com/Azure/ms-rest-browserauth/tree/master/samples/vanilla)|
|[ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure)|[서비스 주체](https://github.com/Azure/azure-sdk-for-node/blob/master/Documentation/Authentication.md#service-principal-authentication)<br>[대화형](https://github.com/Azure/azure-sdk-for-node/blob/master/Documentation/Authentication.md#interactive-login)<br>[기본](https://github.com/Azure/azure-sdk-for-node/blob/master/Documentation/Authentication.md#basic-authentication)|

## <a name="next-steps"></a>다음 단계   

* [Azure npm 패키지](../azure-sdk-library-package-index.md)
* [Azure npm 패키지 설명서](/javascript/api/overview/azure/)
