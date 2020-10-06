---
title: Visual Studio Code에서 Azure에 정적 Node.js 웹 사이트 배포
description: '정적 웹앱 자습서 1부: 소개 및 필수 구성 요소'
ms.topic: tutorial
ms.date: 09/23/2019
ms.custom: devx-track-js
ms.openlocfilehash: bb3c0c552a25729e569abeba86d29d75570bb5e6
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91365086"
---
# <a name="deploy-a-static-website-to-azure-from-visual-studio-code"></a>Visual Studio Code에서 Azure에 정적 웹 사이트 배포

이 자습서에서는 [Azure Storage](/azure/storage)를 사용하여 정적 웹 사이트를 만들고 Azure에 배포합니다. 정적 웹 사이트는 HTML, CSS, JavaScript 및 이미지나 글꼴과 같은 기타 정적 파일로 구성됩니다. 정적 사이트는 일반적으로 Angular, React 또는 Vue로 작성된 단일 페이지 애플리케이션(또는 [SPA](https://en.wikipedia.org/wiki/Single-page_application))입니다. 하지만 앱을 디자인하고 웹 서버를 사용하는 대신 _스토리지_에서 직접 이러한 파일을 호스팅하고 제공합니다. 스토리지에서 호스팅하는 것이 웹 서버를 유지 관리하는 것보다 간단하고 비용이 저렴합니다.

## <a name="walkthrough-video"></a>연습 비디오

이 문서의 내용에 대한 전체 연습을 보려면 이 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Deploy-static-website-to-Azure-from-Visual-Studio-Code/player]

> [!NOTE]
> Node.js/Express 앱과 같은 자체 서버 코드가 있으면, 대신 [App Service 자습서](tutorial-vscode-azure-app-service-node-01.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- [Azure 구독](#azure-subscription).
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure Storage 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)
- [Node.js 및 npm](https://nodejs.org/en/download)(Node.js 패키지 관리자) (이 요구 사항은 샘플 프로젝트를 생성하는 데만 사용됩니다. 앱 코드가 이미 있으면 Node.js를 설치할 필요가 없습니다.)

> <a class="tutorial-install-extension-btn" href="https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage">Azure Storage 확장 설치</a>

### <a name="azure-subscription"></a>Azure 구독

Azure 구독이 없는 경우 지금 200달러의 Azure 크레딧으로 체험 계정에 [지금 가입](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-static-website&mktingSource=vscode-tutorial-static-website)하여 서비스 조합을 사용해 볼 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [필수 구성 요소를 설치했습니다.](tutorial-vscode-static-website-node-02.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=getting-started)