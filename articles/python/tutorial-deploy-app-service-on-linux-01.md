---
title: '자습서: Visual Studio Code에서 Linux의 Azure App Service에 Python 앱 배포'
description: 자습서 1단계, 소개, 필수 구성 요소 및 Azure에 로그인
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 255e94d863f0c144ff4de402a2d775a21645b95a
ms.sourcegitcommit: 815cf2acff71e849735f7afce54723f03ffa5df3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88501378"
---
# <a name="tutorial-deploy-python-apps-to-azure-app-service-on-linux-from-visual-studio-code"></a>자습서: Visual Studio Code에서 Linux의 Azure App Service에 Python 앱 배포

이 문서에서는 Visual Studio Code를 사용하여 [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) 확장을 통해 Linux에서 Azure App Service에 Python 애플리케이션을 배포하는 과정을 안내합니다.

이 자습서의 단계 중 문제가 발생하는 경우 자세한 내용을 알려주시면 감사하겠습니다. 각 문서의 끝에 있는 **문제가 발생했습니다.** 링크를 사용하여 피드백을 제출합니다.

데모 비디오를 보려면 가상 PyCon 2020에서 <a href="https://www.youtube.com/watch?v=dNVvFttc-sA&feature=youtu.be&ocid=AID3006292" target="_blank">VS Code 및 Azure App Service를 사용하여 WebApps 빌드</a>(youtube.com)를 참조하세요.

> [!TIP]
> [Linux의 Azure App Service](/azure/app-service/overview#app-service-on-linux)는 미리 정의된 Docker 컨테이너에서 소스 코드를 실행합니다. 해당 컨테이너는 [Gunicorn](https://gunicorn.org) 웹 서버를 사용하여 Python 3.7 앱을 실행합니다. 이 컨테이너의 특징은 [Linux에서 App Service용 Python 앱 구성](/azure/app-service/configure-language-python)에 설명되어 있습니다. 컨테이너 정의 자체는 [github.com/Azure-App-Service/python](https://github.com/Azure-App-Service/python/tree/master/3.7)에 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Azure 구독](#azure-subscription).
- [Azure App Service 확장이 있는 Visual Studio Code](#visual-studio-code-python-and-the-azure-app-service-extension)
- Python 환경

### <a name="azure-subscription"></a>Azure 구독

Azure 구독이 없는 경우 지금 200달러의 Azure 크레딧으로 체험 계정에 [지금 가입](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-extension&mktingSource=vscode-tutorial-appservice-extension)하여 서비스 조합을 사용해 볼 수 있습니다.

### <a name="visual-studio-code-python-and-the-azure-app-service-extension"></a>Visual Studio Code, Python 및 Azure App Service 확장

다음 소프트웨어를 설치합니다.

- [Visual Studio Code](https://code.visualstudio.com/)
- [VS Code Python 자습서 - 필수 구성 요소](https://code.visualstudio.com/docs/python/python-tutorial)에 설명된 대로 Python 및 [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) 확장
- VS Code 내에서 Azure App Service와 상호 작용을 제공하는 [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) 확장 일반 정보를 보려면 [App Service 확장 자습서](https://code.visualstudio.com/tutorials/app-service-extension/getting-started)를 검색하고 [vscode-azureappservice GitHub 리포지토리](https://github.com/Microsoft/vscode-azureappservice)를 방문하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Azure에 로그인했습니다. - 2단계 진행 >>>](tutorial-deploy-app-service-on-linux-02.md)

[문제가 있나요? 알려주세요.](https://aka.ms/FlaskVSCQuickstartHelp)
