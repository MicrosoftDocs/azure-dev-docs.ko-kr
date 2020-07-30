---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 588a59014e01c7fc43c0c1573589be9a8776dd2e
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401641"
---
### <a name="create-the-xamarinforms-solution"></a>Xamarin.Forms 솔루션 만들기

1. **Visual Studio**에서 **빈 Forms 앱**을 템플릿으로 사용하여 새 **Xamarin.Forms** 솔루션을 만들고, **프로젝트 이름**에 대해 *PushDemo*를 입력합니다.

    > [!NOTE]
    > **빈 Forms 앱 구성** 대화 상자에서 **조직 식별자**가 이전에 사용한 값과 일치하고 **Android** 및 **iOS** 대상이 모두 선택되어 있는지 확인합니다.

1. *PushDemo* 솔루션을 **Ctrl** + **클릭**한 다음, **NuGet 패키지 업데이트**를 선택합니다.
1. *PushDemo* 솔루션을 **Ctrl** + **클릭**한 다음, **NuGet 패키지 관리..** 를 선택합니다.
1. **Newtonsoft.Json**을 검색하여 선택되어 있는지 확인합니다.
1. **패키지 추가**를 클릭한 다음, 사용 조건에 동의하라는 메시지가 표시되면 **동의함**을 클릭합니다.
1. 각 대상 플랫폼에서 앱을 빌드하고 실행하여(**명령** + **Enter 키**) 디바이스에서 템플릿 기반 앱 실행을 테스트합니다.

### <a name="implement-the-cross-platform-components"></a>플랫폼 간 구성 요소 구현

1. **PushDemo** 프로젝트를 **Ctrl** + **클릭**하고, **추가** 메뉴에서 **새 폴더**를 선택한 다음, **추가**를 클릭하여 *Models*를 **폴더 이름**으로 사용합니다.

1. **Models** 폴더를 **Ctrl** + **클릭**한 다음, **추가** 메뉴에서 **새 파일...** 을 선택합니다.

1. **일반** > **빈 클래스**를 차례로 선택하고, *DeviceInstallation.cs*를 입력한 다음, 다음 구현을 추가합니다.

    ```csharp
    using System.Collections.Generic;
    using Newtonsoft.Json;

    namespace PushDemo.Models
    {
        public class DeviceInstallation
        {
            [JsonProperty("installationId")]
            public string InstallationId { get; set; }

            [JsonProperty("platform")]
            public string Platform { get; set; }

            [JsonProperty("pushChannel")]
            public string PushChannel { get; set; }

            [JsonProperty("tags")]
            public List<string> Tags { get; set; } = new List<string>();
        }
    }
    ```

1. 다음 구현을 사용하여 *PushDemoAction.cs*라는 **빈 열거형**을 **Models** 폴더에 추가합니다.

    ```csharp
    namespace PushDemo.Models
    {
        public enum PushDemoAction
        {
            ActionA,
            ActionB
        }
    }
    ```

