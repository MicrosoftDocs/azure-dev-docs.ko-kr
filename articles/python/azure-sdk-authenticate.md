---
title: Azure 서비스를 사용하여 Python 애플리케이션을 인증하는 방법
description: Azure 라이브러리를 사용하여 Azure 서비스로 Python 앱을 인증하는 데 필요한 자격 증명 개체를 얻는 방법
ms.date: 08/18/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 50f13c09d1c3932446d90420399b18c3247f1640
ms.sourcegitcommit: 800c5e05ad3c0b899295d381964dd3d47436ff90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88614473"
---
# <a name="how-to-authenticate-and-authorize-python-apps-on-azure"></a>Azure에서 Python 앱을 인증하고 권한을 부여하는 방법

Azure에 배포된 대부분의 클라우드 애플리케이션은 스토리지, 데이터베이스, 저장된 비밀 등과 같은 다른 Azure 리소스에 액세스해야 합니다. 이러한 리소스에 액세스하려면 애플리케이션이 인증되고 권한이 부여되어야 합니다.

- **인증**은 Azure Active Directory를 사용하여 앱의 ID를 확인합니다.

- **권한 부여**는 인증된 앱이 지정된 리소스에 대해 수행할 수 있는 작업을 결정합니다. 권한이 부여된 작업은 해당 리소스의 앱 ID에 할당된 **역할**을 통해 정의됩니다. 앱 ID에 할당된 추가 **액세스 정책**에 의해 권한 부여가 결정되는 경우(예: Azure Key Vault)도 있습니다.

이 문서에서는 인증 및 권한 부여에 대해 자세히 설명합니다.

- 앱 ID를 할당하는 방법
- ID에 권한을 부여하는 방법
- 인증 및 권한 부여가 수행되는 방법 및 시기
- 앱이 Azure 라이브러리를 사용하여 Azure에서 인증하는 다양한 방법입니다. `DefaultAzureCredential`이 필수는 아니지만 사용하는 것이 좋습니다.

## <a name="how-to-assign-an-app-identity"></a>앱 ID를 할당하는 방법

Azure에서 앱 ID는 **서비스 주체**에 의해 정의됩니다. (서비스 주체는 앱 또는 서비스를 식별하는 데 사용되는 특정 유형의 "보안 주체"이며, 인간 사용자나 사용자 그룹이 아닌 코드 조각입니다.)

관련된 서비스 주체는 앱이 실행되는 위치에 따라 달라지며, 다음 섹션에 설명되어 있습니다.

### <a name="identity-when-running-the-app-on-azure"></a>Azure에서 앱을 실행하는 경우 ID

클라우드에서 실행하는 경우(예: 프로덕션 환경) 앱은 **시스템이 할당한 관리 ID**를 가장 일반적으로 사용합니다. [관리 ID](/azure/active-directory/managed-identities-azure-resources/overview)를 사용하면 리소스에 대한 역할 및 권한을 할당할 때 앱 이름을 사용합니다. Azure에서는 기본 서비스 주체가 자동으로 관리되고 다른 Azure 리소스를 사용하여 앱이 자동으로 인증됩니다. 따라서 서비스 주체를 직접 처리할 필요가 없습니다. 또한 앱 코드가 Azure 리소스에 대한 액세스 토큰, 비밀 또는 연결 문자열을 처리할 필요가 없기 때문에 이러한 정보가 유출되거나 손상될 위험이 적습니다.

관리 ID 구성은 앱을 호스트하는 데 사용하는 서비스에 따라 달라집니다. 각 서비스에 대한 지침 링크는 [관리 ID를 지원하는 서비스](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities) 문서를 참조하세요. 예를 들어 Azure App Service에 배포된 웹앱의 경우 Azure Portal에서 **ID** > **시스템 할당 항목** 옵션을 사용하거나 Azure CLI에서 `az webapp identity assign` 명령을 사용하여 관리 ID를 사용하도록 설정합니다.

