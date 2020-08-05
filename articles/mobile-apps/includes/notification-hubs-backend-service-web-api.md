---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 9d7db7db5a1b7323bd10e7e9cc87ca3b9a95826a
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401676"
---
### <a name="create-a-web-project"></a>웹 프로젝트 만들기

1. **Visual Studio**에서 **파일** > **새 솔루션**을 차례로 선택합니다.

1. **.NET Core** > **앱** > **ASP.NET Core** > **API** > **다음**을 차례로 선택합니다.
  
1. **새 ASP.NET Core Web API 구성** 대화 상자에서 **.NET Core 3.1**의 **대상 프레임워크**를 선택합니다.

1. **프로젝트 이름**에 대해 *PushDemoApi*를 입력한 다음, **만들기**를 선택합니다.

1. 디버깅을 시작하여(**명령** + **Enter 키**) 템플릿 기반 앱을 테스트합니다.

    > [!NOTE]
    > 템플릿 기반 앱은 **WeatherForecastController**를 *launchUrl*로 사용하도록 구성되어 있습니다. 이는 **Properties** > **launchSettings.json**에서 설정됩니다.  
    >
    > **잘못된 개발 인증서를 찾았습니다.** 라는 메시지가 표시되면 다음을 수행합니다.
    >
    > 1. 이 문제를 해결하기 위해 'dotnet dev-certs https' 도구를 실행하려면 **예**를 클릭하여 동의합니다. 그러면 'dotnet dev-certs https' 도구에서 인증서 암호 및 키 집합 암호를 입력하라는 메시지를 표시합니다.
    >
    > 1. **새 인증서를 설치하고 신뢰할지**를 묻는 메시지가 표시되면 **예**를 클릭한 다음, 키 집합 암호를 입력합니다.

1. **Controllers** 폴더를 펼친 다음, **WeatherForecastController.cs**를 삭제합니다.

1. **WeatherForecast.cs**를 삭제합니다.

1. **PushDemoApi** 프로젝트를 **Ctrl** + **클릭**한 다음, **추가** 메뉴에서 **새 파일...** 을 선택합니다.

