---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 9336229d7dfcf65277b9bd0f9a0a01e444609d5e
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401651"
---
### <a name="configure-the-runner-target-and-infoplist"></a>실행기 대상 및 Info.plist 구성

1. **Visual Studio Code**의 **ios** 폴더에서 **Control** + **클릭**한 다음, **Xcode에서 열기**를 선택합니다.

1. **Xcode**에서 **Runner**(폴더가 아닌 맨 위에 있는 **.xcodeproj**)를 클릭하고 **Runner** 대상을 선택한 다음, **서명 및 기능**을 선택합니다. **모든** 빌드 구성을 선택한 상태에서 **팀**에 대한 개발자 계정을 선택합니다. "자동으로 서명 관리" 옵션이 선택되어 있고 서명 인증서 및 프로비저닝 프로필이 자동으로 선택되어 있는지 확인합니다.

    > [!NOTE]
    > 새 프로비저닝 프로필 값이 표시되지 않으면 **Xcode** > **기본 설정** > **계정**을 차례로 선택하여 서명 ID에 대한 프로필을 새로 고친 다음, **수동 프로필 다운로드** 단추를 선택하여 프로필을 다운로드합니다.

1. **+ 기능**을 클릭한 다음, **푸시 알림**을 검색합니다. 이 기능을 추가하려면 **푸시 알림**에서 **두 번 클릭**합니다.

1. **Info.plist**를 열고 **최소 시스템 버전**을 **13.0**으로 설정합니다.

    > [!NOTE]
    > 이 자습서에서는 **iOS 13.0 이상**을 실행하는 디바이스만 지원되지만, 이전 버전을 실행하는 디바이스를 지원하도록 확장할 수 있습니다.

1. **Runner.entitlements**를 열고 **APS 환경** 설정이 **개발**로 설정되어 있는지 확인합니다.

### <a name="handle-push-notifications-for-ios"></a>iOS 푸시 알림 처리

1. **Runner** 폴더(Runner 프로젝트 내)에서 **Control** + **클릭**한 다음, **Services**를 이름으로 사용하여 **새 그룹**을 선택합니다.

1. **Services** 폴더에서 **Control** + **클릭**한 다음, **새 파일...** 을 선택합니다. 그런 다음, **Swift 파일**을 선택하고 **다음**을 클릭합니다. 이름에 **DeviceInstallationService**를 지정한 다음, **만들기**를 클릭합니다.