관리 ID를 사용할 수 없으면 그 대신 Azure Active Directory에 애플리케이션을 수동으로 등록합니다. 등록하면 역할 및 권한을 할당할 때 사용하는 서비스 주체가 앱에 할당됩니다. 자세한 내용은 [애플리케이션 등록](/azure/active-directory/develop/quickstart-register-app)을 참조하세요.

### <a name="identity-when-running-the-app-locally"></a>로컬에서 앱을 실행하는 경우 ID

개발하는 동안, 개발자 워크스테이션에서 앱 코드를 실행하고 디버그하는 동시에 같은 코드로 클라우드의 Azure 리소스에 액세스해야 하는 경우가 많습니다. 이런 경우에는 Azure Active Directory를 통해 로컬 개발 전용으로 별도의 서비스 주체를 만듭니다. 다시, 해당 리소스에 대한 역할과 권한을 서비스 주체에게 할당합니다. 일반적으로 개발 ID는 비프로덕션 리소스에만 액세스할 수 있도록 권한을 부여합니다.

로컬 서비스 주체를 만들고 Azure 라이브러리에서 사용할 수 있도록 설정하는 방법에 대한 자세한 내용은 [로컬 개발 환경 구성](configure-local-development-environment.md)을 참조하세요. 이러한 일회성 구성을 완료하고 나면, 환경에 맞게 수정하지 않고도 로컬과 클라우드에서 동일한 앱 코드를 실행할 수 있습니다.

각 개발자는 자신의 서비스 주체를 가지고 있어야 하며, 이 주체는 워크스테이션의 사용자 계정으로 보호되고 소스 제어 리포지토리에는 저장되지 않습니다. 서비스 주체 하나가 도난되거나 손상되면 해당 주체를 간편하게 삭제하고 모든 권한을 해지한 다음, 해당 개발자의 서비스 주체를 다시 만들 수 있습니다. 자세한 내용은 [서비스 주체를 관리하는 방법](how-to-manage-service-principals.md)을 참조하세요.

> [!NOTE]
> 자신의 Azure 사용자 자격 증명을 사용하여 앱을 실행할 수 있지만 그렇게 하면 클라우드에 배포할 때 앱에 필요한 특정 리소스 권한을 설정하는 데 도움이 되지 않습니다. 개발용 서비스 주체를 설정하여 필요한 역할 및 권한을 할당하는 것이 훨씬 더 좋습니다. 그런 다음, 배포된 앱의 관리 ID 또는 서비스 주체를 사용하여 복제할 수 있습니다.

## <a name="assign-roles-and-permissions-to-an-identity"></a>ID에 역할 및 권한 할당

Azure에서 그리고 로컬에서 실행할 때 앱의 ID를 알고 나면, RBAC(역할 기반 액세스 제어)를 사용하여 Azure Portal 또는 Azure CLI를 통해 권한을 부여합니다. 자세한 전체 내용은 [앱 ID 또는 서비스 주체에 역할 권한을 할당하는 방법](how-to-assign-role-permissions.md)을 참조하세요.

## <a name="when-does-authentication-and-authorization-occur"></a>인증 및 권한 부여가 수행되는 시기

Python용 Azure 라이브러리(SDK)를 사용하여 앱 코드를 작성하는 경우 다음 패턴을 사용하여 Azure 리소스에 액세스합니다.

1. 이 문서의 뒷부분에서 설명하는 방법 중 하나를 사용하여 앱 ID를 설명하는 자격 증명을 얻습니다.

1. 자격 증명을 사용하여 원하는 리소스에 대한 클라이언트 개체를 가져옵니다. (각 유형의 리소스에는 리소스의 URL을 제공하는 Azure 라이브러리에 자체 클라이언트 개체가 있습니다.)

