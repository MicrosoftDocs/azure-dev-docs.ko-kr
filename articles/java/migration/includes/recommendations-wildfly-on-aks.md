---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: a185e4a01f048d05ffe6951baf5c90d6721dcebb
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738408"
---
### <a name="recommendations"></a>권장 사항

* 수신 컨트롤러 또는 애플리케이션 부하 분산 장치에 할당된 IP 주소에 DNS 이름을 추가하는 것이 좋습니다. 자세한 내용은 [AKS에서 고정 공용 IP 주소를 사용하여 수신 컨트롤러 만들기](/azure/aks/ingress-static-ip)를 참조하세요.

* 애플리케이션에 대한 [HELM 차트](https://helm.sh/docs/topics/charts/)를 추가하는 것이 좋습니다. HELM 차트를 사용하면 더 다양한 고객 세트에서 사용하고 사용자 지정할 수 있도록 애플리케이션 배포를 매개 변수화할 수 있습니다.

* DevOps 전략을 설계하고 구현합니다. 개발 속도를 높이는 동시에 안정성을 유지하기 위해 Azure Pipelines를 사용하여 배포를 자동화하고 테스트하는 것이 좋습니다. 자세한 내용은 [빌드하고 AKS에 배포](/azure/devops/pipelines/ecosystems/kubernetes/aks-template)를 참조하세요.

* 클러스터에 대한 Azure 모니터링을 사용하도록 설정합니다. 자세한 내용은 [이미 배포된 AKS 클러스터에 대한 모니터링 사용](/azure/azure-monitor/insights/container-insights-enable-existing-clusters)을 참조하세요. 이를 통해 Azure Monitor는 컨테이너 로그 수집, 사용량 추적 등을 수행할 수 있습니다.

* Prometheus를 통해 애플리케이션별 메트릭을 공개하는 것이 좋습니다. Prometheus는 Kubernetes 커뮤니티에서 광범위하게 채택된 오픈 소스 메트릭 프레임워크입니다. 사용자 고유의 Prometheus 서버를 호스팅하는 대신 Azure Monitor에서 스크랩하는 Prometheus 메트릭을 구성하여 애플리케이션에서 메트릭 집계를 사용하도록 설정하고 비정상 조건에 대한 자동화된 응답 또는 에스컬레이션을 수행할 수 있습니다. 자세한 내용은 [컨테이너에 Azure Monitor를 사용하여 Prometheus 메트릭 스크랩 구성](/azure/azure-monitor/insights/container-insights-prometheus-integration)을 참조하세요.

* 비즈니스 연속성 및 재해 복구 전략을 설계하고 구현합니다. 중요 업무용 애플리케이션의 경우 다중 지역 배포 아키텍처를 고려하세요. 자세한 내용은 [AKS의 비즈니스 연속성 및 재해 복구를 위한 모범 사례](/azure/aks/operator-best-practices-multi-region)를 참조하세요.

* [Kubernetes 버전 지원 정책](/azure/aks/supported-kubernetes-versions#kubernetes-version-support-policy)을 검토합니다. 항상 지원되는 버전을 실행하도록 AKS 클러스터를 계속 업데이트해야 합니다. 자세한 내용은 [AKS 클러스터 업그레이드](/azure/aks/upgrade-cluster)를 참조하세요.

* 클러스터 관리 및 애플리케이션 개발을 담당하는 모든 팀 멤버가 관련 AKS 모범 사례를 검토하도록 합니다. 자세한 내용은 [AKS에서 애플리케이션을 빌드하고 관리하는 클러스터 운영자 및 개발자의 모범 사례](/azure/aks/best-practices)를 참조하세요.

* 배포 파일에 롤링 업데이트 수행 방법이 지정되어 있는지 확인합니다. 자세한 내용은 Kubernetes 설명서의 [롤링 업데이트 배포](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-update-deployment)를 참조하세요.

* 피크 타임 부하를 처리하도록 자동 크기 조정을 설정합니다. 자세한 내용은 [AKS에서 애플리케이션 수요에 맞게 자동으로 클러스터 크기 조정](/azure/aks/cluster-autoscaler)을 참조하세요.

* 성능을 더 최적화하려면 코드 캐시 크기를 모니터링하고 Dockerfile의 JVM 매개 변수 `-XX:InitialCodeCacheSize` 및 `-XX:ReservedCodeCacheSize`를 추가하는 것이 좋습니다. 자세한 내용은 Oracle 설명서의 [Codecache 조정](https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm)을 참조하세요.
