---
title: Linux 가상 머신 리소스 제거
description: Azure CLI 명령으로 리소스 그룹을 제거하여 Azure 리소스를 정리합니다.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: ac61f1b73e873ee1c1c6cd343792a79a88e76123
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98560989"
---
# <a name="7-clean-up-resources"></a>7. 리소스 정리

이 자습서를 완료한 후에는 사용 요금이 청구되지 않도록 리소스 그룹과 그 안에 포함된 모든 리소스를 제거해야 합니다. 

## <a name="remove-all-the-resources-by-removing-resource-group"></a>리소스 그룹을 제거하여 모든 리소스 제거

다음과 같이 동일한 터미널에서 [Azure CLI 명령](/cli/azure/group#az_group_delete)을 사용하여 리소스 그룹을 삭제합니다.

```azurecli
az group delete --name rg-demo-vm-eastus -y
```

이 명령이 완료될 때까지 몇 분 정도 걸립니다. 

## <a name="next-step"></a>다음 단계

* [Azure Linux VM](/azure/virtual-machines)에 대한 자세한 정보
