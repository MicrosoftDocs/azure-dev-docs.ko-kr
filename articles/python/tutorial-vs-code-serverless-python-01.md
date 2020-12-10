---
title: '자습서: VS Code를 사용하여 Python에서 서버리스 Azure Functions 만들기 및 배포'
description: 자습서 1단계, 서버리스 Azure Functions에 대한 로컬 환경 구성
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, seo-python-october2019, contperfq2
ms.openlocfilehash: a4fbf9d46a4158fe67660db9f9b7eae8ff83e810
ms.sourcegitcommit: 0cda024089784b92c1db3a4506c1dccd6bfe6339
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96759440"
---
# <a name="tutorial-create-and-deploy-serverless-azure-functions-in-python-with-visual-studio-code"></a>자습서: Visual Studio Code를 사용하여 Python에서 서버리스 Azure Functions 만들기 및 배포

이 문서에서는 Visual Studio Code 및 Azure Functions 확장을 사용하여 Python으로 "서버리스" HTTP 엔드포인트를 만들거나, 스토리지에 연결(또는 "바인딩")도 추가합니다. Visual Studio Code에 대한 Azure Functions 확장은 많은 구성 문제를 자동으로 처리하여 Functions를 사용하는 프로세스를 크게 간소화합니다.

Azure Functions의 서버리스 환경은 Azure가 가상 머신을 프로비저닝하거나 웹앱을 게시하거나 서버 및 리소스를 관리할 필요 없이 앱의 엔드포인트와 공용 URL을 제공함을 의미합니다. Azure는 이러한 모든 리소스를 효율적으로 관리하므로 애플리케이션 호스팅에 드는 오버헤드와 비용이 크게 줄어듭니다. (자세한 내용은 [Azure Functions 개요](/azure/azure-functions/functions-overview)를 참조하세요.)

이 자습서의 단계 중 문제가 발생하는 경우 자세한 내용을 알려주시면 감사하겠습니다. 각 문서의 끝에 있는 **이 페이지** 피드백 단추를 사용합니다.

데모 비디오를 보려면 가상 PyCon 2020에서 <a href="https://www.youtube.com/watch?v=9bMsdBYy-D0&feature=youtu.be&ocid=AID3006292" target="_blank">VS Code로 Azure Functions 빌드</a>(youtube.com)를 참조하세요. 더 긴 세션인 <a href="https://www.youtube.com/watch?v=PV7iy6FPjAY&feature=youtu.be&t=13&ocid=AID3006292" target="_blank">Azure Functions를 사용한 손쉬운 데이터 처리</a>(youtube.com)에 관심이 있을 수도 있습니다.

## <a name="configure-your-environment"></a>환경 구성

- 활성 구독이 있는 Azure 계정이 없는 경우 [체험 계정을 만듭니다](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-functions-extension&mktingSource=vscode-tutorial-functions-extension).

