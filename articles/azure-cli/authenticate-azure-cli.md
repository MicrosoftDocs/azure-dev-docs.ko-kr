---
title: Azure CLI로 로그인
description: Azure CLI를 사용하여 대화형으로 로그인 또는 로컬 자격 증명을 사용하여 로그인
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 26112c9a7f338a7f178dc627d89f6e1f33d97714
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82030865"
---
# <a name="sign-in-with-azure-cli"></a>Azure CLI로 로그인 

Azure CLI에 대한 인증 유형은 여러 가지가 있습니다. 시작하는 가장 쉬운 방법은 [Azure Cloud Shell](/azure/cloud-shell/overview)을 사용하는 것으로서, 자동으로 로그인합니다.
로컬에서는, [az 로그인](/cli/azure/reference-index#az-login) 명령을 사용하여 브라우저를 통해 대화형으로 로그인할 수 있습니다. 스크립트를 작성할 때 서비스 주체를 사용하는 것이 좋습니다. 서비스 주체에게 필요한 접근 권한만 부여하여 자동화를 안전하게 보호할 수 있습니다.

CLI에 의해 저장된 로그인 정보가 없습니다. 대신 [인증 새로 고침 토큰](https://docs.microsoft.com/azure/active-directory/develop/v1-id-and-access-tokens#refresh-tokens)이 Azure에서 생성되고 저장됩니다. 2018년 8월부터 비활성 기간이 90일 지나면 이 토큰이 철회되지만, Microsoft 또는 테넌트 관리자가 이 값을 변경할 수 있습니다. 토큰이 철회되면 CLI에서 다시 로그인하라는 메시지가 표시됩니다.

로그인한 후 CLI 명령은 기본 구독에 대해 실행됩니다. 구독이 두 개 이상인 경우 [기본 구독 변경](manage-azure-subscriptions-azure-cli.md)을 수행할 수 있습니다.

## <a name="sign-in-interactively"></a>대화형으로 로그인

Azure CLI의 기본 인증 방법은 웹 브라우저와 액세스 토큰으로 로그인하는 것입니다.

[!INCLUDE [interactive_login](includes/interactive-login.md)]

## <a name="sign-in-with-credentials-on-the-command-line"></a>명령줄에 자격 증명을 입력하여 로그인합니다.

명령줄에 Azure 사용자 자격 증명을 입력합니다.

> [!Note]
> 이 접근 방식은 Microsoft 계정 또는 2단계 인증을 사용하는 계정에서는 작동하지 않습니다.

```azurecli-interactive
az login -u <username> -p <password>
```

> [!IMPORTANT]
> 콘솔에 암호를 표시하지 않고 `az login`을 대화식으로 사용하려는 경우 `bash`에서 `read -s` 명령을 사용합니다.
>
> ```bash
> read -sp "Azure password: " AZ_PASS && echo && az login -u <username> -p $AZ_PASS
> ```
>
> PowerShell에서 `Get-Credential` cmdlet을 사용합니다.
>
> ```powershell
> $AzCred = Get-Credential -UserName <username>
> az login -u $AzCred.UserName -p $AzCred.GetNetworkCredential().Password
> ```

## <a name="sign-in-with-a-service-principal"></a>서비스 주체를 사용하여 로그인

서비스 주체는 특정 사용자에게 연결되지 않은 계정으로서, 미리 정의된 역할을 통해 사용자에게 권한을 할당할 수 있습니다. 보안 스크립트 또는 프로그램 작성을 위한 가장 좋은 방법은 권한 제한 사항 및 로컬로 저장된 정적 자격 증명 정보를 적용할 수 있게 해주는 서비스 주체를 사용한 인증입니다. 서비스 주체에 대해 자세히 알아보려면 [Azure CLI를 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal)를 참조하십시오.

서비스 주체를 사용하여 로그인하려면 다음이 필요합니다.

* 서비스 주체와 연결된 URL 또는 이름
* PEM 형식에서 서비스 주체를 만드는 데 사용되는 서비스 주체 암호, 또는 X509 인증서
* `.onmicrosoft.com` 도메인 또는 Azure 개체 ID로서 서비스 주체를 사용하여 연결하는 테넌트

> [!NOTE]
> **인증서**는 PEM 파일 내의 **프라이빗 키**에 추가해야 합니다.  PEM 파일 형식의 예는 [Azure CLI를 사용하여 Azure 서비스 사용자 만들기](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal)를 참조하세요. 
>

> [!IMPORTANT]
>
> 서비스 주체에서 Key Vault에 저장된 인증서를 사용하는 경우 Azure에 로그인하지 않고 해당 인증서의 프라이빗 키를 사용할 수 있어야 합니다. 오프라인으로 사용할 프라이빗 키를 검색하려면 [az keyvault secret show](/cli/azure/keyvault/secret)를 사용하세요.

```azurecli-interactive
az login --service-principal -u <app-url> -p <password-or-cert> --tenant <tenant>
```

> [!IMPORTANT]
> 콘솔에 암호를 표시하지 않고 `az login`을 대화식으로 사용하려는 경우 `bash`에서 `read -s` 명령을 사용합니다.
>
> ```bash
> read -sp "Azure password: " AZ_PASS && echo && az login --service-principal -u <app-url> -p $AZ_PASS --tenant <tenant>
> ```
>
> PowerShell에서 `Get-Credential` cmdlet을 사용합니다.
>
> ```powershell
> $AzCred = Get-Credential -UserName <app-url>
> az login --service-principal -u $AzCred.UserName -p $AzCred.GetNetworkCredential().Password --tenant <tenant>
> ```

## <a name="sign-in-with-a-different-tenant"></a>다른 테넌트로 로그인

`--tenant` 인수를 사용하여 로그인할 테넌트를 선택할 수 있습니다. 이 인수의 값은 `.onmicrosoft.com` 도메인 또는 테넌트에 대한 Azure 개체 ID일 수 있습니다. 대화형 및 명령줄 로그인 방법 모두 `--tenant`(으)로 작동합니다.

```azurecli-interactive
az login --tenant <tenant>
```

## <a name="sign-in-with-a-managed-identity"></a>관리 ID를 사용하여 로그인

Azure 리소스에 대한 관리 ID로 구성된 리소스에서 관리 ID를 사용하여 로그인할 수 있습니다. 리소스의 ID로 로그인하는 것은 `--identity` 플래그를 통해 이루어집니다.

```azurecli-interactive
az login --identity
```

Azure 리소스에 대한 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID 구성](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm) 및 [로그인에 대한 Azure 리소스를 위한 관리 ID 사용](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in)을 참조하십시오.
