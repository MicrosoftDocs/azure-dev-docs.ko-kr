---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: d3c472b66ffc622fb3ff3e5d149a902f99fcd6f1
ms.sourcegitcommit: e97cb81a245ce7dcabeac3260abc3db7c30edd79
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91493273"
---
### <a name="validate-package-name-and-permissions"></a>패키지 이름 및 권한 유효성 검사

1. **PushDemo.Android**에서 **프로젝트 옵션** 연 다음, **빌드** 섹션에서 **Android 애플리케이션**을 엽니다.

1. **패키지 이름**이 [Firebase 콘솔](https://console.firebase.google.com) **PushDemo** 프로젝트에서 사용한 값과 일치하는지 확인합니다. **패키지 이름**은 ``com.<organization>.pushdemo``형식입니다.

1. **최소 Android 버전**을 **Android 8.0(API 레벨 26)** 으로 설정하고 **대상 Android 버전**을 최신 **API 레벨**로 설정합니다.

    > [!NOTE]
    > 이 자습서에서는 **API 레벨 26 이상**을 실행하는 디바이스만 지원되지만, 이전 버전을 실행하는 디바이스를 지원하도록 확장할 수 있습니다.

1. **필요한 권한**에서 **인터넷** 및 **READ_PHONE_STATE** 권한을 사용하도록 설정합니다.

1. **확인**을 클릭합니다.

### <a name="add-the-xamarin-google-play-services-base-and-xamarinfirebasemessaging-packages"></a>Xamarin Google Play 서비스 기본 및 Xamarin.Firebase.Messaging 패키지 추가

1. **PushDemo.Android**에서, **Packages** 폴더를 **Ctrl** + **클릭**한 다음, **NuGet 패키지 관리...** 를 선택합니다.

1. **Xamarin.GooglePlayServices.Base**(**Basement** 아님)를 검색하여 선택합니다.

1. **Xamarin.Firebase.Messaging**을 검색하여 선택합니다.

1. **패키지 추가**를 클릭한 다음, **사용 조건**에 동의하라는 메시지가 표시되면 **동의**를 클릭합니다.

### <a name="add-the-google-services-json-file"></a>Google Services JSON 파일 추가

1. `PushDemo.Android` 프로젝트를 **Ctrl** + **클릭**한 다음, **추가** 메뉴에서 **기존 파일...** 을 선택합니다.

1. 이전에 [Firebase 콘솔](https://console.firebase.google.com)에서 **PushDemo** 프로젝트를 설정할 때 다운로드한 *google-services.json* 파일을 선택한 다음, **열기**를 클릭합니다.

1. 메시지가 표시되면 **파일을 디렉터리에 복사**를 선택합니다.

1. `PushDemo.Android` 프로젝트 내에서 *google-services.json* 파일을 **Ctrl** + **클릭**한 다음, **GoogleServicesJson**이 **빌드 작업**으로 설정되었는지 확인합니다.

### <a name="handle-push-notifications-for-android"></a>Android 푸시 알림 처리

1. `PushDemo.Android` 프로젝트를 **Ctrl** + **클릭**하고, **추가** 메뉴에서 **새 폴더**를 선택한 다음, **추가**를 클릭하여 *Services*를 **폴더 이름**으로 사용합니다.

1. **Services** 폴더를 **Ctrl** + **클릭**한 다음, **추가** 메뉴에서 **새 파일...** 을 선택합니다.

1. **일반** > **빈 클래스**를 차례로 선택하고, **이름**으로 *DeviceInstallationService.cs*를 입력한 다음, **새로 만들기**를 클릭하여 다음 구현을 추가합니다.

    ```csharp
    using System;
    using Android.App;
    using Android.Gms.Common;
    using PushDemo.Models;
    using PushDemo.Services;
    using static Android.Provider.Settings;

    namespace PushDemo.Droid.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            public string Token { get; set; }

            public bool NotificationsSupported
                => GoogleApiAvailability.Instance
                    .IsGooglePlayServicesAvailable(Application.Context) == ConnectionResult.Success;

            public string GetDeviceId()
                => Secure.GetString(Application.Context.ContentResolver, Secure.AndroidId);

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetPlayServicesError());

                if (string.isNullOrWhitespace(Token))
                    throw new Exception("Unable to resolve token for FCM");

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "fcm",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetPlayServicesError()
            {
                int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(Application.Context);

                if (resultCode != ConnectionResult.Success)
                    return GoogleApiAvailability.Instance.IsUserResolvableError(resultCode) ?
                               GoogleApiAvailability.Instance.GetErrorString(resultCode) :
                               "This device is not supported";

                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > 이 클래스는 알림 허브 등록 페이로드의 일부로 고유한 ID([Secure.AndroidId](/dotnet/api/android.provider.settings.secure.androidid?view=xamarin-android-sdk-9) 사용)를 제공합니다.

1. 다른 **빈 클래스**를 *PushNotificationFirebaseMessagingService.cs*라는 **Services** 폴더에 추가하고 다음 구현을 추가합니다.

    ```csharp
    using Android.App;
    using Android.Content;
    using Firebase.Messaging;
    using PushDemo.Services;

    namespace PushDemo.Droid.Services
    {
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class PushNotificationFirebaseMessagingService : FirebaseMessagingService
        {
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

            public override void OnNewToken(string token)
            {
                DeviceInstallationService.Token = token;

                NotificationRegistrationService.RefreshRegistrationAsync()
                    .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; });
            }

            public override void OnMessageReceived(RemoteMessage message)
            {
                if(message.Data.TryGetValue("action", out var messageAction))
                    NotificationActionService.TriggerAction(messageAction);
            }
        }
    }
    ```

1. **MainActivity.cs**에서 다음 네임스페이스가 파일의 맨 위에 추가되었는지 확인합니다.

    ```csharp
    using System;
    using Android.App;
    using Android.Content;
    using Android.Content.PM;
    using Android.OS;
    using Android.Runtime;
    using Firebase.Iid;
    using PushDemo.Droid.Services;
    using PushDemo.Services;
    ```

1. **MainActivity.cs**에서 **LaunchMode**를 **SingleTop**으로 설정합니다. 이렇게 하면 열 때 **MainActivity**가 다시 생성되지 않습니다.

    ```csharp
    [Activity(
        Label = "PushDemo",
        LaunchMode = LaunchMode.SingleTop,
        Icon = "@mipmap/icon",
        Theme = "@style/MainTheme",
        MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    ```

1. **IPushNotificationActionService** 및 **IDeviceInstallationService** 구현에 대한 참조를 저장할 프라이빗 속성과 해당 지원 필드를 추가합니다.

    ```csharp
    IPushDemoNotificationActionService _notificationActionService;
    IDeviceInstallationService _deviceInstallationService;

    IPushDemoNotificationActionService NotificationActionService
        => _notificationActionService ??
            (_notificationActionService =
            ServiceContainer.Resolve<IPushDemoNotificationActionService>());

    IDeviceInstallationService DeviceInstallationService
        => _deviceInstallationService ??
            (_deviceInstallationService =
            ServiceContainer.Resolve<IDeviceInstallationService>());
    ```

1. **Firebase** 토큰을 검색하여 저장할 **IOnSuccessListener** 인터페이스를 구현합니다.

    ```csharp
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity, Android.Gms.Tasks.IOnSuccessListener
    {
        ...

        public void OnSuccess(Java.Lang.Object result)
            => DeviceInstallationService.Token =
                result.Class.GetMethod("getToken").Invoke(result).ToString();
    }
    ```

1. 주어진 **의도**에 *action*이라는 추가 값이 있는지 확인하는 **ProcessNotificationActions**라는 새 메서드를 추가합니다. **IPushDemoNotificationActionService** 구현을 사용하여 해당 작업을 조건부로 트리거합니다.

    ```csharp
    void ProcessNotificationActions(Intent intent)
    {
        try
        {
            if (intent?.HasExtra("action") == true)
            {
                var action = intent.GetStringExtra("action");

                if (!string.IsNullOrEmpty(action))
                    NotificationActionService.TriggerAction(action);
            }
        }
        catch (Exception ex)
        {
            System.Diagnostics.Debug.WriteLine(ex.Message);
        }
    }
    ```

1. **OnNewIntent** 메서드를 재정의하여 **ProcessNotificationActions** 메서드를 호출합니다.

    ```csharp
    protected override void OnNewIntent(Intent intent)
    {
        base.OnNewIntent(intent);
        ProcessNotificationActions(intent);
    }
    ```

    > [!NOTE]
    > **활동**에 대한 **LaunchMode**가 **SingleTop**로 설정되었기 때문에, **의도**가 **OnCreate** 메서드가 아닌 **OnNewIntent** 메서드를 통해 기존 **활동** 인스턴스로 전송되므로 **OnCreate** 및 **OnNewIntent** 메서드에서 들어오는 의도를 모두 처리해야 합니다.

1. `base.OnCreate` 호출이 플랫폼별 **IDeviceInstallationService** 구현에 전달되는 즉시 `Bootstrap.Begin`을 호출하도록 **OnCreate** 메서드를 업데이트합니다.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. 동일한 메서드에서, **FirebaseApp** 인스턴스에서 **GetInstanceId**를 조건부로 호출하고, `Bootstrap.Begin` 호출 바로 뒤에 **MainActivity**를 **IOnSuccessListener**로 추가합니다.

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        FirebaseInstanceId.GetInstance(Firebase.FirebaseApp.Instance)
            .GetInstanceId()
            .AddOnSuccessListener(this);
    }
    ```

1. 여전히 **OnCreate**에서, `LoadApplication` 호출 바로 뒤에 **ProcessNotificationActions**를 호출하여 현재 **의도**를 전달합니다.

    ```cs
    ...

    LoadApplication(new App());

    ProcessNotificationActions(Intent);
    ```

> [!NOTE]
> 푸시 알림을 계속 받으려면 앱을 실행할 때마다 버그 세션에서 앱을 다시 등록하고 중지해야 합니다.