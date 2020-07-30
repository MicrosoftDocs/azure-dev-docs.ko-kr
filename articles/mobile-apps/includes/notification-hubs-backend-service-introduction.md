---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 20a8fa10a27a41621c2b08839682fefbd023ee2e
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401601"
---
[ASP.NET Core Web API](https://dotnet.microsoft.com/apps/aspnet/apis) 백 엔드는 최신의 [설치](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#installations) 방식을 사용하여 클라이언트에 대한 [디바이스 등록](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration)을 처리하는 데 사용됩니다. 또한 이 서비스는 플랫폼 간 방식으로 푸시 알림을 보냅니다. 

이러한 작업은 [백 엔드 작업에 대한 Notification Hubs SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)를 사용하여 처리됩니다. 전체 접근 방식에 대한 자세한 내용은 [앱 백 엔드에서 등록](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#registration-management-from-a-backend) 설명서에 나와 있습니다.
