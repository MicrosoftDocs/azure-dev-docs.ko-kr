---
title: Python용 Azure 라이브러리를 사용하여 리소스 그룹 프로비저닝
description: Python용 Azure SDK의 리소스 관리 라이브러리를 사용하여 Python 코드에서 리소스 그룹을 만듭니다.
ms.date: 05/29/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 1a87e53253eff5679245ac5528fac3bb10e2bc8d
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87982615"
---
# <a name="example-use-the-azure-libraries-to-provision-a-resource-group"></a>예제: Azure 라이브러리를 사용하여 리소스 그룹 프로비저닝

이 예제에서는 Python 스크립트에서 Azure SDK 관리 라이브러리를 사용하여 리소스 그룹을 프로비저닝하는 방법을 보여줍니다. ([동등 Azure CLI 명령](#for-reference-equivalent-azure-cli-commands)은 이 문서의 뒷부분에 있습니다.)

이 문서의 모든 명령은 언급되지 않는 한 Linux/Mac OS bash 및 Windows 명령 셸에서 동일하게 작동합니다.

## <a name="1-set-up-your-local-development-environment"></a>1: 로컬 개발 환경 설정

아직 수행하지 않은 경우 [Azure에 대한 로컬 Python 개발 환경 구성](configure-local-development-environment.md)의 모든 지침을 따르세요.

로컬 개발을 위한 서비스 주체를 만들고 이 프로젝트의 가상 환경을 만들어 활성화해야 합니다.

## <a name="2-install-the-azure-library-packages"></a>2: Azure 라이브러리 패키지 설치

다음과 같은 콘텐츠가 포함된 *requirements.txt*라는 파일을 만듭니다.

```text
azure-mgmt-resource
azure-cli-core
```

가상 환경이 활성화된 터미널 또는 명령 프롬프트에서 다음 요구 사항을 설치합니다.

```cmd
pip install -r requirements.txt
```

## <a name="3-write-code-to-provision-a-resource-group"></a>3: 리소스 그룹을 프로비저닝하는 코드 작성

다음 코드를 사용하여 *provision_rg.py*라는 Python 파일을 만듭니다. 주석은 세부 정보를 설명합니다.

```python
# Import the needed management objects from the libraries. The azure.common library
# is installed automatically with the other libraries.
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient

# Obtain the management object for resources, using the credentials from the CLI login.
resource_client = get_client_from_cli_profile(ResourceManagementClient)

# Provision the resource group.
rg_result = resource_client.resource_groups.create_or_update(
    "PythonAzureExample-ResourceGroup-rg",
    {
        "location": "centralus"
    }
)

# Within the ResourceManagementClient is an object named resource_groups,
# which is of class ResourceGroupsOperations, which contains methods like
# create_or_update.
#
# The second parameter to create_or_update here is technically a ResourceGroup
# object. You can create the object directly using ResourceGroup(location=LOCATION)
# or you can express the object as inline JSON as shown here. For details,
# see Inline JSON pattern for object arguments at
# https://docs.microsoft.com/azure/developer/python/azure-sdk-overview#inline-json-pattern-for-object-arguments.

print(f"Provisioned resource group {rg_result.name} in the {rg_result.location} region")

# The return value is another ResourceGroup object with all the details of the
# new group. In this case the call is synchronous: the resource group has been
# provisioned by the time the call returns.

# Optional line to delete the resource group
#resource_client.resource_groups.delete("PythonAzureExample-ResourceGroup-rg")
```

이 코드는 Azure CLI에서 직접 수행할 수 있는 작업을 보여주므로 CLI 기반 인증 메서드(`get_client_from_cli_profile`)를 사용합니다. 두 경우 모두 인증에 동일한 ID를 사용합니다.

프로덕션 스크립트에서 이러한 코드를 사용하려면 대신 [Azure 서비스로 Python 앱을 인증하는 방법](azure-sdk-authenticate.md)에 설명된 대로 `DefaultAzureCredential`(권장) 또는 서비스 주체 기반 메서드를 사용해야 합니다.

### <a name="reference-links-for-classes-used-in-the-code"></a>코드에 사용된 클래스에 대한 참조 링크

- [ResourceManagementClient(azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient?view=azure-python)

## <a name="4-run-the-script"></a>4: 스크립트 실행

```cmd
python provision_rg.py
```

## <a name="5-verify-the-resource-group"></a>5: 리소스 그룹 확인

Azure Portal 또는 Azure CLI를 통해 그룹이 존재하는지 확인할 수 있습니다.

- Azure Portal: [Azure Portal](https://portal.azure.com)을 열어 **리소스 그룹**을 선택하고 그룹이 나열되어 있는지 확인합니다. 포털을 이미 연 경우 **새로 고침** 명령을 사용하여 목록을 업데이트합니다.

- Azure CLI: 다음 명령을 실행합니다.

    ```azurecli
    az group show -n PythonAzureExample-ResourceGroup-rg
    ```

## <a name="6-clean-up-resources"></a>6: 리소스 정리

```azurecli
az group delete -n PythonAzureExample-ResourceGroup-rg
```

이 예제에서 리소스 그룹을 프로비저닝된 상태로 유지할 필요가 없는 경우 이 명령을 실행합니다. 리소스 그룹에는 구독에서 진행 중인 요금이 발생하지 않지만, 적극적으로 사용하지 않는 그룹을 정리하는 것이 좋습니다.

[`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-) 메서드를 사용하여 코드에서 리소스 그룹을 삭제할 수도 있습니다.

### <a name="for-reference-equivalent-azure-cli-commands"></a>참조용: 해당 Azure CLI 명령

다음 Azure CLI 명령은 Python 스크립트와 동일한 프로비저닝 단계를 완료합니다.

```azurecli
az group create -n PythonAzureExample-ResourceGroup-rg -l centralus
```

## <a name="see-also"></a>참고 항목

- [예: Azure Storage 프로비저닝](azure-sdk-example-storage.md)
- [예: Azure Storage 사용](azure-sdk-example-storage-use.md)
- [예: 웹앱 프로비저닝 및 코드 배포](azure-sdk-example-web-app.md)
- [예: 데이터베이스 프로비저닝 및 쿼리](azure-sdk-example-database.md)
- [예: 가상 머신 프로비저닝](azure-sdk-example-virtual-machines.md)
