---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 92caae7f50ddb0e58f14e8c99a4cd598a72f884f
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738156"
---
### <a name="provision-a-public-ip-address"></a>공용 IP 주소 프로비저닝

내부 또는 가상 네트워크 외부에서 애플리케이션에 액세스할 수 있는 경우 공용 정적 IP 주소가 필요합니다. 다음 예제와 같이 클러스터의 노드 리소스 그룹 내에서 이 IP 주소를 프로비저닝해야 합니다.

```bash
nodeResourceGroup=$(az aks show -g $resourceGroup -n $aksName --query 'nodeResourceGroup' -o tsv)
publicIp=$(az network public-ip create -g $nodeResourceGroup -n applicationIp --sku Standard --allocation-method Static --query 'publicIp.ipAddress' -o tsv)
echo "Your public IP address is ${publicIp}."
```
