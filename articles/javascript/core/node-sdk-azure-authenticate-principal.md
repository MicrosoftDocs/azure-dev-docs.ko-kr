---
title: Node.js를 사용하여 Azure 서비스 사용자 만들기
description: Azure에서 Node.js 및 JavaScript를 통해 서비스 사용자 인증을 사용하는 방법 알아보기
ms.topic: how-to
ms.date: 11/05/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: e837ca7f83f115a9d7c2bbe34e46d884a02dffe0
ms.sourcegitcommit: dc74b60217abce66fe6cc93923e869e63ac86a8f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94872774"
---
# <a name="create-an-azure-service-principal-for-nodejs"></a>Node.js용 Azure 서비스 주체 만들기

앱에서 리소스에 액세스해야 하는 경우 앱에 대한 ID를 설정하고 자체의 자격 증명으로 해당 앱을 인증할 수 있습니다. 이 ID를 *서비스 사용자* 라고 합니다. 기본적으로 Azure Active Directory 계정에 대한 키를 만들어 사용자 개입이나 사용자 이름/암호를 요구하는 대신 SDK를 제공하여 인증합니다.

서비스 사용자 방법을 통해 다음을 수행할 수 있습니다.
- 자체 사용 권한과 다른 앱 ID에 대한 사용 권한을 할당합니다. 일반적으로 이러한 권한은 정확히 앱 실행에 필요한 것으로 제한됩니다.
- 무인 스크립트를 실행할 때 인증용 인증서를 사용합니다.

이 항목에서는 서비스 사용자를 만드는 세 가지 기술을 보여 줍니다.

- Azure portal
- Azure CLI 2.0

[!INCLUDE [chrome-note](../includes/chrome-note.md)]

## <a name="create-a-service-principal-using-the-azure-cli-20"></a>Azure CLI 2.0을 사용하여 서비스 사용자 만들기

다음 단계를 수행하려면 [Azure CLI를 설치](/cli/azure/install-azure-cli)하고 [Azure에 로그인](/cli/azure/authenticate-azure-cli)합니다. 

1. `az account list` 명령을 사용하여 구독 및 테넌트 ID를 가져옵니다. 이는 Azure 패키지를 사용하여 작업할 때 필요합니다. 다음은 이 명령의 출력 예제를 보여 줍니다.

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

1. [Azure CLI를 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli) 항목에 설명된 단계에 따라 서비스 주체를 생성합니다. 출력의 JSON 개체에는 Azure로 인증하는 데 필요한 정보가 포함됩니다.

## <a name="using-the-service-principal"></a>서비스 사용자 사용

서비스 주체가 있으면 다음을 수행할 수 있습니다.

1. 인증서, 환경 변수 또는 `.json` 파일을 사용하여 서비스 주체를 통해 프로그래밍 방식으로 Azure에 인증합니다. 
1. 서비스 주체를 사용하는 Azure 리소스를 만들고 서비스를 사용합니다.

[JavaScript용 Azure 관리 모듈로 인증](./node-sdk-azure-authenticate.md) 토픽에서 Azure Active Directory를 사용하여 클라이언트를 인증하는 데 사용할 수 있는 자격 증명을 만드는 방법을 참조하세요.
