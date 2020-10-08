---
title: Azure 개발을 위한 로컬 Python 환경 구성
description: Visual Studio Code, Azure SDK 라이브러리 및 라이브러리 인증에 필요한 자격 증명을 포함하여 Azure를 사용하기 위한 로컬 Python 개발 환경을 설정하는 방법을 설명합니다.
ms.date: 05/29/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 14955bc63ab44884254e2304b0e5ceb97e7bcb0d
ms.sourcegitcommit: 29b161c450479e5d264473482d31e8d3bf29c7c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91764815"
---
# <a name="configure-your-local-python-dev-environment-for-azure"></a>Azure를 위한 로컬 Python 개발 환경 구성

클라우드 애플리케이션을 만들 때 개발자는 일반적으로 로컬 워크스테이션에서 코드를 테스트한 후 Azure 같은 클라우드 환경에 코드를 배포하는 방법을 선호합니다. 로컬 개발은 속도가 빠르고 다양한 디버깅 도구를 사용할 수 있다는 장점이 있습니다.

이 문서에서는 Azure에서 Python을 사용하기에 적합한 로컬 개발 환경을 만들고 유효성을 검사하기 위한 일회성 설치 지침을 제공합니다.

- Azure 계정, Python, Azure CLI 등 [필요한 구성 요소를 설치](#required-components)합니다.
- Azure 라이브러리를 사용하여 Azure 리소스를 프로비저닝하고, 관리하고, 액세스하기 위한 [인증을 구성](#configure-authentication)합니다.
- 각 프로젝트의 [Python 가상 환경 사용](#use-python-virtual-environments) 프로세스를 검토합니다.

워크스테이션을 구성한 후에는 최소한의 구성만 추가하면 이 개발자 센터와 Azure 설명서의 다른 곳에서 다양한 빠른 시작과 자습서를 완료할 수 있습니다.

로컬 개발을 위한 이 설정은 Azure에서 애플리케이션의 *클라우드 환경*을 구성하는 [프로비저닝 리소스](cloud-development-flow.md)와는 별개의 문제입니다. 개발 프로세스에서는 해당 클라우드 리소스에 액세스할 수 있는 로컬 개발 환경에서 코드를 실행하지만, 코드는 아직 클라우드의 [적절한 호스팅 서비스](quickstarts-app-hosting.md)에 배포되지 않았습니다. 이 배포 단계는 [Azure 개발 흐름](cloud-development-flow.md) 문서에 설명된 대로 나중에 제공됩니다.

## <a name="install-components"></a>구성 요소 설치

### <a name="required-components"></a>필수 구성 요소

| 이름/설치 프로그램 | Description |
| --- | --- |
| [활성 구독이 있는 Azure 계정](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=python-dev-center&mktingSource=environment-setup) | 계정/구독은 무료이며 여러 무료 서비스를 포함하고 있습니다. |
| [Python 2.7+ 또는 3.5.3+](https://www.python.org/downloads) | Python 언어 런타임입니다. 특정 버전이 꼭 필요한 경우 외에는 최신 버전인 Python 3.x를 권장합니다. |
| [Azure CLI(명령줄 인터페이스)](/cli/azure/install-azure-cli) | Azure 리소스를 프로비저닝하고 관리하기 위한 전체 CLI 명령 모음을 제공합니다. Python 개발자는 일반적으로 Azure 관리 라이브러리를 사용하는 사용자 지정 Python 스크립트와 함께 Azure CLI를 사용합니다. |

참고:

- 요구 사항에 따라 프로젝트별로 개별 Azure 라이브러리 패키지를 설치합니다. 각 프로젝트에 [Python 가상 환경을 사용](#use-python-virtual-environments)하는 것이 좋습니다. Python용 독립 실행형 "SDK" 설치 프로그램이 없습니다.
- Azure PowerShell은 일반적으로 Azure CLI와 동일하지만, Python으로 작업할 때는 Azure CLI를 사용하는 것이 좋습니다.

### <a name="recommended-components"></a>권장 구성 요소

| 이름/설치 프로그램 | Description |
| --- | --- |
| [Visual Studio Code](https://code.visualstudio.com) | 적당한 아무 편집기 또는 IDE를 사용해도 되지만, Microsoft에서 제공하는 경량의 무료 IDE는 Python 개발자들 사이에서 매우 인기가 많습니다. 소개는 [VS Code의 Python](https://code.visualstudio.com/docs/python/python-tutorial)을 참조하세요. |
| [VS Code용 Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python) | VS Code에 Python 지원을 추가합니다. |
| [VS Code용 Azure 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) | VS Code에 다양한 Azure 서비스 지원을 추가합니다. 특정 서비스를 지원하는 요소를 개별적으로 설치할 수도 있습니다. |
| [git](https://git-scm.com/downloads) | 소스 제어를 위한 명령줄 도구입니다. 원한다면 다른 소스 제어 도구를 사용해도 됩니다. |

### <a name="optional-components"></a>선택적 구성 요소

| 이름/설치 프로그램 | Description |
| --- | --- |
| [VS Code용 Docker 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python) | VS Code에 Docker 지원을 추가합니다. 컨테이너를 정기적으로 사용하는 경우에 유용합니다. |

### <a name="verify-components"></a>구성 요소 확인

1. 터미널 또는 명령 프롬프트를 엽니다.
1. `python --version` 명령을 실행하여 Python 버전을 확인합니다.
1. `az --version` 명령을 실행하여 Azure CLI 버전을 확인합니다.
1. 다음과 같이 VS Code 설치를 확인합니다.
    1. `code .` 명령을 실행하여 현재 폴더에서 VS Code를 엽니다.
    1. VS Code에서 **보기** > **확장** 명령을 선택하여 확장 보기를 연 다음, 목록에 "Python" 및 "Azure 계정"이 표시되는지 확인합니다(다른 "Azure" 확장 및 "Docker"를 설치했으면 해당 확장도 표시됨).

## <a name="sign-in-to-azure-from-the-cli"></a>CLI에서 Azure에 로그인

다음과 같이 터미널 또는 명령 프롬프트에서 Azure 구독에 로그인합니다.

```azurecli
az login
```

`az` 명령은 Azure CLI의 루트 명령입니다. `az` 뒤에는 하나 이상의 명령(예: `login`)이 붙습니다. [az login](/cli/azure/authenticate-azure-cli) 명령 참조를 확인하세요.

Azure CLI는 일반적으로 세션 간에 로그인을 유지하지만, 새 터미널 또는 명령 프롬프트를 열 때마다 `az login`을 실행하는 것이 좋습니다.

## <a name="configure-authentication"></a>인증 구성

[앱 인증 방법](azure-sdk-authenticate.md#identity-when-running-the-app-locally)에서 설명했듯이, 각 개발자는 로컬에서 앱 코드를 테스트할 때 애플리케이션 ID로 사용할 서비스 주체가 필요합니다.

다음 섹션에서는 서비스 주체를 만드는 방법 및 서비스 주체의 속성을 필요한 때에 Azure 라이브러리에 제공하는 환경 변수를 만드는 방법에 대해 설명합니다.

조직의 각 개발자는 이러한 단계를 개별적으로 수행해야 합니다.

### <a name="create-a-service-principal-and-environment-variables-for-development"></a>개발을 위한 서비스 주체 및 환경 변수 만들기

1. Azure CLI에 로그인한(`az login`) 터미널 또는 명령 프롬프트를 엽니다.

1. 다음과 같이 서비스 주체를 만듭니다.

    ```azurecli
    az ad sp create-for-rbac --name localtest-sp-rbac --skip-assignment --sdk-auth > local-sp.json
    ```

    이 명령은 *local-sp.json*에 출력을 저장합니다. 명령 및 인수에 대한 자세한 내용은 [create-for-rbac 명령의 기능](#what-the-create-for-rbac-command-does)을 참조하세요.

    조직에 속한 개발자는 구독에서 이 명령을 실행할 수 있는 권한이 없을 수도 있습니다. 이 경우 구독 소유자에게 연락하여 서비스 주체를 만들어 달라고 요청합니다.

1. 다음 명령을 사용하여 Azure 라이브러리에 필요한 환경 변수를 만듭니다. (azure-identity 라이브러리의 `DefaultAzureCredential` 개체가 이러한 변수를 찾습니다.)

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set AZURE_SUBSCRIPTION_ID="aa11bb33-cc77-dd88-ee99-0918273645aa"
    set AZURE_TENANT_ID=00112233-7777-8888-9999-aabbccddeeff
    set AZURE_CLIENT_ID=12345678-1111-2222-3333-1234567890ab
    set AZURE_CLIENT_SECRET=abcdef00-4444-5555-6666-1234567890ab
    ```

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    AZURE_SUBSCRIPTION_ID="aa11bb33-cc77-dd88-ee99-0918273645aa"
    AZURE_TENANT_ID="00112233-7777-8888-9999-aabbccddeeff"
    AZURE_CLIENT_ID="12345678-1111-2222-3333-1234567890ab"
    AZURE_CLIENT_SECRET="abcdef00-4444-5555-6666-1234567890ab"
    ```

    ---

    이러한 명령에 표시된 값을 특정 서비스 주체의 값으로 바꿉니다.

    구독 ID를 검색하려면 [`az account show`](/cli/azure/account#az-account-show) 명령을 실행하고 출력에서 `id` 속성을 찾습니다.

    편의를 위해 동일한 명령이 포함된 명령줄 스크립트 파일(예: macOS/Linux의 경우 *setenv.sh* 또는 Windows의 경우 *setenv.cmd*)을 만듭니다. 그런 다음, 로컬 테스트를 위해 터미널 또는 명령 프롬프트를 열 때마다 스크립트를 실행하여 변수를 설정할 수 있습니다. 다시 강조하지만, 파일이 사용자 계정에만 남아 있도록 스크립트 파일을 소스 제어에 추가하지 마세요.

1. 항상 워크스테이션의 특정 사용자 계정 내에 남아 있도록 클라이언트 ID와 클라이언트 암호(및 클라이언트 암호를 저장하는 모든 파일)를 보호합니다. 이러한 속성을 소스 제어에 저장하거나 다른 개발자와 공유하지 마세요. 필요한 경우 서비스 주체를 삭제하고 새로 만들 수 있습니다.

    추가 보안 계층의 경우 주기적으로 서비스 주체를 삭제하고 다시 만들어서 이전 ID와 비밀을 무효화하는 정책을 만들 수 있습니다.

    뿐만 아니라 개발 서비스 주체는 비프로덕션 리소스에 대해서만 권한이 부여되거나 개발 목적으로만 사용되는 Azure 구독 내에 생성됩니다. 그러면 프로덕션 애플리케이션에서는 배포된 클라우드 애플리케이션에만 권한이 부여되는 별도의 구독과 별도의 프로덕션 리소스를 사용합니다.

1. 나중에 서비스 주체를 수정하거나 삭제하려면 [서비스 주체를 관리하는 방법](how-to-manage-service-principals.md)을 참조하세요.

#### <a name="what-the-create-for-rbac-command-does"></a>create-for-rbac 명령의 기능

`az ad create-for-rbac` 명령은 "RBAC(역할 기반 인증)"에 대한 서비스 주체를 만듭니다.

- `ad`는 Azure Active Directory를 의미하고, `sp`는 "서비스 주체"를 의미하고, `create-for-rbac`는 Azure의 기본 인증 형태인 "역할 기반 액세스 제어 만들기"를 의미합니다. [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) 명령 참조를 확인하세요.

- `--name` 인수는 조직 내에서 고유해야 하며 일반적으로 서비스 주체를 사용하는 개발자의 이름을 사용합니다. 이 인수를 생략하면 Azure CLI는 `azure-cli-<timestamp>` 형식의 일반 이름을 사용합니다. 원하는 경우 Azure Portal에서 서비스 사용자의 이름을 바꿀 수 있습니다.

- `--skip-assignment` 인수는 기본 권한 없는 서비스 주체를 만듭니다. 그 후에는 로컬에서 실행되는 코드가 리소스에 액세스할 수 있도록 서비스 주체에게 특정 권한을 할당해야 합니다. 서비스 주체에게 관련 리소스에 대한 권한을 부여하는 방법을 자세히 설명하는 여러 빠른 시작과 자습서가 있습니다.

- 이 명령은 *local-sp.json*이라는 파일에 저장되는 JSON 출력을 예로서 제공합니다.

- `--sdk-auth` 인수는 다음 값과 유사한 JSON 출력을 생성합니다. ID 값과 비밀은 완전히 다릅니다.

    <pre>
    {
      "clientId": "12345678-1111-2222-3333-1234567890ab",
      "clientSecret": "abcdef00-4444-5555-6666-1234567890ab",
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "tenantId": "00112233-7777-8888-9999-aabbccddeeff",
      "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
      "resourceManagerEndpointUrl": "https://management.azure.com/",
      "activeDirectoryGraphResourceId": "https://graph.windows.net/",
      "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
      "galleryEndpointUrl": "https://gallery.azure.com/",
      "managementEndpointUrl": "https://management.core.windows.net/"
    }
    </pre>

    `--sdk-auth` 인수가 없으면 이 명령은 다음과 같이 보다 간단한 출력을 생성합니다.

    <pre>
    {
      "appId": "12345678-1111-2222-3333-1234567890ab",
      "displayName": "localtest-sp-rbac",
      "name": "http://localtest-sp-rbac",
      "password": "abcdef00-4444-5555-6666-1234567890ab",
      "tenant": "00112233-7777-8888-9999-aabbccddeeff"
    }
    </pre>

    이 경우 `tenant`는 테넌트 ID이고, `appId`는 클라이언트 ID이고, `password`는 클라이언트 암호입니다.

    > [!IMPORTANT]
    > 이 명령의 출력은 클라이언트 암호/암호가 표시되는 유일한 장소입니다. 이후에는 비밀/암호를 검색할 수 없습니다. 그러나 필요하다면 서비스 주체 또는 기존 암호를 무효화하지 않고 새 비밀을 추가할 수 있습니다.

## <a name="use-python-virtual-environments"></a>Python 가상 환경 사용

모든 프로젝트에서 다음 단계에 따라 항상 *가상 환경*을 만들고 활성화하는 것이 좋습니다.

1. 터미널 또는 명령 프롬프트를 엽니다.

1. 프로젝트 폴더를 만듭니다.

1. 다음과 같이 가상 환경을 만듭니다.

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```bash
    python -m venv .venv
    ```

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    python -m venv .venv
    ```

    ---

    이 명령은 Python `venv` 모듈을 실행하고 `.venv`라는 폴더에 가상 환경을 만듭니다.

1. 다음과 같이 가상 환경을 활성화합니다.

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```bash
    .venv\scripts\activate
    ```

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    source .venv/scripts/activate
    ```

    ---

가상 환경은 특정 Python 인터프리터의 복사본을 격리하는 프로젝트 내의 폴더입니다. 환경을 활성화한 후(Visual Studio Code에서 자동으로 활성화) `pip install` 명령을 실행하면 해당 환경에만 라이브러리가 설치됩니다. 그런 다음, Python 코드를 실행하면 모든 라이브러리의 특정 버전을 사용하여 환경의 정확한 컨텍스트에서 코드가 실행됩니다. 그리고 `pip freeze` 명령을 실행하면 해당 라이브러리의 정확한 목록이 표시됩니다. (이 설명서의 여러 예제에서는 필요한 라이브러리에 대한 *requirements.txt* 파일을 만든 다음, `pip install -r requirements.txt` 명령을 사용합니다. 요구 사항 파일은 일반적으로 Azure에 코드를 배포할 때 필요합니다.

가상 환경을 사용하지 않는 경우 Python은*글로벌 환경*에서 실행됩니다. 글로벌 환경을 사용하면 빠르고 편리하지만, 프로젝트 또는 실험을 위해 라이브러리를 계속 설치해야 하므로 시간이 지나면서 점점 커지는 경향이 있습니다. 뿐만 아니라 한 프로젝트의 라이브러리를 업데이트하면 해당 라이브러리의 다른 버전에 종속된 다른 프로젝트가 손상될 수 있습니다. 그리고 여러 프로젝트에서 환경을 공유하기 때문에 `pip freeze` 명령을 사용하여 한 프로젝트 종속성 목록을 검색할 수 없습니다.

글로벌 환경에서는 여러 프로젝트에 사용할 도구 패키지를 개발자가 설치합니다. 예를 들어 글로벌 환경에서 `pip install gunicorn` 명령을 실행하여 어디서나 gunicorn 웹 서버를 사용 가능하게 만들 수 있습니다.

## <a name="use-source-control"></a>소스 제어 사용

프로젝트를 시작할 때마다 소스 제어 리포지토리를 만드는 습관을 기르는 것이 좋습니다. Git이 설치되어 있는 경우 간단하게 다음 명령을 실행하기만 하면 됩니다.

```cmd
git init
```

여기서 `git add` 및 `git commit` 같은 명령을 실행하여 변경 내용을 커밋할 수 있습니다. 정기적으로 변경 내용을 커밋하면 이전 상태로 되돌릴 수 있는 커밋 기록이 생성됩니다.

온라인으로 프로젝트 백업을 만들려면 리포지토리를 [GitHub](https://github.com) 또는 [Azure DevOps](/azure/devops/user-guide/code-with-git?view=azure-devops&preserve-view=true)에 업로드하는 것이 좋습니다. 로컬 리포지토리를 처음으로 초기화한 경우에는 `git remote add` 명령을 사용하여 로컬 리포지토리를 GitHub 또는 Azure DevOps에 연결합니다.

git에 대한 설명서는 [git-scm.com/docs](https://git-scm.com/docs) 및 인터넷에서 찾을 수 있습니다.

Visual Studio Code에는 여러 가지 기본 git 기능이 포함되어 있습니다. 자세한 내용은 [VS Code에서 버전 제어 사용](https://code.visualstudio.com/docs/editor/versioncontrol)을 참조하세요.

원하는 다른 소스 제어 도구를 사용할 수도 있습니다. Git은 가장 널리 사용되고 지원되는 도구 중 하나일 뿐입니다.

## <a name="next-step"></a>다음 단계

로컬 개발 환경이 준비되었으므로, Azure 라이브러리의 일반적인 사용 패턴을 빠르게 살펴보겠습니다.

> [!div class="nextstepaction"]
> [일반적인 사용 패턴 검토 >>>](azure-sdk-library-usage-patterns.md)
