---
title: Python용 Azure 라이브러리의 사용 패턴
description: Python용 Azure SDK 라이브러리의 일반적인 사용 패턴 개요
ms.date: 11/12/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 6f1a2c07bbda4ebe409722d2381e046ee45f7902
ms.sourcegitcommit: 6514a061ba5b8003ce29d67c81a9f0795c3e3e09
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94601395"
---
# <a name="azure-libraries-for-python-usage-patterns"></a>Python용 Azure 라이브러리 사용 패턴

Python용 Azure SDK는 [Python SDK 패키지 인덱스](azure-sdk-library-package-index.md)에 나열된 여러 독립적인 라이브러리로만 구성됩니다.

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

## <a name="asynchronous-operations"></a>비동기 작업

클라이언트 및 관리 클라이언트 개체(예: [`ComputeManagementClient.virtual_machines.begin_create_or_update`](/python/api/azure-mgmt-compute/azure.mgmt.compute.v2020_06_01.operations.virtualmachinesoperations#begin-create-or-update-resource-group-name--vm-name--parameters----kwargs-) 및 [`WebSiteManagementClient.web_apps.create_or_update`](/python/api/azure-mgmt-web/azure.mgmt.web.v2019_08_01.operations.webappsoperations#create-or-update-resource-group-name--name--site-envelope--custom-headers-none--raw-false--polling-true----operation-config-))를 통해 호출하는 많은 작업에서는 `AzureOperationPoller[<type>]` 형식의 개체를 반환합니다. 여기서 `<type>`은 해당 작업에만 적용됩니다.

두 메서드 모두 비동기 메서드입니다. 메서드 이름이 다른 이유는 버전이 다르기 때문입니다. azure.core를 기반으로 하지 않는 이전 라이브러리는 일반적으로 `create_or_update` 형식의 이름을 사용합니다. azure.core 기반의 라이브러리는 비동기 메서드라는 것을 나타내기 위해 메서드 이름에 `begin_` 접두사를 추가합니다. 이전 코드를 최신 azure.core 기반 라이브러리로 마이그레이션하면 일반적으로 메서드 이름에 `begin_` 접두사가 추가됩니다. 대부분의 메서드 서명이 동일하게 유지되기 때문입니다.

어떤 경우든 [`AzureOperationPoller`](/python/api/msrestazure/msrestazure.azure_operation.azureoperationpoller) 반환 형식은 작업이 비동기 작업이라는 것을 의미합니다. 따라서 해당 폴러의 `result` 메서드를 호출한 후, 작업이 완료되고 작업 결과를 얻게 될 때까지 기다려야 합니다.

[예제: 웹앱 프로비저닝 및 배포](azure-sdk-example-web-app.md)에서 가져온 다음 코드는 폴러를 사용하여 결과를 기다리는 예제를 보여 줍니다.

```python
poller = app_service_client.web_apps.create_or_update(RESOURCE_GROUP_NAME,
    WEB_APP_NAME,
    {
        "location": LOCATION,
        "server_farm_id": plan_result.id,
        "site_config": {
            "linux_fx_version": "python|3.8"
        }
    }
)

web_app_result = poller.result()
```

이 경우 `create_or_update`의 반환 값이 `AzureOperationPoller[Site]` 형식이며, 이는 `poller.result()`의 반환 값이 [Site](/python/api/azure-mgmt-web/azure.mgmt.web.v2019_08_01.models.site) 개체임을 의미합니다.

## <a name="exceptions"></a>예외

Azure REST API에 대한 실패한 HTTP 요청을 포함하여 작업이 의도한 대로 수행되지 않으면 일반적으로 Azure 라이브러리에서 예외가 발생합니다. 그러면 앱 코드에서 라이브러리 작업 주위에 `try...except` 블록을 사용할 수 있습니다.

발생할 수 있는 예외의 형식에 대한 자세한 내용은 해당 작업에 대한 설명서를 참조하세요.

## <a name="logging"></a>로깅

최신 Azure 라이브러리는 Python 표준 `logging` 라이브러리를 사용하여 로그 출력을 생성합니다. 개별 라이브러리, 라이브러리 그룹 또는 모든 라이브러리에 대한 로깅 수준을 설정할 수 있습니다. 로깅 스트림 처리기가 등록되면 특정 클라이언트 개체 또는 특정 작업에 대한 로깅을 사용하도록 설정할 수 있습니다. 자세한 내용은 [Azure 라이브러리의 로깅](azure-sdk-logging.md)을 참조하세요.

## <a name="proxy-configuration"></a>프록시 구성

프록시를 지정하려면 환경 변수 또는 선택적 인수를 사용할 수 있습니다. 자세한 내용은 [프록시를 구성하는 방법](azure-sdk-configure-proxy.md)을 참조하세요.

## <a name="optional-arguments-for-client-objects-and-methods"></a>클라이언트 개체 및 메서드에 대한 선택적 인수

라이브러리 참조 설명서에서 `**kwargs` 또는 `**operation_config**` 인수는 클라이언트 개체 생성자 또는 특정 작업 메서드의 서명에 표시되는 경우가 많습니다. 이러한 자리 표시자는 해당 개체 또는 메서드에서 명명된 추가 인수를 지원할 수 있음을 의미합니다. 일반적으로 참조 설명서에는 사용할 수 있는 특정 인수가 표시됩니다. 또한 다음 섹션에서 설명한 대로 자주 지원되는 몇 가지 일반적인 인수도 있습니다.

### <a name="arguments-for-libraries-based-on-azurecore"></a>azure.core를 기반으로 하는 라이브러리에 대한 인수

이러한 인수는 [Python - 새 라이브러리](https://azure.github.io/azure-sdk/releases/latest/#python)에 나열된 라이브러리에 적용됩니다.

| Name                       | Type | 기본값     | Description |
| ---                        | ---  | ---         | ---         |
| logging_enable             | bool | False       | 로깅을 사용하도록 설정합니다. 자세한 내용은 [Azure 라이브러리의 로깅](azure-sdk-logging.md)을 참조하세요. |
| proxies                    | dict | {}          | 프록시 서버 URL입니다. 자세한 내용은 [프록시를 구성하는 방법](azure-sdk-configure-proxy.md)을 참조하세요. |
| use_env_settings           | bool | True        | True이면 `HTTP_PROXY` 및 `HTTPS_PROXY` 환경 변수를 프록시에 사용할 수 있습니다. False이면 환경 변수가 무시됩니다. 자세한 내용은 [프록시를 구성하는 방법](azure-sdk-configure-proxy.md)을 참조하세요. |
| connection_timeout         | int  | 300         | Azure REST API 엔드포인트에 연결하는 데 적용되는 시간 제한(초)입니다. |
| read_timeout               | int  | 300         | Azure REST API 작업을 완료하는 데 적용되는 시간 제한(초), 즉 응답을 기다리는 시간입니다. |
| retry_total                | int  | 10          | REST API 호출에 대해 허용되는 재시도 횟수입니다. 재시도를 사용하지 않도록 설정하려면 `retry_total=0`을 사용합니다. |
| retry_mode                 | enum | exponential | 재시도 타이밍을 linear(선형) 또는 exponential(지수) 방식으로 적용합니다. 'single'이면 재시도가 일정한 간격으로 수행됩니다. 'exponential'이면 각 재시도에서 이전 재시도보다 두 배 더 오래 기다립니다. |

개별 라이브러리는 이러한 인수를 지원할 필요가 없으므로 정확한 세부 정보는 항상 각 라이브러리에 대한 참조 설명서를 참조하세요.

### <a name="arguments-for-non-core-libraries"></a>코어가 아닌 라이브러리에 대한 인수

| Name               | Type | 기본값 | Description |
| ---                | ---  | ---     | ---         |
| verify             | bool | True    | SSL 인증서를 확인합니다. |
| cert               | str  | None    | 클라이언트 측 확인을 위한 로컬 인증서의 경로입니다. |
| 시간 제한            | int  | 30      | 서버 연결을 설정하기 위한 시간 제한(초). |
| allow_redirects    | bool | False   | 리디렉션을 사용하도록 설정합니다. |
| max_redirects      | int  | 30      | 최대 허용 리디렉션 수입니다. |
| proxies            | dict | {}      | 프록시 서버 URL입니다. 자세한 내용은 [프록시를 구성하는 방법](azure-sdk-configure-proxy.md)을 참조하세요. |
| use_env_proxies    | bool | False   | 로컬 환경 변수에서 프록시 설정을 읽을 수 있도록 설정합니다. |
| retries            | int  | 10      | 허용되는 총 재시도 횟수입니다. |
| enable_http_logger | bool | False   | 디버그 모드에서 HTTP 로그를 사용하도록 설정합니다. |

## <a name="inline-json-pattern-for-object-arguments"></a>개체 인수에 대한 인라인 JSON 패턴

Azure 라이브러리 내의 많은 작업을 통해 개체 인수를 개별 개체 또는 인라인 JSON으로 표현할 수 있습니다.

예를 들어 [`create_or_update`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations#create-or-update-resource-group-name--parameters--custom-headers-none--raw-false----operation-config-) 메서드를 통해 리소스 그룹을 생성할 수 있는 [`ResourceManagementClient`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.resourcemanagementclient) 개체가 있다고 가정하겠습니다. 이 메서드의 두 번째 인수는 [`ResourceGroup`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.models.resourcegroup) 형식입니다.

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

예를 들어 [`KeyVaultManagementClient`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.keyvaultmanagementclient) 개체 인스턴스가 있고 해당 [`create_or_update`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.operations.vaultsoperations#create-or-update-resource-group-name--vault-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) 메서드를 호출한다고 가정하겠습니다. 이 경우 세 번째 인수는 [`VaultCreateOrUpdateParameters`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultcreateorupdateparameters) 형식이며 인수 자체에 [`VaultProperties`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultproperties) 형식의 인수를 포함합니다. `VaultProperties`는 [`Sku`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.sku) 형식 및 [`list[AccessPolicyEntry]`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.accesspolicyentry) 형식의 개체 인수를 차례로 포함합니다. `Sku`는 [`SkuName`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.skuname) 개체를 포함하고 각 `AccessPolicyEntry`는 [`Permissions`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.permissions) 개체를 포함합니다.

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

두 양식이 모두 동일하므로 원하는 양식을 선택하거나 혼합할 수도 있습니다.

JSON이 제대로 구성되지 않으면 일반적으로 다음 오류가 발생합니다. "DeserializationError: Unable to deserialize to object: type, AttributeError: 'str' object has no attribute 'get'". 이 오류의 일반적인 원인은 라이브러리에 중첩된 JSON 개체가 필요할 때 속성에 단일 문자열을 제공하기 때문입니다. 예를 들어 앞의 예제에서 `"sku": "standard"`를 사용하면 이러한 오류가 발생합니다. `sku` 매개 변수는 `Sku` 개체이고 이 개체에는 필요한 `SkuName` 형식에 매핑되는 인라인 개체 JSON(이 경우, `{ "name": "standard"}`)이 필요하기 때문입니다.

## <a name="next-steps"></a>다음 단계

이제 Python용 Azure 라이브러리 사용에 대한 일반적인 패턴을 이해했으며, 이제 다음 독립 실행형 예제를 참조하여 특정 관리 및 클라이언트 라이브러리 시나리오를 살펴보세요.

- [예: 리소스 그룹 만들기](azure-sdk-example-resource-group.md)
- [예: Azure Storage 사용](azure-sdk-example-storage.md)
- [예: 웹앱 프로비저닝 및 코드 배포](azure-sdk-example-web-app.md)
- [예: 데이터베이스 프로비저닝 및 쿼리](azure-sdk-example-database.md)
- [예: 가상 머신 프로비저닝](azure-sdk-example-virtual-machines.md)

이들 예는 순차적이거나 상호 의존적이지 않으므로 어떤 순서로든 시도할 수 있습니다.
