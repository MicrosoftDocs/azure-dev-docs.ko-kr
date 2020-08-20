---
title: '6단계: Azure App Service에서 VS Code로 로그 스트리밍'
description: 자습서 6단계, 앱 로그를 Visual Studio Code로 스트리밍
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: a303976143b2b2579c23bde8eb320ee4dc902dba
ms.sourcegitcommit: 815cf2acff71e849735f7afce54723f03ffa5df3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88501418"
---
# <a name="6-stream-logs-from-azure-app-service-into-visual-studio-code"></a>6: Azure App Service에서 Visual Studio Code로 로그 스트리밍

[이전 단계: 앱 배포](tutorial-deploy-app-service-on-linux-05.md)

이 절차에 따라 Azure App Service에서 Visual Studio Code로 로그를 스트리밍할 수 있습니다.

1. Visual Studio Code에서 **Azure: App Service** 탐색기를 열고, App Service를 마우스 오른쪽 단추로 클릭하고, **로그 스트리밍 시작**을 선택합니다.

   ![App Service 탐색기에서 스트리밍 로그 시작](media/deploy-azure/start-streaming-logs-in-visual-studio-code.png)

1. 파일 로깅을 사용하도록 설정하고 웹앱을 다시 시작하라는 메시지가 표시되면 **예**를 선택합니다. 앱이 다시 시작되는 동안 VS Code의 **출력** 창에 진행률이 표시됩니다. 로깅 사용은 일회성 프로세스입니다.

1. 로깅을 사용하도록 설정한 후 App Service를 마우스 오른쪽 단추로 클릭하고 **로그 스트리밍 시작**을 다시 선택합니다. VS Code의 **출력** 창에 "라이브 로그 스트림 시작"이 표시되고 로그 출력이 표시되기 시작합니다. 브라우저에서 웹앱을 새로 고쳐 추가 로그 정보를 생성해 보세요.

1. (로깅을 사용하지 않고) 로그 스트리밍을 중지하려면 **Azure: App Service** 탐색기에서 앱을 마우스 오른쪽 단추로 클릭하고 **로그 스트리밍 중지**를 선택합니다.

> [!div class="nextstepaction"]
> [로그가 보입니다. - 7단계 진행 >>>](tutorial-deploy-app-service-on-linux-07.md)

[문제가 있나요? 알려주세요.](https://aka.ms/FlaskVSCQuickstartHelp)