1. 다음 코드를 사용하여 **DeviceInstallationService.swift**를 구현합니다.

    ```swift
    import Foundation

    class DeviceInstallationService {

        enum DeviceRegistrationError: Error {
            case notificationSupport(message: String)
        }

        var token : Data? = nil

        let DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
        let GET_DEVICE_ID = "getDeviceId"
        let GET_DEVICE_TOKEN = "getDeviceToken"
        let GET_DEVICE_PLATFORM = "getDevicePlatform"

        private let deviceInstallationChannel : FlutterMethodChannel

        var notificationsSupported : Bool {
            get {
                if #available(iOS 13.0, *) {
                    return true
                }
                else {
                    return false
                }
            }
        }

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
            deviceInstallationChannel = FlutterMethodChannel(name: DEVICE_INSTALLATION_CHANNEL, binaryMessenger: binaryMessenger)
            deviceInstallationChannel.setMethodCallHandler(handleDeviceInstallationCall)
        }

        func getDeviceId() -> String {
            return UIDevice.current.identifierForVendor!.description
        }

        func getDeviceToken() throws -> String {
            if(!notificationsSupported) {
                let notificationSupportError = getNotificationsSupportError()
                throw DeviceRegistrationError.notificationSupport(message: notificationSupportError)
            }

            if (token == nil) {
                throw DeviceRegistrationError.notificationSupport(message: "Unable to resolve token for APNS.")
            }

            return token!.reduce("", {$0 + String(format: "%02X", $1)})
        }

        func getDevicePlatform() -> String {
            return "apns"
        }

        private func handleDeviceInstallationCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_DEVICE_ID:
                result(getDeviceId())
            case GET_DEVICE_TOKEN:
                getDeviceToken(result: result)
            case GET_DEVICE_PLATFORM:
                result(getDevicePlatform())
            default:
                result(FlutterMethodNotImplemented)
            }
        }

        private func getDeviceToken(result: @escaping FlutterResult) {
            do {
                let token = try getDeviceToken()
                result(token)
            }
            catch let error {
                result(FlutterError(code: "UNAVAILABLE", message: error.localizedDescription, details: nil))
            }
        }

        private func getNotificationsSupportError() -> String {

            if (!notificationsSupported) {
                return "This app only supports notifications on iOS 13.0 and above. You are running \(UIDevice.current.systemVersion)"
            }

            return "An error occurred preventing the use of push notifications."
        }
    }
    ```

    > [!NOTE]
    > 이 클래스는 `com.<your_organization>.pushdemo/deviceinstallation` 채널에 대한 플랫폼별 대응을 구현합니다. 이는 **DeviceInstallationService.dart** 내에서 앱의 Flutter 부분에 정의되어 있습니다. 이 경우 호출이 공용 코드에서 네이티브 호스트로 수행됩니다. **<your_organization>** 이 사용되는 위치마다 이를 사용자 고유의 조직으로 바꿔야 합니다.
    >
    > 이 클래스는 알림 허브 등록 페이로드의 일부로 고유 ID([UIDevice.identifierForVendor](https://developer.apple.com/documentation/uikit/uidevice/1620059-identifierforvendor) 값 사용)를 제공합니다.

1. *NotificationRegistrationService*라는 **Services** 폴더에 다른 **Swift 파일**을 추가한 후, 다음 코드를 추가합니다.

    ```swift
    import Foundation

    class NotificationRegistrationService {

        let NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
        let REFRESH_REGISTRATION = "refreshRegistration"

        private let notificationRegistrationChannel : FlutterMethodChannel

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
           notificationRegistrationChannel = FlutterMethodChannel(name: NOTIFICATION_REGISTRATION_CHANNEL, binaryMessenger: binaryMessenger)
        }

        func refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, arguments: nil)
        }
    }
    ```

    > [!NOTE]
    > 이 클래스는 `com.<your_organization>.pushdemo/notificationregistration` 채널에 대한 플랫폼별 대응을 구현합니다. 이는 **NotificationRegistrationService.dart** 내에서 앱의 Flutter 부분에 정의되어 있습니다. 이 경우 호출이 네이티브 호스트에서 공용 코드로 수행됩니다. 다시 한 번, **<your_organization>** 이 사용되는 위치마다 이를 사용자 고유의 조직으로 바꿔야 합니다.

1. *NotificationActionService*라는 **Services** 폴더에 다른 **Swift 파일**을 추가한 후, 다음 코드를 추가합니다.

    ```swift
    import Foundation

    class NotificationActionService {

        let NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
        let TRIGGER_ACTION = "triggerAction"
        let GET_LAUNCH_ACTION = "getLaunchAction"

        private let notificationActionChannel: FlutterMethodChannel

        var launchAction: String? = nil

        init(withBinaryMessenger binaryMessenger: FlutterBinaryMessenger) {
            notificationActionChannel = FlutterMethodChannel(name: NOTIFICATION_ACTION_CHANNEL, binaryMessenger: binaryMessenger)
            notificationActionChannel.setMethodCallHandler(handleNotificationActionCall)
        }

        func triggerAction(action: String) {
           notificationActionChannel.invokeMethod(TRIGGER_ACTION, arguments: action)
        }

        private func handleNotificationActionCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_LAUNCH_ACTION:
                result(launchAction)
            default:
                result(FlutterMethodNotImplemented)
            }
        }
    }
    ```

    > [!NOTE]
    > 이 클래스는 `com.<your_organization>.pushdemo/notificationaction` 채널에 대한 플랫폼별 대응을 구현합니다. 이는 **NotificationActionService.dart** 내에서 앱의 Flutter 부분에 정의되어 있습니다. 이 경우 호출이 양방향으로 수행될 수 있습니다. **<your_organization>** 이 사용되는 위치마다 이를 사용자 고유의 조직으로 바꿔야 합니다.

1. **AppDelegate.swift**에서 이전에 만든 서비스에 대한 참조를 저장할 변수를 추가합니다.

    ```swift
    var deviceInstallationService : DeviceInstallationService?
    var notificationRegistrationService : NotificationRegistrationService?
    var notificationActionService : NotificationActionService?
    ```

1. 알림 데이터를 처리하는 **processNotificationActions**라는 함수를 추가합니다. 앱 실행 중에 작업이 처리되는 경우 해당 작업을 조건부로 트리거하거나 나중에 사용하기 위해 저장합니다.

    ```swift
    func processNotificationActions(userInfo: [AnyHashable : Any], launchAction: Bool = false) {
        if let action = userInfo["action"] as? String {
            if (launchAction) {
                notificationActionService?.launchAction = action
            }
            else {
                notificationActionService?.triggerAction(action: action)
            }
        }
    }
    ```

1. **DeviceInstallationService**에 대한 **토큰** 값을 설정하는 **didRegisterForRemoteNotificationsWithDeviceToken** 함수를 재정의합니다. 그런 다음, **NotificationRegistrationService**에서 **refreshRegistration**을 호출합니다.

    ```swift
    override func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
      deviceInstallationService?.token = deviceToken
      notificationRegistrationService?.refreshRegistration()
    }
    ```

1. **didReceiveRemoteNotification** 함수를 재정의하여 **userInfo** 인수를 **processNotificationActions** 함수에 전달합니다.

    ```swift
    override func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) {
        processNotificationActions(userInfo: userInfo)
    }
    ```

1. **didFailToRegisterForRemoteNotificationsWithError** 함수를 재정의하여 오류를 기록합니다.

    ```swift
    override func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
        print(error);
    }
    ```

    > [!NOTE]
    > 이것은 매우 중요한 자리 표시자입니다. 프로덕션 시나리오에 대한 적절한 로깅 및 오류 처리를 구현해야 합니다.

1. **didFinishLaunchingWithOptions**에서 **deviceInstallationService**, **notificationRegistrationService** 및 **notificationActionService** 변수를 인스턴스화합니다.

    ```swift
    let controller : FlutterViewController = window?.rootViewController as! FlutterViewController

    deviceInstallationService = DeviceInstallationService(withBinaryMessenger: controller.binaryMessenger)
    notificationRegistrationService = NotificationRegistrationService(withBinaryMessenger: controller.binaryMessenger)
    notificationActionService = NotificationActionService(withBinaryMessenger: controller.binaryMessenger)
    ```

1. 동일한 함수에서 조건부로 권한 부여를 요청하고 원격 알림을 등록합니다.

    ```swift
    if #available(iOS 13.0, *) {
      UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
          (granted, error) in

          if (granted)
          {
              DispatchQueue.main.async {
                  let pushSettings = UIUserNotificationSettings(types: [.alert, .sound, .badge], categories: nil)
                  application.registerUserNotificationSettings(pushSettings)
                  application.registerForRemoteNotifications()
              }
          }
      }
    }
    ```

1. **launchOptions**에 **remoteNotification** 키가 포함되어 있는 경우 **didFinishLaunchingWithOptions** 함수 끝에서 **processNotificationActions**를 호출합니다. 결과 **userInfo** 개체를 전달하고 *launchAction* 인수에 *true*를 사용합니다. *true* 값은 앱 실행 중에 작업이 처리되고 있음을 나타냅니다.

    ```swift
    if let userInfo = launchOptions?[.remoteNotification] as? [AnyHashable : Any] {
        processNotificationActions(userInfo: userInfo, launchAction: true)
    }
    ```
