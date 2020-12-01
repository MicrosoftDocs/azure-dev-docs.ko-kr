---
title: Azure 리소스 제거
description: Azure CLI 명령으로 리소스 그룹을 제거하여 비용이 발생할 수 있는 리소스를 정리합니다.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 0b425ce873c53ca95628cfe8c3f68ea4b010aba3
ms.sourcegitcommit: 4dac39849ba2e48034ecc91ef578d11aab796e58
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94993539"
---
# <a name="6-clean-up-resources"></a>6. 리소스 정리

이 자습서를 완료한 후에는 사용 요금이 청구되지 않도록 Computer Vision 리소스와 Static 웹앱을 비롯한 리소스 그룹을 제거해야 합니다. 

## <a name="remove-all-the-resources-by-removing-resource-group"></a>리소스 그룹을 제거하여 모든 리소스 제거

다음과 같이 동일한 터미널에서 [Azure CLI 명령](/cli/azure/group?view=azure-cli-latest#az_group_delete)을 사용하여 리소스 그룹을 삭제합니다.

```azurecli
az group delete --name rg-demo  -y
```

이 명령은 몇 분 정도 걸릴 수 있습니다. 
