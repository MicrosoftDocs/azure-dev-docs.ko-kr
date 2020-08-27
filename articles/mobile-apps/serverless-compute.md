---
title: Azure Functions 및 기타 서비스를 사용하여 서버리스 모바일 애플리케이션 백 엔드 빌드
description: 견고한 서버리스 모바일 애플리케이션을 빌드하는 데 사용되는 컴퓨팅 서비스에 대해 알아봅니다.
author: codemillmatt
ms.service: mobile-services
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 9de5f65045ab7d947902c18be68abf1f4880eeae
ms.sourcegitcommit: 2f832baf90c208a8a69e66badef5f126d23bbaaf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88725157"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>컴퓨팅 서비스를 사용하여 모바일 백 엔드 구성 요소 빌드

모든 모바일 애플리케이션에는 데이터 스토리지, 비즈니스 논리 및 보안을 담당하는 백 엔드가 필요합니다. 백 엔드 코드를 호스팅하고 실행하는 인프라를 관리하려면 여러 서버를 크기 조정, 프로비저닝 및 확장해야 합니다. 또한 OS 업데이트 및 관련 하드웨어를 관리하고 보안 패치를 적용해야 합니다. 그런 다음, 이러한 인프라 구성 요소를 모두 모니터링하여 성능, 가용성 및 내결함성을 확인해야 합니다. 

서버리스 아키텍처에는 관리할 서버가 없고 OS 또는 관련 소프트웨어/하드웨어 업데이트가 없으므로 이러한 유형의 시나리오에 유용합니다. 서버리스 아키텍처는 개발자의 시간과 비용을 절약하여 출시 시간을 단축하고 애플리케이션을 빌드하는 데 집중할 수 있습니다.

## <a name="benefits-of-compute"></a>컴퓨팅의 이점

- 서버 추상화는 호스팅, 패치 및 보안에 대해 걱정할 필요가 없으므로 코드에만 집중할 수 있습니다.
- 즉각적이고 효율적인 크기 조정을 통해 필요한 규모에 관계없이 리소스를 자동으로 또는 필요에 따라 프로비저닝할 수 있습니다.
- 고가용성 및 내결함성을 제공합니다.
- 마이크로 청구를 사용하면 코드가 실제로 실행되는 경우에 대해서만 요금이 청구됩니다.
- 코드는 선택한 언어로 작성된 클라우드에서 실행됩니다.

다음 서비스를 사용하여 모바일 앱에서 서버리스 컴퓨팅 기능을 사용하도록 설정합니다.

## <a name="azure-functions"></a>Azure 기능

[Azure Functions](https://azure.microsoft.com/services/functions/)는 서버에 대해 걱정하지 않고 선택한 프로그래밍 언어로 작성된 코드를 실행하는 데 사용할 수 있는 이벤트 구동 컴퓨팅 환경입니다. 애플리케이션 또는 이를 실행하는 인프라를 관리할 필요가 없습니다. Functions는 필요에 따라 크기 조정되며, 코드가 실행되는 시간에 대해서만 비용을 지불합니다. Azure Functions는 모바일 애플리케이션용 API를 구현하는 좋은 방법입니다. 구현 및 유지 관리가 쉽고, HTTP를 통해 액세스할 수 있습니다.

### <a name="azure-functions-key-features"></a>Azure Functions 주요 기능

- 트리거와 바인딩을 사용하여 함수가 호출되는 시기 및 연결되는 데이터를 정의할 수 있는 확장 가능한 이벤트 구동 기능입니다.
- Functions에서 NuGet 및 NPM을 지원하여 고유한 종속성을 가져올 수 있으므로 즐겨찾는 라이브러리를 사용할 수 있습니다.
- 통합 보안을 통해 OAuth 공급자(예: Azure Active Directory, Facebook, Google, Twitter, Microsoft 계정)에서 HTTP 트리거 함수를 보호할 수 있습니다.
- 다양한 [Azure 서비스](/azure/azure-functions/functions-overview) 및 SaaS(Software as a Service) 제품과 간편하게 통합할 수 있습니다.
- 유연한 개발을 통해 Azure Portal에서 직접 함수를 코딩하거나, 연속 통합을 설정하고 GitHub, Azure DevOps Services 및 기타 지원되는 개발 도구를 통해 코드를 배포할 수 있습니다.
- Functions 런타임은 오픈 소스이며 [GitHub](https://github.com/azure/azure-webjobs-sdk-script)에서 사용할 수 있습니다.
- 통합 도구 및 기본 제공 DevOps 기능을 사용하는 모니터링을 통해 기본 설정 편집기 또는 사용하기 쉬운 웹 인터페이스를 사용하여 로컬로 코딩, 테스트 및 디버그할 수 있는 향상된 개발 환경을 제공합니다.
- 개발을 위한 다양한 프로그래밍 언어 및 호스팅 옵션(예: C#, Node.js, Java, JavaScript 또는 Python)을 제공합니다.
- 종량제 가격 책정 모델에서는 코드를 실행하는 데 사용한 시간에 대해서만 비용을 지불합니다.

### <a name="azure-functions-references"></a>Azure Functions 참조

- [Azure Portal](https://portal.azure.com)
- [Azure Functions 설명서](/azure/azure-functions/) 리소스를 참조하세요.
- [Azure Functions 개발자 가이드](/azure/azure-functions/functions-reference)
- [빠른 시작](/azure/azure-functions/functions-create-first-function-vs-code)
- [샘플](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure App Service

[Azure App Service](https://azure.microsoft.com/services/app-service/)를 사용하면 인프라를 관리하지 않고도 선택한 프로그래밍 언어로 웹앱 및 RESTful API를 빌드하고 호스팅할 수 있습니다. 자동 크기 조정 및 고가용성을 제공하고, Windows 및 Linux를 모두 지원하며, GitHub, Azure DevOps 또는 Git 리포지토리에서 자동화된 배포를 사용하도록 설정합니다.

### <a name="azure-app-service-key-features"></a>Azure App Service 주요 기능

- ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP 또는 Python에 대한 다중 언어 및 프레임워크를 지원합니다. 또한 PowerShell 및 기타 스크립트 또는 실행 파일을 백그라운드 서비스로 실행할 수 있습니다.
- Azure DevOps, GitHub, BitBucket, Docker Hub 또는 Azure Container Registry와의 연속 통합 및 배포를 통해 DevOps를 최적화합니다. Azure PowerShell 또는 플랫폼 간 CLI(명령줄 인터페이스)를 사용하여 App Service에서 앱을 관리합니다.
- 수동 또는 자동으로 확장하거나 축소할 수 있는 고가용성을 사용하여 크기를 전역적으로 조정합니다.
- 엔터프라이즈 시스템(예: SAP), SaaS 서비스(예: Salesforce) 및 인터넷 서비스(예: Facebook)를 위해 50개 이상의 커넥터 중에서 선택할 수 있도록 SaaS 플랫폼 및 온-프레미스 데이터에 연결합니다. 하이브리드 연결 및 Azure Virtual Network를 사용하여 온-프레미스 데이터에 액세스합니다.
- Azure App Service는 ISO, SOC 및 PCI와 호환됩니다. Azure Active Directory를 사용하거나 소셜 미디어(예: Google, Facebook, Twitter 및 Microsoft)에 로그인하여 사용자를 인증합니다. IP 주소 제한을 만들고 서비스 ID를 관리합니다.
- Azure Marketplace의 광범위한 애플리케이션 템플릿 목록에서 애플리케이션 템플릿(예: WordPress, Joomla 및 Drupal)을 선택할 수 있습니다.
- Visual Studio를 Visual Studio의 전용 도구와 통합하여 만들기, 배포 및 디버깅 작업을 간소화합니다.

### <a name="azure-app-service-references"></a>Azure App Service 참조

- [Azure Portal](https://portal.azure.com/)
- [Azure App Service 설명서](/azure/app-service/)
- [빠른 시작](/azure/app-service/app-service-web-get-started-dotnet)
- [샘플](/azure/app-service/samples-cli)
- [자습서](/azure/app-service/app-service-web-tutorial-dotnet-sqldatabase)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

[AKS(Azure Kubernetes Service)](https://azure.microsoft.com/services/kubernetes-service/)는 호스트된 Kubernetes 환경을 관리합니다. AKS를 사용하면 컨테이너 오케스트레이션 전문 지식 없이도 컨테이너화된 애플리케이션을 쉽고 빠르게 배포 및 관리할 수 있습니다. 또한 지속적인 운영 및 유지 관리에 대한 부담을 제거할 수 있습니다. AKS는 애플리케이션을 오프라인으로 전환하지 않고도 필요에 따라 리소스를 프로비저닝, 업그레이드 및 크기 조정합니다.

### <a name="azure-kubernetes-service-key-features"></a>Azure Kubernetes Service 주요 기능

- 기존 애플리케이션을 컨테이너로 쉽게 마이그레이션하고 AKS 내에서 실행합니다.
- 마이크로서비스 기반 애플리케이션의 배포 및 관리를 간소화합니다.
- AKS에 대한 DevOps를 안전하게 유지하여 속도와 보안 간의 균형을 유지하고 코드를 규모에 맞게 더 빠르게 제공합니다.
- AKS 및 Azure Container Instances를 통해 크기를 쉽게 조정하여 몇 초 안에 시작되는 Container Instances 내에 Pod를 프로비저닝합니다.
- IoT 디바이스를 필요에 따라 배포하고 관리합니다.
- 도구(예: TensorFlow 및 KubeFlow)를 사용하여 기계 학습 모델을 학습시킵니다.

### <a name="azure-kubernetes-service-references"></a>Azure Kubernetes Service 참조

- [Azure Portal](https://portal.azure.com/)
- [Azure Kubernetes Service 설명서](/azure/aks/)
- [빠른 시작](/azure/aks/kubernetes-walkthrough-portal)
- [자습서](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances

[Azure Container Instances](https://azure.microsoft.com/services/container-instances/)는 간단한 애플리케이션, 작업 자동화 및 빌드 작업과 같이 격리된 컨테이너에서 작동할 수 있는 모든 시나리오에 적합한 솔루션입니다. VM을 관리하지 않고도 앱을 빠르게 개발할 수 있습니다.

### <a name="azure-container-instances-key-features"></a>Azure Container Instances 주요 기능

- VM을 프로비저닝하고 관리할 필요 없이 Container Instances를 통해 Azure에서 몇 초 안에 컨테이너를 시작할 수 있을 만큼 시작 시간이 빠릅니다.
- 공용 IP 연결 및 사용자 지정 DNS 이름을 제공합니다.
- VM에서와 같이 애플리케이션을 컨테이너 내에서 격리할 수 있는 하이퍼바이저 수준 보안을 제공합니다.
- CPU 코어 및 메모리의 정확한 사양을 허용하여 최적으로 활용할 수 있는 사용자 지정 크기를 제공합니다. 필요한 만큼 비용을 초 단위로 지불하므로 실제로 필요한 양에 따라 지출을 미세하게 조정할 수 있습니다.
- 상태를 검색하고 유지할 수 있는 영구 스토리지를 제공합니다. Container Instances는 Azure Files 공유를 직접 탑재할 수 있습니다.
- 동일한 API를 사용하여 예약되는 Linux 및 Windows 컨테이너를 제공합니다.

### <a name="azure-container-instances-references"></a>Azure Container Instances 참조

- [Azure Portal](https://portal.azure.com/)
- [Azure Container Instances 설명서](/azure/container-instances/)
- [빠른 시작](/azure/container-instances/container-instances-quickstart-portal)
- [샘플](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [자습서](/azure/container-instances/container-instances-tutorial-prepare-app)