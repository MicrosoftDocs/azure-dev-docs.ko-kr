---
title: '자습서: Visual Studio Code에서 Linux의 Azure App Service에 Python 앱 배포'
description: 자습서 1단계, App Service에 대한 환경 구성
ms.topic: conceptual
ms.date: 11/20/2020
ms.custom: devx-track-python, seo-python-october2019
adobe-target: true
ms.openlocfilehash: f73f968849cdf6e9324074427f6156a706d65dc0
ms.sourcegitcommit: b380f6e637b47e6e3822b364136853e1d342d5cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100395348"
---
# <a name="tutorial-deploy-python-apps-to-azure-app-service-on-linux-from-visual-studio-code"></a>자습서: Visual Studio Code에서 Linux의 Azure App Service에 Python 앱 배포

이 문서에서는 Visual Studio Code를 사용하여 [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) 확장을 통해 Linux에서 Azure App Service에 Python 애플리케이션을 배포하는 과정을 안내합니다.

이 자습서의 단계 중 문제가 발생하는 경우 자세한 내용을 알려주시면 감사하겠습니다. 각 문서의 끝에 있는 **문제가 있나요? 알려주세요.** 링크를 사용하여 피드백을 제출합니다.

데모 비디오를 보려면 가상 PyCon 2020에서 <a href="https://www.youtube.com/watch?v=dNVvFttc-sA&feature=youtu.be&ocid=AID3006292" target="_blank">VS Code 및 Azure App Service를 사용하여 WebApps 빌드</a>(youtube.com)를 참조하세요.

> [!NOTE]
> CLI를 통해 앱을 배포하려면 **[빠른 시작: Azure App Service on Linux에서 Python 앱 만들기](/azure/app-service/quickstart-python)** 를 참조하세요.

> [!TIP]
> [Linux의 Azure App Service](/azure/app-service/overview#app-service-on-linux)는 미리 정의된 Docker 컨테이너에서 소스 코드를 실행합니다. 해당 컨테이너는 [Gunicorn](https://gunicorn.org) 웹 서버를 사용하여 Python 3.6 이상 앱을 실행합니다. 이 컨테이너의 특징은 [Linux에서 App Service용 Python 앱 구성](/azure/app-service/configure-language-python)에 설명되어 있습니다. 컨테이너 정의는 [github.com/Azure-App-Service/python](https://github.com/Azure-App-Service/python/tree/master/)에 있습니다.

## <a name="configure-your-environment"></a>환경 구성

- 활성 구독이 있는 Azure 계정이 없는 경우 [체험 계정을 만듭니다](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-extension&mktingSource=vscode-tutorial-appservice-extension).

- [로컬에 Python 3.7 또는 3.8이 설치](https://python.org/downloads)되어 있는지 확인합니다. 버전을 확인하려면 다음 명령을 실행합니다.

    ```bash
    python --version
    ```

- 다음 소프트웨어를 설치합니다.
  - [Visual Studio Code](https://code.visualstudio.com/)
  - [VS Code Python 자습서 - 필수 구성 요소](https://code.visualstudio.com/docs/python/python-tutorial)에 설명된 대로 Python 및 [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) 확장
  - VS Code 내에서 Azure App Service와 상호 작용을 제공하는 [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) 확장 일반 정보를 보려면 [App Service 확장 자습서](https://code.visualstudio.com/tutorials/app-service-extension/getting-started)를 검색하고 [vscode-azureappservice GitHub 리포지토리](https://github.com/Microsoft/vscode-azureappservice)를 방문하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Azure에 로그인했습니다. - 2단계 진행 >>>](tutorial-deploy-app-service-on-linux-02.md)

[문제가 있나요? 알려주세요.](https://aka.ms/FlaskVSCQuickstartHelp)
