---
title: 서비스 주체를 사용한 Azure 인증
description: 서비스 주체를 통한 애플리케이션 인증과 관련된 Java용 Azure SDK 개념 개요
author: g2vinay
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: vigera
ms.openlocfilehash: fd8c107d4fb5d37e3f2c2e99ba45ad46a6de34ae
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528702"
---
# <a name="azure-authentication-with-service-principal"></a>서비스 주체를 사용한 Azure 인증

이 문서에서는 Azure ID 라이브러리가 서비스 주체를 통해 Azure Active Directory 토큰 인증을 지원하는 방법을 보여줍니다. 이 문서에서 다루는 항목은 다음과 같습니다.

* [Azure CLI를 통해 서비스 주체 만들기](#create-a-service-principal-with-the-azure-cli)
* [클라이언트 암호 자격 증명](#client-secret-credential)
* [클라이언트 인증서 자격 증명](#client-certificate-credential)

자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 주체 개체](/azure/active-directory/develop/app-objects-and-service-principals)를 참조하세요.

## <a name="create-a-service-principal-with-the-azure-cli"></a>Azure CLI를 통해 서비스 주체 만들기

아래 [Azure CLI][azure_cli] 예제를 사용하여 클라이언트 암호 자격 증명을 만들거나 가져옵니다.

다음 명령을 사용하여 서비스 주체를 만들고 Azure 리소스에 대한 액세스를 구성합니다.

```azurecli
az ad sp create-for-rbac -n <your application name> --skip-assignment
```

이 명령은 다음 출력과 유사한 값을 반환합니다.

```output
{
"appId": "generated-app-ID",
"displayName": "dummy-app-name",
"name": "http://dummy-app-name",
"password": "random-password",
"tenant": "tenant-ID"
}
```

다음 명령을 사용하여 인증서와 함께 서비스 주체를 만듭니다. 이 인증서의 경로/위치를 적어둡니다.

```azurecli
az ad sp create-for-rbac -n <your application name> --skip-assignment --cert <certificate name> --create-cert
```

반환된 자격 증명을 확인하고 다음 정보를 적어둡니다.

* appId의 경우 `AZURE\_CLIENT\_ID`입니다.
* 암호의 경우 `AZURE\_CLIENT\_SECRET`입니다.
* 테넌트의 경우 `AZURE\_TENANT\_ID`입니다.

## <a name="client-secret-credential"></a>클라이언트 암호 자격 증명

이 자격 증명은 클라이언트 암호(암호)를 통해 만들어진 서비스 주체를 인증합니다. 이 예제에서는 `ClientSecretCredential`을 사용하여 [azure-security-keyvault-secrets][secrets_client_library] 클라이언트 라이브러리에서 `SecretClient`를 인증하는 방법을 보여줍니다.

```java
/**
 *  Authenticate with client secret.
 */
ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
  .clientId("<your client ID>")
  .clientSecret("<your client secret>")
  .tenantId("<your tenant ID>")
  .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(clientSecretCredential)
  .buildClient();
```

## <a name="client-certificate-credential"></a>클라이언트 인증서 자격 증명

이 자격 증명은 클라이언트 인증서를 통해 만들어진 서비스 주체를 인증합니다. 이 예제에서는 `ClientCertificateCredential`을 사용하여 [azure-security-keyvault-secrets][secrets_client_library] 클라이언트 라이브러리에서 `SecretClient`를 인증하는 방법을 보여줍니다.

```java
/**
 *  Authenticate with a client certificate.
 */
ClientCertificateCredential clientCertificateCredential = new ClientCertificateCredentialBuilder()
  .clientId("<your client ID>")
  .pemCertificate("<path to PEM certificate>")
  // Choose between either a PEM certificate or a PFX certificate.
  //.pfxCertificate("<path to PFX certificate>", "PFX CERTIFICATE PASSWORD")
  .tenantId("<your tenant ID>")
  .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(clientCertificateCredential)
  .buildClient();
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 서비스 주체를 통한 인증을 설명했습니다. 이 인증 형식은 Java용 Azure SDK에서 인증할 수 있는 여러 방법 중 하나입니다. 다음 문서에서는 다른 방법에 대해 설명합니다.

* [개발 환경의 Azure 인증](identity-dev-env-auth.md)
* [Azure에서 호스트되는 애플리케이션 인증](identity-azure-hosted-auth.md)
* [사용자 자격 증명으로 인증](identity-user-auth.md)

인증을 익힌 후 SDK에서 제공하는 로깅 기능에 대한 정보는 [Java용 Azure SDK에서 로깅 구성](logging-overview.md)을 참조하세요.

<!-- LINKS -->
[azure_cli]: /cli/azure
[secrets_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets
