---
title: Python용 Azure SDK 사용
description: 개발자가 Azure 리소스를 프로비저닝, 사용 및 관리하는 경우 생산성을 높이는 데 도움이 되는 Python용 Azure SDK의 특징과 기능에 대해 간략히 설명합니다.
ms.date: 05/13/2020
ms.topic: conceptual
ms.openlocfilehash: 856487b06e7a84b0659126d8959ce45b5309a103
ms.sourcegitcommit: fbbc341a0b9e17da305bd877027b779f5b0694cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83631541"
---
# <a name="use-the-azure-sdk-for-python"></a>Python용 Azure SDK 사용

Python용 오픈 소스 Azure SDK는 Python 애플리케이션 코드에서 Azure 리소스를 프로비저닝하고, 관리하고, 사용하는 작업을 간소화합니다.

## <a name="the-details-you-really-want-to-know"></a>꼭 알아야 하는 정보

- SDK는 Python 2.7 및 Python 3.5.3 이상을 지원하며 PyPy 5.4 이상에서도 테스트되었습니다.

- SDK는 특정 Azure 서비스와 관련된 180개가 넘는 개별 Python 라이브러리로 구성되어 있습니다.

- `pip install <library_name>`에 [릴리스 목록](https://azure.github.io/azure-sdk/releases/latest/all/python.html)의 라이브러리 이름을 사용하여 필요한 라이브러리를 설치할 수 있습니다. 자세한 내용은 [Azure SDK 라이브러리 설치](azure-sdk-install.md)를 참조하세요.

- "관리" 및 "클라이언트" 라이브러리("관리 평면" 및 "데이터 평면" 라이브러리라고도 함)가 별도로 있습니다. 각 세트는 서로 다른 용도로 사용되며 서로 다른 종류의 코드에 사용됩니다. 자세한 내용은 이 문서의 뒷부분에서 다음 섹션을 참조하십시오.
  - [관리 라이브러리를 사용하여 Azure 리소스 프로비저닝 및 관리](#provision-and-manage-azure-resources-with-management-libraries)
  - [클라이언트 라이브러리를 사용하여 Azure 리소스에 연결 및 사용](#connect-to-and-use-azure-resources-with-client-libraries)

- SDK에 대한 설명서는 [Python용 Azure SDK 참조](/python/api/overview/azure/?view=azure-python)(Azure 서비스로 구성되어 있음) 또는 [Python API 브라우저](/python/api/?view=azure-python)(패키지 이름으로 구성되어 있음)에서 찾을 수 있습니다. 현재, 관심 있는 클래스와 메서드로 이동하려면 여러 레이어를 클릭해야 하는 경우가 종종 있습니다. 환경이 열악한 점에 대해 미리 사과드립니다. 개선하기 위해 노력하고 있습니다!

- 라이브러리를 직접 사용해보려면 [예제: 리소스 그룹 만들기](azure-sdk-example-resource-group.md)부터 시작한 다음, [예제: Azure Storage 사용](azure-sdk-example-storage.md)을 시도해보세요.

### <a name="non-essential-but-still-interesting-details"></a>필수는 아니지만 흥미로운 정보

- Azure CLI는 SDK 관리 라이브러리를 사용하여 Python으로 작성했기 때문에 Azure CLI 명령으로 수행할 수 있는 모든 작업은 Python 스크립트로도 수행할 수 있습니다. 즉, CLI 명령은 여러 작업을 함께 수행하고, 비동기 작업을 자동으로 처리하고, 연결 문자열과 같은 출력 형식을 지정하는 등의 유용한 기능을 제공합니다. 따라서 자동화된 프로비저닝 및 관리 스크립트에 CLI(또는 상응하는 Azure PowerShell)를 사용하는 경우, 프로세스에 대해 훨씬 정확한 제어 수준이 필요한 경우가 아니면, 상응하는 Python 코드를 작성하는 것보다 훨씬 더 편리할 수 있습니다.

- Python용 Azure SDK는 기본 Azure REST API를 기반으로 구축된 Python 레이어이기 때문에, 익숙한 Python 패러다임을 통해 해당 API를 사용할 수 있습니다. 하지만 필요할 때는 언제든지 Python 코드에서 직접 REST API를 사용할 수 있습니다.

- SDK용 소스 코드는 [https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python)에서 찾을 수 있습니다. 오픈 소스 프로젝트이기 때문에 여러분의 기여를 환영합니다!

- 테스트를 거치지 않은 IronPython 및 Jython과 같은 인터프리터에서 SDK를 사용할 수 있지만 격리된 문제와 비호환성 문제가 발생할 수 있습니다.

- SDK 설명서의 소스 리포지토리는 [https://github.com/MicrosoftDocs/azure-docs-sdk-python/](https://github.com/MicrosoftDocs/azure-docs-sdk-python/)에서 찾을 수 있습니다.

- 현재 인증 프로토콜, 로깅, 추적, 전송 프로토콜, 버퍼링된 응답 및 재시도와 같은 일반적인 클라우드 패턴을 공유하도록 Python용 Azure SDK 라이브러리를 업데이트하고 있습니다.

  - 이 공유 기능은 [azure-core](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/core/azure-core) 라이브러리에 포함되어 있습니다.

  - 현재 코어 라이브러리와 작동하는 라이브러리는 [Python용 Azure SDK 최신 릴리스](https://azure.github.io/azure-sdk/releases/latest/#python)에 나열되어 있습니다. 이러한 라이브러리는 주로 클라이언트 라이브러리이며 "트랙 2"라고 하는 경우도 있습니다.

  - 관리 라이브러리는 아직 업데이트되지 않았으며 "트랙 1"이라고 하는 경우도 있습니다.

- SDK에 적용되는 지침에 대한 자세한 내용은 [Python 지침: 소개](https://azure.github.io/azure-sdk/python_introduction.html)를 참조하세요.

## <a name="provision-and-manage-azure-resources-with-management-libraries"></a>관리 라이브러리를 사용하여 Azure 리소스 프로비저닝 및 관리

SDK의 관리(또는 "관리 평면") 라이브러리는 이름이 모두 `azure-mgmt-`로 시작되며 Python 스크립트에서 Azure 리소스를 생성, 프로비저닝 및 관리하는 데 유용합니다. 모든 Azure 서비스에는 해당하는 관리 라이브러리가 있습니다.

관리 라이브러리를 사용하면 구성 및 배포 스크립트를 작성하여 [Azure Portal](https://portal.azure.com)이나 [Azure CLI](/cli/azure/install-azure-cli)를 통해 수행할 수 있는 동일한 작업을 수행할 수 있습니다. (앞서 언급했듯이 Azure CLI는 Python으로 작성되었으며 관리 라이브러리를 사용하여 다양한 명령을 구현합니다.)

각 관리 라이브러리를 사용하는 방법에 대한 자세한 내용은 SDK의 [GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk)에서 라이브러리의 프로젝트 폴더에 있는 *README.md* 또는 *README.rst* 파일을 참조하세요. 추가 코드 조각은 [참조 설명서](/python/api?view=azure-python) 및 [Azure 샘플](https://docs.microsoft.com/samples/browse/?languages=python&products=azure)에서 찾을 수도 있습니다.

## <a name="connect-to-and-use-azure-resources-with-client-libraries"></a>클라이언트 라이브러리를 사용하여 Azure 리소스에 연결 및 사용

SDK의 클라이언트(또는 "데이터 평면") 라이브러리는 이미 프로비저닝된 서비스와 상호 작용하는 Python 애플리케이션 코드를 작성하는 데 유용합니다. SDK는 클라이언트 API를 지원하는 서비스에 대해서만 클라이언트 라이브러리를 제공합니다.

각 클라이언트 라이브러리를 사용하는 방법에 대한 자세한 내용은 SDK의 [GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk)에서 라이브러리의 프로젝트 폴더에 있는 *README.md* 또는 *README.rst* 파일을 참조하세요. 추가 코드 조각은 [참조 설명서](/python/api?view=azure-python) 및 [Azure 샘플](https://docs.microsoft.com/samples/browse/?languages=python&products=azure)에서 찾을 수도 있습니다.

## <a name="inline-json-pattern-for-object-arguments"></a>개체 인수에 대한 인라인 JSON 패턴

Azure SDK에 포함된 많은 작업은 공통 패턴을 지원합니다. 이를 통해 개체 인수를 개별 개체나 인라인 JSON으로 표현할 수 있습니다.

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

JSON을 사용하는 경우 SDK는 인라인 JSON을 해당 인수에 적합한 개체 형식으로 자동 변환합니다.

개체에 중첩된 개체 인수도 있을 수 있으며, 이런 경우 중첩된 JSON도 사용할 수 있습니다.

예를 들어 [`KeyVaultManagementClient`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.keyvaultmanagementclient?view=azure-python) 개체 인스턴스가 있고 해당 [`create_or_update`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.operations.vaultsoperations?view=azure-python#create-or-update-resource-group-name--vault-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) 메서드를 호출한다고 가정하겠습니다. 이 경우 세 번째 인수는 [`VaultCreateOrUpdateParameters`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultcreateorupdateparameters?view=azure-python) 형식이며 인수 자체에 [`VaultProperties`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultproperties?view=azure-python) 형식의 인수를 포함합니다. `VaultProperties`는 [`Sku`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.sku?view=azure-python) 형식 및 [`list[AccessPolicyEntry`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.accesspolicyentry?view=azure-python) 형식의 개체 인수를 차례로 포함합니다. `Sku`는 [`SkuName`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.skuname?view=azure-python) 개체를 포함하고 각 `AccessPolicyEntry`는 [`Permissions`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.permissions?view=azure-python) 개체를 포함합니다.

포함 개체를 사용하여 `create_or_update`를 호출하려면 다음과 같은 코드를 사용합니다(`tenant_id`와 `object_id`가 이미 정의되어 있다고 가정).

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

두 형태가 완전히 동일하기 때문에 어느 쪽을 선호하든 괜찮고 혼합할 수도 있습니다.

JSON이 제대로 구성되지 않으면 일반적으로 다음 오류가 발생합니다. "DeserializationError: Unable to deserialize to object: type, AttributeError: 'str' object has no attribute 'get'". 이 오류의 일반적인 원인은 SDK에 중첩된 JSON 개체가 필요할 때 속성에 단일 문자열을 제공하기 때문입니다. 예를 들어 앞의 예제에서 `"sku": "standard"`를 사용하면 이러한 오류가 발생합니다. `sku` 매개 변수는 `Sku` 개체이고 이 개체에는 필요한 `SkuName` 형식에 매핑되는 인라인 개체 JSON(이 경우, `{ "name": "standard"}`)이 필요하기 때문입니다.

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [SDK 라이브러리 설치 >>>](azure-sdk-install.md)

## <a name="get-help-and-connect-with-the-sdk-team"></a>SDK 팀과 교류하고 도움 받기

- [Python용 Azure SDK 설명서](https://aka.ms/python-docs)를 참조합니다.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sdk-python)의 커뮤니티에 질문을 게시합니다.
- [GitHub](https://github.com/Azure/azure-sdk-for-python/issues)에서 SDK에 대한 문제를 엽니다.
- Twitter에서 [@AzureSDK](https://twitter.com/AzureSdk/)를 언급합니다.