1. [비밀 관리자 도구](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=linux#secret-manager)를 사용하여 로컬 구성 값을 설정합니다. 솔루션에서 비밀을 분리하면 원본 제어에서 종료되지 않습니다. **터미널**을 연 다음, 프로젝트 파일의 디렉터리로 이동하여 다음 명령을 실행합니다.

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set "NotificationHub:Name" <value>
    dotnet user-secrets set "NotificationHub:ConnectionString" <value>
    ```

    자리 표시자 값을 사용자 고유의 알림 허브 이름 및 연결 문자열 값으로 바꿉니다. [알림 허브 만들기](#create-a-notification-hub) 섹션에서 이를 적어 두었습니다. 그렇지 않으면 [Azure](https://portal.azure.com)에서 조회할 수 있습니다.

    **NotificationsHub:Name**:  
    **개요**의 위쪽에 있는 **기본 정보** 요약의 *이름*을 참조하세요.  

    **NotificationHub:ConnectionString**:  
    **액세스 정책**의 *DefaultFullSharedAccessSignature*를 참조하세요.

    > [!NOTE]
    > 프로덕션 시나리오의 경우 연결 문자열을 안전하게 저장하는 [Azure KeyVault](https://azure.microsoft.com/services/key-vault)와 같은 옵션을 살펴볼 수 있습니다. 간단히 하기 위해 비밀이 [Azure App Service](https://azure.microsoft.com/services/app-service/) 애플리케이션 설정에 추가됩니다.

### <a name="authenticate-clients-using-an-api-key-optional"></a>API 키를 사용하여 클라이언트 인증(선택 사항)

API 키는 토큰만큼 안전하지 않지만 이 자습서의 용도로 충분합니다. API 키는 [ASP.NET 미들웨어](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/?view=aspnetcore-3.1)를 통해 쉽게 구성할 수 있습니다.

1. **API 키**를 로컬 구성 값에 추가합니다.

    ```bash
    dotnet user-secrets set "Authentication:ApiKey" <value>
    ```

    > [!NOTE]
    > 자리 표시자 값을 사용자 고유의 값으로 바꾸고 적어 둡니다.

1. **PushDemoApi** 프로젝트를 **Ctrl** + **클릭**하고, **추가** 메뉴에서 **새 폴더**를 선택한 다음, **추가**를 클릭하여 *Authentication*을 **폴더 이름**으로 사용합니다.

1. **Authentication** 폴더를 **Ctrl** + **클릭**한 다음, **추가** 메뉴에서 **새 파일...** 을 선택합니다.

1. **일반** > **빈 클래스**를 차례로 선택하고, **이름**에 대해 *ApiKeyAuthOptions.cs*를 입력한 다음, **새로 만들기**를 클릭하여 다음 구현을 추가합니다.

    ```csharp
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthOptions : AuthenticationSchemeOptions
        {
            public const string DefaultScheme = "ApiKey";
            public string Scheme => DefaultScheme;
            public string ApiKey { get; set; }
        }
    }
    ```

1. *ApiKeyAuthHandler.cs*라는 다른 **빈 클래스**를 **Authentication** 폴더에 추가하고, 다음 구현을 추가합니다.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Security.Claims;
    using System.Text.Encodings.Web;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthHandler : AuthenticationHandler<ApiKeyAuthOptions>
        {
            const string ApiKeyIdentifier = "apikey";

            public ApiKeyAuthHandler(
                IOptionsMonitor<ApiKeyAuthOptions> options,
                ILoggerFactory logger,
                UrlEncoder encoder,
                ISystemClock clock)
                : base(options, logger, encoder, clock) {}

            protected override Task<AuthenticateResult> HandleAuthenticateAsync()
            {
                string key = string.Empty;

                if (Request.Headers[ApiKeyIdentifier].Any())
                {
                    key = Request.Headers[ApiKeyIdentifier].FirstOrDefault();
                }
                else if (Request.Query.ContainsKey(ApiKeyIdentifier))
                {
                    if (Request.Query.TryGetValue(ApiKeyIdentifier, out var queryKey))
                        key = queryKey;
                }

                if (string.IsNullOrWhiteSpace(key))
                    return Task.FromResult(AuthenticateResult.Fail("No api key provided"));

                if (!string.Equals(key, Options.ApiKey, StringComparison.Ordinal))
                    return Task.FromResult(AuthenticateResult.Fail("Invalid api key."));

                var identities = new List<ClaimsIdentity> {
                    new ClaimsIdentity("ApiKeyIdentity")
                };

                var ticket = new AuthenticationTicket(
                    new ClaimsPrincipal(identities), Options.Scheme);

                return Task.FromResult(AuthenticateResult.Success(ticket));
            }
        }
    }
    ```

    > [!NOTE]
    > [인증 처리기](https://docs.microsoft.com/aspnet/core/security/authentication/?view=aspnetcore-3.1#authentication-handler)는 체계(이 경우 사용자 지정 API 키 체계)의 동작을 구현하는 유형입니다.

1. *ApiKeyAuthenticationBuilderExtensions.cs*라는 다른 **빈 클래스**를 **Authentication** 폴더에 추가하고, 다음 구현을 추가합니다.

    ```csharp
    using System;
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public static class AuthenticationBuilderExtensions
        {
            public static AuthenticationBuilder AddApiKeyAuth(
                this AuthenticationBuilder builder,
                Action<ApiKeyAuthOptions> configureOptions)
            {
                return builder
                    .AddScheme<ApiKeyAuthOptions, ApiKeyAuthHandler>(
                        ApiKeyAuthOptions.DefaultScheme,
                        configureOptions);
            }
        }
    }
    ```

    > [!NOTE]
    > 이 확장 메서드는 **Startup.cs**의 미들웨어 구성 코드를 간소화하여 읽기 쉽고 일반적으로 더 쉽게 수행할 수 있도록 합니다.

1. **Startup.cs**의 **services.AddControllers** 메서드에 대한 호출 아래에서 API 키 인증을 구성하도록 **ConfigureServices** 메서드를 업데이트합니다.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();

        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = ApiKeyAuthOptions.DefaultScheme;
            options.DefaultChallengeScheme = ApiKeyAuthOptions.DefaultScheme;
        }).AddApiKeyAuth(Configuration.GetSection("Authentication").Bind);
    }
    ```

1. 여전히 **Startup.cs**에서 **UseAuthentication** 및 **UseAuthorization** 확장 메서드를 앱의 **IApplicationBuilder**에서 호출하도록 **Configure** 메서드를 업데이트합니다. 이러한 메서드는 **UseRouting** 이후 및 **app.UseEndpoints** 이전에 호출해야 합니다.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseHttpsRedirection();

        app.UseRouting();

        app.UseAuthentication();

        app.UseAuthorization();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
    ```

    > [!NOTE]
    > **UseAuthentication**을 호출하면 이전에 등록된 **ConfigureServices**의 인증 체계를 사용하는 미들웨어가 등록됩니다. 이는 인증되는 사용자에 따라 달라지는 미들웨어를 먼저 호출해야 합니다.

