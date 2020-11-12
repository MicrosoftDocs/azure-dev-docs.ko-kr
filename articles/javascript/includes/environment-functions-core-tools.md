---
title: completed.md 포함 파일
description: completed.md 포함 파일
ms.topic: include
ms.date: 10/13/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 09f69293062f52dfc2190cfeb040ad45ea428c67
ms.sourcegitcommit: 801682d3fc9651bf95d44e58574d5a4564be6feb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94338514"
---
## <a name="install-the-azure-functions-core-tools"></a>Azure Functions 핵심 도구 설치

로컬 디버깅을 사용하려면 [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools)를 설치해야 하며 Visual Studio Code에서 직접 수행할 수 있습니다.

1. Visual Studio Code를 시작합니다.

1. **명령 팔레트** ( **F1** )를 열고 **Azure Functions: Install or Update Azure Functions Core Tools** (Azure Functions Core Tools 설치 또는 업데이트)를 입력하고 **Enter** 를 눌러서 명령을 실행합니다.

1. 설치를 확인하려면 VS Code에서 메뉴 명령 **터미널** > **새 터미널** 을 선택한 다음, `func` 명령을 실행합니다. 명령을 실행하면 아래와 같은 출력(사용법 정보와 함께)이 표시됩니다.

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