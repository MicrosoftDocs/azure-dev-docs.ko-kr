---
title: Azure 개발을 위한 로컬 JavaScript 환경 구성
description: 편집기, Azure SDK 라이브러리, 선택적 도구 및 라이브러리 인증에 필요한 자격 증명을 포함하여 Azure를 사용하기 위한 로컬 JavaScript 개발 환경을 설정하는 방법을 설명합니다.
ms.date: 07/01/2020
ms.topic: conceptual
ms.openlocfilehash: 2285e79ea62d2de961fd7d4dc7647fec2312b83c
ms.sourcegitcommit: 7be67fb768fb5e19f7de573068cc1376b3d90d1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85911192"
---
# <a name="configure-your-local-javascript-dev-environment-for-azure"></a>Azure를 위한 로컬 JavaScript 개발 환경 구성

클라우드 애플리케이션을 만들 때 개발자는 일반적으로 로컬 워크스테이션에서 코드를 테스트한 후 Azure 같은 클라우드 환경에 코드를 배포하는 방법을 선호합니다. 로컬 개발을 통해 친숙한 개발 환경과 함께 다양한 도구를 활용할 수 있습니다.

이 문서에서는 Azure를 사용한 JavaScript에 적합한 로컬 개발 환경을 만들고 유효성을 검사하기 위한 설치 지침을 제공합니다.

## <a name="one-time-subscription-creation"></a>일회성 구독 만들기

Azure 리소스는 Azure 사용의 청구 단위인 구독 내에 생성됩니다. 무료 리소스(각 구독은 대부분의 서비스에 무료 리소스를 제공함)를 만들 수 있지만, 리소스를 프로덕션에 배포할 때 유료 계층 리소스를 만들어야 합니다.

* 구독이 이미 있는 경우 새로 만들 필요가 없습니다. 기존 구독에 액세스하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
* [평가판 구독 시작]()

## <a name="one-time-installation"></a>일회성 설치

로컬 워크스테이션에서 JavaScript를 사용하여 Azure 리소스를 사용하여 개발하려면 다음이 설치되어 있어야 합니다.

