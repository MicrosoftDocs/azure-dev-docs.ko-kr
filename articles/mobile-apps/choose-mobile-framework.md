---
title: Visual Studio 및 Azure 서비스를 사용하여 클라이언트 애플리케이션을 빌드하기 위한 프런트 엔드 개발 플랫폼 선택
description: 클라이언트 애플리케이션을 빌드할 수 있도록 지원되는 네이티브 및 플랫폼 간 언어에 대해 알아봅니다.
author: codemillmatt
ms.service: mobile-services
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 8972c2b24334c964e00f5a3ccc27fa0c99e6f597
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632485"
---
# <a name="choose-a-mobile-development-framework"></a>모바일 개발 프레임워크 선택

개발자는 클라이언트 쪽 기술을 통해 플랫폼 간 접근 방식에 특정 프레임워크 및 패턴을 사용하여 모바일 애플리케이션을 직접 빌드할 수 있습니다. 개발자는 결정 요소에 따라 다음을 빌드할 수 있습니다.

- Objective C, Java 등의 언어를 사용하여 네이티브 단일 플랫폼 애플리케이션 빌드
- Xamarin, .NET 및 C#을 사용하여 플랫폼 간 애플리케이션 빌드
- Cordova 및 해당 변형을 사용하여 하이브리드 애플리케이션 빌드

## <a name="native-platforms"></a>네이티브 플랫폼

네이티브 애플리케이션을 빌드하려면 플랫폼과 관련된 프로그래밍 언어, SDK, 개발 환경 및 OS 공급 업체에서 제공하는 기타 도구가 필요합니다.

### <a name="ios"></a>iOS

Apple에서 만들고 개발한 iOS는 iPhone, iPad 등의 Apple 디바이스에서 앱을 빌드하는 데 사용됩니다.

- **프로그래밍 언어**: Objective-C, Swift
- **IDE**: Xcode
- **SDK**: iOS SDK

### <a name="android"></a>Android

Google에서 설계했으며 전 세계에서 가장 많이 사용되는 OS인 Android는 다양한 스마트폰과 태블릿에서 실행할 수 있는 애플리케이션을 빌드하는 데 사용됩니다.

- **프로그래밍 언어**: Java, Kotlin 
- **IDE**: Android Studio 및 Android 개발자 도구 
- **SDK**: Android SDK

### <a name="windows"></a>Windows

- **프로그래밍 언어**: C#
- **IDE**: Visual Studio, Visual Studio Code
- **SDK**: Windows SDK

#### <a name="native-platform-pros"></a>네이티브 플랫폼의 장점

- 우수한 사용자 환경
- 네이티브 라이브러리와 상호 연결하는 기능 및 고성능을 갖춘 응답성이 뛰어난 애플리케이션
- 매우 안전한 애플리케이션

#### <a name="native-platform-cons"></a>네이티브 플랫폼의 단점

- 애플리케이션이 한 플랫폼에서만 실행됨
- 애플리케이션을 빌드하는 데 더 많은 개발자 리소스와 비용이 필요
- 적은 코드 재사용

## <a name="cross-platforms-and-hybrid-applications"></a>플랫폼 간 애플리케이션 및 하이브리드 애플리케이션

플랫폼 간 애플리케이션은 네이티브 모바일 애플리케이션을 작성하고, 코드를 공유하고, iOS, Android 및 Windows에서 실행할 수 있는 기능을 제공합니다.

### <a name="xamarin"></a>Xamarin

Microsoft 소유의 [Xamarin](https://visualstudio.microsoft.com/xamarin/)은 C#에서 강력한 플랫폼 간 모바일 애플리케이션을 빌드하는 데 사용됩니다. Xamarin에는 iOS, Android, Windows 등의 여러 플랫폼에서 작동하는 클래스 라이브러리 및 런타임이 있습니다. 또한 고성능을 제공하는 네이티브(해석되지 않은) 애플리케이션을 컴파일합니다. Xamarin은 네이티브 플랫폼의 모든 기능을 결합하고 여러 가지 강력한 자체 기능을 추가합니다.

- **프로그래밍 언어**: C#
- **IDE**: Windows 또는 Mac의 Visual Studio

### <a name="react-native"></a>React Native

2015년에 Facebook에서 출시한 [React Native](https://facebook.github.io/react-native/)는 진정한 iOS 및 Android용 네이티브 렌더링 모바일 애플리케이션을 작성하는 데 사용되는 [오픈 소스](https://github.com/facebook/react-native) JavaScript 프레임워크입니다. 사용자 인터페이스를 빌드하는 Facebook의 JavaScript 라이브러리인 React를 기반으로 합니다. 브라우저 대신 모바일 플랫폼을 대상으로 합니다. React Native는 웹 구성 요소 대신 네이티브 구성 요소를 빌딩 블록으로 사용합니다.

- **프로그래밍 언어**: JavaScript
- **IDE**: Visual Studio Code

### <a name="unity"></a>Unity

 Unity는 게임 만들기에 최적화된 엔진입니다. 이 엔진을 사용하여 C#에서 Windows, iOS, Android, Xbox 등의 플랫폼을 위한 고품질 2D 또는 3D 애플리케이션을 만들 수 있습니다.

### <a name="cordova"></a>Cordova

Cordova를 사용하면 Apache Cordova용 Visual Studio Tools 또는 Cordova용 확장이 설치된 Visual Studio Code를 사용하여 하이브리드 애플리케이션을 빌드할 수 있습니다. 하이브리드 방식을 사용하면 웹 사이트와 구성 요소를 공유하고, Cordova 기반의 호스트된 웹 애플리케이션 방식으로 웹 서버 기반 애플리케이션을 재사용할 수 있습니다.

#### <a name="cross-platform-pros"></a>플랫폼 간 도구의 장점

- 여러 플랫폼에 사용 가능한 코드 베이스를 만들어 코드 사용성 향상
- 여러 플랫폼에서 더 넓은 대상 그룹의 요구를 충족
- 개발 시간 대폭 감소
- 쉽게 시작하고 업데이트할 수 있음

#### <a name="cross-platform-cons"></a>플랫폼 간 도구의 단점

- 낮은 성능
- 유연성 부족
- 창의적인 네이티브 애플리케이션을 만들기 위한 기능 세트가 플랫폼마다 다름
- 길어지는 UI 설계 시간
- 도구 제한