1. 클라이언트 개체를 통해 리소스에 액세스하거나 수정하려고 시도합니다. 그러면 리소스의 REST API에 대한 HTTP 요청이 생성됩니다. API 호출은 Azure에서 앱 ID를 인증하고 권한 부여를 확인하는 지점입니다.

다음 코드는 Azure Key Vault에 액세스를 시도하여 이러한 단계를 설명하고 보여줍니다.

```python
import os
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

# Acquire the resource URL. In this code we assume the resource URL is in an
# environment variable, KEY_VAULT_URL in this case.

vault_url = os.environ["KEY_VAULT_URL"]


# Acquire a credential object for the app identity. When running in the cloud,
# DefaultAzureCredential uses the app's managed identity or user-assigned service principal.
# When run locally, DefaultAzureCredential relies on environment variables named
# AZURE_CLIENT_ID, AZURE_CLIENT_SECRET, and AZURE_TENANT_ID.

credential = DefaultAzureCredential()


# Acquire an appropriate client object for the resource identified by the URL. The
# client object only stores the given credential at this point but does not attempt
# to authenticate it.
#
# **NOTE**: SecretClient here is only an example; the same process applies to all
# other Azure client libraries.

secret_client = SecretClient(vault_url=vault_url, credential=credential)

# Attempt to perform an operation on the resource using the client object (in
# this case, retrieve a secret from Key Vault). The operation fails for any of
# the following reasons:
#
# 1. The information in the credential object is invalid (for example, the AZURE_CLIENT_ID
#    environment variable cannot be found).
# 2. The app identity cannot be authenticated using the information in the credential object.
# 3. The app identity is not authorized to perform the requested operation on the
#    resource (identified in this case by the vault_url.

retrieved_secret = secret_client.get_secret("secret-name-01")
```

또한, 코드가 클라이언트 개체를 통해 Azure REST API에 특정 요청을 수행할 때까지 인증이나 권한 부여는 발생하지 않습니다. `DefaultAzureCredential`(다음 섹션 참조)을 만드는 명령문은 메모리에 클라이언트 측 개체만 만들고 다른 검사는 수행하지 않습니다. 

SDK [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python) 개체를 만드는 경우에도 해당 리소스와 통신하지 않습니다. `SecretClient` 개체는 기본 Azure REST API 주위의 래퍼일 뿐이며 앱의 런타임 메모리에만 존재합니다. 

코드가 [`get_secret`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-) 메서드를 호출하는 경우에만 클라이언트 개체에서 Azure에 대한 적절한 REST API 호출을 생성합니다. 그런 다음, `get_secret`에 대한 Azure의 엔드포인트에서 호출자의 ID를 인증하고 권한 부여를 확인합니다.

## <a name="authenticate-with-defaultazurecredential"></a>DefaultAzureCredential을 사용하여 인증

대부분의 애플리케이션에서 [`azure-identity`](/python/api/azure-identity/azure.identity?view=azure-python) 라이브러리의 [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) 클래스는 가장 간단하고 권장되는 인증 방법을 제공합니다. `DefaultAzureCredential`은 클라우드에서 앱의 관리 ID를 자동으로 사용하고, 로컬에서 실행될 때는 환경 변수에서 로컬 서비스 주체를 자동으로 로드합니다.

```python
import os
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

# Acquire the resource URL
vault_url = os.environ["KEY_VAULT_URL"]

# Aquire a credential object
credential = DefaultAzureCredential()

# Acquire a client object
secret_client = SecretClient(vault_url=vault_url, credential=credential)

# Attempt to perform an operation
retrieved_secret = secret_client.get_secret("secret-name-01")
```

