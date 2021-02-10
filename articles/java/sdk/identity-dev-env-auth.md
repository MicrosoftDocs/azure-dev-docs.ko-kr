---
title: Java 개발 환경의 Azure 인증
description: 개발 환경 내 인증과 관련된 Java용 Azure SDK 개념 개요
author: g2vinay
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: vigera
ms.openlocfilehash: 7913430a81a0fe223d6eb23a48541e0f752323b2
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528679"
---
# <a name="azure-authentication-in-java-development-environments"></a>Java 개발 환경의 Azure 인증

이 문서에서는 Azure Active Directory 토큰 인증에 대한 Azure ID 라이브러리 지원에 대한 개요를 제공합니다. 이 지원은 `TokenCredential` 구현 세트를 통해 개발자 머신에서 로컬로 실행되는 애플리케이션에 대한 인증을 수행합니다.

이 문서에서 다루는 항목은 다음과 같습니다.

* [디바이스 코드 자격 증명](#device-code-credential)
* [대화형 브라우저 자격 증명](#interactive-browser-credential)
* [Azure CLI 자격 증명](#azure-cli-credential)
* [IntelliJ 자격 증명](#intellij-credential)
* [Visual Studio Code 자격 증명](#visual-studio-code-credential)

## <a name="device-code-credential"></a>디바이스 코드 자격 증명

디바이스 코드 자격 증명은 UI가 제한된 디바이스에서 사용자를 대화형으로 인증합니다. 애플리케이션에서 인증을 시도할 때 브라우저 사용 머신의 로그인 URL을 방문하도록 사용자에게 메시지를 표시하는 방식으로 작동합니다. 그런 다음, 사용자는 지침에 설명된 디바이스 코드를 로그인 자격 증명과 함께 입력합니다. 인증에 성공하면 인증을 요청한 애플리케이션이 실행 중인 디바이스에서 성공적으로 인증됩니다.

자세한 내용은 [Microsoft ID 플랫폼 및 OAuth 2.0 디바이스 권한 부여 흐름](/azure/active-directory/develop/v2-oauth2-device-code)을 참조하세요.

### <a name="enable-applications-for-device-code-flow"></a>디바이스 코드 흐름에 대한 애플리케이션 사용

디바이스 코드 흐름을 통해 사용자를 인증하려면 다음 단계를 수행합니다.

1. Azure Portal의 Azure Active Directory로 이동하여 앱 등록을 찾습니다.
2. **인증** 섹션으로 이동합니다.
3. **제안된 리디렉션 URI** 에서 `/common/oauth2/nativeclient`로 끝나는 URI를 확인합니다.
4. **기본 클라이언트 유형** 에서 **애플리케이션을 퍼블릭 클라이언트로 처리** 에 대해 *예* 를 선택합니다.

이러한 단계를 수행하면 애플리케이션이 인증되지만, 사용자를 대신하여 Active Directory에 로그인하거나 리소스에 액세스할 수 있는 권한이 아직 없습니다. 이 문제를 해결하려면 **API 권한** 으로 이동하고 Microsoft Graph 및 액세스하려는 리소스를 사용하도록 설정합니다.

또한 처음 로그인할 때 애플리케이션 사용에 동의하려면 테넌트의 관리자여야 합니다.

Active Directory에서 디바이스 코드 흐름 옵션을 구성할 수 없는 경우 앱이 다중 테넌트여야 할 수 있습니다. 앱을 다중 테넌트로 만들려면 **인증** 패널로 이동한 다음, **조직 디렉터리의 계정** 을 선택합니다. 그런 다음, **애플리케이션을 퍼블릭 클라이언트로 처리** 에 대해 *예* 를 선택합니다.

### <a name="authenticate-a-user-account-with-device-code-flow"></a>디바이스 코드 흐름으로 사용자 계정 인증

다음 예제에서는 IoT 디바이스에서 `DeviceCodeCredential`을 사용하여 [azure-security-keyvault-secrets][secrets_client_library] 클라이언트 라이브러리의 `SecretClient`를 인증하는 방법을 보여줍니다.

```java
DeviceCodeCredential deviceCodeCredential = new DeviceCodeCredentialBuilder()
  .challengeConsumer(challenge -> {
    // lets user know of the challenge
    System.out.println(challenge.getMessage());
  }).build();

// Azure SDK client builders accept the credential as a parameter
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(deviceCodeCredential)
  .buildClient();
```

## <a name="interactive-browser-credential"></a>대화형 브라우저 자격 증명

이 자격 증명은 기본 시스템 브라우저를 사용하여 사용자를 대화형으로 인증하고 사용자 자신의 자격 증명을 사용하여 애플리케이션을 인증할 수 있도록 하여 원활한 인증 환경을 제공합니다.

### <a name="enable-applications-for-interactive-browser-oauth-2-flow"></a>대화형 브라우저 OAuth 2 흐름에 대한 애플리케이션 사용

`InteractiveBrowserCredential`을 사용하려면 사용자를 대신하여 로그인할 수 있는 권한이 있는 Azure Active Directory에 애플리케이션을 등록해야 합니다. 디바이스 코드 흐름에 대한 위의 단계에 따라 애플리케이션을 등록합니다. 앞에서 설명한 것처럼 테넌트의 관리자는 사용자 계정이 로그인하기 전에 애플리케이션 사용에 동의해야 합니다.

`InteractiveBrowserCredentialBuilder`에서 리디렉션 URL이 필요하다는 것을 알 수 있습니다. 등록된 AAD 애플리케이션의 **인증** 섹션 아래에 있는 **리디렉션 URI** 하위 섹션에 리디렉션 URL을 추가합니다.

### <a name="authenticate-a-user-account-interactively-in-the-browser"></a>브라우저에서 대화형으로 사용자 계정 인증

다음 예제에서는 `InteractiveBrowserCredential`을 사용하여 [azure-security-keyvault-secrets][secrets_client_library] 클라이언트 라이브러리의 `SecretClient`를 인증하는 방법을 보여줍니다.

```java
InteractiveBrowserCredential interactiveBrowserCredential = new InteractiveBrowserCredentialBuilder()
  .clientId("<your client ID>")
  .redirectUrl("http://localhost:8765")
  .build();

// Azure SDK client builders accept the credential as a parameter
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(interactiveBrowserCredential)
  .buildClient();
```

## <a name="azure-cli-credential"></a>Azure CLI 자격 증명

Visual Studio Code 자격 증명은 Azure CLI에서 사용된 사용자 또는 서비스 주체를 통해 개발 환경에서 인증합니다. 이미 로그인한 사용자가 지정된 Azure CLI를 사용하고 CLI를 사용하여 Azure Active Directory에 대해 애플리케이션을 인증합니다.

### <a name="sign-in-azure-cli-for-azureclicredential"></a>AzureCliCredential용 Azure CLI 로그인

다음 [Azure CLI][azure_cli] 명령을 사용하여 사용자로 로그인합니다.

```bash
az login
```

다음 명령을 사용하여 서비스 주체로 로그인합니다.

```bash
az login --service-principal --username <client ID> --password <client secret> --tenant <tenant ID>
```

계정 또는 서비스 주체가 여러 테넌트에 액세스할 수 있는 경우, 원하는 테넌트 또는 구독이 다음 명령의 출력에서 "사용" 상태인지 확인합니다.

```bash
az account list
```

코드에서 `AzureCliCredential`을 사용하기 전에 다음 명령을 실행하여 계정이 성공적으로 구성되었는지 확인합니다.

```bash
az account get-access-token
```

조직의 새로 고침 토큰 유효성에 따라 일정 기간 후에 이 프로세스를 반복해야 할 수 있습니다. 일반적으로 새로 고침 토큰 유효 기간은 몇 주에서 몇 개월입니다. `AzureCliCredential`은 다시 로그인하라는 메시지를 표시합니다.

### <a name="authenticate-a-user-account-with-azure-cli"></a>Azure CLI로 사용자 계정 인증

다음 예제에서는 Azure CLI가 설치되고 로그인한 워크스테이션에서 `AzureCliCredential`을 사용하여 [azure-security-keyvault-secrets][secrets_client_library] 클라이언트 라이브러리의 `SecretClient`를 인증하는 방법을 보여줍니다.

```java
AzureCliCredential cliCredential = new AzureCliCredentialBuilder().build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(cliCredential)
  .buildClient();
```

## <a name="intellij-credential"></a>IntelliJ 자격 증명

Visual Studio Code 자격 증명은 Azure Toolkit for IntelliJ의 계정을 통해 개발 환경에서 인증합니다. IntelliJ IDE에서 로그인한 사용자 정보를 사용하고 Azure Active Directory에 대해 애플리케이션을 인증하는 데 사용합니다.

## <a name="sign-in-azure-toolkit-for-intellij-for-intellijcredential"></a>IntelliJCredential용 Azure Toolkit for IntelliJ 로그인

아래에 설명된 단계를 수행합니다.

1. IntelliJ 창에서 **파일 > 설정 > 플러그 인** 를 엽니다.
1. 마켓플레이스에서 “Azure Toolkit for IntelliJ”를 검색합니다. IDE를 설치하고 다시 시작합니다.
1. 새 메뉴 항목 **도구 > Azure > Azure 로그인...** 을 찾습니다.
1. **디바이스 로그인** 은 사용자 계정으로 로그인하는 데 도움이 됩니다. 지침에 따라 디바이스 코드를 사용하여 `login.microsoftonline.com` 웹 사이트에 로그인합니다. IntelliJ에서 구독을 선택하라는 메시지를 표시합니다. 액세스하려는 리소스가 있는 구독을 선택합니다.

Windows에서 IntelliJ 자격 증명을 읽으려면 KeePass 데이터베이스 경로도 필요합니다. **파일> 설정> 모양 및 동작> 시스템 설정> 암호** 의 IntelliJ 설정에서 경로를 찾을 수 있습니다. KeePassDatabase 경로의 위치를 적어둡니다.

## <a name="authenticate-a-user-account-with-intellij-idea"></a>IntelliJ IDEA로 사용자 계정 인증

다음 예제에서는 IntelliJ IDEA가 설치되었고 사용자가 Azure 계정으로 로그인한 워크스테이션에서 `IntelliJCredential`을 사용하여 [azure-security-keyvault-secrets][secrets_client_library] 클라이언트 라이브러리의 `SecretClient`를 인증하는 방법을 보여줍니다.

```java
IntelliJCredential intelliJCredential = new IntelliJCredentialBuilder()
  // KeePass configuration isrequired only for Windows. No configuration needed for Linux / Mac.
  .keePassDatabasePath("C:\\Users\\user\\AppData\\Roaming\\JetBrains\\IdeaIC2020.1\\c.kdbx")
  .build();

// Azure SDK client builders accept the credential as a parameter
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(intelliJCredential)
  .buildClient();
```

## <a name="visual-studio-code-credential"></a>Visual Studio Code 자격 증명

Visual Studio Code 자격 증명은 [VS Code Azure Account 확장](https://github.com/Microsoft/vscode-azure-account)을 사용하여 VS Code가 설치된 개발 환경에서 인증을 수행합니다. VS Code IDE에서 로그인한 사용자 정보를 사용하고 Azure Active Directory에 대해 애플리케이션을 인증하는 데 사용합니다.

### <a name="sign-in-visual-studio-code-azure-account-extension-for-visualstudiocodecredential"></a>VisualStudioCodeCredential용 Visual Studio Code Azure Account 확장 로그인

Visual Studio Code 인증은 [Azure Account 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)과의 통합으로 처리됩니다. 이 인증 양식을 사용하려면 Azure Account 확장을 설치한 다음, **보기 > 명령 팔레트** 를 사용하여 **Azure: 로그인** 명령을 실행합니다. 이 명령은 브라우저 창을 열고 Azure에 로그인할 수 있는 페이지를 표시합니다. 로그인 프로세스를 완료한 후에는 지시에 따라 브라우저를 닫을 수 있습니다. 애플리케이션을 실행하면(디버거 또는 개발 머신의 모든 위치에서) 로그인의 자격 증명이 사용됩니다

### <a name="authenticate-a-user-account-with-visual-studio-code"></a>Visual Studio Code로 사용자 계정 인증

다음 예제에서는 Visual Studio Code가 설치되었고 사용자가 Azure 계정으로 로그인한 워크스테이션에서 `VisualStudioCodeCredential`을 사용하여 [azure-security-keyvault-secrets][secrets_client_library] 클라이언트 라이브러리의 `SecretClient`를 인증하는 방법을 보여줍니다.

```java
VisualStudioCodeCredential visualStudioCodeCredential = new VisualStudioCodeCredentialBuilder().build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(visualStudioCodeCredential)
  .buildClient();
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 컴퓨터에서 사용할 수 있는 자격 증명을 사용하여 개발 중 인증에 대해 설명했습니다. 이 인증 형식은 Java용 Azure SDK에서 인증할 수 있는 여러 방법 중 하나입니다. 다음 문서에서는 아래와 같은 다른 방법에 대해 설명합니다.

* [Azure에서 호스트되는 애플리케이션 인증](identity-azure-hosted-auth.md)
* [서비스 주체로 인증](identity-service-principal-auth.md)
* [사용자 자격 증명으로 인증](identity-user-auth.md)

인증을 익힌 후 SDK에서 제공하는 로깅 기능에 대한 정보는 [Java용 Azure SDK에서 로깅 구성](logging-overview.md)을 참조하세요.

<!-- LINKS -->
[azure_cli]: /cli/azure
[secrets_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets
