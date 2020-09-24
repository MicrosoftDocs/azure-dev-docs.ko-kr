---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: c8cd0672bfedf640077e9ae3b9272b12d8ce0b61
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738157"
---
### <a name="provision-azure-container-registry-and-azure-kubernetes-service"></a>Azure Container Registry 및 Azure Kubernetes Service 프로비저닝

다음 명령을 사용하여 서비스 주체가 레지스트리에서 읽기 권한자 역할을 갖는 컨테이너 레지스트리와 Azure Kubernetes 클러스터를 만듭니다. 클러스터의 네트워킹 요구 사항에 [적합한 네트워크 모델을 선택](/azure/aks/operator-best-practices-network#choose-the-appropriate-network-model)해야 합니다.

```bash
az group create -g $resourceGroup -l eastus
az acr create -g $resourceGroup -n $acrName --sku Standard
az aks create -g $resourceGroup -n $aksName --attach-acr $acrName --network-plugin azure
```