- 다음 섹션의 지침을 따릅니다.

  - [Azure Functions Core Tools 설치](#azure-functions-core-tools).

  - [Azure Functions 확장을 사용하여 Python 및 Visual Studio Code 설치](#visual-studio-code-python-and-the-azure-functions-extension).

  - [Azure에 로그인](#sign-in-to-azure)

  - [환경 확인](#verify-your-environment)
 
### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[Azure Functions Core Tools 작업](/azure/azure-functions/functions-run-local#v2)에서 사용하는 운영 체제에 대한 지침을 따르세요. 이 자습서를 완료하는 데 필요하지 않은 Chocolatey 패키지 관리자에 대한 문서의 설명은 무시합니다.

Node.js를 설치할 때는 기본 옵션을 사용하고 필요한 도구를 자동으로 설치하는 옵션을 선택하지 *않습니다*.  또한 `-g` 옵션을 `npm install` 명령과 함께 사용하여 Core Tools를 후속 명령에서 사용할 수 있도록 해야 합니다.

> [!TIP]
> Core Tools는 .NET Core로 작성되며, Core Tools 패키지는 Node.js 패키지 관리자(npm)를 사용하여 설치하는 것이 가장 좋습니다. 이러한 이유 때문에 Azure Functions에서 Python 코드로 작업하는 경우에도 .NET Core 및 Node.js를 설치해야 합니다. 하지만 앞서 언급한 설명서에 나온 대로 “확장 번들”을 사용하여 .NET Core 요구 사항을 우회할 수 있습니다. 어떤 경우든 이러한 구성 요소를 한 번만 설치해야 하며, 그 후에는 Visual Studio Code에서 업데이트를 설치하라는 메시지가 자동으로 표시됩니다.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code, Python 및 Azure Functions 확장

다음 소프트웨어를 설치합니다.

- Azure Functions에 필요한 64비트 버전의 Python 3.6, 3.7 또는 3.8입니다. [python.org](https://www.python.org/downloads)에서 Python을 설치합니다. 설치할 때 **PATH에 Python 3.x 추가** 를 선택하고 **지금 설치** 옵션을 선택하여 기본 옵션을 사용합니다. Windows에서는 프로세스가 끝날 때 **경로 길이 제한 사용 안 함** 을 선택합니다.
- [Visual Studio Code](https://code.visualstudio.com/)
- [Visual Studio Code Python 자습서 - 필수 구성 요소](https://code.visualstudio.com/docs/python/python-tutorial)에 설명된 [Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python).
- [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). 일반 정보는 [vscode-azurefunctions GitHub 리포지토리](https://github.com/Microsoft/vscode-azurefunctions)를 참조하세요.

    > [!NOTE]
    > Azure Functions 확장은 [Azure Tools 확장 팩](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)에 포함되어 있습니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 확장이 설치되면, **Azure** 탐색기로 이동하여 **Functions** 에서 **Azure에 로그인** 을 선택하고 브라우저에 표시되는 메시지에 따라 Azure 계정에 로그인합니다.

![VS Code를 통해 Azure에 로그인](media/tutorial-vs-code-serverless-python/azure-sign-in.png)

로그인한 후 상태 표시줄에 **Azure: 로그인됨** 이 표시되고 **Azure** 탐색기에 구독이 표시되는지 확인합니다.

![Azure 계정을 보여주는 Visual Studio Code 상태 표시줄](media/tutorial-vs-code-serverless-python/azure-account-status-bar.png)

![구독을 보여주는 Visual Studio Code Azure App Service 탐색기](media/tutorial-vs-code-serverless-python/azure-subscription-view.png)

[!INCLUDE [proxy-config](includes/proxy-config.md)]

### <a name="verify-your-environment"></a>환경 확인

모든 Azure Functions 도구가 설치되어 있는지 확인하려면 Visual Studio Code 명령 팔레트(**F1**)를 열고 **터미널: 새 통합 터미널 만들기** 명령을 선택하고 터미널이 열리면 `func` 명령을 실행합니다.

![Azure Functions 핵심 도구 필수 구성 요소 확인](media/tutorial-vs-code-serverless-python/check-azure-functions-tools-prerequisites-in-visual-studio-code.png)

Azure Functions 로고로 시작하는 출력(출력을 위쪽으로 스크롤 해야 함)은 Azure Functions Core Tools가 존재함을 나타냅니다.

`func` 명령이 인식되지 않으면 `npm install -g azure-functions-core-tools`를 다시 실행하여 설치에 성공했는지 확인합니다. 또한 install 명령에 `-g` 스위치를 사용해야 합니다. 그렇지 않으면 npm이 현재 폴더에만 패키지를 설치합니다.

`func` 명령은 Node.js 글로벌 폴더에 설치된 *func.cmd* 파일을 통해 작동합니다. 이 폴더의 위치를 확인하려면 `npm -l`를 실행하고 출력의 끝에 있는 위치를 살펴봅니다.

> [!div class="nextstepaction"]
> [Azure에 로그인했습니다. - 2단계 진행 >>>](tutorial-vs-code-serverless-python-02.md)

[문제가 있나요? 알려주세요.](https://aka.ms/python-functions-qs-ms-survey)
