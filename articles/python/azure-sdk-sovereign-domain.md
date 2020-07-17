---
title: Python 다중 클라우드용 Azure 라이브러리를 사용하여 모든 지역에 연결
description: msrestazure의 azure_cloud 모듈을 사용하여 여러 소버린 지역에서 Azure에 연결하는 방법
ms.date: 07/13/2020
ms.topic: conceptual
ms.custom: seo-python-october2019
ms.openlocfilehash: 25e8851a8812782712ff65ec4627a0d2ead848ae
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86377907"
---
# <a name="multi-cloud-connect-to-all-regions-with-the-azure-libraries-for-python"></a>다중 클라우드: Python용 Azure 라이브러리를 사용하여 모든 지역에 연결

Python용 Azure 라이브러리를 사용하여 Azure를 [사용 가능한](https://azure.microsoft.com/regions/services) 모든 지역에 연결할 수 있습니다.

기본적으로 Azure 라이브러리는 글로벌 Azure에 연결하도록 구성됩니다.

## <a name="using-pre-defined-sovereign-cloud-constants"></a>미리 정의된 소버린 클라우드 상수 사용

미리 정의된 소버린 클라우드 상수는 `msrestazure`(0.4.11 +)의 `azure_cloud` 모듈에 의해 제공됩니다.

- `AZURE_PUBLIC_CLOUD`
- `AZURE_CHINA_CLOUD`
- `AZURE_US_GOV_CLOUD`
- `AZURE_GERMAN_CLOUD`

모든 코드에서 상수를 적용하려면 이전 목록의 값 중 하나를 사용하여 `AZURE_CLOUD`라는 환경 변수를 정의합니다. (기본값은 `AZURE_PUBLIC_CLOUD`입니다.)

특정 작업 내에서 상수를 적용하려면 `msrest.azure_cloud`에서 원하는 상수를 가져와 자격 증명과 클라이언트 개체를 만들 때 사용합니다.

```python
from msrestazure.azure_cloud import AZURE_CHINA_CLOUD
from msrestazure.azure_active_directory import UserPassCredentials
from azure.mgmt.resource import ResourceManagementClient

credentials = UserPassCredentials(login, password,
    cloud_environment=AZURE_CHINA_CLOUD)

client = ResourceManagementClient(credentials,
    subscription_id, base_url=AZURE_CHINA_CLOUD.endpoints.resource_manager)
```
  
## <a name="using-your-own-cloud-definition"></a>사용자 고유의 클라우드 정의 사용

다음 코드는 프라이빗 클라우드(예: Azure Stack에 빌드된 클라우드)에 대한 Azure Resource Manager 엔드포인트와 함께 `get_cloud_from_metadata_endpoint`를 사용합니다.

```python
from msrestazure.azure_cloud import get_cloud_from_metadata_endpoint
from msrestazure.azure_active_directory import UserPassCredentials
from azure.mgmt.resource import ResourceManagementClient

stack_cloud = get_cloud_from_metadata_endpoint("https://contoso-azurestack-arm-endpoint.com")
credentials = UserPassCredentials(login, password,
    cloud_environment=stack_cloud)

client = ResourceManagementClient(credentials, subscription_id,
    base_url=stack_cloud.endpoints.resource_manager)
```

## <a name="using-adal"></a>ADAL 사용

다른 지역에 연결하는 경우 다음 사항을 고려합니다.

- 토큰에 요청할 엔드포인트는 어디인가요(인증)?
- 이 토큰을 사용할 엔드포인트는 어디인가요(사용량)?

다음은 일반적인 예입니다.

```python
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from azure.mgmt.resource import ResourceManagementClient

# Service Principal
tenant = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
client_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
password = 'password'

# Public Azure - default values
authentication_endpoint = 'https://login.microsoftonline.com/'
azure_endpoint = 'https://management.azure.com/'

context = adal.AuthenticationContext(authentication_endpoint+tenant)
credentials = AdalAuthentication(context.acquire_token_with_client_credentials,
    azure_endpoint, client_id, password)

subscription_id = '33333333-3333-3333-3333-333333333333'

resource_client = ResourceManagementClient(credentials,
    subscription_id, base_url=azure_endpoint)
```

### <a name="azure-government"></a>Azure Government

```python
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from azure.mgmt.resource import ResourceManagementClient

# Service Principal
tenant = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
client_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
password = 'password'

# Government
authentication_endpoint = 'https://login.microsoftonline.us/'
azure_endpoint = 'https://management.usgovcloudapi.net/'

context = adal.AuthenticationContext(authentication_endpoint+tenant)
credentials = AdalAuthentication(context.acquire_token_with_client_credentials,
    azure_endpoint, client_id, password)

subscription_id = '33333333-3333-3333-3333-333333333333'

resource_client = ResourceManagementClient(credentials,
    subscription_id, base_url=azure_endpoint)
```

### <a name="azure-germany"></a>Azure Germany

```python
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from azure.mgmt.resource import ResourceManagementClient

# Service Principal
tenant = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
client_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
password = 'password'

# Azure Germany
authentication_endpoint = 'https://login.microsoftonline.de/'
azure_endpoint = 'https://management.microsoftazure.de/'

context = adal.AuthenticationContext(authentication_endpoint+tenant)
credentials = AdalAuthentication(context.acquire_token_with_client_credentials,
    azure_endpoint, client_id, password)

subscription_id = '33333333-3333-3333-3333-333333333333'

resource_client = ResourceManagementClient(credentials,
    subscription_id, base_url=azure_endpoint)
```

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

```python
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from azure.mgmt.resource import ResourceManagementClient

# Service Principal
tenant = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
client_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
password = 'password'

# Azure China
authentication_endpoint = 'https://login.chinacloudapi.cn/'
azure_endpoint = 'https://management.chinacloudapi.cn/'

context = adal.AuthenticationContext(authentication_endpoint+tenant)
credentials = AdalAuthentication(context.acquire_token_with_client_credentials,
    azure_endpoint, client_id, password)

subscription_id = '33333333-3333-3333-3333-333333333333'

resource_client = ResourceManagementClient(credentials,
    subscription_id, base_url=azure_endpoint)
```
