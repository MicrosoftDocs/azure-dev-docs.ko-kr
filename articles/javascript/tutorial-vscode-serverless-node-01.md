---
title: Visual Studio Code에서 Node.js 형식으로 Azure Functions 배포
description: '서버리스 자습서 1부: 소개 및 필수 구성 요소'
ms.topic: tutorial
ms.date: 09/23/2019
ms.custom: devx-track-js
ms.openlocfilehash: 1d0c1795b7bec0138507ceadea966c421ca12a13
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91364646"
---
# <a name="deploy-azure-functions-from-visual-studio-code"></a>Visual Studio Code에서 Azure Functions 배포

이 자습서에서는 Visual Studio Code와 Azure Functions 확장을 사용하여 JavaScript로 작성된 Azure Functions 애플리케이션을 만들고 배포합니다.

## <a name="walkthrough-video"></a>연습 비디오

이 문서의 내용에 대한 전체 연습을 보려면 이 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Deploy-Azure-Functions-Visual-Studio-Code/player]

## <a name="prerequisites"></a>사전 요구 사항

- [Azure 구독](#azure-subscription).
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- [Node.js 및 npm](https://nodejs.org/en/download)(Node.js 패키지 관리자)

> <a class="tutorial-install-extension-btn" href="https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions">Azure Functions 확장 설치</a>

### <a name="azure-subscription"></a>Azure 구독

Azure 구독이 없는 경우 지금 200달러의 Azure 크레딧으로 체험 계정에 [지금 가입](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-functions-extension&mktingSource=vscode-tutorial-functions-extension)하여 서비스 조합을 사용해 볼 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

## <a name="install-the-azure-functions-core-tools"></a>Azure Functions 핵심 도구 설치

로컬 디버깅을 사용하려면 [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools)를 설치해야 하며 Visual Studio Code에서 직접 수행할 수 있습니다.

1. Visual Studio Code를 시작합니다.

1. **명령 팔레트**(**F1**)를 열고 **Azure Functions: Install or Update Azure Functions Core Tools**(Azure Functions Core Tools 설치 또는 업데이트)를 입력하고 **Enter**를 눌러서 명령을 실행합니다.

1. 설치를 확인하려면 VS Code에서 메뉴 명령 **터미널** > **새 터미널**을 선택한 다음, `func` 명령을 실행합니다. 명령을 실행하면 아래와 같은 출력(사용법 정보와 함께)이 표시됩니다.

    <pre>
                      %%%%%%
                     %%%%%%
                @   %%%%%%    @
              @@   %%%%%%      @@
           @@@    %%%%%%%%%%%    @@@
         @@      %%%%%%%%%%        @@
           @@         %%%%       @@
             @@      %%%       @@
               @@    %%      @@
                    %%
                    %

    Azure Functions Core Tools (2.4.419 Commit hash: c9c1724d002bd90b2e6b41393915ea3a26bcf0ce)
    Function Runtime Version: 2.0.12332.0
    </pre>

> [!div class="nextstepaction"]
> [필수 구성 요소를 설치했습니다.](tutorial-vscode-serverless-node-02.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=getting-started)
