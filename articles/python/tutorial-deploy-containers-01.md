---
title: '자습서: Visual Studio Code를 사용하여 Azure App Service에 Docker 컨테이너 배포'
description: 자습서 1단계, 컨테이너에 대한 환경 구성
ms.topic: conceptual
ms.date: 09/17/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 447643662ef3c839823d81d29cfb55cc2ee442b2
ms.sourcegitcommit: 69933dcce571b2686897b295b7822e207d944617
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2020
ms.locfileid: "90772613"
---
# <a name="tutorial-deploy-docker-containers-to-azure-app-service-with-visual-studio-code"></a>자습서: Visual Studio Code를 사용하여 Azure App Service에 Docker 컨테이너 배포

이 문서에서는 Visual Studio Code를 사용하여 컨테이너 레지스트리의 컨테이너 이미지를 [Azure App Service](/azure/app-service/)에 배포하는 과정을 안내합니다.

이 자습서의 단계 중 문제가 발생하는 경우 자세한 내용을 알려주시면 감사하겠습니다. 각 문서의 끝에 있는 **이 페이지** 피드백 단추를 사용합니다.

관련 데모 비디오를 보려면 가상 PyCon 2020에서 <a href="https://www.youtube.com/watch?v=t79HDLC5kQA&feature=youtu.be&ocid=AID3006292" target="_blank">VS Code 개발 컨테이너의 Django 앱</a>(youtube.com)을 참조하세요.

## <a name="configure-your-environment"></a>환경 구성

- [Azure 계정](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
- [Visual Studio Code](https://code.visualstudio.com/)
- 컨테이너 레지스트리에 업로드된 적절한 컨테이너입니다. Python 웹앱을 사용하여 컨테이너를 만드는 방법에 대한 자세한 내용은 [컨테이너의 Python](https://code.visualstudio.com/docs/containers/quickstart-python)에서 찾을 수 있습니다.
- [VS Code용 Azure App Service 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice).
- [VS Code용 Docker 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker).

## <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Azure에 로그인했습니다. - 2단계 진행 >>>](tutorial-deploy-containers-02.md)
