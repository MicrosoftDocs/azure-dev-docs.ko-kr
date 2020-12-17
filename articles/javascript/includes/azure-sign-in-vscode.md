---
ms.custom: devx-track-js
ms.topic: include
ms.date: 10/16/2020
ms.openlocfilehash: 624ba9739549b260018e05450598d17460f2e5d8
ms.sourcegitcommit: c1ef7aa8ed2e88e98b190e42cffde52cf301958d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97096365"
---
Azure 서비스 확장을 이미 사용하는 경우 이미 로그인되어 있으며 이 단계를 건너뛸 수 있습니다. Azure 서비스 확장을 사용하지 않는 경우 이 섹션을 계속 진행하여 Azure에 로그인합니다.

Azure 확장이 Visual Studio Code에 설치되면 **Azure** 탐색기로 이동하여 Azure 계정에 로그인하고, **Azure에 로그인** 을 선택하고, 표시되는 메시지를 따릅니다. (Azure 확장이 여러 개 설치되어 있으면, 작업 중인 영역에 맞는 확장(예: App Service, Functions)을 선택합니다.)

![VS Code를 통해 Azure에 로그인](../media/deploy-azure/azure-sign-in.png)

로그인한 후에는 Azure 계정의 이메일 주소(또는 "로그인함")가 상태 표시줄에 표시되고 구독이 **Azure** 탐색기에 표시되는지 확인합니다.

![Azure 계정을 보여 주는 VS Code 상태 표시줄](../media/deploy-azure/azure-account-status-bar.png)

![구독을 보여 주는 VS Code Azure 탐색기](../media/deploy-azure/azure-subscription-view.png)

> [!NOTE]
> **"이름이 [구독 ID]인 구독을 찾을 수 없습니다"** 오류가 표시되면 프록시를 사용하고 Azure API에 연결할 수 없기 때문일 수 있습니다. 터미널의 프록시 정보를 사용하여 `HTTP_PROXY` 및 `HTTPS_PROXY` 환경 변수를 구성합니다.
>
> # <a name="bash"></a>[bash](#tab/bash)
>
> ```bash
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> # <a name="powershell"></a>[PowerShell](#tab/powershell)
>
> ```powershell
> $env: HTTPS_PROXY = "https://username:password@proxy:8080"
> $env: HTTP_PROXY = "http://username:password@proxy:8080"
> ```
>
> # <a name="cmd"></a>[Cmd](#tab/cmd)
>
> ```cmd
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ---