1. *Services*라는 새 폴더를 **PushDemo** 프로젝트에 추가하고, 다음 구현을 사용하여 *ServiceContainer.cs*라는 **빈 클래스**를 해당 폴더에 추가합니다.

     ```csharp
    using System;
    using System.Collections.Generic;

    namespace PushDemo.Services
    {
        public static class ServiceContainer
        {
            static readonly Dictionary<Type, Lazy<object>> services
                = new Dictionary<Type, Lazy<object>>();

            public static void Register<T>(Func<T> function)
                => services[typeof(T)] = new Lazy<object>(() => function());

            public static T Resolve<T>()
                => (T)Resolve(typeof(T));

            public static object Resolve(Type type)
            {
                {
                    if (services.TryGetValue(type, out var service))
                        return service.Value;

                    throw new KeyNotFoundException($"Service not found for type '{type}'");
                }
            }
        }
    }
    ```

    > [!NOTE]
    > 이는 [XamCAT](https://github.com/xamcat/mobcat-library) 리포지토리에서 잘라낸 [ServiceContainer](https://github.com/xamcat/mobcat-library/blob/master/MobCAT/ServiceContainer.cs) 클래스 버전입니다. 간단한 IoC(제어 반전) 컨테이너로 사용됩니다.

1. *IDeviceInstallationService.cs*라는 **빈 인터페이스**를 **Services** 폴더에 추가하고, 다음 코드를 추가합니다.

    ```csharp
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IDeviceInstallationService
        {
            string Token { get; set; }
            bool NotificationsSupported { get; }
            string GetDeviceId();
            DeviceInstallation GetDeviceInstallation(params string[] tags);
        }
    }
    ```

    > [!NOTE]
    > 이 인터페이스는 나중에 각 대상에서 구현하고 부트스트랩하여 백 엔드 서비스에 필요한 플랫폼 특정 기능 및 **DeviceInstallation** 정보를 제공합니다.

1. *INotificationRegistrationService.cs*라는 다른 **빈 인터페이스**를 **Services** 폴더에 추가하고, 다음 코드를 추가합니다.  

    ```csharp
    using System.Threading.Tasks;

    namespace PushDemo.Services
    {
        public interface INotificationRegistrationService
        {
            Task DeregisterDeviceAsync();
            Task RegisterDeviceAsync(params string[] tags);
            Task RefreshRegistrationAsync();
        }
    }
    ```

    > [!NOTE]
    > 이는 클라이언트와 백 엔드 서비스 간의 상호 작용을 처리합니다.

1. *INotificationActionService.cs*라는 다른 **빈 인터페이스**를 **Services** 폴더에 추가하고, 다음 코드를 추가합니다.  

    ```csharp
    namespace PushDemo.Services
    {
        public interface INotificationActionService
        {
            void TriggerAction(string action);
        }
    }
    ```

    > [!NOTE]
    > 이는 알림 작업의 처리를 중앙 집중화하는 간단한 메커니즘으로 사용됩니다.

1. 다음 구현을 사용하여 *INotificationActionService*에서 파생된 *IPushDemoNotificationActionService.cs*라는 **빈 인터페이스**를 **Services** 폴더에 추가합니다.  

    ```csharp
    using System;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IPushDemoNotificationActionService : INotificationActionService
        {
            event EventHandler<PushDemoAction> ActionTriggered;
        }
    }
    ```

    > [!NOTE]
    > 이 형식은 **PushDemo** 애플리케이션에만 적용되며, **PushDemoAction** 열거형을 사용하여 강력한 형식으로 트리거되는 작업을 식별합니다.

1. 다음 코드를 사용하여 *INotificationRegistrationService*를 구현하는 *NotificationRegistrationService.cs*라는 **빈 클래스**를 **Services** 폴더에 추가합니다.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using PushDemo.Models;
    using Xamarin.Essentials;

    namespace PushDemo.Services
    {
        public class NotificationRegistrationService : INotificationRegistrationService
        {
            const string RequestUrl = "api/notifications/installations";
            const string CachedDeviceTokenKey = "cached_device_token";
            const string CachedTagsKey = "cached_tags";

            string _baseApiUrl;
            HttpClient _client;
            IDeviceInstallationService _deviceInstallationService;

            public NotificationRegistrationService(string baseApiUri, string apiKey)
            {
                _client = new HttpClient();
                _client.DefaultRequestHeaders.Add("Accept", "application/json");
                _client.DefaultRequestHeaders.Add("apikey", apiKey);

                _baseApiUrl = baseApiUri;
            }

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService = ServiceContainer.Resolve<IDeviceInstallationService>());

            public async Task DeregisterDeviceAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                if (cachedToken == null)
                    return;

                var deviceId = DeviceInstallationService?.GetDeviceId();

                if (string.IsNullOrWhiteSpace(deviceId))
                    throw new Exception("Unable to resolve an ID for the device.");

                await SendAsync(HttpMethod.Delete, $"{RequestUrl}/{deviceId}")
                    .ConfigureAwait(false);

                SecureStorage.Remove(CachedDeviceTokenKey);
                SecureStorage.Remove(CachedTagsKey);
            }

            public async Task RegisterDeviceAsync(params string[] tags)
            {
                var deviceInstallation = DeviceInstallationService?.GetDeviceInstallation(tags);

                await SendAsync<DeviceInstallation>(HttpMethod.Put, RequestUrl, deviceInstallation)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedDeviceTokenKey, deviceInstallation.PushChannel)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedTagsKey, JsonConvert.SerializeObject(tags));
            }

            public async Task RefreshRegistrationAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                var serializedTags = await SecureStorage.GetAsync(CachedTagsKey)
                    .ConfigureAwait(false);

                if (string.IsNullOrWhiteSpace(cachedToken) ||
                    string.IsNullOrWhiteSpace(serializedTags) ||
                    string.IsNullOrWhiteSpace(DeviceInstallationService.Token) ||
                    cachedToken == DeviceInstallationService.Token)
                    return;

                var tags = JsonConvert.DeserializeObject<string[]>(serializedTags);

                await RegisterDeviceAsync(tags);
            }

            async Task SendAsync<T>(HttpMethod requestType, string requestUri, T obj)
            {
                string serializedContent = null;

                await Task.Run(() => serializedContent = JsonConvert.SerializeObject(obj))
                    .ConfigureAwait(false);

                await SendAsync(requestType, requestUri, serializedContent);
            }

            async Task SendAsync(
                HttpMethod requestType,
                string requestUri,
                string jsonRequest = null)
            {
                var request = new HttpRequestMessage(requestType, new Uri($"{_baseApiUrl}{requestUri}"));

                if (jsonRequest != null)
                    request.Content = new StringContent(jsonRequest, Encoding.UTF8, "application/json");

                var response = await _client.SendAsync(request).ConfigureAwait(false);

                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

    > [!NOTE]
    > **apiKey** 인수는 [API 키를 사용하여 클라이언트 인증](#authenticate-clients-using-an-api-key-optional) 섹션을 완료하도록 선택한 경우에만 필요합니다.

1. 다음 코드를 사용하여 *IPushDemoNotificationActionService*를 구현하는 *PushDemoNotificationActionService.cs*라는 **빈 클래스**를 **Services** 폴더에 추가합니다.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public class PushDemoNotificationActionService : IPushDemoNotificationActionService
        {
            readonly Dictionary<string, PushDemoAction> _actionMappings = new Dictionary<string, PushDemoAction>
            {
                { "action_a", PushDemoAction.ActionA },
                { "action_b", PushDemoAction.ActionB }
            };

            public event EventHandler<PushDemoAction> ActionTriggered = delegate { };

            public void TriggerAction(string action)
            {
                if (!_actionMappings.TryGetValue(action, out var pushDemoAction))
                    return;

                List<Exception> exceptions = new List<Exception>();

                foreach (var handler in ActionTriggered?.GetInvocationList())
                {
                    try
                    {
                        handler.DynamicInvoke(this, pushDemoAction);
                    }
                    catch (Exception ex)
                    {
                        exceptions.Add(ex);
                    }
                }

                if (exceptions.Any())
                    throw new AggregateException(exceptions);
            }
        }
    }
    ```

1. 다음 구현을 사용하여 *Config.cs*라는 **빈 클래스**를 **PushDemo** 프로젝트에 추가합니다.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            public static string ApiKey = "API_KEY";
            public static string BackendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
        }
    }
    ```

    > [!NOTE]
    > 이는 원본 제어에서 비밀을 유지하는 간단한 방법으로 사용됩니다. 이러한 값을 자동화된 빌드의 일부로 바꾸거나 로컬 partial 클래스를 사용하여 재정의할 수 있습니다. 이 작업은 다음 단계에서 수행합니다.
    >
    > **ApiKey** 필드는 [API 키를 사용하여 클라이언트 인증](#authenticate-clients-using-an-api-key-optional) 섹션을 완료하도록 선택한 경우에만 필요합니다.

1. 이번에는 다음 구현을 사용하여 *Config.local_secrets.cs*라는 다른 **빈 클래스**를 **PushDemo** 프로젝트에 추가합니다.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            static Config()
            {
                ApiKey = "<your_api_key>";
                BackendServiceEndpoint = "<your_api_app_url>";
            }
        }
    }
    ```

    > [!NOTE]
    > 자리 표시자 값을 사용자 고유의 값으로 바꿉니다. 백 엔드 서비스를 빌드할 때 이러한 값을 적어 두어야 합니다. **API 앱** URL은 ``https://<api_app_name>.azurewebsites.net/``이어야 합니다. 이 파일을 커밋하지 않으려면 ``*.local_secrets.*``를 gitignore 파일에 추가해야 합니다.
    >
    > **ApiKey** 필드는 [API 키를 사용하여 클라이언트 인증](#authenticate-clients-using-an-api-key-optional) 섹션을 완료하도록 선택한 경우에만 필요합니다.

1. 다음 구현을 사용하여 *Bootstrap.cs*라는 다른 **빈 클래스**를 **PushDemo** 프로젝트에 추가합니다.  

    ```csharp
    using System;
    using PushDemo.Services;

    namespace PushDemo
    {
        public static class Bootstrap
        {
            public static void Begin(Func<IDeviceInstallationService> deviceInstallationService)
            {
                ServiceContainer.Register(deviceInstallationService);

                ServiceContainer.Register<IPushDemoNotificationActionService>(()
                    => new PushDemoNotificationActionService());

                ServiceContainer.Register<INotificationRegistrationService>(()
                    => new NotificationRegistrationService(
                        Config.BackendServiceEndpoint,
                        Config.ApiKey));
            }
        }
    }
    ```

    > [!NOTE]
    > 플랫폼별 **IDeviceInstallationService** 구현에서 전달을 시작하면 각 플랫폼에서 **Begin** 메서드가 호출됩니다.
    >
    > **NotificationRegistrationService** **apiKey** 생성자 인수는 [API 키를 사용하여 클라이언트 인증](#authenticate-clients-using-an-api-key-optional) 섹션을 완료하도록 선택한 경우에만 필요합니다.

### <a name="implement-the-cross-platform-ui"></a>플랫폼 간 UI 구현

1. **PushDemo** 프로젝트에서 **MainPage.xaml**을 열고, **StackLayout** 컨트롤을 다음으로 바꿉니다.

    ```xml
    <StackLayout VerticalOptions="EndAndExpand"  
                 HorizontalOptions="FillAndExpand"
                 Padding="20,40">
        <Button x:Name="RegisterButton"
                Text="Register"
                Clicked="RegisterButtonClicked" />
        <Button x:Name="DeregisterButton"
                Text="Deregister"
                Clicked="DeregisterButtonClicked" />
    </StackLayout>
    ```

1. 이제 **MainPage.xaml.cs**에서 **읽기 전용** 지원 필드를 추가하여 **INotificationRegistrationService** 구현에 대한 참조를 저장합니다.

    ```csharp
    readonly INotificationRegistrationService _notificationRegistrationService;
    ```

1. **MainPage** 생성자에서 **ServiceContainer**를 사용하여 **INotificationRegistrationService** 구현을 확인하고 *_notificationRegistrationService_* 지원 필드에 할당합니다.

    ```csharp
    public MainPage()
    {
        InitializeComponent();

        _notificationRegistrationService =
            ServiceContainer.Resolve<INotificationRegistrationService>();
    }
    ```

1. 해당 **Register**/**Deregister** 메서드를 호출하는 **RegisterButton** 및 **DeregisterButton** 단추 **Clicked** 이벤트에 대한 이벤트 처리기를 구현합니다.

    ```csharp
    void RegisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.RegisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device registered"); });

    void DeregisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.DeregisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device deregistered"); });

    void ShowAlert(string message)
        => MainThread.BeginInvokeOnMainThread(()
            => DisplayAlert("PushDemo", message, "OK").ContinueWith((task)
                => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. 이제 **App.xaml.cs**에서 다음 네임스페이스가 참조되는지 확인합니다.

    ```csharp
    using PushDemo.Models;
    using PushDemo.Services;
    using Xamarin.Essentials;
    using Xamarin.Forms;
    ```

1. **IPushDemoNotificationActionService** **ActionTriggered** 이벤트에 대한 이벤트 처리기를 구현합니다.

    ```csharp
    void NotificationActionTriggered(object sender, PushDemoAction e)
        => ShowActionAlert(e);

    void ShowActionAlert(PushDemoAction action)
        => MainThread.BeginInvokeOnMainThread(()
            => MainPage?.DisplayAlert("PushDemo", $"{action} action received", "OK")
                .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. **App** 생성자에서 **ServiceContainer**를 사용하여 **IPushNotificationActionService** 구현을 확인하고, **IPushDemoNotificationActionService** **ActionTriggered** 이벤트를 구독합니다.

    ```csharp
    public App()
    {
        InitializeComponent();

        ServiceContainer.Resolve<IPushDemoNotificationActionService>()
            .ActionTriggered += NotificationActionTriggered;

        MainPage = new MainPage();
    }
    ```

    > [!NOTE]
    > 이는 단순히 푸시 알림 작업의 수신 및 전파를 보여 주기 위한 것입니다. 일반적으로 이러한 작업은 여기서 루트 **Page**, **MainPage**를 통해 경고를 표시하는 대신 특정 보기로 이동하거나 일부 데이터를 새로 고치는 등과 같은 방법으로 자동으로 처리됩니다.