### <a name="add-dependencies-and-configure-services"></a>종속성 추가 및 서비스 구성

ASP.NET Core는 클래스와 해당 종속성 간에 [IoC(제어 반전)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)를 구현하는 기술인 [DI(종속성 주입)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1) 소프트웨어 디자인 패턴을 지원합니다.  

백 엔드 작업에 대한 알림 허브 및 [Notification Hubs SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)를 사용하면 서비스 내에 캡슐화됩니다. 서비스는 적절한 추상화를 통해 등록되고 사용할 수 있게 됩니다.

1. **Dependencies** 폴더를 **Ctrl** + **클릭**한 다음, **NuGet 패키지 관리...** 를 선택합니다.

1. **Microsoft.Azure.NotificationHubs**를 검색하여 선택되어 있는지 확인합니다.

1. **패키지 추가**를 클릭한 다음, 사용 조건에 동의하라는 메시지가 표시되면 **동의함**을 클릭합니다.

1. **PushDemoApi** 프로젝트를 **Ctrl** + **클릭**하고, **추가** 메뉴에서 **새 폴더**를 선택한 다음, **추가**를 클릭하여 *Models*를 **폴더 이름**으로 사용합니다.

1. **Models** 폴더를 **Ctrl** + **클릭**한 다음, **추가** 메뉴에서 **새 파일...** 을 선택합니다.

1. **일반** > **빈 클래스**를 차례로 선택하고, **이름**에 대해 *PushTemplates.cs*를 입력한 다음, **새로 만들기**를 클릭하여 다음 구현을 추가합니다.

    ```csharp
    namespace PushDemoApi.Models
    {
        public class PushTemplates
        {
            public class Generic
            {
                public const string Android = "{ \"notification\": { \"title\" : \"PushDemo\", \"body\" : \"$(alertMessage)\"}, \"data\" : { \"action\" : \"$(alertAction)\" } }";
                public const string iOS = "{ \"aps\" : {\"alert\" : \"$(alertMessage)\"}, \"action\" : \"$(alertAction)\" }";
            }

            public class Silent
            {
                public const string Android = "{ \"data\" : {\"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\"} }";
                public const string iOS = "{ \"aps\" : {\"content-available\" : 1, \"apns-priority\": 5, \"sound\" : \"\", \"badge\" : 0}, \"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\" }";
            }
        }
    }
    ```

    > [!NOTE]
    > 이 클래스에는 이 시나리오에 필요한 제네릭 및 자동 알림에 대한 토큰화된 알림 페이로드가 포함되어 있습니다. 페이로드는 서비스를 통해 기존 설치를 업데이트하지 않고도 실험을 수행할 수 있도록 [설치](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation?view=azure-dotnet) 외부에서 정의됩니다. 설치에 대한 변경을 이 방식으로 처리하는 것은 이 자습서의 범위를 벗어납니다. 프로덕션의 경우 [사용자 지정 템플릿](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages)을 사용하는 것이 좋습니다.

