---
title: 백 엔드 서비스를 통해 Azure Notification Hubs를 사용하여 Xamarin.Forms 앱에 푸시 알림 보내기 | Microsoft Docs
description: 백 엔드 서비스를 통해 Azure Notification Hubs를 사용하는 Xamarin.Forms 앱에 알림을 푸시하는 방법을 알아봅니다.
author: mikeparker104
ms.service: mobile-services
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: miparker
ms.openlocfilehash: 308e727f57ed086899d4fb5906235cb5a17bda16
ms.sourcegitcommit: ced8331ba36b28e6e2eacd23a64b39ddc7ffe6ab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337188"
---
# <a name="tutorial-send-push-notifications-to-xamarinforms-apps-using-azure-notification-hubs-via-a-backend-service"></a>자습서: 백 엔드 서비스를 통해 Azure Notification Hubs를 사용하여 Xamarin.Forms 앱에 푸시 알림 보내기  

[![샘플 다운로드](media/download.png) 샘플 다운로드](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Flutter](notification-hubs-backend-service-flutter.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

이 자습서에서는 [Azure Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview)를 사용하여 **Android** 및 **iOS**를 대상으로 하는 [Xamarin.Forms](https://dotnet.microsoft.com/apps/xamarin/xamarin-forms) 애플리케이션에 푸시 알림을 보냅니다.  

[!INCLUDE [Notification Hubs Backend Service Introduction](includes/notification-hubs-backend-service-introduction.md)]

이 자습서에서는 다음 단계를 수행합니다.

> [!div class="checklist"]
>
> * [Push Notification Services 및 Azure Notification Hubs 설정](#set-up-push-notification-services-and-azure-notification-hub)
> * [ASP.NET Core Web API 백 엔드 애플리케이션 만들기](#create-an-aspnet-core-web-api-backend-application)
> * [플랫폼 간 Xamarin.Forms 애플리케이션 만들기](#create-a-cross-platform-xamarinforms-application)
> * [푸시 알림에 사용할 기본 Android 프로젝트 구성](#configure-the-native-android-project-for-push-notifications)
> * [푸시 알림에 사용할 기본 iOS 프로젝트 구성](#configure-the-native-ios-project-for-push-notifications)
> * [솔루션 테스트](#test-the-solution)

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 따라 하려면 다음이 필요합니다.

* 리소스를 만들고 관리할 수 있는 [Azure 구독](https://azure.microsoft.com/free/dotnet)
* [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)가 설치된 Mac 또는 [Visual Studio 2019](https://visualstudio.microsoft.com/vs)를 실행하는 PC
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs) 사용자는 **.NET을 사용한 모바일 개발**과 **ASP.NET 및 웹 개발** 워크로드가 설치되어 있어야 합니다.
* **Android**(물리적 또는 에뮬레이터 디바이스) 또는 **iOS**(물리적 디바이스만 해당)에서 앱을 실행하는 기능

Android의 경우 다음이 필요합니다.

* 개발자가 잠금 해제한 물리적 디바이스 또는 에뮬레이터 *(API 26 이상을 실행하고 Google Play 서비스가 설치되어 있어야 함)* .

iOS의 경우 다음이 필요합니다.

* 활성 [Apple Developer](https://developer.apple.com) 계정
* [개발자 계정에 등록된](https://help.apple.com/developer-account/#/dev40df0d9fa) 물리적 iOS 디바이스 *(iOS 13.0 이상 실행)*
* [물리적 디바이스에서 앱을 실행](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca)할 수 있도록 **키 집합**에 설치된 **.p12** [개발 인증서](https://help.apple.com/developer-account/#/dev04fd06d56)

> [!NOTE]
> iOS 시뮬레이터는 원격 알림을 지원하지 않으므로 iOS에서 이 샘플을 탐색할 때 물리적 디바이스가 필요합니다. 그러나 이 자습서를 완료하기 위해 **Android** 및 **iOS** 둘 다에서 앱을 실행할 필요는 없습니다.

이전 환경 없이 이 첫 번째 원칙 예제의 단계를 따라 할 수 있습니다. 하지만 다음과 같은 내용을 알아두는 것이 좋습니다.

* [Apple 개발자 포털](https://developer.apple.com)
* [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1) 및 [웹 API](https://dotnet.microsoft.com/apps/aspnet/apis)
* [Google Firebase 콘솔](https://console.firebase.google.com/u/0/)
* [Microsoft Azure](https://portal.azure.com) 및 [Azure Notification Hubs를 사용하여 iOS 앱에 푸시 알림 보내기](/azure/notification-hubs/ios-sdk-get-started)
* [Xamarin](https://dotnet.microsoft.com/apps/xamarin) 및 [Xamarin.Forms](https://dotnet.microsoft.com/apps/xamarin/xamarin-forms)

> [!IMPORTANT]
> 제공된 단계는 [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)에만 적용됩니다. [Visual Studio 2019](https://visualstudio.microsoft.com/vs)를 사용하여 진행할 수 있지만 약간의 차이를 조정해야 할 수 있습니다. 예를 들어 사용자 인터페이스와 워크플로, 템플릿 이름, 환경 구성 등에 대한 설명이 다를 수 있습니다.

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Push Notification Services 및 Azure Notification Hub 설정

이 섹션에서는 **[FCM(Firebase Cloud Messaging)](https://firebase.google.com/docs/cloud-messaging)** 및 **[APNS(Apple Push Notification Services)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)** 를 설정합니다. 그런 다음, 해당 서비스를 사용하는 알림 허브를 만들고 구성합니다.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>ASP.NET Core Web API 백 엔드 애플리케이션 만들기

이 섹션에서는 [디바이스 등록](/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration)을 처리하고 Xamarin.Forms 모바일 앱에 알림을 보낼 [ASP.NET Core Web API](https://dotnet.microsoft.com/apps/aspnet/apis) 백 엔드를 만듭니다.

[!INCLUDE [Create an ASP.NET Core Web API backend application](includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-xamarinforms-application"></a>플랫폼 간 Xamarin.Forms 애플리케이션 만들기

이 섹션에서는 플랫폼 간 방식으로 푸시 알림을 구현하는 [Xamarin.Forms](https://dotnet.microsoft.com/apps/xamarin/xamarin-forms) 모바일 애플리케이션을 빌드합니다.

[!INCLUDE [Sample application generic overview](includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create Xamarin.Forms application](includes/notification-hubs-backend-service-sample-app-xamarin-forms.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>푸시 알림에 사용할 기본 Android 프로젝트 구성

[!INCLUDE [Configure the native Android project](includes/notification-hubs-backend-service-configure-xamarin-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>푸시 알림에 사용할 기본 iOS 프로젝트 구성

[!INCLUDE [Configure the native iOS project](includes/notification-hubs-backend-service-configure-xamarin-ios.md)]

## <a name="test-the-solution"></a>솔루션 테스트

이제 백 엔드 서비스를 통해 알림 보내기를 테스트할 수 있습니다.

[!INCLUDE [Testing the solution](includes/notification-hubs-backend-service-testing.md)]

## <a name="troubleshooting"></a>문제 해결

[!INCLUDE [Troubleshooting](includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>관련 링크

* [Azure Notification Hubs 개요](/azure/notification-hubs/notification-hubs-push-notification-overview)
* [Mac용 Visual Studio 설치](/visualstudio/mac/installation?view=vsmac-2019)
* [Windows에 Xamarin 설치](/xamarin/get-started/installation/windows)
* [백 엔드 작업에 대한 Notification Hubs SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [GitHub의 Notification Hubs SDK](https://github.com/Azure/azure-notificationhubs)
* [애플리케이션 백 엔드에 등록](/azure/notification-hubs/notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification)
* [등록 관리](/azure/notification-hubs/notification-hubs-push-notification-registration-management)
* [태그 사용](/azure/notification-hubs/notification-hubs-tags-segment-push-message)
* [사용자 지정 템플릿 사용](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages)

## <a name="next-steps"></a>다음 단계

이제 백 엔드 서비스를 통해 알림 허브에 연결되는 기본 Xamarin.Forms 앱이 생겼으며, 알림을 보내고 받을 수 있습니다.

[!INCLUDE [Next steps](includes/notification-hubs-backend-service-next-steps.md)]
