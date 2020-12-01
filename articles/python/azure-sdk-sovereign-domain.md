---
title: Python 다중 클라우드용 Azure 라이브러리를 사용하여 모든 지역에 연결
description: msrestazure의 azure_cloud 모듈을 사용하여 여러 소버린 지역에서 Azure에 연결하는 방법
ms.date: 11/18/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: ba751604351bd7038a7696b6c8e93d663aa9263f
ms.sourcegitcommit: 29930f1593563c5e968b86117945c3452bdefac1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95485672"
---
# <a name="multi-cloud-connect-to-all-regions-with-the-azure-libraries-for-python"></a>다중 클라우드: Python용 Azure 라이브러리를 사용하여 모든 지역에 연결

Python용 Azure 라이브러리를 사용하여 Azure를 [사용 가능한](https://azure.microsoft.com/regions/services) 모든 지역에 연결할 수 있습니다.

기본적으로 Azure 라이브러리는 글로벌 Azure 클라우드에 연결하도록 구성됩니다.

## <a name="using-pre-defined-sovereign-cloud-constants"></a>미리 정의된 소버린 클라우드 상수 사용

미리 정의된 소버린 클라우드 상수는 `msrestazure` 라이브러리(0.4.11 이상)의 `azure_cloud` 모듈을 통해 제공됩니다.

- `AZURE_PUBLIC_CLOUD`
- `AZURE_CHINA_CLOUD`
- `AZURE_US_GOV_CLOUD`
- `AZURE_GERMAN_CLOUD`

정의를 사용하려면 `msrestazure.azure_cloud`에서 적절한 상수를 가져와서 클라이언트 개체를 만들 때 적용하세요. 

다음 예제와 같이 `DefaultAzureCredential`을 사용하는 경우 `azure.identity.AzureAuthorityHosts`에서도 적절한 값을 사용해야 합니다.

```python
import os
from msrestazure.azure_cloud import AZURE_CHINA_CLOUD as cloud
from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient
from azure.identity import DefaultAzureCredential

# Assumes the subscription ID to use is in the AZURE_SUBSCRIPTION_ID environment variable
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]

# When using sovereign domains (that is, any cloud other than AZURE_PUBLIC_CLOUD),
# you must use an authority with DefaultAzureCredential.
credential = DefaultAzureCredential(authority=cloud.endpoints.active_directory)

resource_client = ResourceManagementClient(credential,
    subscription_id, base_url=cloud.endpoints.resource_manager,
    credential_scopes=[cloud.endpoints.resource_manager + ".default'"])

subscription_client = SubscriptionClient(credential,
    base_url=stack_cloud.endpoints.resource_manager,
    credential_scopes=[cloud.endpoints.resource_manager + ".default'"])
```
  
## <a name="using-your-own-cloud-definition"></a>사용자 고유의 클라우드 정의 사용

다음 코드는 프라이빗 클라우드(예: Azure Stack에 빌드된 클라우드)에 대한 Azure Resource Manager 엔드포인트와 함께 `get_cloud_from_metadata_endpoint`를 사용합니다.

```python
import os
from msrestazure.azure_cloud import get_cloud_from_metadata_endpoint
from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient
from azure.identity import DefaultAzureCredential

# Assumes the subscription ID to use is in the AZURE_SUBSCRIPTION_ID environment variable
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]

stack_cloud = get_cloud_from_metadata_endpoint("https://contoso-azurestack-arm-endpoint.com")

# When using a private, you must use an authority with DefaultAzureCredential.
# The active_directory endpoint should be a URL like https://login.microsoftonline.com.
# https:// is optional in the URL but required on the endpoint.
credential = DefaultAzureCredential(authority=stack_cloud.endpoints.active_directory)

resource_client = ResourceManagementClient(credential, subscription_id,
    base_url=stack_cloud.endpoints.resource_manager,
    credential_scopes=[cloud.endpoints.resource_manager + ".default'"])

subscription_client = SubscriptionClient(credential,
    base_url=stack_cloud.endpoints.resource_manager,
    credential_scopes=[stack_cloud.endpoints.resource_manager + ".default'"])
```