1. **일반** > **빈 클래스**를 차례로 선택하고, **이름**에 대해 *DeviceInstallation.cs*를 입력한 다음, **새로 만들기**를 클릭하여 다음 구현을 추가합니다.

    ```csharp
    using System.Collections.Generic;
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class DeviceInstallation
        {
            [Required]
            public string InstallationId { get; set; }

            [Required]
            public string Platform { get; set; }

            [Required]
            public string PushChannel { get; set; }

            public IList<string> Tags { get; set; } = Array.Empty<string>();
        }
    }
    ```

1. *NotificationRequest.cs*라는 다른 **빈 클래스**를 **Models** 폴더에 추가하고, 다음 구현을 추가합니다.

    ```csharp
    using System;

    namespace PushDemoApi.Models
    {
        public class NotificationRequest
        {
            public string Text { get; set; }
            public string Action { get; set; }
            public string[] Tags { get; set; } = Array.Empty<string>();
            public bool Silent { get; set; }
        }
    }
    ```

1. *NotificationHubOptions.cs*라는 다른 **빈 클래스**를 **Models** 폴더에 추가하고, 다음 구현을 추가합니다.

    ```csharp
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class NotificationHubOptions
        {
            [Required]
            public string Name { get; set; }

            [Required]
            public string ConnectionString { get; set; }
        }
    }
    ```

1. *Services*라는 새 폴더를 **PushDemoApi** 프로젝트에 추가합니다.

1. *INotificationService.cs*라는 **빈 인터페이스**를 **Services** 폴더에 추가하고, 다음 구현을 추가합니다.

    ```csharp
    using System.Threading.Tasks;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public interface INotificationService
        {
            Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token);
            Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token);
            Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token);
        }
    }
    ```

1. *NotificationHubsService.cs*라는 **빈 클래스**를 **Services** 폴더에 추가하고, **INotificationService** 인터페이스를 구현하는 다음 코드를 추가합니다.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public class NotificationHubService : INotificationService
        {
            readonly NotificationHubClient _hub;
            readonly Dictionary<string, NotificationPlatform> _installationPlatform;
            readonly ILogger<NotificationHubService> _logger;

            public NotificationHubService(IOptions<NotificationHubOptions> options, ILogger<NotificationHubService> logger)
            {
                _logger = logger;
                _hub = NotificationHubClient.CreateClientFromConnectionString(
                    options.Value.ConnectionString,
                    options.Value.Name);

                _installationPlatform = new Dictionary<string, NotificationPlatform>
                {
                    { nameof(NotificationPlatform.Apns).ToLower(), NotificationPlatform.Apns },
                    { nameof(NotificationPlatform.Fcm).ToLower(), NotificationPlatform.Fcm }
                };
            }

            public async Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(deviceInstallation?.InstallationId) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.Platform) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.PushChannel))
                    return false;

                var installation = new Installation()
                {
                    InstallationId = deviceInstallation.InstallationId,
                    PushChannel = deviceInstallation.PushChannel,
                    Tags = deviceInstallation.Tags
                };

                if (_installationPlatform.TryGetValue(deviceInstallation.Platform, out var platform))
                    installation.Platform = platform;
                else
                    return false;

                try
                {
                    await _hub.CreateOrUpdateInstallationAsync(installation, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(installationId))
                    return false;

                try
                {
                    await _hub.DeleteInstallationAsync(installationId, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token)
            {
                if ((notificationRequest.Silent &&
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
                    (!notificationRequest.Silent &&
                    (string.IsNullOrWhiteSpace(notificationRequest?.Text)) ||
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)))
                    return false;

                var androidPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.Android :
                    PushTemplates.Generic.Android;

                var iOSPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.iOS :
                    PushTemplates.Generic.iOS;

                var androidPayload = PrepareNotificationPayload(
                    androidPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                var iOSPayload = PrepareNotificationPayload(
                    iOSPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                try
                {
                    if (notificationRequest.Tags.Length == 0)
                    {
                        // This will broadcast to all users registered in the notification hub
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, token);
                    }
                    else if (notificationRequest.Tags.Length <= 20)
                    {
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, notificationRequest.Tags, token);
                    }
                    else
                    {
                        var notificationTasks = notificationRequest.Tags
                            .Select((value, index) => (value, index))
                            .GroupBy(g => g.index / 20, i => i.value)
                            .Select(tags => SendPlatformNotificationsAsync(androidPayload, iOSPayload, tags, token));

                        await Task.WhenAll(notificationTasks);
                    }

                    return true;
                }
                catch (Exception e)
                {
                    _logger.LogError(e, "Unexpected error sending notification");
                    return false;
                }
            }

            string PrepareNotificationPayload(string template, string text, string action) => template
                .Replace("$(alertMessage)", text, StringComparison.InvariantCulture)
                .Replace("$(alertAction)", action, StringComparison.InvariantCulture);

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, token)
                };

                return Task.WhenAll(sendTasks);
            }

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, IEnumerable<string> tags, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, tags, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, tags, token)
                };

                return Task.WhenAll(sendTasks);
            }
        }
    }
    ```

    > [!NOTE]
    > **SendTemplateNotificationAsync**에 제공되는 태그 식은 20개로 제한됩니다. 대부분의 연산자의 경우 6개로 제한되지만, 여기서는 OR(||)만 식에 포함됩니다. 요청에 20개가 넘는 태그가 있으면 여러 요청으로 분할해야 합니다. 자세한 내용은 [라우팅 및 태그 식](https://msdn.microsoft.com/library/azure/Dn530749.aspx?f=255&MSPPError=-2147217396) 설명서를 참조하세요.

1. **Startup.cs**에서 **NotificationHubsService**를 **INotificationService**의 싱글톤 구현으로 추가하도록 **ConfigureServices** 메서드를 업데이트합니다.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ...

        services.AddSingleton<INotificationService, NotificationHubService>();

        services.AddOptions<NotificationHubOptions>()
            .Configure(Configuration.GetSection("NotificationHub").Bind)
            .ValidateDataAnnotations();
    }
    ```

