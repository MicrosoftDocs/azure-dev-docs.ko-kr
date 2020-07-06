---
title: Visual Studio App Center 및 Azure 서비스를 사용하여 모바일 애플리케이션 사용 및 사용자 동작 이해
description: 사용자가 모바일 애플리케이션을 사용하는 방법을 이해하여 현명한 비즈니스 의사 결정을 내리는 데 도움이 되는 App Center와 같은 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 744e41200a5f7e5ff898e7a0786a4284de176b7a
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632595"
---
# <a name="analyze-and-understand-mobile-application-use"></a>모바일 애플리케이션 사용 분석 및 이해

사용자가 애플리케이션을 어떻게 사용하는지 잘 이해하고 있나요? 애플리케이션에 대한 활성 사용자의 수는 몇 명이고, 시간이 지남에 따라 사용량이 어떻게 변하나요? 사용하고 있는 기능은 무엇이고, 어떤 기능이 가장 많이 사용되나요? 이러한 사용자는 어디에 기반을 두고 있나요? 최신 버전의 애플리케이션을 사용하고 있는 사용자는 몇 명인가요? 앱이 성공적인 비즈니스가 되도록 하려면 이러한 모든 질문을 이해하는 것이 중요합니다. 이러한 종류의 사용량 분석 질문에 대답하려면 앱에서 사용량 데이터를 수집해야 합니다.

데이터를 더 자세히 조사할수록 애플리케이션을 개선하고 사용자의 만족도를 유지하는 방법을 더 많이 찾을 수 있습니다. 데이터를 사용하여 실행 가능한 인사이트를 찾고 사용자의 만족도를 유지해야 합니다.

## <a name="importance-of-analytics"></a>분석의 중요성

- 사용자, 애플리케이션과 상호 작용하는 방법 및 애플리케이션을 다시 가져와서 세밀하게 조정하는 구성 요소를 이해하고, 비즈니스를 성장시킬 수 있는 뛰어난 환경을 제공합니다.
- 사용량 메트릭을 추적하여 애플리케이션을 마케팅하고 고객에게 더 나은 서비스를 제공하는 방법에 대한 정보 기반 의사 결정을 내릴 수 있습니다.
- 애플리케이션 성능을 측정합니다.
- 애플리케이션에서 가치와 성능을 창출하는 부분을 파악합니다.
- 변동 및 보존에 영향을 주는 문제에 대한 데이터 기반 인사이트를 얻습니다.

다음 서비스를 사용하여 모바일 애플리케이션 분석을 사용하도록 설정합니다.

## <a name="visual-studio-app-center"></a>Visual Studio App Center

[App Center 분석](/appcenter/analytics/)을 사용하면 중요한 항목에 집중하여 대상 그룹을 늘릴 수 있습니다. 사용자 세션, 상위 디바이스, OS 버전 및 동작 분석에 대한 심층적인 보고와 인사이트를 제공합니다. 광범위한 애플리케이션 분석을 통해 모든 항목을 추적할 수 있는 사용자 지정 이벤트를 쉽게 만듭니다.

### <a name="visual-studio-app-center-features"></a>Visual Studio App Center 기능

- 추가 비용을 들이지 않고 사용 패턴, 사용자 채택 및 기타 참여 메트릭을 추적합니다.
- 사용자 지정 이벤트를 통해 추세, 사용자 동작 및 참여를 파악합니다.
- 단일 대시보드에서 애플리케이션 사용량(일별, 주별, 월별), 세션, 디바이스 속성 및 사용자 인구 통계에 대한 기본 메트릭 및 자세한 인사이트를 얻습니다.
- 모든 App Center 분석 데이터를 Azure로 지속적으로 내보내 무제한으로 보존합니다. App Center 분석은 Azure Blob 스토리지 및 Azure Application Insights로 내보내는 작업을 지원합니다.
- Azure Application Insights와 통합하여 보존, 깔때기형 분석 및 코호트와 같은 더 심층적인 인사이트를 제공합니다.
- 한 줄 SDK 통합을 사용하여 몇 분 안에 시작합니다.
- iOS, Android, macOS, tvOS, Xamarin, React Native, Unity 및 Cordova에 대한 플랫폼 지원을 얻습니다.

