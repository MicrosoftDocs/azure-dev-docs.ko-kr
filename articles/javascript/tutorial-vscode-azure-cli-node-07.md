---
title: Azure CLI를 사용하여 Azure에 Node.js 앱을 배포한 후 리소스 정리
description: 자습서 7부, 리소스 정리
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: fb78f369d80ff40fef1d4a21df68ea79fafa2dbc
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88218531"
---
# <a name="part-7-clean-up-resources"></a>7부: 리소스 정리

[이전 단계: 변경 및 재배포](tutorial-vscode-docker-node-06.md)

사용자가 만든 App Service에는 비용을 발생시킬 수 있는 백업 App Service 계획이 포함됩니다. 리소스를 정리하려면 터미널 또는 명령 프롬프트에서 다음 명령을 실행합니다.

```azurecli
az group delete --name myResourceGroup
```

또한 [Azure Portal](https://portal.azure.com)을 방문하여 왼쪽 탐색 창에서 **리소스 그룹**을 선택하고, 이 자습서의 프로세스에서 만든 리소스 그룹을 선택한 다음, **리소스 그룹 삭제** 명령을 사용합니다.

> [!div class="nextstepaction"]
> [완료했습니다.](node-howto-deploy-web-app.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=clean-up-resources)
