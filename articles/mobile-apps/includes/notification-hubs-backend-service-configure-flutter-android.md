---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 62ff9b49f6b2ba30d3538b2c13812ca7fc1ef583
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401646"
---
### <a name="add-the-google-services-json-file"></a>Google Services JSON 파일 추가

1. **android** 폴더를 **Ctrl 키** + **클릭**한 다음, **Android Studio에서 열기**를 선택합니다. 그런 다음, **프로젝트** 보기로 전환합니다(아직 없는 경우).

1. 이전에 [Firebase 콘솔](https://console.firebase.google.com)에서 **PushDemo** 프로젝트를 설정할 때 다운로드한 *google-services.json* 파일을 찾습니다. 그런 다음, 이 파일을 **app** 모듈 루트 디렉터리(**android** > **android** > **app**)로 끕니다.

### <a name="configure-build-settings-and-permissions"></a>빌드 설정 및 권한 구성

1. **프로젝트** 보기를 **Android**로 전환합니다.

1. **AndroidManifest.xml**을 연 다음, **INTERNET** 및 **READ_PHONE_STATE** 권한을 닫는 **</manifest>** 태그 앞의 **application** 요소 뒤에 추가합니다.

    ```xml
    <manifest>
        <application>...</application>
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    </manifest>
    ```

### <a name="add-the-firebase-sdks"></a>Firebase SDK 추가

1. **Android Studio**에서 프로젝트 수준 **build.gradle** 파일(**Gradle 스크립트** > **build.gradle(프로젝트: android)** )을 엽니다. 그리고 ``buildscript`` > **dependencies** 노드에 'com.google.gms:google-services' 클래스 경로가 있는지 확인합니다.

    ```json
    buildscript {

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
      }

      dependencies {
        // ...

        // Add the following line:
        classpath 'com.google.gms:google-services:4.3.3'  // Google Services plugin
      }
    }

    allprojects {
      // ...

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
        // ...
      }
    }
    ```

    > [!NOTE]
    > **Android 프로젝트**를 만들 때 [Firebase 콘솔](https://console.firebase.google.com)에 제공된 지침에 따라 최신 버전을 참조해야 합니다.

1. 앱 수준 **build.gradle** 파일(**Gradle 스크립트** > **build.gradle(모듈: app)** )에서 **Google Services Gradle 플러그 인**을 적용합니다. 플러그 인을 **android** 노드 바로 위에 적용합니다.

    ```json
    // ...

    // Add the following line:
    apply plugin: 'com.google.gms.google-services'  // Google Services plugin

    android {
      // ...
    }
    ```

1. 동일한 파일의 **dependencies** 노드에서 **Cloud Messaging** Android 라이브러리에 대한 종속성을 추가합니다.

    ```json
    dependencies {
        // ...
        implementation 'com.google.firebase:firebase-messaging:20.2.0'
    }
    ```

    > [!NOTE]
    > [Cloud Messaging Android 클라이언트 설명서](https://firebase.google.com/docs/cloud-messaging/android/client)에 따라 최신 버전을 참조해야 합니다.

1. 변경 내용을 저장한 다음, **지금 동기화** 단추(도구 모음 프롬프트에서) 또는 **Gradle 파일과 프로젝트 동기화**를 클릭합니다.

### <a name="handle-push-notifications-for-android"></a>Android 푸시 알림 처리

1. **Android Studio**에서 **com.<your_organization>.pushdemo** 패키지 폴더(**app** > **src** > **main** > **kotlin**)를 **Ctrl 키** + **클릭**하고, **새로 만들기** 메뉴에서 **패키지**를 선택합니다. 이름으로 **services**를 입력한 다음, **Return** 키를 누릅니다.

1. **services** 폴더를 **Ctrl 키** + **클릭**하고, **새로 만들기** 메뉴에서 **Kotlin 파일/클래스**를 선택합니다. 이름으로 **DeviceInstallationService**를 입력한 다음, **Return** 키를 누릅니다.

1. 다음 코드를 사용하여 **DeviceInstallationService**를 구현합니다.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import android.annotation.SuppressLint
    import android.content.Context
    import android.provider.Settings.Secure
    import com.google.android.gms.common.ConnectionResult
    import com.google.android.gms.common.GoogleApiAvailability
    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    @SuppressLint("HardwareIds")
    class DeviceInstallationService {

        companion object {
            const val DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
            const val GET_DEVICE_ID = "getDeviceId"
            const val GET_DEVICE_TOKEN = "getDeviceToken"
            const val GET_DEVICE_PLATFORM = "getDevicePlatform"
        }

        private var context: Context
        private var deviceInstallationChannel : MethodChannel

        val playServicesAvailable
            get() = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context) == ConnectionResult.SUCCESS

        constructor(context: Context, flutterEngine: FlutterEngine) {
            this.context = context
            deviceInstallationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, DEVICE_INSTALLATION_CHANNEL)
            deviceInstallationChannel.setMethodCallHandler { call, result -> handleDeviceInstallationCall(call, result) }
        }

        fun getDeviceId() : String
            = Secure.getString(context.applicationContext.contentResolver, Secure.ANDROID_ID)

        fun getDeviceToken() : String {
            if(!playServicesAvailable) {
                throw Exception(getPlayServicesError())
            }

            // TODO: Revisit once we have created the PushNotificationsFirebaseMessagingService
            val token = "Placeholder_Get_Value_From_FirebaseMessagingService_Implementation"

            if (token.isNullOrBlank()) {
                throw Exception("Unable to resolve token for FCM.")
            }

            return token
        }

        fun getDevicePlatform() : String = "fcm"

        private fun handleDeviceInstallationCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                GET_DEVICE_ID -> {
                    result.success(getDeviceId())
                }
                GET_DEVICE_TOKEN -> {
                    getDeviceToken(result)
                }
                GET_DEVICE_PLATFORM -> {
                    result.success(getDevicePlatform())
                }
                else -> {
                    result.notImplemented()
                }
            }
        }

        private fun getDeviceToken(result: MethodChannel.Result) {
            try {
                val token = getDeviceToken()
                result.success(token)
            }
            catch (e: Exception) {
                result.error("ERROR", e.message, e)
            }
        }

        private fun getPlayServicesError(): String {
            val resultCode = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context)

            if (resultCode != ConnectionResult.SUCCESS) {
                return if (GoogleApiAvailability.getInstance().isUserResolvableError(resultCode)){
                    GoogleApiAvailability.getInstance().getErrorString(resultCode)
                } else {
                    "This device is not supported"
                }
            }

            return "An error occurred preventing the use of push notifications"
        }
    }
    ```

    > [!NOTE]
    > 이 클래스는 `com.<your_organization>.pushdemo/deviceinstallation` 채널에 대한 플랫폼별 대응을 구현합니다. 이는 **DeviceInstallationService.dart** 내에서 앱의 Flutter 부분에 정의되어 있습니다. 이 경우 호출이 공용 코드에서 네이티브 호스트로 수행됩니다. **<your_organization>** 이 사용되는 위치마다 이를 사용자 고유의 조직으로 바꿔야 합니다.
    >
    > 이 클래스는 알림 허브 등록 페이로드의 일부로 고유한 ID([Secure.AndroidId](https://developer.android.com/reference/android/provider/Settings.Secure#ANDROID_ID) 사용)를 제공합니다.

1. *NotificationRegistrationService*라는 다른 **Kotlin 파일/클래스**를 **services** 폴더에 추가하고, 다음 코드를 추가합니다.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodChannel

    class NotificationRegistrationService {

        companion object {
            const val NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
            const val REFRESH_REGISTRATION = "refreshRegistration"
        }

        private var notificationRegistrationChannel : MethodChannel

        constructor(flutterEngine: FlutterEngine) {
            notificationRegistrationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationRegistrationService.NOTIFICATION_REGISTRATION_CHANNEL)
        }

        fun refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, null)
        }
    }
    ```

    > [!NOTE]
    > 이 클래스는 `com.<your_organization>.pushdemo/notificationregistration` 채널에 대한 플랫폼별 대응을 구현합니다. 이는 **NotificationRegistrationService.dart** 내에서 앱의 Flutter 부분에 정의되어 있습니다. 이 경우 호출이 네이티브 호스트에서 공용 코드로 수행됩니다. 다시 한 번, **<your_organization>** 이 사용되는 위치마다 이를 사용자 고유의 조직으로 바꿔야 합니다.