### <a name="create-the-notifications-api"></a>알림 API 만들기

1. **Controllers** 폴더를 **Ctrl** + **클릭**한 다음, **추가** 메뉴에서 **새 파일...** 을 선택합니다.

1. **ASP.NET Core** > **Web API 컨트롤러 클래스**를 선택하고, **이름**에 대해 *NotificationsController*를 입력한 다음, **새로 만들기**를 클릭합니다.

1. 다음 네임스페이스를 파일의 위쪽에 추가합니다.

    ```csharp
    using System.ComponentModel.DataAnnotations;
    using System.Net;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using PushDemoApi.Models;
    using PushDemoApi.Services;
    ```

1. **ControllerBase**에서 파생되고 **ApiController** 특성으로 데코레이팅되도록 템플릿 기반 컨트롤을 업데이트합니다.

    ```cs
    [ApiController]
    [Route("api/[controller]")]
    public class NotificationsController : ControllerBase
    {
        // Templated methods here
    }
    ```

    > [!NOTE]
    > **Controller** 기본 클래스는 보기 지원을 제공하지만, 이 경우에는 필요하지 않으므로 **ControllerBase**를 대신 사용할 수 있습니다.

1. [API 키를 사용하여 클라이언트 인증](#authenticate-clients-using-an-api-key-optional) 섹션을 완료하도록 선택한 경우 **NotificationsController**도 **Authorize** 특성으로 데코레이팅해야 합니다.

    ```cs
    [Authorize]
    ```

1. 등록된 **INotificationService** 인스턴스를 인수로 수락하고, 이를 읽기 전용 멤버에 할당하도록 생성자를 업데이트합니다.

    ```cs
    readonly INotificationService _notificationService;

    public NotificationsController(INotificationService notificationService)
    {
        _notificationService = notificationService;
    }
    ```

1. **launchSettings.json**(**Properties** 폴더 내)에서 **RegistrationsController** **Route** 특성에 지정된 URL과 일치하도록 **launchUrl**을 `weatherforecast`에서 *api/notifications*로 변경합니다.

1. 디버깅을 시작하여(**명령** + **Enter 키**) 앱에서 새 **NotificationsController**를 사용하고 **401 권한 없음** 상태를 반환하는지 확인합니다.

    > [!NOTE]
    > Visual Studio에서 브라우저를 통해 앱을 자동으로 시작하지 못할 수 있습니다. 이 시점부터 [Postman](https://www.postman.com/downloads)을 사용하여 API를 테스트합니다.

1. 새 **[Postman](https://www.postman.com/downloads)** 탭에서 요청을 **GET**으로 설정하고 아래 주소를 입력합니다.

    ```bash
    https://localhost:5001/api/notifications
    ```

    > [!NOTE]
    > localhost 주소는 **Properties** > **launchSettings.json**에 있는 **applicationUrl** 값과 일치해야 합니다. 기본값은 `https://localhost:5001;http://localhost:5000`이어야 하지만 404 응답이 수신되는지 확인하기 위한 것입니다.

1. [API 키를 사용하여 클라이언트 인증](#authenticate-clients-using-an-api-key-optional) 섹션을 완료하도록 선택한 경우 **apikey** 값을 포함하도록 요청 헤더를 구성해야 합니다.

   | 키                            | 값                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. **보내기** 단추를 클릭합니다.

    > [!NOTE]
    > 일부 **JSON** 콘텐츠가 포함된 **200 OK** 상태를 받습니다.
    >
    > **SSL 인증서 확인** 경고 메시지가 표시되면 **설정**에서 SSL 인증서 확인 요청 **[Postman](https://www.postman.com/downloads)** 설정을 해제할 수 있습니다.

1. 템플릿 기반 클래스 메서드를 다음 코드로 바꿉니다.

    ```csharp
    [HttpPut]
    [Route("installations")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> UpdateInstallation(
        [Required]DeviceInstallation deviceInstallation)
    {
        var success = await _notificationService
            .CreateOrUpdateInstallationAsync(deviceInstallation, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpDelete()]
    [Route("installations/{installationId}")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<ActionResult> DeleteInstallation(
        [Required][FromRoute]string installationId)
    {
        var success = await _notificationService
            .DeleteInstallationByIdAsync(installationId, CancellationToken.None);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpPost]
    [Route("requests")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> RequestPush(
        [Required]NotificationRequest notificationRequest)
    {
        if ((notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
            (!notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Text)))
            return new BadRequestResult();

        var success = await _notificationService
            .RequestNotificationAsync(notificationRequest, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }
    ```

### <a name="create-the-api-app"></a>API 앱 만들기

이제 백 엔드 서비스를 호스팅하기 위해 [Azure App Service](https://docs.microsoft.com/azure/app-service/)에서 [API 앱](https://azure.microsoft.com/services/app-service/api/)을 만듭니다.  

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **리소스 만들기**를 클릭하고, **API 앱**을 검색하여 선택한 다음, **만들기**를 클릭합니다.

1. 다음 필드를 업데이트한 다음, **만들기**를 클릭합니다.

    **앱 이름:**  
    **API 앱**에 대한 전역적으로 고유한 이름을 입력합니다.

    **구독:**  
    알림 허브를 만든 것과 동일한 대상 **구독**을 선택합니다.

    **리소스 그룹:**  
    알림 허브를 만든 것과 동일한 **리소스 그룹**을 선택합니다.

    **App Service 계획/위치:**  
    새 **App Service 계획**을 만듭니다.  

    > [!NOTE]
    > 기본 옵션에서 **SSL** 지원이 포함된 계획으로 변경합니다. 그렇지 않으면 모바일 앱을 사용할 때 **http** 요청이 차단되지 않도록 적절한 단계를 수행해야 합니다.

    **Application Insights:**  
    제안된 옵션을 유지하거나(해당 이름을 사용하여 새 리소스를 만듦) 기존 리소스를 선택합니다.

1. **API 앱**이 프로비저닝되면 해당 리소스로 이동합니다.

1. **개요**의 위쪽에 있는 **기본 정보** 요약의 **URL** 속성을 적어 둡니다. 이 URL은 이 자습서의 뒷부분에서 사용되는 *백 엔드 엔드포인트*입니다.

    > [!NOTE]
    > URL은 이전에 지정한 API 앱 이름을 `https://<app_name>.azurewebsites.net` 형식으로 사용합니다.

1. 목록(**설정** 아래)에서 **구성**을 선택합니다.  

1. 아래의 각 설정에 대해 **새 애플리케이션 설정**을 클릭하여 **이름** 및 **값**을 입력한 다음, **확인**을 클릭합니다.

   | Name                               | 값                          |
   | ---------------------------------- | ------------------------------ |
   | `Authentication:ApiKey`            | <api_key_value>                |
   | `NotificationHub:Name`             | <hub_name_value>               |
   | `NotificationHub:ConnectionString` | <hub_connection_string_value>  |

   > [!NOTE]
   > 이러한 설정은 이전에 사용자 설정에서 정의한 설정과 동일하며, 복사할 수 있습니다. **Authentication:ApiKey** 설정은 [API 키를 사용하여 클라이언트 인증](#authenticate-clients-using-an-api-key-optional) 섹션을 완료하도록 선택한 경우에만 필요합니다. 프로덕션 시나리오의 경우 [Azure KeyVault](https://azure.microsoft.com/services/key-vault)와 같은 옵션을 살펴볼 수 있습니다. 여기서는 간단히 하기 위해 이러한 설정이 애플리케이션 설정으로 추가되었습니다.

1. 모든 애플리케이션 설정이 추가되면 **저장**, **계속**을 차례로 클릭합니다.

### <a name="publish-the-backend-service"></a>백 엔드 서비스 게시

다음으로, 모든 디바이스에서 액세스할 수 있도록 앱을 API 앱에 배포합니다.  

1. 구성을 **디버그**에서 **릴리스**로 아직 변경하지 않은 경우 이렇게 변경합니다.

1. **PushDemoApi** 프로젝트를 **Ctrl** + **클릭**한 다음, **게시** 메뉴에서 **Azure에 게시...** 를 선택합니다.

1. 메시지가 표시되면 인증 흐름을 따릅니다. 이전 [API 앱 만들기](#create-the-api-app) 섹션에서 사용한 계정을 사용합니다.

1. 목록에서 이전에 만든 **Azure App Service API 앱**을 게시 대상으로 선택한 다음, **게시**를 클릭합니다.

마법사가 완료되면 앱을 Azure에 게시한 다음, 해당 앱을 엽니다. 아직 **URL**을 적어 두지 않은 경우 이를 적어 둡니다. 이 URL은 이 자습서의 뒷부분에서 사용되는 *백 엔드 엔드포인트*입니다.

### <a name="validating-the-published-api"></a>게시된 API의 유효성 검사

1. **[Postman](https://www.postman.com/downloads)** 에서 새 탭을 열고, 요청을 **POST**로 설정하고, 아래 주소를 입력합니다. 자리 표시자를 이전 [백 엔드 서비스 게시](#publish-the-backend-service) 섹션에서 적어 둔 기본 주소로 바꿉니다.

    ```csharp
    https://<app_name>.azurewebsites.net/api/notifications/installations
    ```

    > [!NOTE]
    > 기본 주소는 ``https://<app_name>.azurewebsites.net/`` 형식이어야 합니다.

1. [API 키를 사용하여 클라이언트 인증](#authenticate-clients-using-an-api-key-optional) 섹션을 완료하도록 선택한 경우 **apikey** 값을 포함하도록 요청 헤더를 구성해야 합니다.

   | 키                            | 값                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. **본문**에 대해 **raw(원시)** 옵션을 선택하고, 형식 옵션 목록에서 **JSON**을 선택한 다음, 일부 자리 표시자 **JSON** 콘텐츠를 포함시킵니다.

    ```json
    {}
    ```

1. **보내기**를 클릭합니다.

    > [!NOTE]
    > 서비스에서 **400 잘못된 요청** 상태를 받습니다.

1. 1-4단계를 다시 수행하지만, 이번에는 요청 엔드포인트를 지정하여 동일한 **400 잘못된 요청** 응답을 받는지 확인합니다.

    ```bash
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

> [!NOTE]
> 클라이언트 모바일 앱의 플랫폼별 정보가 필요하므로 아직은 유효한 요청 데이터를 사용하여 API를 테스트할 수 없습니다.
