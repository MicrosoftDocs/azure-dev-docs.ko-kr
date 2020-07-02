---
title: Visual Studio Code에서 변경 후 컨테이너를 Azure App Service에 다시 배포
description: 자습서 6단계, 컨테이너 이미지를 다시 빌드하고 다시 배포하는 간단한 단계입니다.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 7920bc9ddb2b9b7cc06f936fb97400a5c1d9dd7d
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85792983"
---
# <a name="make-changes-and-redeploy"></a>변경 및 재배포

[이전 단계: 앱 이미지 배포](tutorial-vscode-docker-node-05.md)

불가피하게 앱을 변경했으므로 컨테이너를 여러 번 다시 빌드하고 다시 배포하게 됩니다. 다행히 프로세스는 간단합니다.

1. 앱을 로컬에서 변경하고 테스트합니다.

1. Visual Studio Code에서 **명령 팔레트**(**F1**)를 열고 **Docker 이미지: 이미지 빌드**를 실행하여 이미지를 다시 빌드합니다. 앱 코드만 변경하는 경우 빌드는 몇 초 밖에 걸리지 않습니다.

1. 이미지를 레지스트리로 푸시하려면 **명령 팔레트**(**F1**)를 다시 열고**Docker 이미지: 푸시**를 실행하여 방금 만든 이미지를 선택합니다. 이전과 마찬가지로 앱 코드 변경이 적기 때문에 해당 레이어만 푸시하면 되고, 이 프로세스는 일반적으로 몇 초 안에 완료됩니다.

1. **Azure: App Service** 탐색기에서 적절한 App Service를 마우스 오른쪽 단추로 클릭하고 **다시 시작**을 선택합니다. 앱 서비스를 다시 시작하면 레지스트리에서 최신 컨테이너 이미지를 자동으로 가져옵니다.

1. 약 15-20초 후 App Service URL을 다시 방문하여 업데이트를 확인합니다.

> [!div class="nextstepaction"]
> [변경 내용이 보입니다.](tutorial-vscode-docker-node-07.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-docker-extension&step=deploy-changes)
