---
title: Azure 개발을 위한 로컬 JavaScript 환경 구성
description: 편집기, Azure SDK 라이브러리, 선택적 도구 및 라이브러리 인증에 필요한 자격 증명을 포함하여 Azure를 사용하기 위한 로컬 JavaScript 개발 환경을 설정하는 방법을 설명합니다.
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: devx-track-js, azure-sdk-ai-text-analytics-5.0.0
ms.openlocfilehash: baf9634395d4e0ad7225abb9bebddfa1aa14fe6d
ms.sourcegitcommit: 8fcb6c2d17be63064090f801f46c9c754821f979
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805944"
---
# <a name="configure-your-local-javascript-dev-environment-for-azure"></a>Azure를 위한 로컬 JavaScript 개발 환경 구성

클라우드 애플리케이션을 만들 때 개발자는 일반적으로 로컬 워크스테이션에서 코드를 테스트한 후 Azure 같은 클라우드 환경에 코드를 배포하는 방법을 선호합니다. 로컬 개발을 통해 친숙한 개발 환경과 함께 다양한 도구를 활용할 수 있습니다.

이 문서에서는 Azure를 사용한 JavaScript에 적합한 로컬 개발 환경을 만들고 유효성을 검사하기 위한 설치 지침을 제공합니다.

## <a name="one-time-subscription-creation"></a>일회성 구독 만들기

Azure 리소스는 Azure 사용의 청구 단위인 구독 내에 생성됩니다. 무료 리소스(각 구독은 대부분의 서비스에 무료 리소스를 제공함)를 만들 수 있지만, 리소스를 프로덕션에 배포할 때 유료 계층 리소스를 만들어야 합니다.

