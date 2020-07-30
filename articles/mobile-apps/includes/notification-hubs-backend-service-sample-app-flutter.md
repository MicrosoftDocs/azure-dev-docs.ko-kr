---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 6a26b6e6f40ae4cb705799a2d49f2f62904c08c2
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401626"
---
### <a name="create-the-flutter-solution"></a>Flutter 솔루션 만들기

1. **Visual Studio Code**의 새 인스턴스를 엽니다.

1. **명령 팔레트**를 엽니다(**Shift** + **명령** + **P**).

1. **Flutter: 새 프로젝트** 명령을 선택한 다음, **Enter** 키를 누릅니다.

1. **프로젝트 이름**에 대해 *push_demo*를 입력한 다음, **프로젝트 위치**를 선택합니다.

1. 패키지를 가져오도록 요청하는 메시지가 표시되면 **패키지 가져오기**를 선택합니다.

1. **kotlin** 폴더(**app** > **src** > **main** 아래)를 **Ctrl 키** + **클릭**한 다음, **Finder에 표시**를 선택합니다. 그런 다음, 자식 폴더(**kotlin** 폴더 아래)의 이름을 각각 ```com```, ```<your_organization>``` 및 ```pushdemo```로 바꿉니다.

    > [!NOTE]
    > **Visual Studio Code** 템플릿을 사용하는 경우 이러한 폴더는 기본적으로 **com**, **example**, **<project_name>** 으로 설정됩니다. **mobcat**을 **organization**에 사용한다고 가정하면 폴더 구조는 다음과 같이 표시됩니다.
    >
    > - kotlin
    >     - com
    >         - mobcat
    >             - pushdemo

1. **Visual Studio Code**로 돌아가서 **android** > **app** > **build.gradle**의 **applicationId** 값을 `com.<your_organization>.pushdemo`로 업데이트합니다.

    > [!NOTE]
    > 사용자 고유의 조직 이름을 *<your_organization>* 자리 표시자에 사용해야 합니다. 예를 들어 조직으로 **mobcat**을 사용하면 **패키지 이름** 값이 *com.mobcat.pushdemo*로 생성됩니다.

1. **src** > **debug**, **src** > **main** 및 **src** > **profile** 아래의 각 **AndroidManifest.xml** 파일에서 **package** 특성을 업데이트합니다. 값이 이전 단계에서 사용한 **applicationId**와 일치하는지 확인합니다.

    ```xml
    <manifest
        xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.<your_organization>.pushdemo>">
        ...
    </manifest>
    ```

1. **src** > **main** 아래의 **AndroidManifest.xml** 파일에서 ```android:label``` 특성을 *PushDemo*로 업데이트합니다. 그런 다음, ```android:allowBackup``` 특성을 ```android:label``` 바로 아래에 추가하고, 해당 값을 **false**로 설정합니다.

    ```xml
    <application
        android:name="io.flutter.app.FlutterApplication"
        android:label="PushDemo"
        android:allowBackup="false"
        android:icon="@mipmap/ic_launcher">
        ...
    </application>
    ```

1. 앱 수준 **build.gradle** 파일(**android** > **app** > **build.gradle**)을 연 다음, API **29**를 사용하도록 **android** 섹션의 *compileSdkVersion*을 업데이트합니다. 그런 다음, **defaultConfig** 섹션의 *minSdkVersion* 및 *targetSdkVersion* 값을 각각 **26** 및 **29**로 업데이트합니다.

    > [!NOTE]
    > 이 자습서에서는 **API 레벨 26 이상**을 실행하는 디바이스만 지원되지만, 이전 버전을 실행하는 디바이스를 지원하도록 확장할 수 있습니다.

1. **ios** 폴더를 **Ctrl 키** + **클릭**한 다음, **Xcode에서 열기**를 선택합니다.

1. **Xcode**에서 **Runner**(위쪽에 있는 **xcodeproj**이며 폴더가 아님)를 클릭합니다. 그런 다음, **Runner** 대상을 선택하고, **일반** 탭을 선택합니다. **모두** 빌드 구성을 선택한 상태에서 **번들 식별자**를 `com.<your_organization>.PushDemo`로 업데이트합니다.

    > [!NOTE]
    > 사용자 고유의 조직 이름을 *<your_organization>* 자리 표시자에 사용해야 합니다. 예를 들어 조직으로 **mobcat**을 사용하면 **번들 식별자** 값이 *com.mobcat.PushDemo*로 생성됩니다.