### <a name="visual-studio-app-center-references"></a>Visual Studio App Center 참조

- [App Center 가입](https://appcenter.ms/signup)
- [App Center 분석 시작](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor

Azure Monitor에는 원격 분석을 수집하고 분석하여 성능을 최대화하고 모바일 애플리케이션을 모니터링하는 도구를 제공하는 [Application Insights](/azure/azure-monitor/app/app-insights-overview)가 포함되어 있습니다. App Center 분석에서 Application Insights로 내보내기를 설정하여 Application Insights를 활용할 수 있습니다. Application Insights는 App Center에서 제공하는 분석 도구 외에도 애플리케이션에서 사용자 지정 이벤트 원격 분석을 쿼리, 분할, 필터링 및 분석할 수 있습니다.

### <a name="azure-monitor-features"></a>Azure Monitor 기능

- 사용자 지정 이벤트 원격 분석을 쿼리합니다.
- 강력한 분할 기능을 사용하여 이벤트 원격 분석을 필터링합니다.
- 전환, 보존 및 탐색 패턴을 분석합니다. 다음을 사용할 수 있습니다.
  - 전환율을 분석하고 모니터링하기 위한 깔때기형
  - 애플리케이션에서 시간에 따라 사용자를 효율적으로 유지하는 수준을 분석하기 위한 보존
  - 시각화와 텍스트를 공유 가능한 보고서에 결합하기 위한 통합 문서
  - 다른 분석 도구에서 쉽게 참조할 수 있도록 특정 사용자 또는 이벤트 그룹의 이름을 지정하고 저장하기 위한 코호트

### <a name="azure-monitor-references"></a>Azure Monitor 참조

- [Azure Portal](https://portal.azure.com/)
- [App Center 및 Application Insights를 사용하여 모바일 애플리케이션 분석](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Application Insights에서 App Center 분석 사용](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab

[Azure PlayFab](https://playfab.com/)은 클라우드에서 연결되는 세계 최고의 게임을 만드는 데 필요한 게임 서비스, 실시간 분석 및 LiveOps를 갖춘 완벽한 백 엔드 플랫폼을 제공합니다. 이러한 서비스는 게임 개발자의 진출 장벽을 줄여줍니다. 게임에 따라 크기가 조정되는 크고 작은 스튜디오에 대한 비용 효율적인 개발 솔루션을 제공합니다. 이 서비스는 스튜디오에서 플레이어를 참여시키고, 유지하며, 수익을 창출하는 데 도움이 될 수 있습니다. PlayFab을 사용하면 개발자가 인텔리전트 클라우드를 사용하여 게임을 빌드 및 운영하고, 게임 데이터를 분석하며, 전체 게임 환경을 향상시킬 수 있습니다.

### <a name="azure-playfab-features"></a>Azure PlayFab 기능

- 실시간 대시보드를 모니터링합니다.
- 상위 메트릭을 통해 게임 성능을 평가합니다.
- 자동 생성된 보고서를 통해 일별 및 월별 게임 성능 요약을 검토합니다. 게임 관리자에서 보고서를 보고, 매일 다운로드하거나 받은 편지함으로 배달할 수 있습니다.
- A/B 테스트를 사용하여 실험을 실행하고 특정 변수에 대한 최적 설정을 결정합니다.
- 플레이어에 대한 조각화를 사용하여 자동화된 플레이어 그룹화를 정의합니다.

### <a name="azure-playfab-references"></a>Azure PlayFab 참조

- [PlayFab 포털](https://developer.playfab.com/en-US/sign-up)
- [분석](/gaming/playfab/#pivot=documentation&panel=analytics)
- [빠른 시작](/gaming/playfab/#pivot=documentation&panel=quickstarts)