이전 코드는 Azure Key Vault에 액세스할 때 `DefaultAzureCredential` 개체를 사용합니다. 여기서 Key Vault의 URL은 `KEY_VAULT_URL`이라는 환경 변수에서 사용할 수 있습니다. 이 코드는 일반적인 라이브러리 사용 패턴을 명확하게 구현합니다. 자격 증명 개체를 확보하고, Azure 리소스에 적절한 클라이언트 개체를 만든 다음, 해당 클라이언트 개체를 사용하여 해당 리소스에 대한 작업을 수행합니다. 다시 말하지만 인증 및 권한 부여는 이러한 마지막 단계까지 수행되지 않습니다.

코드가 Azure에 배포되어 실행되면 `DefaultAzureCredential`은 호스트하는 서비스 내에서 앱에 대해 사용하도록 설정할 수 있는 시스템이 할당한 관리 ID를 자동으로 사용합니다. Azure Storage 또는 Azure Key Vault와 같은 특정 리소스에 대한 권한은 Azure Portal 또는 Azure CLI를 사용하여 해당 ID에 할당됩니다. 이러한 경우 코드에서 명시적인 서비스 주체를 처리하지 않으므로 이 Azure 관리 ID는 보안을 최대화합니다.

코드를 로컬로 실행하면 `DefaultAzureCredential`에서 `AZURE_TENANT_ID`, `AZURE_CLIENT_ID` 및 `AZURE_CLIENT_SECRET`이라는 환경 변수에서 설명하는 서비스 주체를 자동으로 사용합니다. 그런 다음, 클라이언트 개체는 API 엔드포인트를 호출할 때 이러한 값을 HTTP 요청 헤더에 안전하게 포함합니다. 로컬 또는 클라우드에서 실행하는 경우 코드를 변경할 필요가 없습니다. 서비스 주체를 만들고 환경 변수를 설정하는 방법에 대한 자세한 내용은 [Azure를 위한 로컬 Python 개발 환경 구성 - 인증 구성](configure-local-development-environment.md#configure-authentication)을 참조하세요.

두 경우 모두 적절한 리소스에 대한 권한이 관련 ID에 할당되어야 합니다. 일반적인 프로세스는 [역할 권한을 할당하는 방법](how-to-assign-role-permissions.md)에 설명되어 있습니다. 세부 정보는 개별 서비스의 설명서에서 찾을 수 있습니다. 예를 들어 이전 코드에 필요한 Key Vault 권한에 대한 자세한 내용은 [액세스 제어 정책을 사용하여 Key Vault 인증 제공](/azure/key-vault/general/group-permissions-for-apps)을 참조하세요.

<a name="cli-auth-note"></a>
> [!IMPORTANT]
> 향후에는 서비스 주체 환경 변수를 사용할 수 없는 경우 `DefaultAzureCredential`에서 `az login`을 통해 Azure CLI에 서명된 ID를 사용합니다. 구독의 소유자 또는 관리자인 경우 이 기능의 실질적 결과는 코드에서 특정 권한을 할당하지 않고도 해당 구독의 리소스 대부분에 고유하게 액세스할 수 있다는 것입니다. 이 동작은 실험에 편리합니다. 그러나 프로덕션 코드 작성을 시작하는 경우 특정 서비스 주체를 사용하고 특정 권한을 할당하는 것이 좋습니다. 이렇게 하면 프로덕션에 배포하기 전에 정확한 권한을 다른 ID에 할당하는 방법을 알아보고 테스트 환경에서 해당 권한의 유효성을 정확하게 검사할 수 있기 때문입니다.

### <a name="using-defaultazurecredential-with-sdk-management-libraries"></a>SDK 관리 라이브러리에서 DefaultAzureCredential 사용

```python
# WARNING: this code presently fails with current release libraries!

from azure.identity import DefaultAzureCredential

# azure.mgmt.resource is an Azure SDK management library
from azure.mgmt.resource import SubscriptionClient

# Attempt to retrieve the subscription ID
credential = DefaultAzureCredential()
subscription_client = SubscriptionClient(credential)

# The following line produces a "no attribute 'signed_session'" error:
subscription = next(subscription_client.subscriptions.list())

print(subscription.subscription_id)
```

현재 `DefaultAzureCredential`은 Azure SDK 클라이언트("데이터 평면")와 이 코드 예제처럼 Azure SDK 관리 라이브러리의 미리 보기 버전(이름이 `azure-mgmt`로 시작하는 라이브러리의 최신 미리 보기 버전)에서만 작동합니다. 즉, 현재 릴리스 라이브러리에서는 "'DefaultAzureCredential' 개체에 'signed_session' 특성이 없습니다."라는 약간 모호한 오류로 인해 `subscription_client.subscriptions.list()`에 대한 호출이 실패합니다. 이 오류는 현재 SDK 관리 라이브러리에서 `DefaultAzureCredential`이 없는 `signed_session` 속성이 자격 증명 개체에 포함되어 있다고 가정하므로 발생합니다.

블로그 게시물 [Azure 관리 라이브러리에 대한 새 미리 보기 소개](https://devblogs.microsoft.com/azure-sdk/introducing-new-previews-for-azure-management-libraries/)에 설명된 대로 미리 보기 관리 라이브러리를 사용하여 오류를 해결할 수 있습니다.

또는 다음 메서드를 사용할 수 있습니다.

1. 이 문서의 이후 섹션에서 설명하는 다른 인증 방법 중 하나를 사용합니다. 이 방법은 *SDK 관리 라이브러리만* 사용하고 클라우드에 배포되지 않는 코드에서 제대로 작동할 수 있습니다. 이 경우 로컬 서비스 주체만 사용할 수 있습니다.

1. `DefaultAzureCredential` 대신 Azure SDK 엔지니어링 팀의 구성원이 제공하는 [CredentialWrapper 클래스(cred_wrapper.py)](https://gist.github.com/lmazuel/cc683d82ea1d7b40208de7c9fc8de59d)를 사용합니다. 업데이트된 관리 라이브러리가 미리 보기에서 벗어나면 `DefaultAzureCredential`로 간단히 다시 전환할 수 있습니다. 이 방법은 SDK 클라이언트 및 관리 라이브러리 모두에서 동일한 자격 증명을 사용할 수 있다는 장점이 있으며 로컬 및 클라우드 모두에서 작동합니다.

    *cred_wrapper.py* 복사본을 프로젝트 폴더에 다운로드했다고 가정하는 경우 이전 코드는 다음과 같습니다.

    ```python
    from cred_wrapper import CredentialWrapper
    from azure.mgmt.resource import SubscriptionClient

    credential = CredentialWrapper()
    subscription_client = SubscriptionClient(credential)
    subscription = next(subscription_client.subscriptions.list())
    print(subscription.subscription_id)
    ```

    다시 말해, 업데이트된 관리 라이브러리가 미리 보기에서 벗어나면 `DefaultAzureCredential`을 직접 사용할 수 있습니다.

## <a name="other-authentication-methods"></a>기타 인증 방법

`DefaultAzureCredential`이 대부분의 시나리오에 추천되는 인증 방법이지만 다른 방법은 다음과 같은 경우에 사용할 수 있습니다.

- 대부분의 방법은 명시적 서비스 주체에서만 작동하며 클라우드에 배포된 코드의 관리 ID를 활용하지 않습니다. 프로덕션 코드에서 사용하는 경우 클라우드 애플리케이션에 대한 고유한 서비스 주체를 관리 및 유지 관리해야 합니다.

- CLI 기반 인증과 같은 일부 방법은 로컬 스크립트에서만 작동하며 프로덕션 코드에서 사용할 수 없습니다.

클라우드에 배포된 애플리케이션에 대한 서비스 주체는 구독 Active Directory에서 관리됩니다. 자세한 내용은 [서비스 주체를 관리하는 방법](how-to-manage-service-principals.md)을 참조하세요.

모든 경우, 적절한 서비스 주체 또는 사용자에게 해당 리소스 및 작업에 대한 적절한 권한이 있어야 합니다.

### <a name="authenticate-with-a-json-file"></a>JSON 파일을 사용하여 인증

이 방법에서는 서비스 주체에 필요한 자격 증명이 포함된 JSON 파일을 만듭니다. 그런 다음, 해당 파일을 사용하여 SDK 클라이언트 개체를 만듭니다. 이 방법은 로컬 및 클라우드 모두에서 사용할 수 있습니다.

1. 다음 형식의 JSON 파일을 만듭니다.

    ```json
    {
        "subscriptionId": "<azure_aubscription_id>",
        "tenantId": "<tenant_id>",
        "clientId": "<application_id>",
        "clientSecret": "<application_secret>",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com/",
        "activeDirectoryGraphResourceId": "https://graph.windows.net/",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

    4개의 자리 표시자를 Azure 구독 ID, 테넌트 ID, 클라이언트 ID 및 클라이언트 암호로 바꿉니다.

    > [!TIP]
    > [로컬 개발 환경 구성](configure-local-development-environment.md#create-a-service-principal-and-environment-variables-for-development)에서 설명한 대로 이 JSON 형식은 `--sdk-auth` 매개 변수가 포함된 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용하여 직접 생성할 수 있습니다.

1. 코드에서 액세스할 수 있는 안전한 위치에 파일을 *credentials.json*이라는 이름으로 저장합니다. 자격 증명을 안전하게 유지하려면 이 파일을 원본 제어에서 생략하고 다른 개발자와 공유하지 않아야 합니다. 즉, 서비스 주체의 테넌트 ID, 클라이언트 ID 및 클라이언트 암호는 항상 개발 워크스테이션에서 격리되어 있어야 합니다.

1. JSON 파일의 경로를 값으로 사용하여 `AZURE_AUTH_LOCATION`이라는 환경 변수를 만듭니다.

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set AZURE_AUTH_LOCATION=../credentials.json
    ```

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    AZURE_AUTH_LOCATION="../credentials.json"
    ```

    ---

    이러한 예제에서는 JSON 파일의 이름이 *credentials.json*이고 프로젝트의 부모 폴더에 있다고 가정합니다.

1. [get_client_from_auth_file](/python/api/azure-common/azure.common.client_factory?view=azure-python#get-client-from-auth-file-client-class--auth-path-none----kwargs-) 메서드를 사용하여 클라이언트 개체를 만듭니다.

    ```python
    from azure.common.client_factory import get_client_from_auth_file
    from azure.mgmt.resource import SubscriptionClient

    # This form of get_client_from_auth_file relies on the AZURE_AUTH_LOCATION
    # environment variable.
    subscription_client = get_client_from_auth_file(SubscriptionClient)

    subscription = next(subscription_client.subscriptions.list())
    print(subscription.subscription_id)
    ```

`auth_path` 인수를 사용하여 코드에서 직접 경로를 번갈아 지정할 수 있습니다. 이 경우 환경 변수는 필요하지 않습니다.

```python
subscription_client = get_client_from_auth_file(SubscriptionClient, auth_path="../credentials.json")
```

### <a name="authenticate-with-a-json-dictionary"></a>JSON 사전으로 인증

```python
import os
from azure.common.client_factory import get_client_from_json_dict
from azure.mgmt.resource import SubscriptionClient

# Retrieve the IDs and secret to use in the JSON dictionary
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]
tenant_id = os.environ["AZURE_TENANT_ID"]
client_id = os.environ["AZURE_CLIENT_ID"]
client_secret = os.environ["AZURE_CLIENT_SECRET"]

config_dict = {
   "subscriptionId": subscription_id,
    "tenantId": tenant_id,
   "clientId": client_id,
   "clientSecret": client_secret,
   "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
   "resourceManagerEndpointUrl": "https://management.azure.com/",
   "activeDirectoryGraphResourceId": "https://graph.windows.net/",
   "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
   "galleryEndpointUrl": "https://gallery.azure.com/",
   "managementEndpointUrl": "https://management.core.windows.net/"
}

subscription_client = get_client_from_json_dict(SubscriptionClient, config_dict)

subscription = next(subscription_client.subscriptions.list())
print(subscription.subscription_id)
```

이전 섹션에서 설명한 대로 파일을 사용하는 대신 변수에 필요한 JSON 데이터를 작성하고 [get_client_from_json_dict](/python/api/azure-common/azure.common.client_factory?view=azure-python#get-client-from-json-dict-client-class--config-dict----kwargs-)를 호출할 수 있습니다. 이 코드는 [로컬 개발 환경 구성](configure-local-development-environment.md#create-a-service-principal-and-environment-variables-for-development)에서 설명한 환경 변수를 만들었다고 가정합니다. 클라우드에 배포된 코드의 경우 Azure App Service 및 Azure Functions와 같은 플랫폼 서비스를 사용할 때 이러한 환경 변수를 서버 VM에 만들거나 애플리케이션 설정으로 만들 수 있습니다.

또한 환경 변수를 사용하는 대신 값을 Azure Key Vault에 저장하고 런타임에 이러한 값을 검색할 수 있습니다.

### <a name="authenticate-with-token-credentials"></a>토큰 자격 증명을 사용하여 인증

```python
import os
from azure.mgmt.resource import SubscriptionClient
from azure.common.credentials import ServicePrincipalCredentials

# Retrieve the IDs and secret to use with ServicePrincipalCredentials
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]
tenant_id = os.environ["AZURE_TENANT_ID"]
client_id = os.environ["AZURE_CLIENT_ID"]
client_secret = os.environ["AZURE_CLIENT_SECRET"]

credential = ServicePrincipalCredentials(tenant=tenant_id, client_id=client_id, secret=client_secret)

subscription_client = SubscriptionClient(credential)

subscription = next(subscription_client.subscriptions.list())
print(subscription.subscription_id)
```

이 방법에서는 Azure Key Vault 또는 환경 변수와 같은 보안 스토리지에서 가져온 자격 증명을 사용하여 [`ServicePrincipalCredentials`](/python/api/msrestazure/msrestazure.azure_active_directory.serviceprincipalcredentials?view=azure-python) 개체를 만듭니다. 이전 코드에서는 [로컬 개발 환경 구성](configure-local-development-environment.md#create-a-service-principal-and-environment-variables-for-development)에서 설명한 환경 변수를 만들었다고 가정합니다.

이 방법을 사용하면 클라이언트 개체에 대해 `base_url` 인수를 지정하여 Azure 퍼블릭 클라우드 대신 [Azure 소버린 클라우드 또는 국가별 클라우드](/azure/active-directory/develop/authentication-national-cloud)를 사용할 수 있습니다.

```python
from msrestazure.azure_cloud import AZURE_CHINA_CLOUD

#...

subscription_client = SubscriptionClient(credentials, base_url=AZURE_CHINA_CLOUD.endpoints.resource_manager)
```

소버린 클라우드 상수는 [msrestazure.azure_cloud 라이브러리](https://github.com/Azure/msrestazure-for-python/blob/master/msrestazure/azure_cloud.py)에 있습니다.

### <a name="authenticate-with-token-credentials-and-an-adal-context"></a>토큰 자격 증명 및 ADAL 컨텍스트를 사용하여 인증

토큰 자격 증명을 사용할 때 더 많은 제어가 필요한 경우 [Python용 Azure ADAL(Active Directory 인증 라이브러리)](https://github.com/AzureAD/azure-activedirectory-library-for-python) 및 SDK ADAL 래퍼를 사용합니다.

```python
import os, adal
from azure.mgmt.resource import SubscriptionClient
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD

# Retrieve the IDs and secret to use with ServicePrincipalCredentials
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]
tenant_id = os.environ["AZURE_TENANT_ID"]
client_id = os.environ["AZURE_CLIENT_ID"]
client_secret = os.environ["AZURE_CLIENT_SECRET"]

LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + tenant_id)

credential = AdalAuthentication(context.acquire_token_with_client_credentials,
    RESOURCE, client_id, client_secret)

subscription_client = SubscriptionClient(credential)

subscription = next(subscription_client.subscriptions.list())
print(subscription.subscription_id)
```

ADAL 라이브러리가 필요한 경우 `pip install adal`을 실행합니다.

이 방법을 사용하면 Azure 퍼블릭 클라우드 대신 [Azure 소버린 클라우드 또는 국가별 클라우드](/azure/active-directory/develop/authentication-national-cloud)를 사용할 수 있습니다.

```python
from msrestazure.azure_cloud import AZURE_CHINA_CLOUD

# ...

LOGIN_ENDPOINT = AZURE_CHINA_CLOUD.endpoints.active_directory
RESOURCE = AZURE_CHINA_CLOUD.endpoints.active_directory_resource_id
```

[msrestazure.azure_cloud 라이브러리](https://github.com/Azure/msrestazure-for-python/blob/master/msrestazure/azure_cloud.py)에서 `AZURE_PUBLIC_CLOUD`를 적절한 소버린 클라우드 상수로 바꾸기만 하면 됩니다.

### <a name="cli-based-authentication-development-purposes-only"></a>CLI 기반 인증(개발 용도로만)

```python
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import SubscriptionClient

