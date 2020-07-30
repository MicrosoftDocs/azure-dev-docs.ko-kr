---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 28b1db01e2b509198a837f96dcb19f119cf09335
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401631"
---
### <a name="configure-infoplist-and-entitlementsplist"></a>Info.plist 및 Entitlements.plist 구성

1. **Visual Studio** > **기본 설정...**  > **게시** > **Apple 개발자 계정**에서 **Apple 개발자 계정**으로 로그인했는지 확인하고, 적절한 *인증서* 및 *프로비저닝 프로필*을 다운로드했는지 확인합니다. 이전 단계에서 이 자산을 만들었습니다.

1. **PushDemo.iOS**에서 **Info.plist**를 열고, **BundleIdentifier**가 [Apple 개발자 포털](https://developer.apple.com)의 각 프로비저닝 프로필에 사용된 값과 일치하는지 확인합니다. **BundleIdentifier**는 ``com.<organization>.PushDemo`` 형식입니다.

1. 동일한 파일에서 **최소 시스템 버전**을 **13.0**으로 설정합니다.

    > [!NOTE]
    > 이 자습서에서는 **iOS 13.0 이상**을 실행하는 디바이스만 지원되지만, 이전 버전을 실행하는 디바이스를 지원하도록 확장할 수 있습니다.

1. **PushDemo.iOS**에 대한 **프로젝트 옵션**를 엽니다(프로젝트를 두 번 클릭).

1. **프로젝트 옵션**의 **빌드 > iOS 번들 서명**에 있는 **팀**에서 개발자 계정을 선택했는지 확인합니다. 그런 다음, "자동으로 서명 관리"를 선택하고 서명 인증서 및 프로비저닝 프로필이 자동으로 선택되어 있는지 확인합니다.

    > [!NOTE]
    > *서명 인증서* 및 *프로비저닝 프로필*이 자동으로 선택되지 않으면 **수동 프로비저닝**을 선택한 다음, **번들 서명 옵션**을 클릭합니다. **서명 ID**로 *팀*을 선택하고, 두 방법 모두 **플랫폼**으로 **iPhone**이 선택되도록 **디버그** 및 **릴리스** 구성의 **프로비저닝 프로필**을 모두 *PushDemo* 관련 프로비저닝 프로필로 선택합니다.

1. **PushDemo.iOS**에서 **Entitlements.plist**를 열고, **자격** 탭에서 볼 때 **푸시 알림 사용**이 선택되어 있는지 확인합니다. 그런 다음, **원본** 탭에서 볼 때 **APS 환경** 설정이 **개발**로 설정되어 있는지 확인합니다.

### <a name="handle-push-notifications-for-ios"></a>iOS 푸시 알림 처리

1. **PushDemo.iOS** 프로젝트를 **Ctrl** + **클릭**하고, **추가** 메뉴에서 **새 폴더**를 선택한 다음, **추가**를 클릭하여 *Services*를 **폴더 이름**으로 사용합니다.

1. **Services** 폴더를 **Ctrl** + **클릭**한 다음, **추가** 메뉴에서 **새 파일...** 을 선택합니다.

1. **일반** > **빈 클래스**를 차례로 선택하고, **이름**으로 *DeviceInstallationService.cs*를 입력한 다음, **새로 만들기**를 클릭하여 다음 구현을 추가합니다.

    ```csharp
    using System;
    using PushDemo.Models;
    using PushDemo.Services;
    using UIKit;

    namespace PushDemo.iOS.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            const int SupportedVersionMajor = 13;
            const int SupportedVersionMinor = 0;

            public string Token { get; set; }

            public bool NotificationsSupported
                => UIDevice.CurrentDevice.CheckSystemVersion(SupportedVersionMajor, SupportedVersionMinor);

            public string GetDeviceId()
                => UIDevice.CurrentDevice.IdentifierForVendor.ToString();

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetNotificationsSupportError());

                if (string.isNullOrWhitespace(Token))
                    throw new Exception("Unable to resolve token for APNS");

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "apns",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetNotificationsSupportError()
            {
                if (!NotificationsSupported)
                    return $"This app only supports notifications on iOS {SupportedVersionMajor}.{SupportedVersionMinor} and above. You are running {UIDevice.CurrentDevice.SystemVersion}.";

                if (Token == null)
                    return $"This app can support notifications but you must enable this in your settings.";


                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > 이 클래스는 고유 ID([UIDevice.IdentifierForVendor](https://docs.microsoft.com/dotnet/api/uikit.uidevice.identifierforvendor?view=xamarin-ios-sdk-12) 값 사용) 및 알림 허브 등록 페이로드를 제공합니다.

1. *Extensions*라는 새 폴더를 **PushDemo.iOS** 프로젝트에 추가하고, 다음 구현을 사용하여 *NSDataExtensions.cs*라는 **빈 클래스**를 해당 폴더에 추가합니다.

    ```csharp
    using System.Text;
    using Foundation;

    namespace PushDemo.iOS.Extensions
    {
        internal static class NSDataExtensions
        {
            internal static string ToHexString(this NSData data)
            {
                var bytes = data.ToArray();

                if (bytes == null)
                    return null;

                StringBuilder sb = new StringBuilder(bytes.Length * 2);

                foreach (byte b in bytes)
                    sb.AppendFormat("{0:x2}", b);

                return sb.ToString().ToUpperInvariant();
            }
        }
    }
    ```

1. **AppDelegate.cs**에서 다음 네임스페이스가 파일의 맨 위에 추가되었는지 확인합니다.

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Foundation;
    using PushDemo.iOS.Extensions;
    using PushDemo.iOS.Services;
    using PushDemo.Services;
    using UIKit;
    using UserNotifications;
    using Xamarin.Essentials;
    ```

1. **IPushDemoNotificationActionService**, **INotificationRegistrationService** 및 **IDeviceInstallationService** 구현에 대한 참조를 저장할 프라이빗 속성과 해당 지원 필드를 추가합니다.

    ```csharp
    IPushDemoNotificationActionService _notificationActionService;
    INotificationRegistrationService _notificationRegistrationService;
    IDeviceInstallationService _deviceInstallationService;

    IPushDemoNotificationActionService NotificationActionService
        => _notificationActionService ??
            (_notificationActionService =
            ServiceContainer.Resolve<IPushDemoNotificationActionService>());

    INotificationRegistrationService NotificationRegistrationService
        => _notificationRegistrationService ??
            (_notificationRegistrationService =
            ServiceContainer.Resolve<INotificationRegistrationService>());

    IDeviceInstallationService DeviceInstallationService
        => _deviceInstallationService ??
            (_deviceInstallationService =
            ServiceContainer.Resolve<IDeviceInstallationService>());
    ```

1. **RegisterForRemoteNotifications** 메서드를 추가하여 사용자 알림 설정을 등록한 다음, **APNS**를 사용하여 원격 알림을 등록합니다.

    ```csharp
    void RegisterForRemoteNotifications()
    {
        MainThread.BeginInvokeOnMainThread(() =>
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert |
                UIUserNotificationType.Badge |
                UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
        });
    }
    ```

1. **CompleteRegistrationAsync** 메서드를 추가하여 `IDeviceInstallationService.Token` 속성 값을 설정합니다. 디바이스 토큰이 마지막으로 저장된 이후 업데이트되었으면 등록을 새로 고치고 디바이스 토큰을 캐시합니다.

    ```csharp
    Task CompleteRegistrationAsync(NSData deviceToken)
    {
        DeviceInstallationService.Token = deviceToken.ToHexString();
        return NotificationRegistrationService.RefreshRegistrationAsync();
    }
    ```

1. **NSDictionary** 알림 데이터를 처리하고 **NotificationActionService.TriggerAction**을 조건부로 호출할 **ProcessNotificationActions** 메서드를 추가합니다.

    ```csharp
    void ProcessNotificationActions(NSDictionary userInfo)
    {
        if (userInfo == null)
            return;

        try
        {
            var actionValue = userInfo.ObjectForKey(new NSString("action")) as NSString;

            if (!string.IsNullOrWhiteSpace(actionValue?.Description))
                NotificationActionService.TriggerAction(actionValue.Description);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(ex.Message);
        }
    }
    ```

1. **RegisteredForRemoteNotifications** 메서드를 재정의하여 **deviceToken** 인수를 **CompleteRegistrationAsync** 메서드에 전달합니다.

    ```csharp
    public override void RegisteredForRemoteNotifications(
        UIApplication application,
        NSData deviceToken)
        => CompleteRegistrationAsync(deviceToken).ContinueWith((task)
            => { if (task.IsFaulted) throw task.Exception; });
    ```

1. **ReceivedRemoteNotification** 메서드를 재정의하여 **userInfo** 인수를 **ProcessNotificationActions** 메서드에 전달합니다.

    ```csharp
    public override void ReceivedRemoteNotification(
        UIApplication application,
        NSDictionary userInfo)
        => ProcessNotificationActions(userInfo);
    ```

1. **FailedToRegisterForRemoteNotifications** 메서드를 재정의하여 오류를 기록합니다.

    ```csharp
    public override void FailedToRegisterForRemoteNotifications(
        UIApplication application,
        NSError error)
        => Debug.WriteLine(error.Description);
    ```

    > [!NOTE]
    > 이것은 매우 중요한 자리 표시자입니다. 프로덕션 시나리오에 대한 적절한 로깅 및 오류 처리를 구현해야 합니다.

1. `Forms.Init` 호출이 플랫폼별 **IDeviceInstallationService** 구현에 전달되는 즉시 `Bootstrap.Begin`을 호출하도록 **FinishedLaunching** 메서드를 업데이트합니다.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. 동일한 메서드에서 조건부로 권한 부여를 요청하고 `Bootstrap.Begin` 직후 원격 알림을 등록합니다.

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        UNUserNotificationCenter.Current.RequestAuthorization(
                UNAuthorizationOptions.Alert |
                UNAuthorizationOptions.Badge |
                UNAuthorizationOptions.Sound,
                (approvalGranted, error) =>
                {
                    if (approvalGranted && error == null)
                        RegisterForRemoteNotifications();
                });
    }
    ```

1. 여전히 **FinishedLaunching**에서, 최종 **userInfo** 개체를 전달하는 **UIApplication.LaunchOptionsRemoteNotificationKey**가 **options** 인수에 포함된 경우 `LoadApplication`을 호출한 직후 **ProcessNotificationActions**를 호출합니다.

    ```csharp
    using (var userInfo = options?.ObjectForKey(
        UIApplication.LaunchOptionsRemoteNotificationKey) as NSDictionary)
            ProcessNotificationActions(userInfo);
    ```
