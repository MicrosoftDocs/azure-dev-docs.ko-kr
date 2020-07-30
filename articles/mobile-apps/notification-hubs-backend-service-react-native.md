---
title: 백 엔드 서비스를 통해 Azure Notification Hubs를 사용하여 React Native 앱에 푸시 알림 보내기 | Microsoft Docs
description: 백 엔드 서비스를 통해 Azure Notification Hubs를 사용하는 React Native 앱에 알림을 푸시하는 방법을 알아봅니다.
author: alexeystrakh
ms.service: mobile-services
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: alstrakh
ms.openlocfilehash: ddc35adf00b41dd421cbcd64437a7366bf4c74b1
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401616"
---
# <a name="tutorial-send-push-notifications-to-react-native-apps-using-azure-notification-hubs-via-a-backend-service"></a>자습서: 백 엔드 서비스를 통해 Azure Notification Hubs를 사용하여 React Native 앱에 푸시 알림 보내기  

[![샘플 다운로드](media/download.png) 샘플 다운로드](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Flutter](notification-hubs-backend-service-flutter.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

이 자습서에서는 [Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview)를 사용하여 **Android** 및 **iOS**를 대상으로 하는 [React Native](https://reactnative.dev/) 애플리케이션에 푸시 알림을 보냅니다.  

[!INCLUDE [Notification Hubs Backend Service Introduction](includes/notification-hubs-backend-service-introduction.md)]

이 자습서에서는 다음 단계를 수행합니다.

> [!div class="checklist"]
>
> * [Push Notification Services 및 Azure Notification Hubs 설정](#set-up-push-notification-services-and-azure-notification-hub)
> * [ASP.NET Core Web API 백 엔드 애플리케이션 만들기](#create-an-aspnet-core-web-api-backend-application)
> * [플랫폼 간 React Native 애플리케이션 만들기](#create-a-cross-platform-react-native-application)
> * [푸시 알림에 사용할 기본 Android 프로젝트 구성](#configure-the-native-android-project-for-push-notifications)
> * [푸시 알림에 사용할 기본 iOS 프로젝트 구성](#configure-the-native-ios-project-for-push-notifications)
> * [솔루션 테스트](#test-the-solution)

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 따라 하려면 다음이 필요합니다.

* 리소스를 만들고 관리할 수 있는 [Azure 구독](https://portal.azure.com)
* [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)가 설치된 Mac(또는 [Visual Studio 2019](https://visualstudio.microsoft.com/vs)를 실행하고 **.NET을 사용한 모바일 개발** 워크로드가 설치된 PC)
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
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Google Firebase 콘솔](https://console.firebase.google.com/u/0/)
* [Microsoft Azure](https://portal.azure.com) 및 [Azure Notification Hubs를 사용하여 iOS 앱에 푸시 알림 보내기](/azure/notification-hubs/ios-sdk-get-started.md)
* [React Native](https://reactnative.dev/docs/getting-started).

제공된 단계는 [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)와 [Visual Studio Code](https://code.visualstudio.com/download)용이지만 [Visual Studio 2019](https://visualstudio.microsoft.com/vs)에서도 따라 할 수 있습니다.

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Push Notification Services 및 Azure Notification Hub 설정

이 섹션에서는 **[FCM(Firebase Cloud Messaging)](https://firebase.google.com/docs/cloud-messaging)** 및 **[APNS(Apple Push Notification Services)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)** 를 설정합니다. 그런 다음, 해당 서비스를 사용하는 알림 허브를 만들고 구성합니다.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>ASP.NET Core Web API 백 엔드 애플리케이션 만들기

이 섹션에서는 [디바이스 등록](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) 및 React Native 모바일 앱에 알림 보내기를 처리하는 [ASP.NET Core Web API](https://dotnet.microsoft.com/apps/aspnet/apis) 백 엔드를 만듭니다.

[!INCLUDE [Create an ASP.NET Core Web API backend application](includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-react-native-application"></a>플랫폼 간 React Native 애플리케이션 만들기

이 섹션에서는 플랫폼 간 방식으로 푸시 알림을 구현하는 [React Native](https://reactnative.dev/) 모바일 애플리케이션을 빌드합니다.

[!INCLUDE [Sample application generic overview](includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create React Native application](includes/notification-hubs-backend-service-sample-app-react-native.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>푸시 알림에 사용할 기본 Android 프로젝트 구성

[!INCLUDE [Configure the native Android project](includes/notification-hubs-backend-service-configure-react-native-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>푸시 알림에 사용할 기본 iOS 프로젝트 구성

[!INCLUDE [Configure the native iOS project](includes/notification-hubs-backend-service-configure-react-native-ios.md)]

## <a name="test-the-solution"></a>솔루션 테스트

이제 백 엔드 서비스를 통해 알림 보내기를 테스트할 수 있습니다.

[!INCLUDE [Testing the solution](includes/notification-hubs-backend-service-testing.md)]

## <a name="troubleshooting"></a>문제 해결

[!INCLUDE [Troubleshooting](includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>관련 링크

* [Azure Notification Hubs 개요](/azure/notification-hubs/notification-hubs-push-notification-overview.md)
* [Mac용 Visual Studio 설치](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* [Visual Studio Code 설치](https://code.visualstudio.com/download)
* [React Native 개발 환경 설정](https://reactnative.dev/docs/environment-setup)
* [백 엔드 작업에 대한 Notification Hubs SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [GitHub의 Notification Hubs SDK](https://github.com/Azure/azure-notificationhubs)
* [애플리케이션 백 엔드에 등록](/azure/notification-hubs/notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [등록 관리](/azure/notification-hubs/notification-hubs-push-notification-registration-management.md)
* [태그 사용](/azure/notification-hubs/notification-hubs-tags-segment-push-message.md)
* [사용자 지정 템플릿 사용](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)

## <a name="next-steps"></a>다음 단계

이제 백 엔드 서비스를 통해 알림 허브에 연결되는 기본 React Native 앱이 생겼으며, 알림을 보내고 받을 수 있습니다.

[!INCLUDE [Next steps](includes/notification-hubs-backend-service-next-steps.md)]