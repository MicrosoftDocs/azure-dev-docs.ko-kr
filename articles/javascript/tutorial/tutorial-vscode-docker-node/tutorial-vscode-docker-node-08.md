---
title: Visual Studio Code에서 컨테이너화된 Node.js 앱을 배포한 후 리소스 정리
description: 'Docker 자습서 8부: 리소스 정리'
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: devx-track-js
ms.openlocfilehash: 47f258ef2b6bb31e7b3a9ee47749c9b0bdd22aaf
ms.sourcegitcommit: f723980ade4cbc13548a5d8ac3f3fa681b8a2dbd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609305"
---
# <a name="part-8-clean-up-resources"></a>8부 리소스 정리

[이전 단계: 로그 스트리밍](tutorial-vscode-docker-node-07.md)

## <a name="delete-app-service-resource-and-resource-group"></a>App Service 리소스 및 리소스 그룹 삭제

컨테이너에 대해 사용자가 만든 App Service에는 비용이 발생할 수 있는 지원 App Service 계획이 포함됩니다. 리소스를 정리하려면 **Azure: App Service** 에서 App Service를 마우스 오른쪽 단추로 클릭하고 **삭제** 를 선택합니다.

또한 [Azure Portal](https://portal.azure.com)을 방문하여 왼쪽 탐색 창에서 **리소스 그룹** 을 선택하고, 이 자습서의 프로세스에서 만든 리소스 그룹을 선택한 다음, **리소스 그룹 삭제** 명령을 사용합니다.

### <a name="next-steps"></a>다음 단계

[!INCLUDE [tutorial-next-steps](../../includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [완료했습니다.](../../how-to/deploy-containers.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-docker-extension&step=clean-up-resources)