---
title: Visual Studio App Center를 사용하여 애플리케이션의 장애 및 오류를 실시간으로 모니터링
description: 모바일 애플리케이션의 장애 및 오류를 모니터링하는 App Center 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 12a8a079-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: ad2a3ae817f177a54cdbf63093023c06124063a4
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632655"
---
# <a name="monitor-failures-and-errors-in-real-time-for-your-mobile-application"></a>모바일 애플리케이션의 실패 및 오류 실시간 모니터링

수천 명의 사용자가 애플리케이션을 사용하기 시작한 후에 버그가 발견될 수 있습니다. 사용자가 예기치 않은 애플리케이션 오류를 경험할 수 있습니다. 사용자가 원하는 것은 정상적으로 작동하는 믿을 수 있는 애플리케이션입니다. 버그가 있고 오류가 발생하는 애플리케이션은 좋은 사용자 환경을 제공할 수 없습니다. 실망한 사용자는 앱을 제거하고, 부정적인 리뷰를 남기거나, 자신의 경험에 대해 공개적으로 비판하고 불만을 토로합니다.

모바일 애플리케이션의 경우 실제 오류 동작을 이해하는 것이 중요합니다. 개발자의 업무는 앱 스토어에 앱을 출시하는 것에서 끝나지 않습니다. 지속적으로 성능을 모니터링하고 측정하여 앱 안정성을 보장하고 사용자를 만족시키는 것이 중요합니다. 그러려면 애플리케이션을 모니터링할 수 있는 방법이 필요합니다. 비즈니스 요구 사항에 중요한 문제를 우선적으로 지정하여 해결할 수 있도록 문제 발생 시 상황을 파악할 수 있어야 합니다.

## <a name="importance-of-failure-monitoring"></a>오류 모니터링의 중요성

개발자에게 필요한 것은 다음과 같습니다.

- 애플리케이션의 상태와 사용자에게 제공하는 성능에 대해 자세히 알아야 합니다.
- 사용자 디바이스에서 오류가 발생하는 위치와 근본 원인을 찾아야 합니다.
- 선제적으로 문제를 분류하고 오류를 해결하여 애플리케이션을 안정적으로 유지해야 합니다.
- 사용자가 애플리케이션을 지속적으로 사용할 수 있도록 보다 나은 애플리케이션을 빌드하고 더 빠르게 반복하여 뛰어난 사용자 환경을 제공해야 합니다.

다음 서비스를 활용하여 애플리케이션 오류를 모니터링하고, 문제를 진단하고, 신속하게 문제를 해결하여 안정적인 고품질 모바일 애플리케이션 환경을 제공하세요.

## <a name="visual-studio-app-center"></a>Visual Studio App Center

[App Center Diagnostics](/appcenter/diagnostics/) 서비스를 사용하여 애플리케이션 상태를 모니터링할 수 있습니다. 이 서비스는 애플리케이션에서 어떤 일이 발생했으며 그 원인은 무엇인지 이해하는 데 도움이 되는 자세한 인사이트를 제공합니다. App Center Diagnostics는 오류 보고서의 우선 순위를 지정하고 관리하는 데도 도움이 됩니다.

### <a name="visual-studio-app-center-features"></a>Visual Studio App Center 기능

- 스마트하게 그룹화되는 실시간 자동 오류 - 가장 심각한 문제와 필요한 관련 정보를 강조 표시
- 근본 원인을 식별하는 데 도움이 되는 컨텍스트 및 명확성을 제공하는 완전한 스택 추적 및 디바이스 데이터
- 가장 중요한 문제를 찾는 데 도움이 되는 강력한 검색 기능
- 사용자의 행동을 이해하고 오류로 이어지는 이벤트를 볼 수 있는 분석 통합
- 문제 진단에 도움이 되는 컨텍스트를 더 추가할 수 있는 사용자 지정 속성, 사용자 ID 및 파일 첨부
- 사용자에게 영향을 미치는 새로운 오류와 예기치 않은 오류를 항상 파악할 수 있는 경고, 알림 및 버그 추적기 통합
- 이 서비스를 몇 분 내에 시작할 수 있도록 단일 원라이너 SDK 통합
- iOS, Android, macOS, tvOS, Xamarin, React Native, Unity, Cordova, WPF 및 WinForms용 플랫폼 지원

### <a name="visual-studio-app-center-references"></a>Visual Studio App Center 참조

- [Visual Studio App Center 가입](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center Diagnostics 시작](/appcenter/diagnostics/)
