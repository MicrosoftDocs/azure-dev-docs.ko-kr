---
title: Visual Studio App Center 및 Azure 서비스를 사용한 모바일 앱 개발의 여러 단계
description: 모바일 애플리케이션 개발과 관련된 여러 단계와 Visual Studio App Center 같은 Microsoft 서비스를 사용하여 고급 모바일 애플리케이션을 빌드하는 방법을 알아봅니다.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-1234-5678-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: d1e20d59f238f6f0abb9438550ae541cfe7598a7
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632675"
---
# <a name="different-stages-in-mobile-application-development"></a>모바일 애플리케이션 개발의 여러 단계

모바일 애플리케이션은 여러 단계를 거쳐 빌드됩니다. 개발자는 간편하게 Microsoft 서비스, 도구 및 기술을 활용하여 고급 모바일 애플리케이션을 빌드할 수 있습니다.

## <a name="app-development"></a>앱 개발

Objective-C, Java 등의 언어를 사용하여 네이티브 단일 플랫폼 클라이언트 애플리케이션을 빌드할 수 있습니다. Xamarin, React Native 및 Unity를 사용하여 플랫폼 간 앱을 빌드할 수 있습니다. Cordova를 사용하여 하이브리드 앱을 빌드할 수도 있습니다. 클라이언트 애플리케이션을 만들려면 PC나 Mac에서 Visual Studio, Mac용 Visual Studio 또는 Visual Studio Code 같은 IDE와 코드 편집기를 사용하세요.

## <a name="plan-and-design-with-back-end-services"></a>백 엔드 서비스를 사용하여 계획 및 디자인

Microsoft는 모바일 애플리케이션을 위한 안전하고 확장성 있고 안정적인 백 엔드를 설정할 수 있는 다양한 서비스를 제공합니다. 개발자는 자체 서버를 유지 관리할 필요 없이 서버리스 API 및 트리거를 만들 수 있습니다. 푸시 알림, 오프라인 데이터 동기화, 인증 및 데이터 서비스를 사용하여 다양한 앱 작성 시나리오를 지원하는 포괄적이고 광범위한 모바일 백 엔드를 만들어 보세요.

백 엔드 서비스를 빌드하려면 여러 가지 요구 사항을 충족해야 합니다. 그러려면 다음 작업을 수행해야 합니다.

- 디바이스의 데이터 스토리지를 제한합니다.
- 여러 디바이스에서 데이터를 동기화합니다.
- 네트워크 연결 없이 애플리케이션이 실행되는지 확인합니다.
- 사용자에게 알림을 보냅니다.
- 디바이스 사용량을 줄입니다.
- 확장성 있고, 안정적이고 안전한 백 엔드를 빌드합니다.
- 서버 유지 관리를 자동화합니다.

## <a name="devops-and-continuous-monitoring"></a>DevOps 및 지속적인 모니터링

모바일 및 클라우드 중심 애플리케이션을 만들려면 CI(연속 통합), CD(지속적인 업데이트) 및 지속적인 모니터링 서비스를 활용하여 모든 플랫폼에 사용 가능한 네이티브 빌드를 만듭니다. 수천 대의 실제 디바이스에서 UI 자동화를 테스트하고, 베타 테스트 채널을 통해 릴리스를 자동화하고, 앱 스토어에 직접 배포할 수 있습니다. 애플리케이션의 사용량 분석, 오류 및 예외 정보를 수집할 수도 있습니다.

## <a name="additional-services"></a>서비스

빌드하려는 애플리케이션의 유형과 특성에 따라 다음과 같은 여러 Azure 서비스를 사용할 수 있습니다.

- [Azure Cognitive Search](azure-search.md) - 빠른 쿼리를 통해 모든 유형의 내부 데이터를 검색합니다.
- [Azure Cognitive Services](cognitive-services.md) - 애플리케이션에 AI를 사용합니다.
- Azure SignalR - 애플리케이션에서 [실시간 통신](real-time-communication.md)이 가능합니다.
