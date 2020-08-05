---
author: alexeystrakh
ms.author: alstrakh
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: a5872650356f8b3ae9b33381615d6128cb2d8d72
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401611"
---
### <a name="configure-required-android-packages"></a>필요한 Android 패키지 구성

이 패키지는 앱을 빌드할 때 [자동으로 연결](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md)됩니다. 구성 프로세스를 완료하는 몇 가지 추가 단계가 아래에 있습니다.

### <a name="configure-android-manifest"></a>Android 매니페스트 구성

"android/app/src/main/AndroidManifest.xml"에서 패키지 이름, 권한 및 필요한 서비스를 확인합니다. `RNPushNotificationPublisher` 및 `RNPushNotificationBootEventReceiver` 수신자를 등록하고 `RNPushNotificationListenerService` 서비스를 등록했는지 확인합니다. 알림 메타데이터는 푸시 알림 모양을 사용자 지정하는 데 사용할 수 있습니다.

  ```xml
  <manifest xmlns:android="http://schemas.android.com/apk/res/android"
      package="YOUR_PACKAGE_NAME">

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>

        <application
          android:name=".MainApplication"
          android:label="@string/app_name"
          android:usesCleartextTraffic="true"
          android:icon="@mipmap/ic_launcher"
          android:roundIcon="@mipmap/ic_launcher_round"
          android:allowBackup="false"
          android:theme="@style/AppTheme">

          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_name"
                      android:value="PushDemo Channel"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_description"
                      android:value="PushDemo Channel Description"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_foreground"
                      android:value="true"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_color"
                      android:resource="@android:color/white"/>

          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationPublisher" />
          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationBootEventReceiver">
              <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
              </intent-filter>
          </receiver>

          <service
              android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationListenerService"
              android:exported="false" >
              <intent-filter>
                  <action android:name="com.google.firebase.MESSAGING_EVENT" />
              </intent-filter>
          </service>

          <activity
            android:name=".MainActivity"
            android:label="@string/app_name"
            android:configChanges="keyboard|keyboardHidden|orientation|screenSize|uiMode"
            android:launchMode="singleTask"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
          </activity>
          <activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />
        </application>

  </manifest>
  ```

### <a name="configure-google-services"></a>Google 서비스 구성

"android/app/build.gradle"에서 Google Services를 등록합니다.

```gradle
dependencies {
  ...
  implementation 'com.google.firebase:firebase-analytics:17.3.0'
  ...
}

apply plugin: 'com.google.gms.google-services'
```

FCM 설치 중에 다운로드한 "google-services.json" 파일을 프로젝트 폴더 "android/app/"에 복사합니다.

### <a name="handle-push-notifications-for-android"></a>Android 푸시 알림 처리

수신 Android 푸시 알림을 처리하도록 기존 `RNPushNotificationListenerService` 서비스를 구성했습니다. 이 서비스는 이전에 애플리케이션 매니페스트에 등록되었습니다. 수신 알림을 처리하고 플랫폼 간 React Native 파트로 프록시합니다. 추가 단계가 필요하지 않습니다.
