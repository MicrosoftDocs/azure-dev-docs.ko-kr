---
title: '3단계: Visual Studio Code에서 변경 후 컨테이너를 Azure App Service에 다시 배포'
description: 자습서 3단계, 컨테이너 이미지를 다시 빌드하고 다시 배포하는 간단한 단계입니다.
ms.topic: conceptual
ms.date: 12/09/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: f760720755a170e6ff47c5971384f3f83b2b07be
ms.sourcegitcommit: c8330128d5d6a71859933a890ecdf047cb950996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97522097"
---
# <a name="2-redeploy-a-container-to-azure-app-service-after-making-changes"></a>2: 변경 후 컨테이너를 Azure App Service에 다시 배포

[이전 단계: Azure에 이미지 배포](tutorial-deploy-containers-02.md)

이 문서에서는 Visual Studio Code에서 변경 후 컨테이너를 Azure App Service에 다시 배포하는 방법을 설명합니다.

불가피하게 앱을 변경했으므로 컨테이너를 여러 번 다시 빌드하고 다시 배포하게 됩니다. 다행히 프로세스는 간단합니다.

1. 앱을 로컬에서 변경하고 테스트합니다. (이 단계와 다음 단계는 자습서, [VS Code에서 Python 컨테이너 만들기](https://code.visualstudio.com/docs/python/tutorial-create-containers)에 설명되어 있습니다.)

1. Docker 이미지를 다시 빌드합니다. 앱 코드만 변경하는 경우 빌드는 몇 초 밖에 걸리지 않습니다.

1. 이미지를 레지스트리에 밀어넣습니다. 앱 코드를 다시 변경하는 경우에는 해당 작은 레이어만 밀어넣어야 하며 프로세스는 일반적으로 몇 초 내에 완료됩니다.

1. **Azure: App Service** 탐색기에서 적절한 App Service를 마우스 오른쪽 단추로 클릭하고 **다시 시작** 을 선택합니다. 앱 서비스를 다시 시작하면 레지스트리에서 최신 컨테이너 이미지를 자동으로 가져옵니다.

1. 약 15-20초 후 App Service URL을 다시 방문하여 업데이트를 확인합니다.

> [!div class="nextstepaction"]
> [변경하고 다시 배포했습니다. - 4단계 진행 >>>](tutorial-deploy-containers-04.md)
