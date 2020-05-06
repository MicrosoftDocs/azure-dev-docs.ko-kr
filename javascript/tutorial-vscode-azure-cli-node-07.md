---
title: Azure CLI를 사용하여 Azure에 Node.js 앱을 배포한 후 리소스 정리
description: 자습서 7부, 리소스 정리
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 183539b8e2f0246bd812e5fa364a885b75626819
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "77709860"
---
# <a name="clean-up-resources"></a>리소스 정리

[이전 단계: 변경 및 재배포](tutorial-vscode-docker-node-06.md)

사용자가 만든 App Service에는 비용을 발생시킬 수 있는 백업 App Service 계획이 포함됩니다. 리소스를 정리하려면 터미널 또는 명령 프롬프트에서 다음 명령을 실행합니다.

```azurecli
az group delete --name myResourceGroup
```

또한 [Azure Portal](https://portal.azure.com)을 방문하여 왼쪽 탐색 창에서 **리소스 그룹**을 선택하고, 이 자습서의 프로세스에서 만든 리소스 그룹을 선택한 다음, **리소스 그룹 삭제** 명령을 사용합니다.

> [!div class="nextstepaction"]
> [완료했습니다.](node-howto-deploy-web-app.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=clean-up-resources)
