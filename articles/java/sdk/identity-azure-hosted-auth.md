---
title: Azure에서 호스트되는 Java 애플리케이션 인증
description: Azure 내에 호스트되는 애플리케이션의 인증과 관련된 Java용 Azure SDK 개념 소개
author: g2vinay
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: vigera
ms.openlocfilehash: e9097a432abf5957c9983ec2846bfb18ba581db4
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528684"
---
# <a name="authenticate-azure-hosted-java-applications"></a>Azure에서 호스트되는 Java 애플리케이션 인증

이 문서에서는 Azure Identity 라이브러리가 Azure에서 호스트되는 애플리케이션에 대한 Azure Active Directory 토큰 인증을 어떻게 지원하는지 보여 줍니다. 이러한 지원은 아래에서 설명하는 `TokenCredential` 구현 세트를 통해 수행할 수 있습니다.

이 문서는 다음 항목을 설명합니다.

* [기본 Azure 자격 증명](#default-azure-credential)
* [관리 ID 자격 증명](#managed-identity-credential)

## <a name="default-azure-credential"></a>기본 Azure 자격 증명

`DefaultAzureCredential`은 애플리케이션이 최종적으로 Azure 클라우드에서 실행되는 대부분의 시나리오에 적합합니다. `DefaultAzureCredential`은 배포 시 일반적으로 인증에 사용되는 자격 증명과 개발 환경에서 인증에 사용되는 자격 증명을 결합합니다. `DefaultAzureCredential`은 다음 메커니즘을 통해 순서대로 인증을 시도합니다.

![DefaultAzureCredential 인증 흐름](./media/defaultazurecredential.svg)

* 환경 - `DefaultAzureCredential`은 [환경 변수](#environment-variables)를 통해 지정된 계정 정보를 읽고 인증에 사용합니다.
* 관리 ID - 관리 ID를 사용하는 Azure 호스트에 애플리케이션이 배포되면 `DefaultAzureCredential`은 해당 계정으로 인증합니다.
* IntelliJ - Azure Toolkit for IntelliJ를 통해 인증한 경우 `DefaultAzureCredential`은 해당 계정으로 인증합니다.
* Visual Studio Code - Visual Studio Code Azure 계정 플러그 인을 통해 인증한 경우 `DefaultAzureCredential`은 해당 계정으로 인증합니다.
* Azure CLI - Azure CLI `az login` 명령을 통해 계정을 인증한 경우 `DefaultAzureCredential`은 해당 계정으로 인증합니다.

### <a name="configure-defaultazurecredential"></a>DefaultAzureCredential 구성

`DefaultAzureCredential`은 `DefaultAzureCredentialBuilder` 또는 환경 변수의 setter를 통해 구성 세트를 지원합니다.

* 환경 변수 `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` 및 `AZURE_TENANT_ID`를 [환경 변수](#environment-variables)에 정의된 대로 설정하면 값에 지정된 서비스 주체로 인증하도록 `DefaultAzureCredential`이 구성됩니다.
* 작성기 또는 환경 변수 `AZURE_CLIENT_ID`에 `.managedIdentityClientId(String)`를 설정하면 사용자 정의 관리 ID로 인증하도록 `DefaultAzureCredential`이 구성되고, 이를 비워 두면 시스템 할당 관리 ID로 인증하도록 구성됩니다.
* 작성기 또는 환경 변수 `AZURE_TENANT_ID`에 `.tenantId(String)`를 설정하면 공유 토큰 캐시, Visual Studio Code 및 IntelliJ IDEA에 대한 특정 테넌트에 인증하도록 `DefaultAzureCredential`이 구성됩니다.
* 환경 변수 `AZURE_USERNAME`을 설정하면 공유 토큰 캐시에서 해당하는 캐시된 토큰을 선택하도록 `DefaultAzureCredential`이 구성됩니다.
* 작성기에 `.intelliJKeePassDatabasePath(String)`를 설정하면 IntelliJ 자격 증명으로 인증할 때 특정 KeePass 파일을 읽도록 `DefaultAzureCredential`이 구성됩니다.

### <a name="authenticate-with-defaultazurecredential"></a>DefaultAzureCredential을 사용하여 인증

다음 예에서는 `DefaultAzureCredential`을 사용하여 [azure-security-keyvault-secrets][secrets_client_library] 클라이언트 라이브러리의 `SecretClient`를 인증하는 방법을 보여 줍니다.

```java
// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(new DefaultAzureCredentialBuilder().build())
  .buildClient();
```

### <a name="authenticate-a-user-assigned-managed-identity-with-defaultazurecredential"></a>DefaultAzureCredential로 사용자가 할당한 관리 ID 인증

다음 예에서는 사용자가 할당한 관리 ID가 구성된 Azure 리소스에 배포된 `DefaultAzureCredential`을 사용하여 [azure-security-keyvault-secrets][secrets_client_library] 클라이언트 라이브러리의 `SecretClient`를 인증하는 방법을 보여 줍니다.

```java
/**
 * The default credential will use the user-assigned managed identity with the specified client ID.
 */
DefaultAzureCredential defaultCredential = new DefaultAzureCredentialBuilder()
  .managedIdentityClientId("<managed identity client ID>")
  .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(defaultCredential)
  .buildClient();
```

### <a name="authenticate-a-user-in-azure-toolkit-for-intellij-with-defaultazurecredential"></a>Azure Toolkit for IntelliJ에서 DefaultAzureCredential로 사용자 인증

다음 예에서는 IntelliJ IDEA가 설치되었고 사용자가 Azure 계정으로 Azure Toolkit for IntelliJ에 로그인한 워크스테이션에서 `DefaultAzureCredential`을 사용하여 [azure-security-keyvault-secrets][secrets_client_library] 클라이언트 라이브러리의 `SecretClient`를 인증하는 방법을 보여 줍니다.

IntelliJ IDEA를 구성하는 방법에 대한 자세한 내용은 [IntelliJCredential용 Azure Toolkit for IntelliJ 로그인](identity-dev-env-auth.md#sign-in-azure-toolkit-for-intellij-for-intellijcredential)을 참조하세요.

```java
/**
 * The default credential will use the KeePass database path to find the user account in IntelliJ on Windows.
 */
// KeePass configuration is required only for Windows. No configuration needed for Linux / Mac.
DefaultAzureCredential defaultCredential = new DefaultAzureCredentialBuilder()
  .intelliJKeePassDatabasePath("C:\\Users\\user\\AppData\\Roaming\\JetBrains\\IdeaIC2020.1\\c.kdbx")
  .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(defaultCredential)
  .buildClient();
```

## <a name="managed-identity-credential"></a>관리 ID 자격 증명

관리 ID는 시스템 또는 사용자가 할당한 Azure 리소스의 관리 ID를 인증합니다. 따라서 `IDENTITY/MSI`, `IMDS` 엔드포인트 또는 둘 다를 통해 관리 ID를 지원하는 Azure 리소스 내에서 애플리케이션이 실행되는 경우 이 자격 증명은 애플리케이션을 인증하고 강력한 무암호 인증 환경을 제공합니다.

자세한 내용은 [Azure 리소스에 대한 관리 ID란?](/azure/active-directory/managed-identities-azure-resources/overview)을 참조하세요.

### <a name="authenticate-in-azure-with-managed-identity"></a>Azure에서 관리 ID로 인증

다음 예에서는 시스템이 할당한 또는 사용자가 할당한 관리 ID를 사용하도록 설정된 Azure의 가상 머신, 앱 서비스, 함수 앱, Cloud Shell, Service Fabric, Arc 또는 AKS 환경에서 `ManagedIdentityCredential`을 사용하여 [azure-security-keyvault-secrets][secrets_client_library] 클라이언트 라이브러리의 `SecretClient`를 인증하는 방법을 보여 줍니다.

```java
/**
 * Authenticate with a managed identity.
 */
ManagedIdentityCredential managedIdentityCredential = new ManagedIdentityCredentialBuilder()
  .clientId("<user-assigned managed identity client ID>") // required only for user-assigned
  .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(managedIdentityCredential)
  .buildClient();
```

## <a name="environment-variables"></a>환경 변수

환경 변수를 사용하여 `DefaultAzureCredential` 및 `EnvironmentCredential`을 구성할 수 있습니다. 각 인증 유형에는 다음과 같은 특정 변수 값이 필요합니다.

### <a name="service-principal-with-secret"></a>비밀을 사용하는 서비스 주체

| 변수 이름         | 값                                                  |
| --------------------- | ------------------------------------------------------ |
| `AZURE_CLIENT_ID`     | Azure Active Directory 애플리케이션 ID           |
| `AZURE_TENANT_ID`     | 애플리케이션의 Azure Active Directory 테넌트 ID |
| `AZURE_CLIENT_SECRET` | 애플리케이션의 클라이언트 암호 중 하나               |

### <a name="service-principal-with-certificate"></a>인증서를 사용하는 서비스 주체

| 변수 이름         | 값                                                                                                |
| --------------------- | ---------------------------------------------------------------------------------------------------- |
| `AZURE_CLIENT_ID`     | Azure Active Directory 애플리케이션 ID                                                         |
| `AZURE_TENANT_ID`     | 애플리케이션의 Azure Active Directory 테넌트 ID                                               |
| `AZURE_CLIENT_CERTIFICATE_PATH` | 암호 보호가 적용되지 않은 프라이빗 키를 포함하고 있는 PEM 인코딩 인증서 파일의 경로|

### <a name="username-and-password"></a>사용자 이름 및 암호

| 변수 이름         | 값                                            |
| --------------------- | ------------------------------------------------ |
| `AZURE_CLIENT_ID`     | Azure Active Directory 애플리케이션 ID     |
| `AZURE_USERNAME`      | 사용자 이름(일반적으로 이메일 주소)           |
| `AZURE_PASSWORD`      | 지정된 사용자 이름과 연결된 암호  |

위의 순서대로 구성이 시도됩니다. 예를 들어 클라이언트 암호 및 인증서 값이 모두 있는 경우 클라이언트 암호가 사용됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure에서 호스트되는 애플리케이션의 인증을 살펴보았습니다. 이 인증 형식은 Java용 Azure SDK에서 인증할 수 있는 여러 방법 중 하나입니다. 다음 문서에서는 아래와 같은 다른 방법에 대해 설명합니다.

* [개발 환경의 Azure 인증](identity-dev-env-auth.md)
* [서비스 주체로 인증](identity-service-principal-auth.md)
* [사용자 자격 증명으로 인증](identity-user-auth.md)

인증을 완전히 익히고 나면 [Java용 Azure SDK에서 로깅 구성](logging-overview.md)에서 SDK가 제공하는 로깅 기능에 대한 정보를 참조하세요.

<!-- LINKS -->
[secrets_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets
