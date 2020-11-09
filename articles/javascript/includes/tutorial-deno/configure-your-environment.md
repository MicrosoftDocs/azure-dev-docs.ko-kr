---
title: 포함 파일 1
description: 포함 파일 1
ms.topic: include
ms.date: 06/01/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 8f0bda8929699f61fd3c79f9ae6fb38762e622c9
ms.sourcegitcommit: 5541f993c01ce356e1b0eaa8f95aea9051c3c21e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93278665"
---
- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-deno&mktingSource=vscode-tutorial-appservice-deno)
- [Visual Studio Code](https://code.visualstudio.com/)
- [Deno](https://deno.land/#installation)
- Azure CLI 설치 및 로그인

## <a name="install-or-run-in-azure-cloud-shell"></a>Azure Cloud Shell에서 설치 또는 실행

Azure CLI를 시작하는 가장 쉬운 방법은 브라우저를 통해 Azure Cloud Shell 환경에서 실행하는 것입니다. Cloud Shell에 대해 자세히 알아보려면 [Azure Cloud Shell의 Bash에 대한 빠른 시작](/azure/cloud-shell/quickstart)을 참조하세요.

CLI를 설치할 준비가 되었으면 [설치 지침](/cli/azure/install-azure-cli)을 참조하세요.

CLI가 처음으로 설치되면 `az --version`을 실행하여 CLI가 설치되어 있고 올바른 버전인지 확인합니다.

> [!NOTE]
> Azure 클래식 배포 모델을 사용하는 경우 [Azure클래식 CLI를 설치합니다](/cli/azure/install-classic-cli).

## <a name="sign-in"></a>로그인

로컬 설치에 CLI 명령을 사용하려면 먼저 [az login](/cli/azure/reference-index#az-login)으로 로그인해야 합니다.

[!INCLUDE [interactive-login](../../../azure-cli/includes/interactive-login.md)]

로그인하면 Azure 계정에 연결된 구독 목록이 나타납니다. `isDefault: true`가 있는 구독 정보는 로그인한 후 현재 활성화된 구독입니다. 다른 구독을 선택하려면 전환할 구독 ID와 함께 [az account set](/cli/azure/account#az-account-set) 명령을 사용합니다. 구독 선택에 대한 자세한 내용은 [여러 Azure 구독 사용](/cli/azure/manage-azure-subscriptions-azure-cli)을 참조하세요.
