---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: abf3824dd83d0da27a411694e144a8e6bf7285ac
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738494"
---
### <a name="deploy-to-aks"></a>AKS에 배포

Kubernetes YAML 파일을 만들고 적용합니다. 자세한 내용은 [빠른 시작: Azure CLI를 사용하여 Azure Kubernetes Service 클러스터 배포](/azure/aks/kubernetes-walkthrough#run-the-application)를 참조하세요. 외부 부하 분산 장치를 만드는 경우(애플리케이션 또는 수신 컨트롤러에 있는지 여부에 관계없이) 이전 섹션에서 프로비저닝된 IP 주소를 `LoadBalancerIP`로 제공해야 합니다.

표면화된 매개 변수를 환경 변수로 포함합니다. 자세한 내용은 [컨테이너의 환경 변수 정의](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/)를 참조하세요. 암호, API 키 및 JDBC 연결 문자열과 같은 비밀은 포함하지 않습니다. 이러한 내용은 다음 섹션에 설명되어 있습니다.

배포 YAML을 만들 때는 컨테이너의 크기가 적절히 조정되도록 메모리 및 CPU 설정을 포함해야 합니다.
