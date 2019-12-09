---
title: Python용 Azure 관리 라이브러리를 사용하여 인증
description: Python용 Azure 관리 라이브러리에 서비스 사용자를 인증합니다.
ms.date: 04/11/2019
ms.topic: conceptual
ms.custom: seo-python-october2019
ms.openlocfilehash: 93d41e76bc8fb58d70a3590e2ea3658014a8673a
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74466445"
---
# <a name="authenticate-with-the-azure-management-libraries-for-python"></a>Python용 Azure 관리 라이브러리를 사용하여 인증

이 문서에서는 Python 관리 라이브러리를 사용할 때 애플리케이션을 인증하는 방법을 보여줍니다. Azure를 사용하여 애플리케이션을 인증하는 몇 가지 옵션이 있습니다.

## <a name="mgmt-auth-token"></a>토큰 자격 증명을 사용하여 인증

구성 파일, 레지스트리 또는 Azure Key Vault에 자격 증명을 안전하게 저장합니다.

다음 예제에서는 [서비스 사용자](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)를 사용하여 인증합니다.

> [!NOTE]
> Azure CLI로 서비스 사용자를 만들려면 다음 명령을 사용합니다.
>
> ```bash
> az ad sp create-for-rbac --name "MY-PRINCIPAL-NAME" --password "STRONG-SECRET-PASSWORD"
> ```
>
> CLI로 서비스 사용자를 설정하는 방법을 자세히 알아보려면 [Azure CLI를 사용하여 Azure 서비스 사용자 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)를 참조하세요.

```python
from azure.common.credentials import ServicePrincipalCredentials

# Tenant ID for your Azure subscription
TENANT_ID = '<Your tenant ID>'

# Your service principal App ID
CLIENT = '<Your service principal ID>'

# Your service principal password
KEY = '<Your service principal password>'

credentials = ServicePrincipalCredentials(
    client_id = CLIENT,
    secret = KEY,
    tenant = TENANT_ID
)
```

> [!NOTE]
> 다른 지역의 Azure 클라우드에 연결하려면 `cloud_environment` 매개 변수를 사용합니다.
>
> ```python
> from azure.common.credentials import ServicePrincipalCredentials
> from msrestazure.azure_cloud import AZURE_CHINA_CLOUD
> 
> # Tenant ID for your Azure Subscription
> TENANT_ID = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
> 
> # Your Service Principal App ID
> CLIENT = 'a2ab11af-01aa-4759-8345-7803287dbd39'
> 
> # Your Service Principal Password
> KEY = 'password'
> 
> credentials = ServicePrincipalCredentials(
>     client_id = CLIENT,
>     secret = KEY,
>     tenant = TENANT_ID,
>     cloud_environment = AZURE_CHINA_CLOUD
> )
> ```

더 많은 제어가 필요하면 [ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-python) 및 SDK ADAL 래퍼를 사용하는 것이 좋습니다. 사용 가능한 모든 시나리오 목록과 샘플은 ADAL 웹 사이트를 참조하세요. 예를 들어 서비스 사용자 인증의 경우 다음과 같습니다.

```python
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD

# Tenant ID for your Azure Subscription
TENANT_ID = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'

# Your Service Principal App ID
CLIENT = 'a2ab11af-01aa-4759-8345-7803287dbd39'

# Your Service Principal Password
KEY = 'password'

LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
credentials = AdalAuthentication(
    context.acquire_token_with_client_credentials,
    RESOURCE,
    CLIENT,
    KEY
)
```

유효한 모든 ADAL 호출은 `AdalAuthentication` 클래스에서 사용할 수 있습니다.

다음으로 클라이언트 개체를 만들어 API 작업을 시작합니다.

```python
from azure.mgmt.compute import ComputeManagementClient

# Your Azure Subscription ID
subscription_id = '33333333-3333-3333-3333-333333333333'

client = ComputeManagementClient(credentials, subscription_id)
```

> [!NOTE]
> 다른 지역의 Azure 클라우드를 사용하는 경우 관리 클라이언트를 만들 때 지역에 맞는 기본 URL도 지정해야 합니다(`msrestazure.azure_cloud`의 상수를 통해). 예를 들어 Azure 중국 클라우드의 경우 다음과 같습니다.
> ```python
> client = ComputeManagementClient(credentials, subscription_id,
>     base_url=AZURE_CHINA_CLOUD.endpoints.resource_manager)
> ```


