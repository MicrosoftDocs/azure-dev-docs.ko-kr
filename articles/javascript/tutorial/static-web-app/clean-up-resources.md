---
title: Azure 리소스 제거
description: Azure CLI 명령으로 리소스 그룹을 제거하여 비용이 발생할 수 있는 리소스를 정리합니다.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: bc68f550d0a2c1bc1550eb755e6ef33bae6ae9b7
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561639"
---
# <a name="7-clean-up-resources-for-static-web-app"></a>7. 정적 웹앱에 대한 리소스 정리

이 자습서를 완료한 후에는 사용 요금이 청구되지 않도록 Computer Vision 리소스와 Static 웹앱을 비롯한 리소스 그룹을 제거해야 합니다. 

## <a name="remove-all-the-resources-by-removing-resource-group"></a>리소스 그룹을 제거하여 모든 리소스 제거

다음과 같이 동일한 터미널에서 [Azure CLI 명령](/cli/azure/group#az_group_delete)을 사용하여 리소스 그룹을 삭제합니다.

```azurecli
az group delete --name rg-demo  -y
```

이 명령은 몇 분 정도 걸릴 수 있습니다. 
