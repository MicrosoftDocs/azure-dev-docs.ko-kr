---
title: Visual Studio App Center를 사용하여 수천 대의 실제 디바이스에서 모바일 애플리케이션 테스트
description: 수천 대의 실제 디바이스에서 모바일 애플리케이션을 지속적으로 테스트할 수 있는 Visual Studio App Center 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 12a8a079-9b3c-4faf-0000-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: b7251e355c7753b689d9dcb9f0b0ff5d413c001f
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632665"
---
# <a name="perform-automated-testing-on-thousands-of-mobile-devices-simultaneously"></a>수천 대의 모바일 디바이스에서 동시에 자동 테스트 수행

디바이스 조각화, 애플리케이션 복잡성, 빠른 릴리스 주기, 짧은 세션 및 높은 사용자 기대치로 인해 테스트가 어렵고 점점 복잡해질 수 있습니다. 뛰어난 고객 환경을 제공하도록 철저한 테스트를 거친 애플리케이션을 지속적으로 제공해야 합니다. 테스트는 쉬운 일이 아니며, 개발자가 각 릴리스를 수동으로 테스트하는 것은 좋은 방법이 아닙니다.

테스트 과제를 효율적으로 해결할 수 있습니다. 자동 테스트를 사용하여 다양한 디바이스에서 모바일 애플리케이션을 테스트할 수 있습니다. 클라우드 서비스를 사용하여 모바일 앱의 릴리스 속도를 높이고, 애플리케이션을 개발하고 배포할 때 병목 현상을 일으키는 테스트 과제를 극복할 수 있습니다.

## <a name="importance-of-automated-testing"></a>자동 테스트의 중요성

- 다양한 폼 팩터, 연결 형식 및 OS 버전을 대상으로 하여 테스트 범위를 넓힐 수 있습니다.
- 다양한 디바이스와 OS에서 애플리케이션을 테스트하여 시간, 비용 및 리소스를 절약할 수 있습니다.
- 반복적인 수동 테스트를 없애고, 새로운 자동 소프트웨어 테스트를 만들고 복잡한 기능을 처리하는 데 더 많은 시간을 투자하여 정확성을 높일 수 있습니다.
- 지속적으로 테스트하고, 애플리케이션 성능을 분석하고, 사용자가 애플리케이션을 실행하기 전에 문제를 찾아 해결하여 빠르게 릴리스할 수 있습니다.
- 다양한 디바이스를 사용하는 고객에게 최상의 환경을 제공하여 고품질 애플리케이션을 공급할 수 있습니다.

## <a name="visual-studio-app-center"></a>Visual Studio App Center

[App Center 테스트](/appcenter/test-cloud/)는 클라우드 기반 서비스로, 네이티브 및 하이브리드 모바일 앱용 Android 및 iOS를 통해 클라우드에서 수천 대의 실제 디바이스에 대한 자동 UI 테스트를 실행하는 데 사용할 수 있습니다. 2,000대가 넘는 디바이스에서, 자동으로 실행하여 테스트를 용이하게 수행할 수 있는 테스트 스크립트를 작성할 수 있습니다.

### <a name="visual-studio-app-center-key-features"></a>Visual Studio App Center 주요 기능

- 루팅되거나 탈옥되지 않는 실제 디바이스에서 실행되어 사용자 환경과 가장 근접한 환경을 제공하는 테스트
- 자동으로 캡처되어 각 테스트 단계에 연결되는 스크린샷. 테스트 실행에서 대상으로 지정된 모든 디바이스에 대해 나란히 놓고 볼 수 있습니다.
- 테스트 실행의 모든 대상 디바이스에 대한 디바이스 로그와 실패한 테스트 단계에 대한 스택 추적
- 여러 디바이스에서 동시에 실행하여 신속하게 결과를 얻을 수 있는 동시 테스트 실행
- Android 및 iOS용 Appium(JUnit를 사용하는 Java), Android용 Espresso(Java), Android 및 iOS용 Calabash, Android 및 iOS용 Xamarin.UITest, iOS용 XCUITest를 포함하여 인기 있는 테스트 프레임워크 지원
- iOS, Android Xamarin 및 React Native용 플랫폼 지원

### <a name="visual-studio-app-center-references"></a>Visual Studio App Center 참조

- [Visual Studio App Center 가입](https://appcenter.ms/signup)
- [App Center 테스트 시작](/appcenter/test-cloud/)