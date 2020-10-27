---
title: azure-sign-in.md 포함 파일
description: azure-sign-in.md 포함 파일
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: 085958cd12352273d5433279bae5f3edac934714
ms.sourcegitcommit: ced8331ba36b28e6e2eacd23a64b39ddc7ffe6ab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92344217"
---
[Azure Storage 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)이 Visual Studio Code에 설치되면 **Azure** 탐색기로 이동하여 Azure 계정에 로그인하고, **Azure에 로그인** 을 선택하고, 표시되는 메시지를 따릅니다. 

![VS Code를 통해 Azure에 로그인](../../media/tutorial-browser-file-upload/azure-sign-in.png)

로그인한 후에는 Azure 계정의 이메일 주소(또는 "로그인함")가 상태 표시줄에 표시되고 구독이 **Azure** 탐색기에 표시되는지 확인합니다.

![Azure 계정을 보여 주는 VS Code 상태 표시줄](../../media/tutorial-browser-file-upload/azure-account-status-bar.png)

![구독을 보여 주는 VS Code Azure 탐색기](../../media/tutorial-browser-file-upload/azure-subscription-view.png)

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
