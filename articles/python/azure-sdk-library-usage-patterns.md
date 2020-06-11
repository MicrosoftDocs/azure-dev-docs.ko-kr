---
title: Python용 Azure 라이브러리의 사용 패턴
description: Python용 Azure SDK 라이브러리의 일반적인 사용 패턴 개요
ms.date: 05/26/2020
ms.topic: conceptual
ms.openlocfilehash: f712dc41233b8301e370c9eb63786d8e2d7f8c70
ms.sourcegitcommit: efab6be74671ea4300162e0b30aa8ac134d3b0a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84256278"
---
# <a name="azure-libraries-for-python-usage-patterns"></a>Python용 Azure 라이브러리 사용 패턴

Python용 Azure SDK는 [Python용 Azure SDK 인덱스 페이지](https://azure.github.io/azure-sdk/releases/latest/all/python.html)에 나열된 여러 독립 라이브러리로만 구성됩니다.

모든 라이브러리는 설치 및 개체 인수에 대한 인라인 JSON 사용과 같은 특정 공통 특성 및 사용 패턴을 공유합니다.

## <a name="library-installation"></a>라이브러리 설치

특정 라이브러리 패키지를 설치하려면 `pip install`을 사용합니다.

```cmd
# Install the management library for Azure Storage
pip install azure-mgmt-storage
```

```cmd
# Install the client library for Azure Storage
pip install azure-storage-blob
```

`pip install`은 현재 Python 환경에서 최신 버전의 라이브러리를 검색합니다.

`pip`를 사용하여 라이브러리를 제거하고 미리 보기 버전을 비롯한 특정 버전을 설치할 수도 있습니다. 자세한 내용은 [Python용 Azure 라이브러리 패키지 설치 방법](azure-sdk-install.md)을 참조하세요.

## <a name="inline-json-pattern-for-object-arguments"></a>개체 인수에 대한 인라인 JSON 패턴

Azure 라이브러리 내의 많은 작업을 통해 개체 인수를 개별 개체 또는 인라인 JSON으로 표현할 수 있습니다.

예를 들어 [`create_or_update`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#create-or-update-resource-group-name--parameters--custom-headers-none--raw-false----operation-config-) 메서드를 통해 리소스 그룹을 생성할 수 있는 [`ResourceManagementClient`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.resourcemanagementclient?view=azure-python) 개체가 있다고 가정하겠습니다. 이 메서드의 두 번째 인수는 [`ResourceGroup`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.models.resourcegroup?view=azure-python) 형식입니다.

`create_or_update`를 호출하려면 필요한 인수(이 경우 `location`)를 직접 사용하여 개별 `ResourceGroup` 인스턴스를 만들면 됩니다.

```python
rg_result = resource_client.resource_groups.create_or_update(
    "PythonSDKExample-rg",
    ResourceGroup(location="centralus")
)
```

또는 인라인 JSON과 동일한 매개 변수를 전달할 수 있습니다.

```python
rg_result = resource_client.resource_groups.create_or_update(
    "PythonSDKExample-rg",
    {
      "location": "centralus"
    }
)
```

JSON을 사용하는 경우 Azure 라이브러리는 인라인 JSON을 해당 인수에 적합한 개체 형식으로 자동 변환합니다.

개체에 중첩된 개체 인수도 있을 수 있으며, 이런 경우 중첩된 JSON도 사용할 수 있습니다.

예를 들어 [`KeyVaultManagementClient`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.keyvaultmanagementclient?view=azure-python) 개체 인스턴스가 있고 해당 [`create_or_update`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.operations.vaultsoperations?view=azure-python#create-or-update-resource-group-name--vault-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) 메서드를 호출한다고 가정하겠습니다. 이 경우 세 번째 인수는 [`VaultCreateOrUpdateParameters`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultcreateorupdateparameters?view=azure-python) 형식이며 인수 자체에 [`VaultProperties`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultproperties?view=azure-python) 형식의 인수를 포함합니다. `VaultProperties`는 [`Sku`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.sku?view=azure-python) 형식 및 [`list[AccessPolicyEntry`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.accesspolicyentry?view=azure-python) 형식의 개체 인수를 차례로 포함합니다. `Sku`는 [`SkuName`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.skuname?view=azure-python) 개체를 포함하고 각 `AccessPolicyEntry`는 [`Permissions`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.permissions?view=azure-python) 개체를 포함합니다.

포함 개체를 사용하여 `create_or_update`를 호출하려면 다음과 같은 코드를 사용합니다(`tenant_id`와 `object_id`가 이미 정의되어 있다고 가정). 또한 함수 호출 전에 필요한 개체를 만들 수 있습니다.

```python
operation = keyvault_client.vaults.create_or_update(
    "PythonSDKExample-rg",
    "keyvault01",
    VaultCreateOrUpdateParameters(
        location="centralus",
        properties=VaultProperties(
            tenant_id=tenant_id,
            sku=Sku(name="standard"),
            access_policies=[
                AccessPolicyEntry(
                    tenant_id=tenant_id,
                    object_id=object_id,
                    permissions=Permissions(keys=['all'], secrets=['all'])
                )
            ]
        )
    )
)
```

인라인 JSON이 사용된 동일한 호출은 다음과 같이 나타납니다.

```python
operation = keyvault_client.vaults.create_or_update(
    "PythonSDKExample-rg",
    "keyvault01",
    {
        'location': 'centralus',
        'properties': {
            'sku': {
                'name': 'standard'
            },
            'tenant_id': tenant_id,
            'access_policies': [{
                'object_id': object_id,
                'tenant_id': tenant_id,
                'permissions': {
                    'keys': ['all'],
                    'secrets': ['all']
                }
            }]
        }
    }
)
```

두 형태가 동일하기 때문에 어느 쪽을 선호하든 괜찮으며 혼합할 수도 있습니다.

JSON이 제대로 구성되지 않으면 일반적으로 다음 오류가 발생합니다. "DeserializationError: Unable to deserialize to object: type, AttributeError: 'str' object has no attribute 'get'". 이 오류의 일반적인 원인은 라이브러리에 중첩된 JSON 개체가 필요할 때 속성에 단일 문자열을 제공하기 때문입니다. 예를 들어 앞의 예제에서 `"sku": "standard"`를 사용하면 이러한 오류가 발생합니다. `sku` 매개 변수는 `Sku` 개체이고 이 개체에는 필요한 `SkuName` 형식에 매핑되는 인라인 개체 JSON(이 경우, `{ "name": "standard"}`)이 필요하기 때문입니다.

## <a name="next-steps"></a>다음 단계

이제 Python용 Azure 라이브러리 사용에 대한 일반적인 패턴을 이해했으며, 이제 다음 독립 실행형 예제를 참조하여 특정 관리 및 클라이언트 라이브러리 시나리오를 살펴보세요.

- [예: 리소스 그룹 만들기](azure-sdk-example-resource-group.md)
- [예: Azure Storage 사용](azure-sdk-example-storage.md)
- [예: 웹앱 프로비저닝 및 코드 배포](azure-sdk-example-web-app.md)
- [예: 가상 머신 프로비저닝](azure-sdk-example-virtual-machines.md)

이들 예는 순차적이거나 상호 의존적이지 않으므로 어떤 순서로든 시도할 수 있습니다.