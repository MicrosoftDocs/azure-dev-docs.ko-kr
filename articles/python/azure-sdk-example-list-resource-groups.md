---
title: Python용 Azure 라이브러리를 사용하여 리소스 그룹 및 리소스 나열
description: Python용 Azure SDK의 리소스 관리 라이브러리를 사용하여 그룹의 리소스 그룹 및 리소스를 나열합니다.
ms.date: 10/12/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 06395891ea1b8294f9eaafe7dad40bf7a335e965
ms.sourcegitcommit: f460914ac5843eb7392869a08e3a80af68ab227b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92010675"
---
# <a name="example-use-the-azure-libraries-to-list-resource-groups-and-resources"></a>예제: Azure 라이브러리를 사용하여 리소스 그룹 및 리소스 나열

이 예제에서는 Python 스크립트에서 Azure SDK 관리 라이브러리를 사용하여 다음과 같은 두 가지 작업을 수행하는 방법을 보여줍니다.

- Azure 구독의 모든 리소스 그룹을 나열합니다.
- 특정 리소스 그룹 내의 리소스를 나열합니다.
 
이 문서의 모든 명령은 언급되지 않는 한 Linux/macOS bash 및 Windows 명령 셸에서 동일하게 작동합니다.

[해당 Azure CLI 명령](#for-reference-equivalent-azure-cli-commands)은 이 문서의 뒷부분에 있습니다.

## <a name="1-set-up-your-local-development-environment"></a>1: 로컬 개발 환경 설정

아직 수행하지 않은 경우 [Azure에 대한 로컬 Python 개발 환경 구성](configure-local-development-environment.md)의 모든 지침을 따르세요.

이 프로젝트에 대한 가상 환경을 만들고 활성화해야 합니다.

## <a name="2-install-the-azure-library-packages"></a>2: Azure 라이브러리 패키지 설치

다음과 같은 콘텐츠가 포함된 *requirements.txt*라는 파일을 만듭니다.

```text
azure-mgmt-resource
azure-identity
```

가상 환경이 활성화된 터미널 또는 명령 프롬프트에서 다음 요구 사항을 설치합니다.

```cmd
pip install -r requirements.txt
```

## <a name="3-write-code-to-work-with-resource-groups"></a>3: 리소스 그룹 작업을 위한 코드 작성

### <a name="3a-list-resource-groups-in-a-subscription"></a>3a. 구독의 리소스 그룹 나열

다음 코드를 사용하여 *list_groups.py*라는 Python 파일을 만듭니다. 주석은 세부 정보를 설명합니다.

```python
# Import the needed credential and management objects from the libraries.
from azure.identity import AzureCliCredential
from azure.mgmt.resource import ResourceManagementClient
import os

# Acquire a credential object using CLI-based authentication.
credential = AzureCliCredential()

# Retrieve subscription ID from environment variable.
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]

# Obtain the management object for resources.
resource_client = ResourceManagementClient(credential, subscription_id)

# Retrieve the list of resource groups
group_list = resource_client.resource_groups.list()

# Show the groups in formatted output
column_width = 40

print("Resource Group".ljust(column_width) + "Location")
print("-" * (column_width * 2))

for group in list(group_list):
    print(f"{group.name:<{column_width}}{group.location}")
```

### <a name="3b-list-resources-within-a-specific-resource-group"></a>3b. 특정 리소스 그룹 내의 리소스 나열

다음 코드를 사용하여 *list_resources.py*라는 Python 파일을 만듭니다. 주석은 세부 정보를 설명합니다.

```python
# Import the needed credential and management objects from the libraries.
from azure.identity import AzureCliCredential
from azure.mgmt.resource import ResourceManagementClient
import os

# Acquire a credential object using CLI-based authentication.
credential = AzureCliCredential()

# Retrieve subscription ID from environment variable.
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]

# Obtain the management object for resources.
resource_client = ResourceManagementClient(credential, subscription_id)

# Retrieve the list of resources in "myResourceGroup" (change to any name desired).
# The expand argument includes additional properties in the output.
resource_list = resource_client.resources.list_by_resource_group(
    "myResourceGroup",
    expand = "createdTime,changedTime"
)

# Show the resources in formatted output
column_width = 36

print("Resource".ljust(column_width) + "Type".ljust(column_width)
    + "Create date".ljust(column_width) + "Change date".ljust(column_width))
print("-" * (column_width * 4))

for resource in list(resource_list):
    print(f"{resource.name:<{column_width}}{resource.type:<{column_width}}"
       f"{str(resource.created_time):<{column_width}}{str(resource.changed_time):<{column_width}}")
```

### <a name="authentication-in-the-code"></a>코드의 인증

[!INCLUDE [cli-auth-note](includes/cli-auth-note.md)]

### <a name="reference-links-for-classes-used-in-the-code"></a>코드에 사용된 클래스에 대한 참조 링크

- [AzureCliCredential(azure.identity)](/python/api/azure-identity/azure.identity.azureclicredential)
- [ResourceManagementClient(azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient)

## <a name="4-run-the-scripts"></a>4: 스크립트 실행

구독의 모든 리소스 그룹을 나열합니다.

```cmd
python list_groups.py
```

리소스 그룹의 모든 리소스를 나열합니다.

```cmd
python list_resources.py
```

### <a name="for-reference-equivalent-azure-cli-commands"></a>참조용: 해당 Azure CLI 명령

다음 Azure CLI 명령은 JSON 출력을 사용하여 구독에 있는 리소스 그룹을 나열합니다.

```azurecli
az group list
```

다음 명령은 centralus 지역의 "myResourceGroup"에 있는 리소스를 나열합니다(위치 인수는 특정 데이터 센터를 식별하는 데 필요함).

```azurecli
az resource list --resource group myResourceGroup --location centralus
```

## <a name="see-also"></a>참고 항목

- [예: 리소스 그룹 프로비저닝](azure-sdk-example-resource-group.md)
- [예: Azure Storage 프로비저닝](azure-sdk-example-storage.md)
- [예: Azure Storage 사용](azure-sdk-example-storage-use.md)
- [예: 웹앱 프로비저닝 및 코드 배포](azure-sdk-example-web-app.md)
- [예: 데이터베이스 프로비저닝 및 쿼리](azure-sdk-example-database.md)
- [예: 가상 머신 프로비저닝](azure-sdk-example-virtual-machines.md)
