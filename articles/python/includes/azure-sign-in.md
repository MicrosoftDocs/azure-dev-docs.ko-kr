---
ms.openlocfilehash: c78b25e2111e48ed9337bcd85f6585c4bdd4a081
ms.sourcegitcommit: 418e446e6ada5d50df283401df4f6b6370a356b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035519"
---
Azure 확장을 설치했으면 Azure 계정에 로그인합니다.

1. **Azure** 탐색기로 이동합니다.
1. **Azure에 로그인** 을 선택하고 화면의 지시를 따릅니다. (Azure 확장이 여러 개 설치되어 있으면, 작업 중인 영역에 맞는 확장(예: App Service, Functions)을 선택합니다.)

    ![VS Code를 통해 Azure에 로그인](../media/deploy-azure/sign-in-to-azure-through-visual-studio-code.png)

1. 로그인한 후 상태 표시줄에 **Azure: 로그인됨** 이 표시되고 **Azure** 탐색기에 구독이 표시되는지 확인합니다.

    ![Azure 계정을 보여주는 Visual Studio Code 상태 표시줄](../media/deploy-azure/azure-account-status-bar-in-visual-studio-code.png)

    ![구독을 보여주는 Visual Studio Code Azure App Service 탐색기](../media/deploy-azure/view-azure-subscription-in-visual-studio-code-app-service-explorer.png)

> [!NOTE]
> **"이름이 [구독 ID]인 구독을 찾을 수 없습니다"** 오류가 표시되면 프록시를 사용하고 Azure API에 연결할 수 없기 때문일 수 있습니다. 터미널의 프록시 정보를 사용하여 `HTTP_PROXY` 및 `HTTPS_PROXY` 환경 변수를 구성합니다.
>
> ```cmd
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
