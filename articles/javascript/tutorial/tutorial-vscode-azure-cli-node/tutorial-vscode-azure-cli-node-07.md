---
title: Azure CLI를 사용하여 Azure에 Node.js 앱을 배포한 후 리소스 정리
description: '자습서 7부: Azure CLI - 리소스 정리'
ms.topic: tutorial
ms.date: 09/24/2019
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: d2b45ae60a7ad1270547289d1ea8480d14fedd95
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102117967"
---
# <a name="part-7-clean-up-resources"></a>7부: 리소스 정리

[이전 단계: 변경 및 재배포](tutorial-vscode-azure-cli-node-05.md)

사용자가 만든 App Service에는 비용을 발생시킬 수 있는 백업 App Service 계획이 포함됩니다. 리소스를 정리하려면 터미널 또는 명령 프롬프트에서 다음 명령을 실행합니다.

```azurecli
az group delete --name myResourceGroup
```

> [!div class="nextstepaction"]
> [완료했습니다.](../../how-to/deploy-web-app.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=clean-up-resources)