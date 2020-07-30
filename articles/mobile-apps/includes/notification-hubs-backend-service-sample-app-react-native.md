---
author: alexeystrakh
ms.author: alstrakh
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: ef5f71997eca3d3ac35e004b5ee97668fb7582c9
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401666"
---
### <a name="create-the-react-native-solution"></a>React Native 솔루션 만들기

1. `Terminal`에서 다음 명령을 사용하여 React Native 작업에 필요한 환경 도구를 업데이트합니다.

    ```bash
    # install node
    brew install node
    # or update
    brew update node
    # install watchman
    brew install watchman
    # or update
    brew upgrade watchman
    # install cocoapods
    sudo gem install cocoapods
    ```

1. `Terminal`에서 다음 명령을 실행합니다(제거할 `React Native` CLI가 설치되어 있는 경우). `npx`를 사용하여 사용 가능한 최신 React Native CLI 버전에 자동으로 액세스합니다.

    ```bash
    npm uninstall -g react-native-cli
    ```

    > [!NOTE]
    > React Native에는 기본 제공 명령줄 인터페이스가 있습니다. 특정 버전의 CLI를 전역적으로 설치하고 관리하는 대신 Node.js와 함께 제공되는 `npx`를 사용하여 런타임에 현재 버전에 액세스하는 것이 좋습니다. `npx react-native <command>`를 사용하면 명령을 실행할 때 안정적인 최신 CLI 버전이 다운로드되어 실행됩니다

1. 새 애플리케이션을 만들 프로젝트 폴더로 이동합니다. `--template` 매개 변수를 지정하여 Typescript 기반 템플릿을 사용합니다.

    ```bash
    # init new project with npx
    npx react-native init PushDemo --template react-native-template-typescript
    ```

1. JavaScript 번들을 빌드하고 코드 업데이트를 모니터링하여 번들을 실시간으로 새로 고치는 metro 서버를 실행합니다.

    ```bash
    cd PushDemo
    npx react-native start
    ```

1. iOS 앱을 실행하여 설치를 확인합니다. 다음 명령을 실행하기 전에 iOS 시뮬레이터를 시작하거나 iOS 디바이스를 연결했는지 확인합니다.

    ```bash
    npx react-native run-ios
    ```

1. Android 앱을 실행하여 설치를 확인합니다. React Native metro 서버에 액세스할 수 있도록 Android 에뮬레이터 또는 디바이스를 구성하려면 몇 가지 단계가 추가로 필요합니다. 다음 명령은 Android용 초기 JavaScript 번들을 생성하여 자산 폴더에 넣습니다.

    ```bash
    # create assets folder for the bundle
    mkdir android/app/scr/main/assets
    # build the bundle
    npx react-native bundle --platform android --dev true --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res
    # enable ability for sim to access the localhost
    adb reverse tcp:8081 tcp:8081
    ```

    이 스크립트는 초기 버전의 앱과 함께 미리 배포됩니다. 배포되면, 서버 IP 주소를 지정하여 에뮬레이터 또는 디바이스가 metro 서버에 액세스하도록 구성합니다. 다음 명령을 실행하여 Android 애플리케이션을 빌드하고 실행합니다.

    ```bash
    npx react-native run-android
    ```

    앱에서 `CMD+M`(에뮬레이터)을 누르거나 디바이스를 흔들어서 개발자 설정을 채운 다음, `Settings` > `Change Bundle Location`으로 이동하고 기본 포트 `<metro-server-ip-address>:8081`을 사용하여 metro 서버 IP 주소를 지정합니다.

1. `App.tsx` 파일에서 페이지 레이아웃에 변경 내용을 적용하고 저장하여 iOS와 Android 앱 모두에 자동으로 반영되도록 합니다.

    > [!NOTE]
    > 자세한 개발 환경 설치 가이드는 [공식 설명서](https://reactnative.dev/docs/environment-setup)를 참조하세요.

### <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

이 샘플이 작동하려면 다음 세 가지 패키지가 필요합니다.

1. [React Native 푸시 알림 iOS](https://www.npmjs.com/package/@react-native-community/push-notification-ios) - [프로젝트 GitHub](https://github.com/react-native-community/push-notification-ios)

    이 패키지는 PushNotificationIOS가 React Native의 핵심에서 분리될 때 만들어졌습니다. 이 패키지는 기본적으로 iOS에 대한 푸시 알림을 구현하고 여기에 액세스할 수 있는 React Native 인터페이스를 제공합니다. 다음 명령을 실행하여 패키지를 설치합니다.

    ```bash
    yarn add @react-native-community/push-notification-ios
    ```

1. [플랫폼 간 React Native 푸시 알림](https://www.npmjs.com/package/react-native-push-notification)

    이 패키지는 플랫폼 간 방식으로 iOS 및 Android에 로컬 및 원격 알림을 구현합니다. 다음 명령을 실행하여 패키지를 설치합니다.

    ```bash
    yarn add react-native-push-notification
    ```

1. [디바이스 정보 패키지](https://www.npmjs.com/package/react-native-device-info) 이 패키지는 런타임 시 디바이스에 대한 정보를 제공합니다. 푸시 알림에 등록하는 데 사용되는 디바이스 식별자를 정의하는 데 사용됩니다. 다음 명령을 실행하여 패키지를 설치합니다.

    ```bash
    yarn add react-native-device-info
    ```

### <a name="implement-the-cross-platform-components"></a>플랫폼 간 구성 요소 구현

1. `DemoNotificationHandler` 만들기 및 구현:

    ```typescript
    import PushNotification from 'react-native-push-notification';

    class DemoNotificationHandler {
      private _onRegister: any;
      private _onNotification: any;

      onNotification(notification: any) {
        console.log('NotificationHandler:', notification);

        if (typeof this._onNotification === 'function') {
          this._onNotification(notification);
        }
      }

      onRegister(token: any) {
        console.log('NotificationHandler:', token);

        if (typeof this._onRegister === 'function') {
          this._onRegister(token);
        }
      }

      attachTokenReceived(handler: any) {
        this._onRegister = handler;
      }

      attachNotificationReceived(handler: any) {
        this._onNotification = handler;
      }
    }

    const handler = new DemoNotificationHandler();

    PushNotification.configure({
      onRegister: handler.onRegister.bind(handler),
      onNotification: handler.onNotification.bind(handler),
      permissions: {
        alert: true,
        badge: true,
        sound: true,
      },
      popInitialNotification: true,
      requestPermissions: true,
    });

    export default handler;
    ```

1. `DemoNotificationService` 만들기 및 구현:

    ```typescript
    import PushNotification from 'react-native-push-notification';
    import DemoNotificationHandler from './DemoNotificationHandler';

    export default class DemoNotificationService {
      constructor(onTokenReceived: any, onNotificationReceived: any) {
        DemoNotificationHandler.attachTokenReceived(onTokenReceived);
        DemoNotificationHandler.attachNotificationReceived(onNotificationReceived);
        PushNotification.getApplicationIconBadgeNumber(function(number: number) {
          if(number > 0) {
            PushNotification.setApplicationIconBadgeNumber(0);
          }
        });
      }

      checkPermissions(cbk: any) {
        return PushNotification.checkPermissions(cbk);
      }

      requestPermissions() {
        return PushNotification.requestPermissions();
      }

      cancelNotifications() {
        PushNotification.cancelLocalNotifications();
      }

      cancelAll() {
        PushNotification.cancelAllLocalNotifications();
      }

      abandonPermissions() {
        PushNotification.abandonPermissions();
      }
    }
    ```

1. `DemoNotificationRegistrationService` 만들기 및 구현:

    ```typescript
    export default class DemoNotificationService {
        constructor(
            readonly apiUrl: string,
            readonly apiKey: string) {
        }

    async registerAsync(request: any): Promise<Response> {
            const method = 'PUT';
            const registerApiUrl = `${this.apiUrl}/notifications/installations`;
            const result = await fetch(registerApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                },
                body: JSON.stringify(request)
            });

            this.validateResponse(registerApiUrl, method, request, result);
            return result;
        }

        async deregisterAsync(deviceId: string): Promise<Response> {
            const method = 'DELETE';
            const deregisterApiUrl = `${this.apiUrl}/notifications/installations/${deviceId}`;
            const result = await fetch(deregisterApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                }
            });

            this.validateResponse(deregisterApiUrl, method, null, result);
            return result;
        }

        private validateResponse(requestUrl: string, method: string, requestPayload: any, response: Response) {
            console.log(`Request: ${method} ${requestUrl} => ${JSON.stringify(requestPayload)}\nResponse: ${response.status}`);
            if (!response || response.status != 200) {
                throw `HTTP error ${response.status}: ${response.statusText}`;
            }
        }
    }
    ```

1. 앱을 구성합니다. `package.json`을 열고 다음 스크립트 정의를 추가합니다.

    ```json
    "configure": "cp .app.config.tsx src/config/AppConfig.tsx"
    ```

    그런 다음, 이 스크립트를 실행하면 기본 구성이 `config` 폴더에 복사됩니다.

    ```bash
    yarn configure
    ```

    마지막 단계는 이전 단계에서 복사한 구성 파일을 API 액세스 정보로 업데이트하는 것입니다. `apiKey` 및 `apiUrl` 매개 변수를 지정합니다.

    ```typescript
    module.exports = {
        appName: "PushDemo",
        env: "production",
        apiUrl: "https://<azure-push-notifications-api-url>/api/",
        apiKey: "<api-auth-key>",
    };
    ```

### <a name="implement-the-cross-platform-ui"></a>플랫폼 간 UI 구현

1. 페이지 레이아웃 정의

    ```typescript
    <View style={styles.container}>
      {this.state.isBusy &&
        <ActivityIndicator></ActivityIndicator>
      }
      <View style={styles.button}>
        <Button title="Register" onPress={this.onRegisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
      <View style={styles.button}>
        <Button title="Deregister" onPress={this.onDeregisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
    </View>
    ```

1. 스타일 적용

    ```css
    const styles = StyleSheet.create({
      container: {
        flex: 1,
        alignItems: "center",
        justifyContent: 'flex-end',
        margin: 50,
      },
      button: {
        margin: 5,
        width: "100%",
      }
    });
    ```

1. 페이지 구성 요소 초기화

    ```typescript
      state: IState;
      notificationService: DemoNotificationService;
      notificationRegistrationService: DemoNotificationRegistrationService;
      deviceId: string;

      constructor(props: any) {
        super(props);
        this.deviceId = DeviceInfo.getUniqueId();
        this.state = {
          status: "Push notifications registration status is unknown",
          registeredOS: "",
          registeredToken: "",
          isRegistered: false,
          isBusy: false,
        };

        this.notificationService = new DemoNotificationService(
          this.onTokenReceived.bind(this),
          this.onNotificationReceived.bind(this),
        );

        this.notificationRegistrationService = new DemoNotificationRegistrationService(
          Config.apiUrl,
          Config.apiKey,
        );
      }
    ```

1. 단추 클릭 처리기 정의

    ```typescript
      async onRegisterButtonPress() {
        if (!this.state.registeredToken || !this.state.registeredOS) {
          Alert.alert("The push notifications token wasn't received.");
          return;
        }

        let status: string = "Registering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          const pnPlatform = this.state.registeredOS == "ios" ? "apns" : "fcm";
          const pnToken = this.state.registeredToken;
          const request = {
            installationId: this.deviceId,
            platform: pnPlatform,
            pushChannel: pnToken,
            tags: []
          };
          const response = await this.notificationRegistrationService.registerAsync(request);
          status = `Registered for ${this.state.registeredOS} push notifications`;
          isRegistered = true;
        } catch (e) {
          status = `Registration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }

      async onDeregisterButtonPress() {
        if (!this.notificationService)
          return;

        let status: string = "Deregistering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          await this.notificationRegistrationService.deregisterAsync(this.deviceId);
          status = "Deregistered from push notifications";
          isRegistered = false;
        } catch (e) {
          status = `Deregistration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }
    ```

1. 수신 토큰 등록 및 푸시 알림 처리

    ```typescript
      onTokenReceived(token: any) {
        console.log(`Received a notification token on ${token.os}`);
        this.setState({ registeredToken: token.token, registeredOS: token.os, status: `The push notifications token has been received.` });

        if (this.state.isRegistered && this.state.registeredToken && this.state.registeredOS) {
          this.onRegisterButtonPress();
        }
      }

      onNotificationReceived(notification: any) {
        console.log(`Received a push notification on ${this.state.registeredOS}`);
        this.setState({ status: `Received a push notification...` });

        if (notification.data.message) {
          Alert.alert(AppConfig.appName, `${notification.data.action} action received`);
        }
      }
    };
    ```