1. *NotificationActionService*라는 다른 **Kotlin 파일/클래스**를 **services** 폴더에 추가하고, 다음 코드를 추가합니다.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    class NotificationActionService {
        companion object {
            const val NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
            const val TRIGGER_ACTION = "triggerAction"
            const val GET_LAUNCH_ACTION = "getLaunchAction"
        }

        private var notificationActionChannel : MethodChannel
        var launchAction : String? = null

        constructor(flutterEngine: FlutterEngine) {
            notificationActionChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationActionService.NOTIFICATION_ACTION_CHANNEL)
            notificationActionChannel.setMethodCallHandler { call, result -> handleNotificationActionCall(call, result) }
        }

        fun triggerAction(action: String) {
            notificationActionChannel.invokeMethod(NotificationActionService.TRIGGER_ACTION, action)
        }

        private fun handleNotificationActionCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                NotificationActionService.GET_LAUNCH_ACTION -> {
                    result.success(launchAction)
                }
                else -> {
                    result.notImplemented()
                }
            }
        }
    }
    ```

    > [!NOTE]
    > 이 클래스는 `com.<your_organization>.pushdemo/notificationaction` 채널에 대한 플랫폼별 대응을 구현합니다. 이는 **NotificationActionService.dart** 내에서 앱의 Flutter 부분에 정의되어 있습니다. 이 경우 호출이 양방향으로 수행될 수 있습니다. **<your_organization>** 이 사용되는 위치마다 이를 사용자 고유의 조직으로 바꿔야 합니다.

1. *PushNotificationsFirebaseMessagingService*라는 새 **Kotlin 파일/클래스**를 **com.<your_organization>.pushdemo** 패키지에 추가하고, 다음 코드를 사용하여 이를 구현합니다.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.os.Handler
    import android.os.Looper
    import com.google.firebase.messaging.FirebaseMessagingService
    import com.google.firebase.messaging.RemoteMessage
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService

    class PushNotificationsFirebaseMessagingService : FirebaseMessagingService() {

        companion object {
            var token : String? = null
            var notificationRegistrationService : NotificationRegistrationService? = null
            var notificationActionService : NotificationActionService? = null
        }

        override fun onNewToken(token: String) {
            PushNotificationsFirebaseMessagingService.token = token
            notificationRegistrationService?.refreshRegistration()
        }

        override fun onMessageReceived(message: RemoteMessage) {
            message.data.let {
                Handler(Looper.getMainLooper()).post {
                    notificationActionService?.triggerAction(it.getOrDefault("action", null))
                }
            }
        }
    }
    ```

    > [!NOTE]
    > 이 클래스는 앱이 포그라운드에서 실행될 때 알림을 처리합니다. 작업이 **onMessageReceived**에서 받은 알림 페이로드에 포함된 경우 **NotificationActionService**에서 **triggerAction**을 조건부로 호출합니다. 또한 **onNewToken** 함수를 재정의하여 **Firebase** 토큰이 다시 생성되면 **NotificationRegistrationService**에서 **refreshRegistration**도 호출합니다.
    >
    > 다시 한 번, **<your_organization>** 이 사용되는 위치마다 이를 사용자 고유의 조직으로 바꿔야 합니다.

