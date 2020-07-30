---
author: alexeystrakh
ms.author: alstrakh
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: eefda17bfa94261984dfbd86d36bd39a32f7d0ea
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401606"
---
### <a name="configure-required-ios-packages"></a>필요한 iOS 패키지 구성

이 패키지는 앱을 빌드할 때 [자동으로 연결](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md)됩니다. 네이티브 Pod만 설치하면 됩니다.

```bash
npx pod-install
```

### <a name="configure-infoplist-and-entitlementsplist"></a>Info.plist 및 Entitlements.plist 구성

1. "PushDemo/ios" 폴더로 이동하여 "PushDemo.xcworkspace" 작업 영역을 열고 최상위 프로젝트 "PushDemo"를 선택하고 "서명 및 기능" 탭을 선택합니다.

1. 프로비저닝 프로필에 사용된 값과 일치하도록 번들 식별자를 업데이트합니다.

1. "+" 단추를 사용하여 두 가지 새 기능,

    - 백그라운드 모드 기능 및 틱 원격 알림을 추가합니다.
    - 푸시 알림 기능

### <a name="handle-push-notifications-for-ios"></a>iOS 푸시 알림 처리

1. "AppDelegate.h"를 열고 다음 가져오기를 추가합니다.

    ```objective-c
    #import <UserNotifications/UNUserNotificationCenter.h>
    ```

1. `UNUserNotificationCenterDelegate`를 추가하여 "AppDelegate"가 지원하는 프로토콜 목록을 업데이트합니다.

    ```objective-c
    @interface AppDelegate : UIResponder <UIApplicationDelegate, RCTBridgeDelegate, UNUserNotificationCenterDelegate>
    ```

1. "AppDelegate.m"을 열고 필요한 모든 iOS 콜백을 구성합니다.

    ```objective-c
    #import <UserNotifications/UserNotifications.h>
    #import <RNCPushNotificationIOS.h>

    ...

    // Required to register for notifications
    - (void)application:(UIApplication *)application didRegisterUserNotificationSettings:(UIUserNotificationSettings *)notificationSettings
    {
     [RNCPushNotificationIOS didRegisterUserNotificationSettings:notificationSettings];
    }

    // Required for the register event.
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
     [RNCPushNotificationIOS didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
    }

    // Required for the notification event. You must call the completion handler after handling the remote notification.
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
    fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
      [RNCPushNotificationIOS didReceiveRemoteNotification:userInfo fetchCompletionHandler:completionHandler];
    }

    // Required for the registrationError event.
    - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error
    {
     [RNCPushNotificationIOS didFailToRegisterForRemoteNotificationsWithError:error];
    }

    // IOS 10+ Required for localNotification event
    - (void)userNotificationCenter:(UNUserNotificationCenter *)center
    didReceiveNotificationResponse:(UNNotificationResponse *)response
             withCompletionHandler:(void (^)(void))completionHandler
    {
      [RNCPushNotificationIOS didReceiveNotificationResponse:response];
      completionHandler();
    }

    // IOS 4-10 Required for the localNotification event.
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification
    {
     [RNCPushNotificationIOS didReceiveLocalNotification:notification];
    }

    //Called when a notification is delivered to a foreground app.
    -(void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      completionHandler(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge);
    }
    ```
