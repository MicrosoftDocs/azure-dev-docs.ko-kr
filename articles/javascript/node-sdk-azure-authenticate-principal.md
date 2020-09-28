---
title: Node.js를 사용하여 Azure 서비스 사용자 만들기
description: Azure에서 Node.js 및 JavaScript를 통해 서비스 사용자 인증을 사용하는 방법 알아보기
ms.topic: article
ms.date: 06/17/2017
ms.custom: devx-track-javascript
ms.openlocfilehash: 156892d9fd8e8014e3dacaae2492126ac9bf5836
ms.sourcegitcommit: b03cb337db8a35e6e62b063c347891e44a8a5a13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91110431"
---
# <a name="create-an-azure-service-principal-for-nodejs"></a>Node.js용 Azure 서비스 주체 만들기

앱에서 리소스에 액세스해야 하는 경우 앱에 대한 ID를 설정하고 자체의 자격 증명으로 해당 앱을 인증할 수 있습니다. 이 ID를 *서비스 사용자*라고 합니다. 기본적으로 Azure Active Directory 계정에 대한 키를 만들어 사용자 개입이나 사용자 이름/암호를 요구하는 대신 SDK를 제공하여 인증합니다.

서비스 사용자 방법을 통해 다음을 수행할 수 있습니다.
- 자체 사용 권한과 다른 앱 ID에 대한 사용 권한을 할당합니다. 일반적으로 이러한 권한은 정확히 앱 실행에 필요한 것으로 제한됩니다.
- 무인 스크립트를 실행할 때 인증용 인증서를 사용합니다.

이 항목에서는 서비스 사용자를 만드는 세 가지 기술을 보여 줍니다.

- Azure portal
- Azure CLI 2.0
- Node.js용 Azure SDK

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="create-a-service-principal-using-the-azure-portal"></a>Azure Portal을 사용하여 서비스 사용자 만들기

[포털을 사용하여 리소스에 액세스할 수 있는 Azure Active Directory 애플리케이션 및 서비스 사용자 만들기](/azure/active-directory/develop/howto-create-service-principal-portal) 항목에서 설명하는 단계에 따라 서비스 사용자를 생성합니다.

## <a name="create-a-service-principal-using-the-azure-cli-20"></a>Azure CLI 2.0을 사용하여 서비스 사용자 만들기

[Azure CLI 2.0](/cli/azure/install-az-cli2)을 사용하여 서비스 사용자를 만드는 작업은 다음 단계를 통해 수행할 수 있습니다.

1. [Azure CLI 2.0](/cli/azure/install-az-cli2)을 다운로드합니다.

2. 터미널 창을 엽니다.

3. 다음 명령을 입력하여 로그인 프로세스를 시작합니다.

    ```shell
    $ az login
    ```

4. `az login`을 호출하면 URL과 코드가 표시됩니다. 지정된 URL로 이동하고, 코드를 입력하고, Azure ID로 로그인합니다(이미 로그인한 경우 자동으로 발생할 수 있음).
그러면 CLI를 통해 계정에 액세스할 수 있습니다.

5. 구독 및 테넌트 ID 얻기:

    ```shell
    $ az account list
    ```

    다음은 출력 예제입니다.

    ```shell
    {
    "cloudName": "AzureCloud",
    "id": "<subscriptionId>",
    "isDefault": true,
    "name": "<subscriptionName>",
    "registeredProviders": [],
    "state": "Enabled",
    "tenantId": "<tenantId>",
        "user": {
            "name": "hello@example.com",
            "type": "user"
        }
    }
    ```

    **구독 ID는 7단계에서 사용되므로 기록해 두세요.**

6. 서비스 사용자를 만들어 Azure로 인증하는 데 필요한 다른 정보를 포함하는 JSON 개체를 가져옵니다.

    ```shell
    $ az ad sp create-for-rbac
    ```

    다음은 출력 예제입니다.

    ```shell
    {
    "appId": "<appId>",
    "displayName": "<displayName>",
    "name": "<name>",
    "password": "<password>",
    "tenant": "<tenant>"
    }
    ```

    **tenant, name 및 password 값은 7단계에서 사용되므로 기록해 두세요.**

7. 환경 변수 설정 - &lt;subscriptionId>, &lt;tenant>, &lt;name> 및 &lt;password> 자리 표시자를 4단계 및 5단계에서 얻은 값으로 바꿉니다.

    **bash 사용**

    ```shell
    export azureSubId='<subscriptionId>'
    export azureServicePrincipalTenantId='<tenant>'
    export azureServicePrincipalClientId='<name>'
    export azureServicePrincipalPassword='<password>'
    ```

    **PowerShell 사용**

    ```shell
    $env:azureSubId='<subscriptionId>'
    $env:azureServicePrincipalTenantId='<tenant>'
    $env:azureServicePrincipalClientId='<name>'
    $env:azureServicePrincipalPassword='<password>'
    ```

## <a name="create-a-service-principal-using-the-azure-sdk-for-nodejs"></a>Node.js용 Azure SDK를 사용하여 서비스 사용자 만들기

JavaScript를 사용하여 프로그래밍 방식으로 서비스 사용자를 만들려면 [ServicePrincipal 스크립트(영문)](https://github.com/Azure/azure-sdk-for-node/tree/master/Documentation/ServicePrincipal)를 사용합니다.

## <a name="using-the-service-principal"></a>서비스 사용자 사용

서비스 사용자가 있는 경우 다음 JavaScript 코드 조각에서는 Node.js용 Azure SDK를 사용하여 서비스 사용자 키를 통해 인증하는 방법을 보여 줍니다. &lt;clientId 또는 appId>, &lt;secret 또는 password>, &lt;domain 또는 tenant> 자리 표시자를 수정합니다.

```javascript
const Azure = require('azure');
const MsRest = require('ms-rest-azure');

MsRest.loginWithServicePrincipalSecret(
  <clientId or appId>,
  <secret or password>,
  <domain or tenant>,
  (err, credentials) => {
    if (err) throw err

    let storageClient = Azure.createARMStorageManagementClient(credentials, '<azure-subscription-id>');

    // ..use the client instance to manage service resources.
  }
);
```

## <a name="next-steps"></a>다음 단계

* [Node.js용 Azure 모듈을 사용하여 인증](node-sdk-azure-authenticate.md)