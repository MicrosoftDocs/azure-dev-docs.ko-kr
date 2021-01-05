---
title: Azure 리소스 제거
description: Azure CLI 명령으로 리소스 그룹을 제거하여 비용이 발생할 수 있는 리소스를 정리합니다.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 132ccc26a4ddf17eb38be1573f462492fe1f7f0c
ms.sourcegitcommit: 1c508f5ba73a12e4baeacc88ad9a8359301acb50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97687493"
---
# <a name="7-clean-up-resources-for-static-web-app"></a>7. 정적 웹앱에 대한 리소스 정리

이 자습서를 완료한 후에는 사용 요금이 청구되지 않도록 Computer Vision 리소스와 Static 웹앱을 비롯한 리소스 그룹을 제거해야 합니다. 

## <a name="remove-all-the-resources-by-removing-resource-group"></a>리소스 그룹을 제거하여 모든 리소스 제거

다음과 같이 동일한 터미널에서 [Azure CLI 명령](/cli/azure/group?view=azure-cli-latest#az_group_delete)을 사용하여 리소스 그룹을 삭제합니다.

```azurecli
az group delete --name rg-demo  -y
```

이 명령은 몇 분 정도 걸릴 수 있습니다. 