1. **AndroidManifest.xml**(**app** > **src** > **main**)에서 `com.google.firebase.MESSAGING_EVENT` 의도 필터가 있는 **PushNotificationsFirebaseMessagingService**를 **application** 요소의 아래쪽에 추가합니다.

    ```xml
    <manifest>
        <application>
            <!-- EXISTING MANIFEST CONTENT -->
             <service
                android:name="com.<your_organization>.pushdemo.PushNotificationsFirebaseMessagingService"
                android:exported="false">
                <intent-filter>
                    <action android:name="com.google.firebase.MESSAGING_EVENT" />
                </intent-filter>
            </service>
        </application>
    </manifest>
    ```

1. **DeviceInstallationService**로 돌아가서 다음 가져오기가 파일의 위쪽에 있는지 확인합니다.

    ```kotlin
    package com.<your_organization>.pushdemo
    import com.<your_organization>.pushdemo.services.PushNotificationsFirebaseMessagingService
    ```

    > [!NOTE]
    > **<your_organization>** 을 사용자 고유의 조직 값으로 바꿉니다.

1. **PushNotificationFirebaseMessagingService**에서 토큰 값을 가져오도록 *Placeholder_Get_Value_From_FirebaseMessagingService_Implementation* 자리 표시자 텍스트를 업데이트합니다.

    ```kotlin
    fun getDeviceToken() : String {
        if(!playServicesAvailable) {
            throw Exception(getPlayServicesError())
        }

        // Get token from the PushNotificationsFirebaseMessagingService.token field.
        val token = PushNotificationsFirebaseMessagingService.token

        if (token.isNullOrBlank()) {
            throw Exception("Unable to resolve token for FCM.")
        }

        return token
    }
    ```

1. **MainActivity**에서 다음 가져오기가 파일의 위쪽에 있는지 확인합니다.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.content.Intent
    import android.os.Bundle
    import com.google.android.gms.tasks.OnCompleteListener
    import com.google.firebase.iid.FirebaseInstanceId
    import com.<your_organization>.pushdemo.services.DeviceInstallationService
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService
    import io.flutter.embedding.android.FlutterActivity
    ```

    > [!NOTE]
    > **<your_organization>** 을 사용자 고유의 조직 값으로 바꿉니다.

1. **DeviceInstallationService**에 대한 참조를 저장하는 변수를 추가합니다.

    ```kotlin
    private lateinit var deviceInstallationService: DeviceInstallationService
    ```

1. **Intent**에 **action**이라는 추가 값이 있는지 여부를 확인하는 **processNotificationActions**라는 함수를 추가합니다. 앱이 시작되는 동안 작업이 처리되는 경우 해당 작업을 조건부로 트리거하거나 나중에 사용하기 위해 저장합니다.

    ```kotlin
     private fun processNotificationActions(intent: Intent, launchAction: Boolean = false) {
        if (intent.hasExtra("action")) {
            var action = intent.getStringExtra("action");

            if (action.isNotEmpty()) {
                if (launchAction) {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.launchAction = action
                }
                else {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.triggerAction(action)
                }
            }
        }
    }
    ```

1. **processNotificationActions**를 호출하도록 **onNewIntent** 함수를 재정의합니다.

    ```kotlin
    override fun onNewIntent(intent: Intent) {
        super.onNewIntent(intent)
        processNotificationActions(intent)
    }
    ```

    > [!NOTE]
    > **MainActivity**에 대한 **LaunchMode**가 **SingleTop**으로 설정되어 **Intent**가 **onCreate** 함수가 아닌 **onNewIntent** 함수를 통해 기존 **Activity** 인스턴스로 전송되므로 **onCreate** 및 **onNewIntent** 함수 모두에서 들어오는 **Intent**를 처리해야 합니다.

1. **onCreate** 함수를 재정의하고, **deviceInstallationService**를 **DeviceInstallationService**의 새 인스턴스로 설정합니다.

    ```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        flutterEngine?.let {
            deviceInstallationService = DeviceInstallationService(context, it)
        }
    }
    ```

1. **PushNotificationFirebaseMessagingServices**에서 **notificationActionService** 및 **notificationRegistrationService** 속성을 설정합니다.

    ```kotlin
    flutterEngine?.let {
      deviceInstallationService = DeviceInstallationService(context, it)
      PushNotificationsFirebaseMessagingService.notificationActionService = NotificationActionService(it)
      PushNotificationsFirebaseMessagingService.notificationRegistrationService = NotificationRegistrationService(it)
    }
    ```

1. 동일한 함수에서 **FirebaseInstanceId.getInstance().instanceId**를 조건부로 호출합니다. **refreshRegistration**을 호출하기 전에 **PushNotificationFirebaseMessagingService**에서 결과 **토큰** 값을 설정하도록 **OnCompleteListener**를 구현합니다.

    ```kotlin
    if(deviceInstallationService?.playServicesAvailable) {
        FirebaseInstanceId.getInstance().instanceId
            .addOnCompleteListener(OnCompleteListener { task ->
                if (!task.isSuccessful)
                    return@OnCompleteListener

                PushNotificationsFirebaseMessagingService.token = task.result?.token
                PushNotificationsFirebaseMessagingService.notificationRegistrationService?.refreshRegistration()
            })
    }
    ```

1. 여전히 **onCreate** 함수의 끝에서 **processNotificationActions**를 호출합니다. *true*를 *launchAction* 인수에 사용하여 앱을 시작하는 동안 이 작업이 처리됨을 나타냅니다.

    ```kotlin
    processNotificationActions(this.intent, true)
    ```

> [!NOTE]
> 푸시 알림을 계속 받으려면 앱을 실행할 때마다 버그 세션에서 앱을 다시 등록하고 중지해야 합니다.
