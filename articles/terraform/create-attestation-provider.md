---
title: Terraform을 사용하여 Azure 증명 공급자 구성
description: Terraform을 사용하여 Azure에 증명 공급자를 만드는 방법에 대해 알아봅니다.
keywords: azure devops terraform attestation
ms.topic: how-to
ms.date: 11/08/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: c42f2a00886bedf3e9f26566cb619c69bb5073b7
ms.sourcegitcommit: f723980ade4cbc13548a5d8ac3f3fa681b8a2dbd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609399"
---
# <a name="configure-an-azure-attestation-policy-using-terraform"></a>Terraform을 사용하여 Azure Attestation 정책 구성

이 문서에서는 Azure에서 [증명 공급자](https://docs.microsoft.com/azure/attestation/overview)를 만들기 위한 Terraform 코드 예제를 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- *정책 서명 인증서*: *.pem 파일 형식의 신뢰할 수 있는 서명 키 세트를 지정하는 파일입니다.

[!INCLUDE [terraform-configure-environment.md](includes/terraform-configure-environment.md)]

## <a name="configure-an-azure-attestation-provider"></a>Azure 증명 공급자 구성

```hcl
resource "azurerm_resource_group" "corpAttestation" {
  name                        = "corp-attestation"
  location                    = "westus"
}

resource "azurerm_attestation_provider" "corpAttestation" {
  name                              = "attestationprovider007"
  resource_group_name               = azurerm_resource_group.corpAttestation.name
  location                          = azurerm_resource_group.corpAttestation.location

  policy_signing_certificate_data   = file("./certs/cert.pem")
}
```

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure에서 Terraform을 사용하는 방법에 대해 자세히 알아보기](/azure/terraform)