subscription_client = get_client_from_cli_profile(SubscriptionClient)

subscription = next(subscription_client.subscriptions.list())
print(subscription.subscription_id)
```

이 방법에서는 `az login` Azure CLI 명령을 사용하여 로그인한 사용자의 자격 증명을 사용하여 클라이언트 개체를 만듭니다. 애플리케이션에는 사용자로 모든 작업을 수행할 수 있는 권한이 부여됩니다.

SDK에서 기본 구독 ID를 사용하거나 [`az account`](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli)를 사용하여 구독을 설정할 수 있습니다.

로그인한 사용자에게는 일반적으로 소유자 또는 관리자 권한이 있으며 추가 권한 없이 대부분의 리소스에 액세스할 수 있으므로 이 방법은 초기 실험 및 개발 용도로만 사용해야 합니다. 자세한 내용은 [`DefaultAzureCredential`에서 CLI 자격 증명 사용](#cli-auth-note)에 대한 이전 참고 사항을 참조하세요.

### <a name="deprecated-authenticate-with-userpasscredentials"></a>사용되지 않음: UserPassCredentials를 사용하여 인증

[Python용 Azure ADAL(Active Directory 인증 라이브러리)](https://github.com/AzureAD/azure-activedirectory-library-for-python)을 사용하려면 현재 더 이상 사용되지 않는 [`UserPassCredentials`](/python/api/msrestazure/msrestazure.azure_active_directory.userpasscredentials?view=azure-python) 클래스를 사용해야 합니다. 이 클래스는 2단계 인증을 지원하지 않으므로 더 이상 사용할 수 없습니다.

## <a name="see-also"></a>참고 항목

- [Azure를 위한 로컬 Python 개발 환경 구성](configure-local-development-environment.md)
- [역할 권한을 할당하는 방법](how-to-assign-role-permissions.md)
- [예: 리소스 그룹 프로비저닝](azure-sdk-example-resource-group.md)
- [예: Azure Storage 프로비저닝 및 사용](azure-sdk-example-storage.md)
- [예: 웹앱 프로비저닝 및 코드 배포](azure-sdk-example-web-app.md)
- [예: MySQL 데이터베이스 프로비저닝 및 사용](azure-sdk-example-database.md)
- [예: 가상 머신 프로비저닝](azure-sdk-example-virtual-machines.md)