|유형|설명|
|--|--|
|[평가판 구독](https://azure.microsoft.com/free/cognitive-services)|_평가판_ 구독을 만듭니다.|
|[기존 구독](https://portal.azure.com)|구독이 이미 있는 경우 Azure Portal, Azure CLI 또는 JavaScript에서 기존 구독에 액세스합니다.|
|[여러 구독](/azure/governance/management-groups/create-management-group-javascript)|여러 구독을 관리해야 하는 경우 JavaScript를 사용하여 관리 그룹을 만드는 방법을 알아봅니다.|

## <a name="one-time-installation"></a>일회성 설치

로컬 워크스테이션에서 JavaScript를 사용하여 Azure 리소스를 사용하여 개발하려면 다음이 설치되어 있어야 합니다.

|이름/설치 프로그램|설명|
|--|--|
|[Node.js](https://www.npmjs.com/)|로컬 워크스테이션 개발을 위한 최신 LTS(장기 지원) 런타임 환경을 설치합니다. |
| NPM(최신 버전의 node.js와 함께 설치됨) 또는 [Yarn](https://yarnpkg.com/)|Azure SDK 라이브러리를 설치하기 위한 패키지 관리자.|
|[Visual Studio Code](https://code.visualstudio.com/)| Visual Studio Code는 뛰어난 JavaScript 통합 및 코딩 환경을 제공하지만 반드시 필요한 것은 아닙니다. 모든 코드 편집기를 사용할 수 있습니다. 이 문서에서는 다른 편집기를 사용하는 경우 Azure와의 통합을 확인하거나 Azure CLI를 사용합니다.|
|[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)|Azure CLI를 사용하여 명령줄, 터미널 또는 Bash 셸에서 Azure 리소스를 다시 만들고 관리할 수 있습니다.|

> [!CAUTION]
> Azure 웹앱 또는 Azure Container Instance와 같은 Azure 리소스를 코드의 런타임 환경으로 사용하려는 경우 로컬 node.js 개발 환경이 사용하려는 Azure 리소스 런타임과 일치하는지 확인해야 합니다.

### <a name="optional-local-installations"></a>선택적 설치 항목

로컬 개발 작업에 도움이 되는 다음과 같은 일반적인 로컬 워크스테이션 설치는 선택 사항입니다.

|이름/설치 프로그램|설명|
|--|--|
| [git](https://git-scm.com/downloads) | 소스 제어를 위한 명령줄 도구입니다. 원한다면 다른 소스 제어 도구를 사용해도 됩니다. |

## <a name="one-time-configuration-of-service-principal"></a>서비스 주체의 일회성 구성

각 Azure 서비스에는 인증 메커니즘이 있습니다. 여기에는 키와 엔드포인트, 연결 문자열 또는 기타 메커니즘이 포함될 수 있습니다. 모범 사례를 준수하려면 [서비스 주체](node-sdk-azure-authenticate-principal.md)를 사용하여 리소스를 만들고 리소스를 인증합니다. 서비스 주체를 통해 즉각적인 개발 요구에 대한 액세스 범위를 구체적으로 정의할 수 있습니다.

**서비스 주체 생성** 단계: 

1. 개별 사용자 계정으로 Azure에 로그인합니다.
1. 특정 범위의 _명명된_ 서비스 주체를 만듭니다. 대부분의 빠른 시작에서는 Azure 리소스를 만들도록 요청하므로 서비스 주체는 리소스를 만들 수 있어야 합니다.
1. 개별 사용자 계정으로 Azure에서 로그오프합니다.

**서비스 주체 사용** 단계:

1. 인증서, 환경 변수 또는 `.json` 파일을 사용하여 서비스 주체를 통해 프로그래밍 방식으로 Azure에 인증합니다. 
1. 서비스 주체를 사용하는 Azure 리소스를 만들고 서비스를 사용합니다.

[서비스 주체를 만드는 방법](node-sdk-azure-authenticate-principal.md)을 알아봅니다. 생성 단계의 응답을 저장해야 합니다. 서비스 주체를 사용하려면 응답의 `appId`, `tenant` 및 `password` 값이 필요합니다.

[서비스 주체를 사용하는 Azure 리소스를 만듭니다](/cli/azure/create-an-azure-service-principal-azure-cli#create-a-resource-using-service-principal).

## <a name="steps-for-each-new-development-project-setup"></a>새 개발 프로젝트 설정을 위한 단계

[Azure SDK 라이브러리](azure-sdk-library-package-index.md)는 각 서비스에 대해 개별적으로 제공됩니다. 사용해야 하는 Azure 서비스를 기반으로 각 라이브러리를 설치합니다.

Azure를 사용하는 각 새 프로젝트는 다음을 수행해야 합니다.
- Azure 리소스를 만들고 관련 키 또는 구성을 [안전한 위치]()에 저장합니다.
- NPM 또는 Yarn에서 Azure SDK 라이브러리를 설치합니다. 
- 서비스 주체를 사용하여 Azure SDK에 인증한 다음, 구성 정보를 사용하여 특정 서비스에 액세스합니다.

## <a name="securing-configuration-information"></a>구성 정보 보호

구성 정보를 저장하는 몇 가지 옵션이 있습니다.
- [Dotenv](https://www.npmjs.com/package/dotenv)는 `.env` 파일에서 환경 변수를 읽을 때 많이 사용되는 npm 패키지입니다. `.env` 파일이 소스 제어에 체크 인되지 않도록 `.gitignore` 파일에 `.env` 파일을 추가해야 합니다.
- 클라우드 리소스, 앱 및 솔루션에 액세스하고 암호화하는 키를 만들고 유지 관리하는 Azure [Key Vault](https://docs.microsoft.com/azure/key-vault/)

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

## <a name="install-npm-packages"></a>npm 패키지 설치

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
    npm install @azure/ai-text-analytics@5.0.0
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

* [서비스 주체 생성 및 사용](node-sdk-azure-authenticate-principal.md)
* [Node.js용 Azure 모듈을 사용하여 인증](node-sdk-azure-authenticate.md)
* [Visual Studio Code에서 Azure에 정적 웹 사이트 배포](tutorial-vscode-static-website-node-01.md)