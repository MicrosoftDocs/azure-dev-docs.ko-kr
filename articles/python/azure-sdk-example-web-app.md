---
title: Azure SDK 라이브러리를 사용하여 웹앱 프로비저닝 및 배포
description: Python용 Azure SDK 라이브러리의 관리 라이브러리를 사용하여 웹앱을 프로비저닝한 다음, GitHub 리포지토리에서 앱 코드를 배포합니다.
ms.date: 05/29/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 03a2f8b8f8830916243db0778d16650da1892b04
ms.sourcegitcommit: b03cb337db8a35e6e62b063c347891e44a8a5a13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91110461"
---
# <a name="example-use-the-azure-libraries-to-provision-and-deploy-a-web-app"></a>예: Azure 라이브러리를 사용하여 웹앱 프로비저닝 및 배포

이 예제에서는 Python 스크립트에서 Azure SDK 관리 라이브러리를 사용하여 Azure App Service에서 웹앱을 프로비저닝하고 GitHub 리포지토리에서 앱 코드를 배포하는 방법을 보여줍니다. ([동등 Azure CLI 명령](#for-reference-equivalent-azure-cli-commands)은 이 문서의 뒷부분에 있습니다.)

이 문서의 모든 명령은 언급되지 않는 한 Linux/Mac OS bash 및 Windows 명령 셸에서 동일하게 작동합니다.

## <a name="1-set-up-your-local-development-environment"></a>1: 로컬 개발 환경 설정

아직 수행하지 않은 경우 [Azure에 대한 로컬 Python 개발 환경 구성](configure-local-development-environment.md)의 모든 지침을 따르세요.

로컬 개발을 위한 서비스 주체를 만들고 이 프로젝트의 가상 환경을 만들어 활성화해야 합니다.

## <a name="2-install-the-needed-azure-library-packages"></a>2: 필요한 Azure 라이브러리 패키지 설치

다음과 같은 콘텐츠가 포함된 *requirements.txt*라는 파일을 만듭니다.

```text
azure-mgmt-resource
azure-mgmt-web
azure-cli-core
```

가상 환경이 활성화된 터미널 또는 명령 프롬프트에서 다음 요구 사항을 설치합니다.

```cmd
pip install -r requirements.txt
```

## <a name="3-fork-the-sample-repository"></a>3: 샘플 리포지토리 포크

[https://github.com/Azure-Samples/python-docs-hello-world](https://github.com/Azure-Samples/python-docs-hello-world)를 방문하여 리포지토리를 사용자 고유의 GitHub 계정으로 포크합니다. 포크를 사용하여 리포지토리를 Azure에 배포할 수 있는 권한이 있는지 확인합니다.

![GitHub에서 샘플 리포지토리 포크](media/azure-sdk-example-web-app/fork-github-repository.png)

그런 다음, 포크의 URL을 사용하여 `REPO_URL`이라는 환경 변수를 만듭니다. 다음 섹션의 코드 예는 이 환경 변수에 따라 달라집니다.

# <a name="cmd"></a>[cmd](#tab/cmd)

```cmd
set REPO_URL=<url_of_your_fork>
```

# <a name="bash"></a>[bash](#tab/bash)

```bash
REPO_URL=<url_of_your_fork>
```

---

## <a name="4-write-code-to-provision-and-deploy-a-web-app"></a>4: 웹앱을 프로비저닝 및 배포하기 위한 코드 작성

다음 코드를 사용하여 *provision_deploy_webapp.py*라는 Python 파일을 만듭니다. 주석은 세부 정보를 설명합니다.

```python
import random, os
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.web import WebSiteManagementClient

# Constants we need in multiple places: the resource group name and the region
# in which we provision resources. You can change these values however you want.
RESOURCE_GROUP_NAME = 'PythonAzureExample-WebApp-rg'
LOCATION = "centralus"

# Step 1: Provision the resource group.
resource_client = get_client_from_cli_profile(ResourceManagementClient)

rg_result = resource_client.resource_groups.create_or_update(RESOURCE_GROUP_NAME,
    { "location": LOCATION })

print(f"Provisioned resource group {rg_result.name}")

# For details on the previous code, see Example: Provision a resource group
# at https://docs.microsoft.com/azure/developer/python/azure-sdk-example-resource-group


#Step 2: Provision the App Service plan, which defines the underlying VM for the web app.

# Names for the App Service plan and App Service. We use a random number with the
# latter to create a reasonably unique name. If you've already provisioned a
# web app and need to re-run the script, set the WEB_APP_NAME environment 
# variable to that name instead.
SERVICE_PLAN_NAME = 'PythonAzureExample-WebApp-plan'
WEB_APP_NAME = os.environ.get("WEB_APP_NAME", f"PythonAzureExample-WebApp-{random.randint(1,100000):05}")

# Obtain the client object
app_service_client = get_client_from_cli_profile(WebSiteManagementClient)

# Provision the plan; Linux is the default
poller = app_service_client.app_service_plans.create_or_update(RESOURCE_GROUP_NAME,
    SERVICE_PLAN_NAME,
    {
        "location": LOCATION,
        "reserved": True,
        "sku" : {"name" : "B1"}
    }
)

plan_result = poller.result()

print(f"Provisioned App Service plan {plan_result.name}")


# Step 3: With the plan in place, provision the web app itself, which is the process that can host
# whatever code we want to deploy to it.

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

print(f"Provisioned web app {web_app_result.name} at {web_app_result.default_host_name}")

# Step 4: deploy code from a GitHub repository. For Python code, App Service on Linux runs
# the code inside a container that makes certain assumptions about the structure of the code.
# For more information, see How to configure Python apps,
# https://docs.microsoft.com/azure/app-service/configure-language-python.
#
# The create_or_update_source_control method doesn't provision a web app. It only sets the
# source control configuration for the app. In this case we're simply pointing to
# a GitHub repository.
#
# You can call this method again to change the repo.

REPO_URL = 'https://github.com/<your_fork>/python-docs-hello-world'

poller = app_service_client.web_apps.create_or_update_source_control(RESOURCE_GROUP_NAME,
    WEB_APP_NAME,
    {
        "location": "GitHub",
        "repo_url": REPO_URL,
        "branch": "master"
    }
)

sc_result = poller.result()

print(f"Set source control on web app to {sc_result.branch} branch of {sc_result.repo_url}")
```

이 코드는 Azure CLI에서 직접 수행할 수 있는 작업을 보여주므로 CLI 기반 인증 메서드(`get_client_from_cli_profile`)를 사용합니다. 두 경우 모두 인증에 동일한 ID를 사용합니다.

프로덕션 스크립트에서 이러한 코드를 사용하려면 대신 [Azure 서비스로 Python 앱을 인증하는 방법](azure-sdk-authenticate.md)에 설명된 대로 `DefaultAzureCredential`(권장) 또는 서비스 주체 기반 메서드를 사용해야 합니다.

### <a name="reference-links-for-classes-used-in-the-code"></a>코드에 사용된 클래스에 대한 참조 링크

- [ResourceManagementClient(azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient?view=azure-python)
- [WebSiteManagementClient(azure.mgmt.web import)](/python/api/azure-mgmt-web/azure.mgmt.web.websitemanagementclient?view=azure-python)

## <a name="5-run-the-script"></a>5: 스크립트 실행

```cmd
python provision_deploy_web_app.py
```

## <a name="6-verify-the-web-app-deployment"></a>6: 웹앱 배포 확인

1. 다음 명령을 실행하여 배포된 웹 사이트를 방문합니다.

    ```azurecli
    az webapp browse -n PythonAzureExample-WebApp-12345
    ```

    "PythonAzureExample-WebApp-12345"를 웹앱의 특정 이름으로 바꿉니다.

    브라우저에 "Hello, World!"가 표시되어야 합니다.

1. [Azure Portal](https://portal.azure.com)을 방문하여 **리소스 그룹**을 선택하고 "PythonAzureExample-WebApp-rg"가 나열되어 있는지 확인합니다. 그런 다음, 해당 목록으로 이동하여 예상되는 리소스(즉, App Service 계획 및 App Service)가 있는지 확인합니다.

## <a name="7-clean-up-resources"></a>7: 리소스 정리

```azurecli
az group delete -n PythonAzureExample-WebApp-rg --no-wait
```

이 예제에서 프로비저닝된 리소스를 유지할 필요가 없으며 구독에서 지속적인 요금을 방지하려면 이 명령을 실행합니다.

[`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-) 메서드를 사용하여 코드에서 리소스 그룹을 삭제할 수도 있습니다.

### <a name="for-reference-equivalent-azure-cli-commands"></a>참조용: 해당 Azure CLI 명령

다음 Azure CLI 명령은 Python 스크립트와 동일한 프로비저닝 단계를 완료합니다.

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az group create -l centralus -n PythonAzureExample-WebApp-rg

az appservice plan create -n PythonAzureExample-WebApp-plan --is-linux --sku F1

az webapp create -g PythonAzureExample-WebApp-rg -n PythonAzureExample-WebApp-12345 ^
    --plan PythonAzureExample-WebApp-plan --runtime "python|3.8"

rem You can use --deployment-source-url with the first create command. It's shown here
rem to match the sequence of the Python code.

az webapp create -n PythonAzureExample-WebApp-12345 --plan PythonAzureExample-WebApp-plan ^
    --deployment-source-url https://github.com/<your_fork>/python-docs-hello-world

rem Replace <your_fork> with the specific URL of your forked repository.
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az group create -l centralus -n PythonAzureExample-WebApp-rg

az appservice plan create -n PythonAzureExample-WebApp-plan --is-linux --sku F1

az webapp create -g PythonAzureExample-WebApp-rg -n PythonAzureExample-WebApp-12345 \
    --plan PythonAzureExample-WebApp-plan --runtime "python|3.8"

# You can use --deployment-source-url with the first create command. It's shown here
# to match the sequence of the Python code.

az webapp create -n PythonAzureExample-WebApp-12345 --plan PythonAzureExample-WebApp-plan \
    --deployment-source-url https://github.com/<your_fork>/python-docs-hello-world

# Replace <your_fork> with the specific URL of your forked repository.
```

---

## <a name="see-also"></a>참고 항목

- [예: 리소스 그룹 프로비저닝](azure-sdk-example-resource-group.md)
- [예: Azure Storage 프로비저닝](azure-sdk-example-storage.md)
- [예: Azure Storage 사용](azure-sdk-example-storage-use.md)
- [예: MySQL 데이터베이스 프로비저닝 및 사용](azure-sdk-example-database.md)
- [예: 가상 머신 프로비저닝](azure-sdk-example-virtual-machines.md)
