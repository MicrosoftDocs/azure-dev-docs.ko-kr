---
title: Java 및 Azure Identity로 Azure 인증
description: Azure SDK 인증 및 ID 기능 소개
author: g2vinay
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: vigera
ms.openlocfilehash: 2a09b4ccaf39564c1cd2547417d722472ed9017c
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528672"
---
# <a name="azure-authentication-with-java-and-azure-identity"></a>Java 및 Azure Identity로 Azure 인증

이 문서에서는 Java용 Azure SDK 전체에서 Azure Active Directory 토큰 인증 지원을 제공하는 Java Azure Identity 라이브러리를 소개합니다. 이 라이브러리는 AAD 토큰 인증을 지원하는 Azure SDK 클라이언트를 구성하는 데 사용할 수 있는 `TokenCredential` 구현 세트를 제공합니다.

Azure Identity 라이브러리는 현재 다음을 지원합니다.

* 다음과 같은 것이 가능한 [Java 개발 환경의 Azure 인증](identity-dev-env-auth.md):
  * IDEA IntelliJ 인증. 로그인 정보는 [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/)에서 검색합니다.
  * Visual Studio Code 인증. 로그인 정보는 [Visual Studio Code용 Azure 플러그 인](https://code.visualstudio.com/docs/azure/extensions)에 저장됩니다.
  * Azure CLI 인증. 로그인 정보는 [Azure CLI](/cli/azure/what-is-azure-cli)에 저장됩니다.
* 다음과 같은 것이 가능한 [Azure에서 호스트되는 애플리케이션 인증](identity-azure-hosted-auth.md):
  * 기본 Azure 자격 증명 인증
  * 관리 ID 인증
* 다음과 같은 것이 가능한 [서비스 주체로 인증](identity-service-principal-auth.md):
  * 클라이언트 암호 인증
  * 클라이언트 인증서 인증
* 다음과 같은 것이 가능한 [사용자 자격 증명으로 인증](identity-user-auth.md):
  * 대화형 브라우저 인증
  * 디바이스 코드 인증
  * 사용자 이름/암호 인증

위의 링크를 따라 이동하여 각 인증 방법의 세부 사항에 대해 자세히 알아보세요. 이 문서의 나머지 부분에서는 일반적으로 사용되는 `DefaultAzureCredential` 및 관련 항목을 소개합니다.

## <a name="add-the-maven-dependencies"></a>Maven 종속성 추가

Maven 종속성을 추가하려면 프로젝트의 *pom.xml* 파일에 다음 XML을 포함합니다. *1.2.1* 버전 번호를 [Microsoft Azure Client Library For Identity 페이지](https://mvnrepository.com/artifact/com.azure/azure-identity)에 표시된 최신 릴리스 버전 번호로 바꿉니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.1</version>
</dependency>
```

## <a name="key-concepts"></a>주요 개념

Azure Identity 라이브러리를 이해하려면 자격 증명이란 무엇이며 자격 증명 구현에 가장 일반적으로 사용되는 `DefaultAzureCredential`의 개념을 이해해야 합니다.

자격 증명은 서비스 클라이언트가 요청을 인증하는 데 필요한 데이터를 포함하고 있거나 획득할 수 있는 클래스입니다. Azure SDK의 서비스 클라이언트는 생성될 때 자격 증명을 수락하고, 이러한 자격 증명을 사용하여 서비스 요청을 인증합니다.

Azure Identity 라이브러리는 Azure Active Directory를 사용한 OAuth 인증에 초점을 두고, 서비스 요청을 인증할 AAD 토큰을 획득할 수 있는 다양한 자격 증명 클래스를 제공합니다. 이 라이브러리의 모든 자격 증명 클래스는 [azure-core][azure_core_library]에서 `TokenCredential` 추상 클래스로 구현되며, 이러한 클래스 중 하나를 사용하여 `TokenCredential`로 인증할 수 있는 서비스 클라이언트를 생성할 수 있습니다.

`DefaultAzureCredential`은 애플리케이션이 최종적으로 Azure 클라우드에서 실행되는 대부분의 시나리오에 적합합니다. `DefaultAzureCredential`은 배포 시 일반적으로 인증에 사용되는 자격 증명과 개발 환경에서 인증에 사용되는 자격 증명을 결합합니다. `DefaultAzureCredential` 사용 예제를 비롯한 자세한 내용은 [Azure에 호스트되는 애플리케이션 인증](identity-azure-hosted-auth.md)의 [기본 Azure 자격 증명](identity-azure-hosted-auth.md#default-azure-credential) 섹션을 참조하세요.

## <a name="examples"></a>예제

[Java용 Azure SDK 사용](overview.md#provision-and-manage-azure-resources-with-management-libraries)에 설명된 대로, 관리 라이브러리는 약간 다릅니다. 차이점 중 하나는 Azure 서비스를 *사용* 하기 위한 라이브러리(클라이언트 라이브러리)와 Azure 서비스를 *관리* 하기 위한 라이브러리(관리 라이브러리)가 있다는 점입니다. 다음 섹션에서는 클라이언트 라이브러리와 관리 라이브러리에서 진행되는 인증을 간략하게 소개하겠습니다.

### <a name="authenticate-azure-client-libraries"></a>Azure 클라이언트 라이브러리 인증

아래 예에서는 `DefaultAzureCredential`을 사용하여 [azure-security-keyvault-secrets][secrets_client_library] 클라이언트 라이브러리에서 `SecretClient`를 인증하는 방법을 보여 줍니다.

```java
// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(new DefaultAzureCredentialBuilder().build())
  .buildClient();
```

### <a name="authenticate-azure-management-libraries"></a>Azure 관리 라이브러리 인증

Azure 관리 라이브러리는 Azure 클라이언트 라이브러리와 동일한 자격 증명 API를 사용하지만, 해당 구독의 Azure 리소스를 관리하려면 [Azure 구독 ID](/learn/modules/create-an-azure-account/4-multiple-subscriptions)가 필요합니다.

구독 ID는 [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 확인할 수 있습니다. 또는 다음 [Azure CLI][azure_cli] 명령을 사용하여 구독 ID를 가져옵니다.

```azurecli
az account list --output table
```

`AZURE_SUBSCRIPTION_ID` 환경 변수에서 구독 ID를 설정할 수 있습니다. 이 ID는 다음 예와 같이 `Manager` 인스턴스를 만드는 동안 `AzureProfile`에 의해 기본 구독 ID로 선택됩니다.

```java
AzureResourceManager azureResourceManager = AzureResourceManager.authenticate(
        new DefaultAzureCredentialBuilder().build(),
        new AzureProfile(AzureEnvironment.AZURE))
    .withDefaultSubscription();
```

이 예에 사용되는 `DefaultAzureCredential`은 `DefaultAzureCredential`을 사용하여 `AzureResourceManager` 인스턴스를 인증합니다. `DefaultAzureCredential` 대신 Azure Identity 라이브러리에 제공되는 다른 토큰 자격 증명 구현을 사용할 수도 있습니다.

## <a name="troubleshooting"></a>문제 해결

자격 증명은 인증에 실패하거나 인증을 실행할 수 없는 경우 예외를 발생시킵니다. 자격 증명이 인증에 실패하면 `ClientAuthenticationException`이 발생하고 인증이 실패한 이유를 설명하는 `message` 특성이 생깁니다. `ChainedTokenCredential`이 이 예외를 발생시키면 기본 자격 증명 목록의 연쇄 실행이 중지됩니다.

자격 증명에 필요한 기본 리소스 중 하나를 머신에서 사용할 수 없기 때문에 자격 증명이 인증을 실행할 수 없는 경우 `CredentialUnavailableException`이 발생하고 인증 실행에 자격 증명을 사용할 수 없는 이유를 설명하는 `message` 특성이 생깁니다. `ChainedTokenCredential`이 이 예외를 발생시키면 메시지는 체인의 각 자격 증명에서 오류 메시지를 수집합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Java용 Azure SDK에서 사용할 수 있는 Azure Identity 기능을 소개했습니다. 그리고 여러 시나리오에 일반적으로 사용되는 `DefaultAzureCredential`에 대해 설명했습니다. 다음 문서에서는 다음과 같이 Azure Identity 라이브러리를 사용하여 인증하는 다른 방법을 설명하고 `DefaultAzureCredential`에 대한 자세한 정보를 제공합니다.

* [개발 환경의 Azure 인증](identity-dev-env-auth.md)
* [Azure에서 호스트되는 애플리케이션 인증](identity-azure-hosted-auth.md)
* [서비스 주체로 인증](identity-service-principal-auth.md)
* [사용자 자격 증명으로 인증](identity-user-auth.md)

<!-- LINKS -->
[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[source]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity
[aad_doc]: /azure/active-directory/
[code_of_conduct]: https://opensource.microsoft.com/codeofconduct/
[keys_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys
[logging]: https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK
[secrets_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets
[eventhubs_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs
[azure_core_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/core
[javadoc]: https://azure.github.io/azure-sdk-for-java
[jdk_link]: /java/azure/jdk
