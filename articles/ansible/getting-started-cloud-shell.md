---
title: 빠른 시작 - Ansible - Azure Cloud Shell 시작
description: 이 빠른 시작에서는 Azure Cloud Shell에서 Bash를 사용하여 다양한 Ansible 작업을 수행하는 방법을 알아봅니다.
keywords: ansible, azure, devops, bash, cloudshell, 플레이북, Bash
ms.topic: quickstart
ms.date: 06/01/2020
ms.openlocfilehash: f48342dc9abdaa1a93979a6026e453f41e0043a7
ms.sourcegitcommit: 7da78b35a847db9929554962dfcc47860f472fb9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133636"
---
# <a name="quickstart-getting-started-with-ansible---azure-cloud-shell"></a>빠른 시작: Ansible - Azure Cloud Shell 시작

[!INCLUDE [annsible-intro.md](includes/ansible-intro.md)]

이 문서에서는 [Azure Cloud Shell](/azure/cloud-shell/overview) 환경에서 Ansible을 시작하는 방법을 설명합니다.

## <a name="configure-your-environment"></a>환경 구성

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Azure Cloud Shell 구성** - Azure Cloud Shell을 처음 접하는 경우 [Azure Cloud Shell의 Bash에 대한 빠른 시작](https://docs.microsoft.com/azure/cloud-shell/quickstart)을 참조하세요.

[!INCLUDE [open-cloud-shell.md](../includes/open-cloud-shell.md)]

## <a name="automatic-credential-configuration"></a>자동 자격 증명 구성

Cloud Shell에 로그인하면 Ansible은 추가 구성없이 인프라를 관리하기 위해 Azure에서 인증을 받습니다. 

여러 구독을 사용하는 경우 `AZURE_SUBSCRIPTION_ID` 환경 변수를 내보내서 Ansible이 사용하는 구독을 지정합니다. 

모든 Azure 구독을 나열하려면 다음 명령을 실행합니다.

```azurecli-interactive
az account list
```

Azure 구독 ID를 사용하여 `AZURE_SUBSCRIPTION_ID`를 다음과 같이 설정합니다.

```console
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>구성 확인
구성이 성공했는지 확인하려면 Ansible을 사용하여 Azure 리소스 그룹을 만듭니다.

[!INCLUDE [create-resource-group-with-ansible.md](includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [빠른 시작: Azure에서 Ansible을 사용하여 가상 머신 구성](./vm-configure.md)
