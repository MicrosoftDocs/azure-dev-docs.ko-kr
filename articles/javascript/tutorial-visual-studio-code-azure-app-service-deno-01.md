---
title: Azure CLI에서 Azure App Service에 Deno 앱 배포
description: 'Deno 자습서 1부: 소개 및 필수 구성 요소'
ms.topic: tutorial
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: e4c521edd2f23576842d90979813f96a1812f0ec
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91364946"
---
# <a name="deploy-deno-to-azure-app-service-using-visual-studio-code"></a>Visual Studio Code를 사용하여 Azure App Service에 Deno 배포

이 자습서에서는 Azure CLI를 사용하여 Deno 애플리케이션을 Linux 또는 Windows의 Azure App Service에 배포합니다.

## <a name="prerequisites"></a>필수 구성 요소

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

[!INCLUDE [interactive-login](../azure-cli/includes/interactive-login.md)]

로그인하면 Azure 계정에 연결된 구독 목록이 나타납니다. `isDefault: true`가 있는 구독 정보는 로그인한 후 현재 활성화된 구독입니다. 다른 구독을 선택하려면 전환할 구독 ID와 함께 [az account set](/cli/azure/account#az-account-set) 명령을 사용합니다. 구독 선택에 대한 자세한 내용은 [여러 Azure 구독 사용](/cli/azure/manage-azure-subscriptions-azure-cli)을 참조하세요.

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 설치 및 로그인했습니다.](tutorial-visual-studio-code-azure-app-service-deno-02.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=getting-started)

## <a name="next-steps"></a>다음 단계

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [완료했습니다.](node-howto-deploy-web-app.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=clean-up-resources)