## <a name="mgmt-auth-file"></a>파일 기반 인증

가장 간단한 인증 방법은 Azure 서비스 사용자에 대한 자격 증명을 포함하는 JSON 파일을 만드는 것입니다. 다음 CLI 명령을 사용하여 새 서비스 사용자와 JSON 파일을 동시에 만들 수 있습니다.

```bash
az ad sp create-for-rbac --sdk-auth > mycredentials.json
```

코드에서 읽을 수 있는 시스템의 안전한 위치에 JSON 파일을 저장합니다. 셸에서 이 파일의 전체 경로가 포함된 환경 변수를 설정합니다.

```bash
export AZURE_AUTH_LOCATION=~/.azure/azure_credentials.json
```

JSON 파일을 직접 만들려면 다음 형식을 따르십시오.

```json
{
    "clientId": "<Service principal ID>",
    "clientSecret": "<Service principal secret/password>",
    "subscriptionId": "<Subscription associated with the service principal>",
    "tenantId": "<The service principal's tenant>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```

그런 다음 클라이언트 팩터리를 사용하여 클라이언트를 만들 수 있습니다.

```python
from azure.common.client_factory import get_client_from_auth_file
from azure.mgmt.compute import ComputeManagementClient

client = get_client_from_auth_file(ComputeManagementClient)
```

## <a name="mgmt-auth-msi"></a>Azure 관리 ID로 인증
Azure 관리 ID는 특정 자격 증명을 만들 필요 없이 Azure의 리소스로 SDK/CLI를 사용하는 간단한 방법입니다.

> [!IMPORTANT]
>
> 관리 ID를 사용하려면 Azure에서 실행 중인 VM 또는 Azure 함수와 같은 Azure 리소스에서 Azure에 연결해야 합니다. 리소스의 관리 ID를 구성하는 방법을 알아보려면 [Azure 리소스의 관리 ID 구성](/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm) 및 [Azure 리소스의 관리 ID를 사용하는 방법](/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in)을 참조하세요.

```python
from msrestazure.azure_active_directory import MSIAuthentication
from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient

# Create MSI Authentication
credentials = MSIAuthentication()


# Create a Subscription Client
subscription_client = SubscriptionClient(credentials)
subscription = next(subscription_client.subscriptions.list())
subscription_id = subscription.subscription_id

# Create a Resource Management client
resource_client = ResourceManagementClient(credentials, subscription_id)


# List resource groups as an example. The only limit is what role and policy are assigned to this MSI token.
for resource_group in resource_client.resource_groups.list():
    print(resource_group.name)
```

## <a name="mgmt-auth-cli"></a>CLI 기반 인증

SDK는 Azure CLI 활성 구독을 사용하여 클라이언트를 만들 수 있습니다.

> [!IMPORTANT]
> 이 인증 방법은 개발자가 빠르게 시작해볼 때 사용되어야 합니다. 프로덕션 용도로는 [ADAL](#mgmt-auth-legacy) 또는 사용자 고유의 자격 증명 시스템을 사용하세요.
> CLI 구성을 변경하면 SDK 실행에 영향을 줍니다.

활성 자격 증명을 정의하려면 [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)을 사용합니다.
기본 구독 ID는 보유하고 있는 유일한 ID이거나 [az account](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli)를 사용하여 정의할 수 있습니다.

```python
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.compute import ComputeManagementClient

client = get_client_from_cli_profile(ComputeManagementClient)
```

## <a name="mgmt-auth-legacy"></a>토큰 자격 증명(레거시)을 사용하여 인증

이전 버전의 SDK에서는 ADAL을 사용할 수 없었으며 `UserPassCredentials` 클래스를 제공했습니다. 이 클래스는 사용되지 않는 것으로 간주되며, 더 이상 사용되지 않아야 합니다.

이 샘플에서는 2FA를 지원하지 않는 사용자/암호 시나리오를 보여줍니다.

```python
from azure.common.credentials import UserPassCredentials

credentials = UserPassCredentials(
    'user@domain.com',
    'my_smart_password'
)
```