|이름/설치 프로그램|설명|
|--|--|
|[Node.js]()|로컬 워크스테이션 개발을 위한 최신 LTS(장기 지원) 런타임 환경을 설치합니다. |
| NPM(최신 버전의 node.js와 함께 설치됨) 또는 [Yarn]()|Azure SDK 라이브러리를 설치하기 위한 패키지 관리자.|
|[VSCode](https://aka.ms/vscode-deploy)| VSCode는 뛰어난 JavaScript 통합 및 코딩 환경을 제공하지만 반드시 필요한 것은 아닙니다. 모든 코드 편집기를 사용할 수 있습니다. 이 문서에서는 다른 편집기를 사용하는 경우 Azure와의 통합을 확인하거나 Azure CLI를 사용합니다.|
|[Azure CLI]()|Azure CLI를 사용하여 명령줄, 터미널 또는 Bash 셸에서 Azure 리소스를 다시 만들고 관리할 수 있습니다.|

> [!CAUTION]
> Azure 웹앱 또는 Azure Container Instance와 같은 Azure 리소스를 코드의 런타임 환경으로 사용하려는 경우 로컬 node.js 개발 환경이 사용하려는 Azure 리소스 런타임과 일치하는지 확인해야 합니다.

### <a name="optional-local-installations"></a>선택적 설치 항목

로컬 개발 작업에 도움이 되는 다음과 같은 일반적인 로컬 워크스테이션 설치는 선택 사항입니다.

|이름/설치 프로그램|설명|
|--|--|
| [git](https://git-scm.com/downloads) | 소스 제어를 위한 명령줄 도구입니다. 원한다면 다른 소스 제어 도구를 사용해도 됩니다. |

## <a name="one-time-configuration-of-service-principal"></a>서비스 주체의 일회성 구성

각 Azure 서비스에는 인증 메커니즘이 있습니다. 여기에는 키와 엔드포인트, 연결 문자열 또는 기타 메커니즘이 포함될 수 있습니다. 모범 사례를 준수하려면 서비스 주체를 사용하여 리소스를 만들고 리소스를 인증합니다. 서비스 주체를 통해 즉각적인 개발 요구에 대한 액세스 범위를 구체적으로 정의할 수 있습니다.

개념적으로 서비스 주체를 만들고 사용하는 단계는 다음과 같습니다.

* joe@microsoft.com과 같은 개별 사용자 계정으로 Azure에 로그인합니다.
* 특정 범위의 명명된 서비스 주체를 만듭니다. 대부분의 빠른 시작에서는 Azure 리소스를 만들도록 요청하므로 서비스 주체는 리소스를 만들 수 있어야 합니다.
* 사용자 계정으로 Azure에서 로그오프합니다.
* 서비스 주체를 사용하여 프로그래밍 방식으로 Azure에 인증합니다.
* 서비스 주체는 Azure 리소스를 만들고 서비스와 연결된 서비스를 사용합니다.

### <a name="create-service-principal"></a>서비스 주체 만들기

서비스 주체를 보다 쉽게 만들려면 다음 단계와 제공된 스크립트를 사용하여 Azure 빠른 시작에 사용할 서비스 주체를 만듭니다. 다음 단계에서는 `JOE` 이름을 예제 사용자 이름으로 사용합니다. 이것을 자신의 이름 또는 이메일 별칭으로 대체합니다.

1. VSCode를 열고 [Azure CLI 도구](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli) 확장을 설치합니다. 이 확장을 사용하면 스크립트 파일에서 Azure CLI 명령을 한 줄씩 실행할 수 있습니다. 각 명령을 실행하면 VSCode에서 인접 문서를 열어 결과를 확인합니다.

1. `create-service-principal.sh`라는 새 파일을 만들고 다음 Azure 명령을 파일에 복사합니다.

    ```azurecli
    # Replace ALL-CAPS variables with your own values

    ####################################
    # Login as you
    ####################################

    # Login - command opens browser, select your account to finish authentication, then close browser
    az login

    ####################################
    # Optional, set default subscription
    ####################################

    # If you have more than 1 subscription, use the `list` command to find the subscription, then use the `set` command to set the default by name or id
    az account list
    az account set --subscription MYCOMPANYSUBSCRIPTION

    ####################################
    # Create service principal
    ####################################

    # Create a service principal with a name that indicates its purpose and owner - the response includes the `appId` which is necessary in some of the remaining commands
    az ad sp create-for-rbac --name JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS --skip-assignment

    ####################################
    # Add role of contributor
    ####################################

    # Add contributor role to service principal so it can create Azure resources
    az role assignment create --assignee APP-ID --role CONTRIBUTOR

    ####################################
    # Optional, verify role assignment
    ####################################

    # Verify role assignment for service principal
    az role assignment list --assignee APP-ID

    ####################################
    # Logout
    ####################################

    # Logout off Azure CLI
    az logout
    ```

    이 절차의 나머지 단계에서 `#`으로 시작하지 **않는** 파일의 각 줄의 경우, VSCode 커서를 줄에 놓은 다음, **마우스 오른쪽 버튼을 클릭하여** **편집기에서 줄 실행**을 선택합니다.

    :::image type="content" source="media/development-setup/vscode-rightclick-run-line-in-editor.png" alt-text="이 절차의 나머지 단계에서 '#'으로 시작하지 않는 파일의 각 줄의 경우, VSCode 커서를 줄에 놓은 다음, 마우스 오른쪽 단추를 클릭하여 '편집기에서 줄 실행'을 선택합니다.":::

1. 다음 줄에서 마우스 오른쪽 단추를 클릭하거나 편집기의 줄 실행을 사용하여 Azure CLI를 사용하여 고유의 사용자 계정으로 Azure에 인증합니다. 이 명령은 인터넷 브라우저를 엽니다. Azure 계정을 선택합니다. 계정이 인증되고 브라우저 창을 닫으면 나머지 작업에는 계정이 필요하지 않습니다.

    ```azurecli
    az login
    ```

    응답에는 사용자가 액세스할 수 있는 모든 구독이 포함되어 있으며, 다른 VSCode 문서 창에 JSON 배열로 표시됩니다. `name` 또는 `id` 속성을 찾습니다. 나머지 명령에는 다음 값 중 하나가 필요합니다.

    ```json
    [  {
    "cloudName": "AzureCloud",
    "id": "320d9379-aaaa-bbbb-cccc-52f2b0fc40ac",
    "isDefault": false,
    "name": "contoso-development-team",
    "state": "Enabled",
    "tenantId": "72f988bf-aaaa-bbbb-cccc-2d7cd011db47",
    "user": {
      "name": "joe@contoso.com",
      "type": "user"
    }
    }]
    ```

    `isDefault: true`로 표시된 구독은 나머지 명령을 수신하는 구독입니다. 기본 구독을 변경해야 하는 경우 `az account set --subscription <name or id>` 명령을 사용합니다.


<a name='create-service-principal-command'></a>

1. 다음 줄에서 마우스 오른쪽 단추를 클릭하거나 편집기의 줄 실행을 사용하여 사용자 계정에 연결된 서비스 주체를 만듭니다. 이 서비스 주체에는 `--skip-assignment` 매개 변수 때문에 아직 범위 지정 권한이 없습니다.


    ```azurecli
    az ad sp create-for-rbac --name JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS --skip-assignment
    ```

    서비스 주체 이름은 `JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS`입니다. Azure 사용자 계정과 연결된 모든 서비스 주체 목록은 Active Directory 서비스의 애플리케이션 목록 아래에 있는 Azure Portal에서 볼 수 있습니다.

    결과에는 필요한 정보(`appId` 및 `password`)가 있습니다. 파일을 `create-service-principal.json`이라는 이름으로 저장합니다.

    ```json
    {
      "appId": "93453d56-aaaa-bbbb-cccc-db600ecc4f6a",
      "displayName": "JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS",
      "name": "http://JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS",
      "password": "d88b21e0-aaaa-bbbb-cccc-e1e9b06d50f6",
      "tenant": "72f988bf-aaaa-bbbb-cccc-2d7cd011db47"
    }
    ```

1. 다음 줄에서 마우스 오른쪽 단추를 클릭하거나 편집기의 줄 실행을 사용하여 Azure 리소스를 만드는 범위 지정 권한을 할당합니다. `CONTRIBUTOR` 범위를 통해 서비스 주체는 Azure 리소스를 만들 수 있습니다.

    ```azurecli
    az role assignment create --assignee APP-ID --role CONTRIBUTOR
    ```

    결과는 다음과 같이 표시됩니다.

    ```json
    {
      "canDelegate": null,
      "id": "/subscriptions/a5b1ca8b-aaaa-bbbb-cccc-4cf7ec4791a0/providers/Microsoft.Authorization/roleAssignments/3a155db5-aaaa-bbbb-cccc-0cbfebf75464",
      "name": "3a155db5-aaaa-bbbb-cccc-0cbfebf75464",
      "principalId": "c05d56c9-aaaa-bbbb-cccc-0535d6167ed4",
      "principalType": "ServicePrincipal",
      "roleDefinitionId": "/subscriptions/a5b1ca8b-aaaa-bbbb-cccc-4cf7ec4791a0/providers/Microsoft.Authorization/roleDefinitions/b24988ac-aaaa-bbbb-cccc-20f7382dd24c",
      "scope": "/subscriptions/a5b1ca8b-aaaa-bbbb-cccc-4cf7ec4791a0",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    ```

    이제 서비스 주체를 사용할 준비가 되었습니다.

1. 다음 줄에서 마우스 오른쪽 단추를 클릭하거나 편집기의 줄 실행을 사용하여 다음 명령을 실행하여 Azure CLI에서 로그아웃합니다.

    ```azurecli
    az logout
    ```

## <a name="steps-for-each-new-development-project-setup"></a>새 개발 프로젝트 설정을 위한 단계

Azure SDK 라이브러리는 각 서비스에 대해 개별적으로 제공되기 때문에 모든 Azure 리소스에 액세스할 수 있는 다운로드 가능한 패키지가 한 개도 없습니다. 사용할 Azure 서비스를 기반으로 각 라이브러리를 설치합니다.

Azure를 사용하는 각 새 프로젝트는 다음을 수행해야 합니다.
- Azure 리소스를 만들거나 기존 Azure 리소스에 대한 인증 정보를 찾습니다.
- NPM 또는 Yarn에서 Azure SDK 라이브러리를 설치합니다. [라이브러리 버전](#library-versions)에 대해 알아보세요.
- 프로젝트 내의 인증 정보를 안전하게 관리합니다. 일반적인 방법 중 하나는 **[Dotenv](https://www.npmjs.com/package/dotenv)** 를 사용하여 `.env` 파일에서 환경 변수를 읽는 것입니다. `.env` 파일이 소스 제어에 체크 인되지 않도록 `.gitignore` 파일에 `.env` 파일을 추가해야 합니다.

### <a name="library-versions"></a>라이브러리 버전

모든 Azure 라이브러리는 `@azure` 범위로 이동합니다.

| 라이브러리 유형 | 설명|
|--|--|
|최신|[@azure/storage-blob](https://www.npmjs.com/package/@azure/storage-blob) 및 [@azure/cosmos](https://www.npmjs.com/package/@azure/cosmos)와 같이 `@azure`로 범위가 지정되었으며 TypeScript 유형을 포함합니다.|
|이전 패키지|일반적으로 `azure-`로 시작합니다. 대부분의 패키지는 Microsoft가 생성하지 않는 이 이름으로 시작합니다. 패키지의 소유자가 Microsoft인지 Azure인지 확인합니다.|

### <a name="create-resource-using-service-principal"></a>서비스 주체를 사용하여 리소스 만들기

다음 섹션에서는 서비스 주체를 사용하여 Azure 서비스 리소스를 만드는 방법에 대한 예제를 제공합니다. 서비스 주체로 사용하여 로그인하려면 [서비스 주체 만들기](#create-service-principal) 절차에서 `create-service-principal.json`으로 저장한 `appId`, `tenant` 및 `password`가 필요합니다.

1. VSCode를 열고 이전에 설치된 [Azure CLI 도구](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli) 확장을 사용합니다. 이 확장을 사용하면 스크립트 파일에서 Azure CLI 명령을 한 줄씩 실행할 수 있습니다. 각 명령을 실행하면 VSCode에서 인접 문서를 열어 결과를 확인합니다.

1. `create-service-resource.sh`라는 새 파일을 만들고 다음 Azure 명령을 파일에 복사합니다.

    ```azurecli
    ####################################
    # Login as service principal
    ####################################
    # User name for command is the app id
    az login --service-principal --username APP_ID --password PASSWORD --tenant TENANT_ID

    ####################################
    # Create resource group
    ####################################

    # Create resource group in westus region - check your quickstart if it requires a specific region, then change this value to the appropriate region
    # Common naming convention for resource group is `USERNAME-REGION-PURPOSE`
    az group create --location WESTUS --name JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP

    ####################################
    # Create specific service resource
    ####################################

    # Create resource in westus
    # This is an example of creating a Cognitive Services LUIS resource
    # Review your quickstart to find the exact command
    az SERVICENAME account create --name JOE-WESTUS-COGNITIVESERVICES-LUIS --resource-group JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP --kind LUIS --sku F0 --location WESTUS --yes

    ####################################
    # Get resource keys
    ####################################

    # Get resource keys
    az cognitiveservices account keys list --name JOE-WESTUS-COGNITIVESERVICES-LUIS --resource-group JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP
    ```

1. 다음 줄에서 마우스 오른쪽 단추를 클릭하거나 편집기의 줄 실행을 사용하여 서비스 주체로 로그인합니다. 모든 대문자의 변수가 [이전 명령의 응답에서 반환되어 서비스 주체](#create-service-principal-command)를 만듭니다.

    ```azurecli
    az login --service-principal --username APP_ID --password PASSWORD --tenant TENANT_ID
    ```

1. 다음 줄에서 마우스 오른쪽 단추를 클릭하거나 편집기의 줄 실행을 사용하여 빠른 시작에 대해 만들어야 하는 모든 리소스에 리소스 그룹을 만듭니다. 리소스 그룹 지역은 해당 지역의 리소스만 포함할 수 있습니다.

    ```azurecli
    az group create --location WESTUS --name JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP
    ```

    빠른 시작 리소스를 사용하여 작업을 완료한 후에는 한 작업의 리소스에 대해 삭제하는 리소스 그룹을 삭제할 수 있습니다.

1. 다음 줄에서 마우스 오른쪽 단추를 클릭하거나 편집기의 줄 실행을 사용하여 Cognitive Services LUIS 리소스를 만듭니다. 이는 한 가지 예이며, 사용자 고유의 리소스에는 다른 명령이 있습니다.

    ```azurecli
    az SERVICENAME account create --name JOE-WESTUS-COGNITIVESERVICES-LUIS --resource-group JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP --kind LUIS --sku F0 --location WESTUS --yes
    ```

    LUIS 리소스는 LUIS에 빠른 시작을 사용하는 데 필요한 키 및 엔드포인트를 사용합니다.

1. 다음 줄에서 마우스 오른쪽 단추를 클릭하거나 편집기의 줄 실행을 사용하여 LUIS 키와 엔드포인트을 가져옵니다. LUIS 서비스에 대한 인증은 키와 엔드포인트를 사용합니다.

    ```azurecli
    az cognitiveservices account keys list --name JOE-WESTUS-COGNITIVESERVICES-LUIS --resource-group JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP
    ```

### <a name="create-environment-variables-for-the-azure-libraries"></a>Azure 라이브러리에 대한 환경 변수 만들기

Azure SDK 라이브러리에 필요한 Azure 설정을 사용하여 Azure 클라우드에 액세스하려면 가장 일반적인 값을 환경 변수로 설정합니다. 다음 명령은 환경 변수를 로컬 워크스테이션으로 설정합니다. 또 다른 일반적인 메커니즘은 `DOTENV` NPM 패키지를 사용하여 이러한 설정에 대한 `.env` 파일을 만드는 것입니다. `.env`를 사용하려면 파일을 소스 제어에 체크 인하지 않아야 합니다. git의 `.ignore` 파일에 `.env` 파일을 추가하는 것은 이러한 설정이 소스 제어에 체크 인되도록 하는 기본적인 방법입니다.

다음 예제에서 클라이언트 ID는 서비스 주체 ID와 서비스 주체 비밀입니다.

# <a name="bash"></a>[bash](#tab/bash)

```bash
AZURE_SUBSCRIPTION_ID="aa11bb33-cc77-dd88-ee99-0918273645aa"
AZURE_TENANT_ID="00112233-7777-8888-9999-aabbccddeeff"
AZURE_CLIENT_ID="12345678-1111-2222-3333-1234567890ab"
AZURE_CLIENT_SECRET="abcdef00-4444-5555-6666-1234567890ab"
```

# <a name="cmd"></a>[cmd](#tab/cmd)

```cmd
set AZURE_SUBSCRIPTION_ID="aa11bb33-cc77-dd88-ee99-0918273645aa"
set AZURE_TENANT_ID=00112233-7777-8888-9999-aabbccddeeff
set AZURE_CLIENT_ID=12345678-1111-2222-3333-1234567890ab
set AZURE_CLIENT_SECRET=abcdef00-4444-5555-6666-1234567890ab
```

---

이러한 명령에 표시된 값을 특정 서비스 주체의 값으로 바꿉니다.

### <a name="install-npm-packages"></a>NPM 패키지 설치

모든 프로젝트에 대해서는 다음 단계에 따라 항상 별도의 폴더와 해당 폴더 자체의 `package.json` 파일을 만드는 것이 좋습니다.

1. 터미널, 명령 프롬프트 또는 Bash 셸을 열고 프로젝트에 대한 새 폴더를 만듭니다. 그런 다음, 해당 새 폴더로 이동합니다.

    ```console
    mkdir MY-NEW-PROJECT && cd MY-NEW-PROJECT
    ```

1. 패키지 파일을 초기화합니다.

    ```console
    npm init -y
    ```

    이 명령은 NPM 명령을 실행하여 package.json 파일을 만들고 최소 속성을 초기화합니다. NPM 또는 Yarn을 사용하여 Azure SDK 라이브러리 패키지를 설치할 때 package.json 파일이 해당 설치 정보를 캡처합니다.

1. 빠른 시작에 필요한 Azure SDK 라이브러리를 설치합니다. 다음 명령은 하나의 예입니다.

    ```console
    npm install @azure/cognitiveservices-luis-runtime
    ```

## <a name="use-source-control"></a>소스 제어 사용

프로젝트를 시작할 때마다 소스 제어 리포지토리를 만드는 습관을 기르는 것이 좋습니다. Git이 설치되어 있는 경우 다음 명령을 실행하기만 하면 됩니다.

```bash
git init
```

여기서 `git add` 및 `git commit` 같은 명령을 실행하여 변경 내용을 커밋할 수 있습니다. 정기적으로 변경 내용을 커밋하면 이전 상태로 되돌릴 수 있는 커밋 기록이 생성됩니다.

온라인으로 프로젝트 백업을 만들려면 리포지토리를 [GitHub](https://github.com) 또는 [Azure DevOps](/azure/devops/user-guide/code-with-git?view=azure-devops)에 업로드하는 것이 좋습니다. 로컬 리포지토리를 처음으로 초기화한 경우에는 `git remote add` 명령을 사용하여 로컬 리포지토리를 GitHub 또는 Azure DevOps에 연결합니다.

git에 대한 설명서는 [git-scm.com/docs](https://git-scm.com/docs) 및 인터넷에서 찾을 수 있습니다.

Visual Studio Code에는 여러 가지 기본 git 기능이 포함되어 있습니다. 자세한 내용은 [VS Code에서 버전 제어 사용](https://code.visualstudio.com/docs/editor/versioncontrol)을 참조하세요.

원하는 다른 소스 제어 도구를 사용할 수도 있습니다. Git은 가장 널리 사용되고 지원되는 도구 중 하나일 뿐입니다.

## <a name="next-steps"></a>다음 단계

* [Visual Studio Code에서 Azure에 정적 웹 사이트 배포](tutorial-vscode-static-website-node-01.md)