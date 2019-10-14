---
title: '자습서: Visual Studio Code를 사용하여 Python에서 서버리스 Azure Functions 만들기 및 배포'
description: 자습서 1단계로, 소개 및 필수 구성 요소입니다.
services: functions
author: kraigb
manager: barbkess
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: kraigb
ms.custom: seo-python-october2019
ms.openlocfilehash: 792c9962d738a8e70f29d5df78c44b6303a63b77
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172295"
---
# <a name="tutorial-create-and-deploy-serverless-azure-functions-in-python-with-visual-studio-code"></a>자습서: Visual Studio Code를 사용하여 Python에서 서버리스 Azure Functions 만들기 및 배포

이 문서에서는 Visual Studio Code 및 Azure Functions 확장을 사용하여 Python으로 서버리스 HTTP 엔드포인트를 만들거나, 스토리지에 연결(또는 “바인딩”)도 추가합니다. Azure Functions는 가상 머신을 프로비저닝하거나 웹앱을 게시할 필요가 없는 서버리스 환경에서 코드를 실행합니다. Visual Studio Code에 대한 Azure Functions 확장은 많은 구성 문제를 자동으로 처리하여 Functions를 사용하는 프로세스를 크게 간소화합니다.

이 자습서의 단계 중 문제가 발생하는 경우 자세한 내용을 알려주시면 감사하겠습니다. 각 문서의 끝에 있는 **문제가 발생했습니다.** 링크를 사용하여 피드백을 제출해주세요.

## <a name="prerequisites"></a>필수 조건

- [Azure 구독](#azure-subscription).
- [Azure Functions 확장을 사용하는 Visual Studio Code](#visual-studio-code-python-and-the-azure-functions-extension).
- [Azure Functions Core Tools](#azure-functions-core-tools).

### <a name="azure-subscription"></a>Azure 구독

Azure 구독이 없는 경우 지금 200달러의 Azure 크레딧으로 30일 체험 계정에 [지금 가입](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-functions-extension&mktingSource=vscode-tutorial-functions-extension)하여 서비스 조합을 사용해 볼 수 있습니다.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code, Python 및 Azure Functions 확장

다음 소프트웨어를 설치합니다.

- Azure Functions에 필요한 Python 3.6.x. [Python 3.6.8](https://www.python.org/downloads/release/python-368/)은 최신 3.6.x 버전입니다.
- [Visual Studio Code](https://code.visualstudio.com/)
- [Visual Studio Code Python 자습서 - 필수 구성 요소](https://code.visualstudio.com/docs/python/python-tutorial)에 설명된 [Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python).
- [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). 일반 정보는 [vscode-azurefunctions GitHub 리포지토리](https://github.com/Microsoft/vscode-azurefunctions)를 참조하세요.

> [!NOTE]
> Azure Functions 확장은 [Azure Tools 확장 팩](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)에 포함되어 있습니다.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[Azure Functions Core Tools 작업](/azure/azure-functions/functions-run-local#v2)에서 사용하는 운영 체제에 대한 지침을 따르세요. 도구 자체는 .NET Core로 작성되며, Core Tools 패키지는 Node.js 패키지 관리자(npm)를 사용하여 설치하는 것이 가장 좋습니다. 이러한 이유때문에 Python 코드의 경우에도 .NET Core 및 Node.js를 설치해야 합니다. 하지만 앞서 언급한 설명서에 나온 대로 “확장 번들”을 사용하여 .NET Core 요구 사항을 우회할 수 있습니다. 어떤 경우든 이러한 구성 요소를 한 번만 설치해야 하며, 그 후에는 Visual Studio Code에서 업데이트를 설치하라는 메시지가 자동으로 표시됩니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

### <a name="verify-prerequisites"></a>필수 조건 확인

모든 Azure Functions 도구가 설치되어 있는지 확인하려면 Visual Studio Code 명령 팔레트(**F1**)를 열고 **터미널: 새 통합 터미널 만들기** 명령을 선택하고 터미널이 열리면 `func` 명령을 실행합니다.

![Azure Functions 핵심 도구 필수 구성 요소 확인](media/tutorial-vs-code-serverless-python/check-prereqs.png)

Azure Functions 로고로 시작하는 출력(출력을 위쪽으로 스크롤 해야 함)은 Azure Functions Core Tools가 존재함을 나타냅니다.

`func` 명령이 인식되지 않는 경우 Azure Functions Core Tools를 설치한 폴더가 사용자의 PATH 환경 변수에 포함되어 있는지 확인합니다.

> [!div class="nextstepaction"]
> [Azure에 로그인되어 있음](tutorial-vs-code-serverless-python-02.md)

[문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=01-verify-prerequisites)