1. **Info.plist**를 클릭한 다음, **번들 이름** 값을 **PushDemo**로 업데이트합니다.

1. **Xcode**를 닫고, **Visual Studio Code**로 돌아갑니다.

1. **Visual Studio Code**로 돌아가서 **pubspec.yaml**을 열고, [http](https://pub.dev/packages/http) 및 [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) **Dart 패키지**를 종속성으로 추가합니다. 그런 다음, 파일을 저장하고, 패키지를 가져오도록 요청하는 메시지가 표시되면 **패키지 가져오기**를 클릭합니다.

    ```yaml
    dependencies:
      flutter:
        sdk: flutter

      http: ^0.12.1
      flutter_secure_storage: ^3.3.3
    ```

1. **터미널**에서 디렉터리를 Flutter 프로젝트에 대한 **ios** 폴더로 변경합니다. 그런 다음, [**pod install**](https://guides.cocoapods.org/using/getting-started.html#installation) 명령을 실행하여 새 Pod([flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) 패키지에 필요함)를 설치합니다.

1. **lib** 폴더를 **Ctrl 키** + **클릭**한 다음, 메뉴에서 *main_page.dart*를 파일 이름으로 사용하는 **새 파일**을 선택합니다. 그런 다음, 다음 코드를 추가합니다.

    ```kotlin
    import 'package:flutter/material.dart';

    class MainPage extends StatefulWidget {
      @override
      _MainPageState createState() => _MainPageState();
    }

    class _MainPageState extends State<MainPage> {
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          body: SafeArea(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.stretch,
              children: <Widget>[],
            )
          )
        );
      }
    }
    ```

1. **main.dart**에서 템플릿 기반 코드를 다음으로 바꿉니다.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/main_page.dart';

    final navigatorKey = GlobalKey<NavigatorState>();

    void main() => runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey));
    ```

1. **터미널**의 각 대상 플랫폼에서 앱을 빌드하고 실행하여 디바이스에서 템플릿 기반 앱이 실행되는지 테스트합니다. 지원되는 디바이스가 연결되어 있는지 확인합니다.

    ```bash
    flutter run
    ```

### <a name="implement-the-cross-platform-components"></a>플랫폼 간 구성 요소 구현

1. **lib** 폴더를 **Ctrl 키** + **클릭**한 다음, 메뉴에서 *models*를 **폴더 이름**으로 사용하는 **새 폴더**를 선택합니다.

1. **models** 폴더를 **Ctrl 키** + **클릭**한 다음, 메뉴에서 *device_installation.dart*를 파일 이름으로 사용하는 **새 파일**을 선택합니다. 그런 다음, 다음 코드를 추가합니다.

    ```dart
    class DeviceInstallation {
        final String deviceId;
        final String platform;
        final String token;
        final List<String> tags;

        DeviceInstallation(this.deviceId, this.platform, this.token, this.tags);

        DeviceInstallation.fromJson(Map<String, dynamic> json)
          : deviceId = json['installationId'],
            platform = json['platform'],
            token = json['pushChannel'],
            tags = json['tags'];

        Map<String, dynamic> toJson() =>
        {
          'installationId': deviceId,
          'platform': platform,
          'pushChannel': token,
          'tags': tags,
        };
    }
    ```

1. 이 예제에서 지원되는 작업의 열거형을 정의하는 *push_demo_action.dart*라는 새 파일을 **models** 폴더에 추가합니다.

    ```dart
    enum PushDemoAction {
      actionA,
      actionB,
    }
    ```

1. **services**라는 새 폴더를 프로젝트에 추가하고, 다음 구현을 사용하여 *device_installation_service.dart*라는 새 파일을 해당 폴더에 추가합니다.

    ```dart
    import 'package:flutter/services.dart';

    class DeviceInstallationService {
      static const deviceInstallation = const MethodChannel('com.<your_organization>.pushdemo/deviceinstallation');
      static const String getDeviceIdChannelMethod = "getDeviceId";
      static const String getDeviceTokenChannelMethod = "getDeviceToken";
      static const String getDevicePlatformChannelMethod = "getDevicePlatform";

      Future<String> getDeviceId() {
        return deviceInstallation.invokeMethod(getDeviceIdChannelMethod);
      }

      Future<String> getDeviceToken() {
        return deviceInstallation.invokeMethod(getDeviceTokenChannelMethod);
      }

      Future<String> getDevicePlatform() {
        return deviceInstallation.invokeMethod(getDevicePlatformChannelMethod);
      }
    }
    ```

    > [!NOTE]
    > 사용자 고유의 조직 이름을 *<your_organization>* 자리 표시자에 사용해야 합니다. 예를 들어 조직으로 **mobcat**을 사용하면 [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) 이름이 *com.mobcat.pushdemo/deviceinstallation*으로 생성됩니다.
    >
    > 이 클래스는 기본 네이티브 플랫폼과의 작업을 캡슐화하여 필수 디바이스 설치 세부 정보를 가져옵니다. [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html)은 기본 네이티브 플랫폼과의 양방향 비동기 통신을 용이하게 합니다. 이 채널에 대한 플랫폼별 대응은 이후 단계에서 만들어집니다.

1. 다음 구현을 사용하여 *notification_action_service.dart*라는 다른 파일을 해당 폴더에 추가합니다.

    ```dart
    import 'package:flutter/services.dart';
    import 'dart:async';
    import 'package:push_demo/models/push_demo_action.dart';

    class NotificationActionService {
      static const notificationAction =
          const MethodChannel('com.<your_organization>.pushdemo/notificationaction');
      static const String triggerActionChannelMethod = "triggerAction";
      static const String getLaunchActionChannelMethod = "getLaunchAction";

      final actionMappings = {
        'action_a' : PushDemoAction.actionA,
        'action_b' : PushDemoAction.actionB
      };

      final actionTriggeredController = StreamController.broadcast();

      NotificationActionService() {
        notificationAction
            .setMethodCallHandler(handleNotificationActionCall);
      }

      Stream get actionTriggered => actionTriggeredController.stream;

      Future<void> triggerAction({action: String}) async {

        if (!actionMappings.containsKey(action)) {
          return;
        }

        actionTriggeredController.add(actionMappings[action]);
      }

      Future<void> checkLaunchAction() async {
        final launchAction = await notificationAction.invokeMethod(getLaunchActionChannelMethod) as String;

        if (launchAction != null) {
          triggerAction(action: launchAction);
        }
      }

      Future<void> handleNotificationActionCall(MethodCall call) async {
        switch (call.method) {
          case triggerActionChannelMethod:
            return triggerAction(action: call.arguments as String);
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > 이는 강력한 형식의 열거형을 사용하여 플랫폼 간 방식으로 처리할 수 있도록 알림 작업의 처리를 중앙 집중화하는 간단한 메커니즘으로 사용됩니다. 이 서비스를 사용하면 기본 네이티브 플랫폼에서 알림 페이로드에 작업이 지정된 경우 작업을 트리거할 수 있습니다. 또한 Flutter에서 처리할 준비가 되면 애플리케이션이 시작되는 동안 공용 코드를 통해 작업이 지정되었는지 여부를 소급하여 확인할 수 있습니다. 예를 들어 알림 센터에서 알림을 탭하여 앱이 시작되는 경우가 있습니다.

1. 다음 구현을 사용하여 *notification_registration_service.dart*라는 새 파일을 **services** 폴더에 추가합니다.

    ```dart
    import 'dart:convert';
    import 'package:flutter/services.dart';
    import 'package:http/http.dart' as http;
    import 'package:push_demo/services/device_installation_service.dart';
    import 'package:push_demo/models/device_installation.dart';
    import 'package:flutter_secure_storage/flutter_secure_storage.dart';

    class NotificationRegistrationService {
      static const notificationRegistration =
          const MethodChannel('com.<your_organization>.pushdemo/notificationregistration');

      static const String refreshRegistrationChannelMethod = "refreshRegistration";
      static const String installationsEndpoint = "api/notifications/installations";
      static const String cachedDeviceTokenKey = "cached_device_token";
      static const String cachedTagsKey = "cached_tags";

      final deviceInstallationService = DeviceInstallationService();
      final secureStorage = FlutterSecureStorage();

      String baseApiUrl;
      String apikey;

      NotificationRegistrationService(this.baseApiUrl, this.apikey) {
        notificationRegistration
            .setMethodCallHandler(handleNotificationRegistrationCall);
      }

      String get installationsUrl => "$baseApiUrl$installationsEndpoint";

      Future<void> deregisterDevice() async {
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (cachedToken == null || serializedTags == null) {
          return;
        }

        var deviceId = await deviceInstallationService.getDeviceId();

        if (deviceId.isEmpty) {
          throw "Unable to resolve an ID for the device.";
        }

        var response = await http
            .delete("$installationsUrl/$deviceId", headers: {"apikey": apikey});

        if (response.statusCode != 200) {
          throw "Deregister request failed: ${response.reasonPhrase}";
        }

        await secureStorage.delete(key: cachedDeviceTokenKey);
        await secureStorage.delete(key: cachedTagsKey);
      }

      Future<void> registerDevice(List<String> tags) async {
        try {
          final deviceId = await deviceInstallationService.getDeviceId();
          final platform = await deviceInstallationService.getDevicePlatform();
          final token = await deviceInstallationService.getDeviceToken();

          final deviceInstallation =
              DeviceInstallation(deviceId, platform, token, tags);

          final response = await http.put(installationsUrl,
              body: jsonEncode(deviceInstallation),
              headers: {"apikey": apikey, "Content-Type": "application/json"});

          if (response.statusCode != 200) {
            throw "Register request failed: ${response.reasonPhrase}";
          }

          final serializedTags = jsonEncode(tags);

          await secureStorage.write(key: cachedDeviceTokenKey, value: token);
          await secureStorage.write(key: cachedTagsKey, value: serializedTags);
        } on PlatformException catch (e) {
          throw e.message;
        } catch (e) {
          throw "Unable to register device: $e";
        }
      }

      Future<void> refreshRegistration() async {
        final currentToken = await deviceInstallationService.getDeviceToken();
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (currentToken == null ||
            cachedToken == null ||
            serializedTags == null ||
            currentToken == cachedToken) {
          return;
        }

        final tags = jsonDecode(serializedTags);

        return registerDevice(tags);
      }

      Future<void> handleNotificationRegistrationCall(MethodCall call) async {
        switch (call.method) {
          case refreshRegistrationChannelMethod:
            return refreshRegistration();
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > 이 클래스는 **DeviceInstallationService** 및 백 엔드 서비스에 대한 요청을 캡슐화하여 필수 등록, 등록 취소 및 등록 새로 고침 작업을 수행합니다. **apiKey** 인수는 [API 키를 사용하여 클라이언트 인증](#authenticate-clients-using-an-api-key-optional) 섹션을 완료하도록 선택한 경우에만 필요합니다.

1. 다음 구현을 사용하여 *config.dart*라는 새 파일을 **lib** 폴더에 추가합니다.

    ```dart
    class Config {
      static String apiKey = "API_KEY";
      static String backendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
    }
    ```

    > [!NOTE]
    > 이는 앱 비밀을 정의하는 간단한 방법으로 사용됩니다. 자리 표시자 값을 사용자 고유의 값으로 바꿉니다. 백 엔드 서비스를 빌드할 때 이러한 값을 적어 두어야 합니다. **API 앱 URL**은 `https://<api_app_name>.azurewebsites.net/`이어야 합니다. **apiKey** 멤버는 [API 키를 사용하여 클라이언트 인증](#authenticate-clients-using-an-api-key-optional) 섹션을 완료하도록 선택한 경우에만 필요합니다.
    >
    > 이러한 비밀을 원본 제어에 커밋하지 않도록 하려면 이를 gitignore 파일에 추가해야 합니다.

### <a name="implement-the-cross-platform-ui"></a>플랫폼 간 UI 구현

1. **main_page.dart**에서 **build** 함수를 다음으로 바꿉니다.

    ```dart
    @override
    Widget build(BuildContext context) {
    return Scaffold(
        body: Padding(
          padding: const EdgeInsets.symmetric(horizontal: 20.0, vertical: 40.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.end,
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: <Widget>[
              FlatButton(
                child: Text("Register"),
                onPressed: registerButtonClicked,
              ),
              FlatButton(
                child: Text("Deregister"),
                onPressed: deregisterButtonClicked,
              ),
            ],
          ),
        ),
      );
    }
    ```

1. 필수 가져오기를 **main_page.dart** 파일의 위쪽에 추가합니다.

    ```dart
    import 'package:push_demo/services/notification_registration_service.dart';
    import 'config.dart';
    ```

1. **NotificationRegistrationService**에 대한 참조를 저장하는 필드를 **_MainPageState** 클래스에 추가합니다.

    ```dart
    final notificationRegistrationService = NotificationRegistrationService(Config.backendServiceEndpoint, Config.apiKey);
    ```

1. **_MainPageState** 클래스에서 **Register** 및 **Deregister** 단추 **onPressed** 이벤트에 대한 이벤트 처리기를 구현합니다. 해당 **Register**/**Deregister** 메서드를 호출한 다음, 결과를 나타내는 경고를 표시합니다.

    ```dart
    void registerButtonClicked() async {
        try {
          await notificationRegistrationService.registerDevice(List<String>());
          await showAlert(message: "Device registered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      void deregisterButtonClicked() async {
        try {
          await notificationRegistrationService.deregisterDevice();
          await showAlert(message: "Device deregistered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      Future<void> showAlert({ message: String }) async {
        return showDialog<void>(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) {
            return AlertDialog(
              title: Text('PushDemo'),
              content: SingleChildScrollView(
                child: ListBody(
                  children: <Widget>[
                    Text(message),
                  ],
                ),
              ),
              actions: <Widget>[
                FlatButton(
                  child: Text('OK'),
                  onPressed: () {
                    Navigator.of(context).pop();
                  },
                ),
              ],
            );
          },
        );
      }
    ```

1. 이제 **main.dart**에서 다음 가져오기가 파일의 위쪽에 있는지 확인합니다.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/models/push_demo_action.dart';
    import 'package:push_demo/services/notification_action_service.dart';
    import 'package:push_demo/main_page.dart';
    ```

1. **NotificationActionService** 인스턴스에 대한 참조를 저장하는 변수를 선언하고 초기화합니다.

    ```dart
    final notificationActionService = NotificationActionService();
    ```

1. 작업이 트리거될 때 경고 표시를 처리하는 함수를 추가합니다.

    ```dart
    void notificationActionTriggered(PushDemoAction action) {
      showActionAlert(message: "${action.toString().split(".")[1]} action received");
    }

    Future<void> showActionAlert({ message: String }) async {
      return showDialog<void>(
        context: navigatorKey.currentState.overlay.context,
        barrierDismissible: false,
        builder: (BuildContext context) {
          return AlertDialog(
            title: Text('PushDemo'),
            content: SingleChildScrollView(
              child: ListBody(
                children: <Widget>[
                  Text(message),
                ],
              ),
            ),
            actions: <Widget>[
              FlatButton(
                child: Text('OK'),
                onPressed: () {
                  Navigator.of(context).pop();
                },
              ),
            ],
          );
        },
      );
    }
    ```

1. **NotificationActionService** **actionTriggered** 스트림을 관찰하고 앱을 시작하는 동안 캡처된 작업을 확인하도록 **main** 함수를 업데이트합니다.

    ```dart
    void main() async {
      runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey,));
      notificationActionService.actionTriggered.listen((event) { notificationActionTriggered(event as PushDemoAction); });
      await notificationActionService.checkLaunchAction();
    }
    ```

    > [!NOTE]
    > 이는 단순히 푸시 알림 작업의 수신 및 전파를 보여 주기 위한 것입니다. 일반적으로 이러한 작업은 예를 들어 경고를 표시하는 대신 특정 보기로 이동하거나 일부 데이터를 새로 고치는 것과 같은 방법으로 자동으로 처리됩니다.
