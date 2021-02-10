---
title: 사용자 자격 증명을 사용한 Azure 인증
description: 서비스 자격 증명으로 애플리케이션을 인증하는 것과 관련된 Java용 Azure SDK 개념 개요
author: g2vinay
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: vigera
ms.openlocfilehash: d2b5b24b0f39d56ca2235ec56a4ea56c4be6b185
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528673"
---
# <a name="azure-authentication-with-user-credentials"></a>사용자 자격 증명을 사용한 Azure 인증

이 문서에서는 Azure ID 라이브러리가 사용자가 제공한 자격 증명을 통해 Azure Active Directory 토큰 인증을 지원하는 방법을 보여줍니다. 이러한 지원은 아래에서 설명하는 TokenCredential 구현 세트를 통해 수행할 수 있습니다.

이 문서는 다음 항목을 설명합니다.

* [디바이스 코드 자격 증명](#device-code-credential)
* [대화형 브라우저 자격 증명](#interactive-browser-credential)
* [사용자 이름 암호 자격 증명](#username-password-credential)

## <a name="device-code-credential"></a>디바이스 코드 자격 증명

디바이스 코드 자격 증명은 UI가 제한된 디바이스에서 사용자를 대화형으로 인증합니다. 애플리케이션에서 인증을 시도할 때 브라우저 사용 머신의 로그인 URL을 방문하도록 사용자에게 메시지를 표시하는 방식으로 작동합니다. 그런 다음, 사용자는 지침에 설명된 디바이스 코드를 로그인 자격 증명과 함께 입력합니다. 인증에 성공하면 인증을 요청한 애플리케이션이 실행 중인 디바이스에서 성공적으로 인증됩니다.

자세한 내용은 [Microsoft ID 플랫폼 및 OAuth 2.0 디바이스 권한 부여 흐름](/azure/active-directory/develop/v2-oauth2-device-code)을 참조하세요.

### <a name="enable-applications-for-device-code-flow"></a>디바이스 코드 흐름에 대한 애플리케이션 사용

디바이스 코드 흐름을 통해 사용자를 인증하려면 다음 단계를 사용합니다.

1. Azure Portal의 Azure Active Directory로 이동하여 앱 등록을 찾습니다.
2. **인증** 섹션으로 이동합니다.
3. **제안된 리디렉션 URI** 에서 `/common/oauth2/nativeclient`로 끝나는 URI를 확인합니다.
4. **기본 클라이언트 유형** 에서 `Treat application as a public client`에 대해 `yes`를 선택합니다.

이러한 단계를 수행하면 애플리케이션이 인증되지만, 사용자를 대신하여 Active Directory에 로그인하거나 리소스에 액세스할 수 있는 권한이 아직 없습니다. 이 문제를 해결하려면 **API 권한** 으로 이동하고 Microsoft Graph 및 액세스하려는 리소스(예: Azure Service Management, Key Vault 등)를 사용하도록 설정합니다.

또한 처음 로그인할 때 애플리케이션에 동의를 부여하려면 테넌트의 관리자여야 합니다.

Active Directory에서 디바이스 코드 흐름 옵션을 구성할 수 없는 경우 앱이 다중 테넌트여야 할 수 있습니다. 앱을 다중 테넌트로 만들려면 **인증** 패널로 이동한 다음, **조직 디렉터리의 계정** 을 선택합니다. 그런 다음, **애플리케이션을 퍼블릭 클라이언트로 처리** 에 대해 *예* 를 선택합니다.

### <a name="authenticate-a-user-account-with-device-code-flow"></a>디바이스 코드 흐름으로 사용자 계정 인증

다음 예제에서는 IoT 디바이스에서 `DeviceCodeCredential`을 사용하여 [Java용 Azure Key Vault Secret 클라이언트 라이브러리][secrets_client_library]에서 `SecretClient`를 인증하는 방법을 보여줍니다.

```java
/**
 * Authenticate with device code credential.
 */
DeviceCodeCredential deviceCodeCredential = new DeviceCodeCredentialBuilder()
    .challengeConsumer(challenge -> {
    // Lets the user know about the challenge.
    System.out.println(challenge.getMessage());
    }).build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
    .vaultUrl("https://<your Key Vault name>.vault.azure.net")
    .credential(deviceCodeCredential)
    .buildClient();
```

## <a name="interactive-browser-credential"></a>대화형 브라우저 자격 증명

이 자격 증명은 기본 시스템 브라우저를 사용하여 사용자를 대화형으로 인증하고 사용자 자신의 자격 증명을 사용하여 애플리케이션을 인증할 수 있도록 하여 원활한 인증 환경을 제공합니다.

### <a name="enable-applications-for-interactive-browser-oauth-2-flow"></a>대화형 브라우저 OAuth 2 흐름에 대한 애플리케이션 사용

`InteractiveBrowserCredential`을 사용하려면 사용자를 대신하여 로그인할 수 있는 권한이 있는 Azure Active Directory에 애플리케이션을 등록해야 합니다. 디바이스 코드 흐름에 대한 위의 단계에 따라 애플리케이션을 등록합니다. 앞에서 설명한 것처럼 테넌트의 관리자는 사용자 계정이 로그인하기 전에 애플리케이션에 동의를 부여해야 합니다.

`InteractiveBrowserCredentialBuilder`에서 리디렉션 URL이 필요하다는 것을 알 수 있습니다. 등록된 AAD 애플리케이션의 **인증** 섹션 아래에 있는 **리디렉션 URI** 하위 섹션에 리디렉션 URL을 추가합니다.

### <a name="authenticate-a-user-account-interactively-in-the-browser"></a>브라우저에서 대화형으로 사용자 계정 인증

다음 예제에서는 `InteractiveBrowserCredential`을 사용하여 [azure-security-keyvault-secrets][secrets_client_library] 클라이언트 라이브러리에서 `SecretClient`를 인증하는 방법을 보여줍니다.

```java
/**
 * Authenticate interactively in the browser.
 */
InteractiveBrowserCredential interactiveBrowserCredential = new InteractiveBrowserCredentialBuilder()
    .clientId("<your app client ID>")
    .redirectUrl("YOUR_APP_REGISTERED_REDIRECT_URL")
    .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
    .vaultUrl("https://<your Key Vault name>.vault.azure.net")
    .credential(interactiveBrowserCredential)
    .buildClient();
```

## <a name="username-password-credential"></a>사용자 이름 암호 자격 증명

`UsernamePasswordCredential`은 다단계 인증이 필요하지 않은 사용자 자격 증명을 사용하여 퍼블릭 클라이언트 애플리케이션을 인증하는 데 도움이 됩니다. 다음 예제에서는 `UsernamePasswordCredential`을 사용하여 [azure-security-keyvault-secrets][secrets_client_library] 클라이언트 라이브러리에서 `SecretClient`를 인증하는 방법을 보여줍니다. 사용자는 다단계 인증을 사용하지 않아야 합니다.

```java
/**
 * Authenticate with username, password.
 */
UsernamePasswordCredential usernamePasswordCredential = new UsernamePasswordCredentialBuilder()
    .clientId("<your app client ID>")
    .username("<your username>")
    .password("<your password>")
    .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
    .vaultUrl("https://<your Key Vault name>.vault.azure.net")
    .credential(usernamePasswordCredential)
    .buildClient();
```

자세한 내용은 [Microsoft ID 플랫폼 및 OAuth 2.0 리소스 소유자 암호 자격 증명](/azure/active-directory/develop/v2-oauth-ropc)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자 자격 증명을 사용한 Azure 인증에 대해 설명했습니다. 이 인증 형식은 Java용 Azure SDK에서 인증할 수 있는 여러 방법 중 하나입니다. 다음 문서에서는 다른 방법에 대해 설명합니다.

* [개발 환경의 Azure 인증](identity-dev-env-auth.md)
* [Azure에서 호스트되는 애플리케이션 인증](identity-azure-hosted-auth.md)
* [서비스 주체로 인증](identity-service-principal-auth.md)

인증을 익힌 후 SDK에서 제공하는 로깅 기능에 대한 정보는 [Java용 Azure SDK에서 로깅 구성](logging-overview.md)을 참조하세요.

<!-- LINKS -->
[secrets_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets
