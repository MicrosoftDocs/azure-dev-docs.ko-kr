---
title: Visual Studio App Center 및 Azure Notification Hubs를 사용하는 모바일 앱에서 푸시 알림의 중요성
description: 모바일 애플리케이션 사용자와 교류하는 데 사용할 수 있는 Visual Studio App Center 같은 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 6dab56319c04fbf7864094fbaf254621cac82b36
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632445"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>푸시 알림을 보내 애플리케이션 사용자와 함께 참여

소비자용 애플리케이션을 개발하든 아니면 엔터프라이즈 애플리케이션을 개발하든, 개발자는 사용자가 애플리케이션을 적극적으로 사용하기를 바랍니다. 최신 뉴스, 게임 업데이트, 흥미로운 제안, 제품 업데이트 또는 기타 관련 정보를 사용자와 공유하고 싶은 경우가 자주 있습니다. 사용자의 참여를 높이고 내 애플리케이션으로 돌아오도록 만들려면 사용자와 실시간으로 통신할 수 있는 방법이 필요합니다.

푸시 알림은 애플리케이션 사용자와 빠르고 효율적으로 통신할 수 있는 방법을 제공합니다. 사용자가 무엇을 하고 있든 적시에 사용자에게 연락하여 원하는 정보를 전달할 수 있으며, 이 작업은 일반적으로 모바일 디바이스의 팝업 항목 또는 대화 상자를 통해 이루어집니다.

## <a name="value-of-push-notifications"></a>푸시 알림의 가치

푸시 알림은 사용자와 기업에 가치를 제공합니다.

기업은 다음과 같은 작업을 수행할 수 있습니다.

- 지원되는 플랫폼에서 적시에 메시지를 전송하여 사용자와 직접 통신합니다.
- 사용자에게 실시간 업데이트 및 미리 알림을 전송하여 사용자 참여를 높이고, 정기적으로 애플리케이션을 사용하도록 장려합니다.
- 사용자를 유지하고, 애플리케이션을 다운로드했지만 사용하지 않는 비활성 사용자를 다시 참여시켜 다시 활성 사용자로 만듭니다.
- 모바일 푸시 알림을 기반으로 사용자 동작을 분석하고, 사용자를 세분화하고, 캠페인을 활용하여 전환율을 높입니다.
- 사용자에게 푸시 메시지와 시기적절한 업데이트를 전송하여 제품 및 서비스를 홍보합니다.
- 맞춤형 푸시 메시지를 전송하여 사용자를 타게팅합니다.

애플리케이션 사용자의 경우 푸시 알림은 다음과 같은 이점이 있습니다.

- 실시간으로 가치와 정보를 제공합니다.
- 애플리케이션을 사용하라고 사용자에게 알립니다.

다음 서비스를 사용하여 모바일 앱에서 푸시 알림을 사용하도록 설정하세요.

## <a name="azure-notification-hubs"></a>Azure Notification Hubs

[Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview)는 사용하기 쉬운 스케일 아웃 푸시 엔진을 제공합니다. 이 서비스를 사용하여 클라우드 또는 온-프레미스의 모든 백 엔드에서 모든 플랫폼으로 알림을 보낼 수 있습니다.

### <a name="azure-notification-hub-features"></a>Azure Notification Hub 기능

- 클라우드 또는 온-프레미스의 모든 백 엔드에서 모든 플랫폼으로 푸시 알림을 보냅니다.
- API를 한 번 호출하여 수백만 대의 모바일 디바이스에 푸시를 빠르게 브로드캐스트합니다.
- 고객, 언어 및 위치에 따라 푸시 알림을 조정합니다.
- 고객 세그먼트를 동적으로 정의하고 알립니다.
- 즉시 수백만 대의 모바일 디바이스로 스케일링합니다.
- iOS, Android, Windows, Kindle 및 Baidu에 대한 플랫폼 지원을 받습니다.

### <a name="azure-notification-hub-references"></a>Azure Notification Hub 참조

- [Azure Portal](https://portal.azure.com) 
- [Azure Notification Hubs 시작](/azure/notification-hubs/)
- [빠른 시작](/azure/notification-hubs/create-notification-hub-portal)
- [샘플](/azure/notification-hubs/samples)
