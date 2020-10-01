---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 07778fad0d390b297850a10b544f08d9e5ec3569
ms.sourcegitcommit: e97cb81a245ce7dcabeac3260abc3db7c30edd79
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91493162"
---
### <a name="register-your-ios-app-for-push-notifications"></a>푸시 알림에 대해 iOS 앱 등록

푸시 알림을 iOS 앱에 보내려면 Apple에 애플리케이션을 등록하고 푸시 알림도 등록합니다.  

1. 앱을 아직 등록하지 않은 경우 Apple Developer Center에서 [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456)로 이동합니다. Apple ID를 사용하여 포털에 로그인하고, **인증서, 식별자 및 프로필**로 이동한 다음, **식별자**를 선택합니다. **+** 를 클릭하여 새 앱을 등록합니다.

    ![iOS Provisioning Portal 앱 ID 페이지](../media/notification-hubs-ios-appids.png)

1. **Register a New Identifier**(새 식별자 등록) 화면에서 **App ID**(앱 ID) 라디오 단추를 선택합니다. 그런 다음, **계속**을 선택합니다.

    ![iOS Provisioning Portal 새 ID 등록 페이지](../media/notification-hubs-ios-appids-new.png)

1. 새 앱에 다음과 같은 세 개의 값을 업데이트한 다음, **계속**을 선택합니다.

   * **설명**: 앱에 대한 설명이 포함된 이름을 입력합니다.

   * **Bundle ID**(번들 ID): [앱 배포 가이드](https://help.apple.com/xcode/mac/current/#/dev91fe7130a)에서 설명한 대로 **com.<organization_identifier>.<product_name>** 형식의 번들 ID를 입력합니다. 다음 스크린샷에서 `mobcat` 값은 조직 식별자로 사용되고 **PushDemo** 값은 제품 이름으로 사용됩니다.

      ![iOS Provisioning Portal 앱 ID 등록 페이지](../media/notification-hubs-new-appid-bundle.png)

   * **Push Notifications**: **Capabilities**(기능) 섹션에서 **Push Notifications**(푸시 알림) 옵션을 선택합니다.

      ![새 앱 ID를 등록하는 양식](../media/notification-hubs-new-appid-push.png)

      이 작업은 앱 ID를 생성하고 사용자에게 정보 확인을 요청합니다. **Continue**(계속)를 선택한 다음, **Register**(등록)를 선택하여 새 앱 ID를 확인합니다.

      ![새 앱 ID 확인](../media/notification-hubs-new-appid-register.png)

      **Register**(등록)가 선택되면 **Certificates, Identifiers & Profiles**(인증서, 식별자 및 프로필) 페이지에서 새 앱 ID가 줄 항목으로 표시됩니다.

1. **인증서, 식별자 및 프로필** 페이지의 **식별자**에서, 이전에 만든 앱 ID 줄 항목을 찾습니다. 해당 행을 선택하여 **앱 ID 구성 편집** 화면을 표시합니다.

#### <a name="creating-a-certificate-for-notification-hubs"></a>Notification Hubs에 대한 인증서 만들기

알림 허브가 **APNS(Apple Push Notification Services)** 와 함께 작동하려면 인증서가 필요하며 다음 두 가지 방법 중 하나로 인증서를 제공할 수 있습니다.

1. [알림 허브에 직접 업로드할 수 있는 p12 푸시 인증서 만들기](#option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub)(*원래 방법*)

1. [토큰 기반 인증에 사용할 수 있는 p8 인증서 만들기](#option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication)(*새로운 권장 방법*)

새로운 방법은 [APNS에 대한 토큰 기반(HTTP/2) 인증](/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification)에 설명된 것처럼 여러 가지 이점이 있습니다. 필수 단계가 많지 않으며 특정 시나리오에서는 새 방법을 꼭 사용해야 합니다. 그러나 이 자습서에서는 두 방법 모두 사용할 수 있으므로 두 방법의 단계를 모두 제공합니다.

##### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>옵션 1: 알림 허브에 직접 업로드할 수 있는 p12 푸시 인증서 만들기

1. Mac에서 Keychain Access 도구를 실행합니다. 실행 패드의 **Utilities** 폴더 또는 **Other** 폴더에서 열 수 있습니다.

1. **Keychain 액세스**를 선택하고, **인증서 도우미**를 확장한 다음, **인증 기관에서 인증서 요청**을 선택합니다.

    ![Keychain Access를 사용하여 새 인증서 요청](../media/notification-hubs-request-certificate-from-certificate-assistant.png)

   > [!NOTE]
   > 기본적으로 Keychain Access는 목록의 첫 번째 항목을 선택합니다. 이는 사용자가 **인증서** 카테고리에 있고, **Apple Worldwide Developer Relations Certification Authority**가 목록의 첫 번째 항목이 아닌 경우 문제가 될 수 있습니다. CSR(인증서 서명 요청)을 생성하기 전에 키가 아닌 항목이 있거나 **Apple Worldwide Developer Relations Certification Authority** 키가 선택되어 있는지 확인합니다.

1. **사용자 이메일 주소**를 선택하고, **일반 이름** 값을 입력하고, **디스크에 저장됨**을 지정했는지 확인한 다음, **계속**을 선택합니다. **CA 이메일 주소**는 필요하지 않으므로 비워둡니다.

    ![필요한 인증서 정보](../media/notification-hubs-certificate-signing-request-info.png)

1. **Save As**에 **CSR(인증서 서명 요청)** 파일의 이름을 입력하고 **Where**에서 위치를 선택한 다음, **Save**를 선택합니다.

    ![인증서에 대한 파일 이름 선택](../media/notification-hubs-save-certificate-signing-request.png)

    이 작업은 **CSR 파일**을 선택한 위치에 저장합니다. 기본 위치는 **바탕 화면**입니다. 이 파일에 대해 선택한 위치를 기억해 두세요.

1. [iOS 프로비저닝 포털](https://go.microsoft.com/fwlink/p/?LinkId=272456)의 **인증서, 식별자 및 프로필** 페이지로 돌아가서, 선택한 **푸시 알림** 옵션이 나올 때까지 아래로 스크롤한 다음, **구성**을 선택하여 인증서를 만듭니다.

    ![앱 ID 편집 페이지](../media/notification-hubs-edit-appid.png)

1. **Apple Push Notification service TLS/SSL Certificates**(Apple 푸시 알림 서비스 SSL 인증서) 창이 표시됩니다. **Development TLS/SSL Certificate**(개발 TLS/SSL 인증서) 섹션 아래에서 **Create Certificate**(인증서 만들기) 단추를 선택합니다.

    ![앱 ID 인증서 만들기 단추](../media/notification-hubs-appid-create-certificate.png)

    **Create a new Certificate**(새 인증서 만들기) 화면이 표시됩니다.

    > [!NOTE]
    > 이 자습서에서는 개발 인증서를 사용합니다. 프로덕션 인증서를 등록할 때에도 동일한 프로세스가 사용됩니다. 알림을 보낼 때 동일한 인증서 유형을 사용하는지만 확인하면 됩니다.

1. **Choose File**(파일 선택)을 선택하고, **CSR 파일**을 저장한 위치로 이동한 다음, 인증서 이름을 두 번 클릭하여 로드합니다. 그런 다음, **계속**을 선택합니다.

1. 포털에서 인증서가 만들어지면 **Download**(다운로드) 단추를 선택합니다. 인증서를 저장하고 저장된 위치를 기억합니다.

    ![생성된 인증서 다운로드 페이지](../media/notification-hubs-appid-download-certificate.png)

    인증서가 다운로드되고 컴퓨터의 **다운로드** 폴더에 저장됩니다.

    ![Downloads 폴더에서 인증서 파일 찾기](../media/notification-hubs-certificate-downloaded.png)

    > [!NOTE]
    > 기본적으로 다운로드된 개발 인증서는 이름이 **aps_development.cer**로 지정됩니다.

1. 다운로드한 푸시 인증서 **aps_development.cer**을 두 번 클릭합니다. 이 작업은 다음 이미지에 보이는 것처럼 새 인증서를 Keychain에 설치합니다.

    ![새 인증서를 보여 주는 키 집합 액세스 인증서 목록](../media/notification-hubs-certificate-in-keychain.png)

    > [!NOTE]
    > 인증서의 이름이 다를 수 있지만, **Apple 개발 iOS 푸시 서비스**가 접두사로 추가되고 적절한 번들 식별자가 연결됩니다.

1. Keychain Access에서, **인증서** 범주에서 만든 새 푸시 인증서를 **Ctrl** + **클릭**합니다. **내보내기**를 선택하고 파일의 이름을 지정한 다음, **p12** 형식 및 **저장**을 선택합니다.

    ![p12 형식으로 인증서 내보내기](../media/notification-hubs-export-certificate-p12.png)

    암호를 사용하여 인증서를 보호하도록 선택할 수 있지만 이는 선택 사항입니다. 암호 만들기를 무시하려면 **OK**(확인)를 클릭합니다. 내보낸 p12 인증서의 파일 이름과 위치를 적어 둡니다. APNs로 인증하도록 설정할 때 사용됩니다.

    > [!NOTE]
    > p12 파일 이름과 위치가 이 자습서에 표시된 것과 다를 수 있습니다.

##### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>옵션 2: 토큰 기반 인증에 사용할 수 있는 p8 인증서 만들기

1. 다음 세부 사항에 유의합니다.

    * **앱 ID 접두사**(**팀 ID**)
    * **번들 ID**

1. **인증서, 식별자 및 프로필**로 돌아가서 **키**를 클릭합니다.

   > [!NOTE]
   > **APNS**에 대해 구성된 키가 이미 있는 경우 만든 직후 바로 다운로드한 p8 인증서를 다시 사용할 수 있습니다. 이 경우 **3** ~ **5**단계를 생략할 수 있습니다.

1. **+** 단추(**키 만들기** 단추)를 클릭하여 새 키를 만듭니다.
1. 적절한 **키 이름** 값을 입력하고 **APNS(Apple Push Notification service)** 옵션을 선택한 다음, **계속**을 클릭하고 다음 화면에서 **등록**을 클릭합니다.
1. **다운로드**를 클릭한 다음, **p8** 파일(*AuthKey_* 가 앞에 있음)을 안전한 로컬 디렉터리로 이동하고 **완료**를 클릭합니다.

   > [!NOTE]
   > p8 파일을 안전한 장소에 보관하고 백업을 저장해야 합니다. 키를 다운로드한 후 서버 복사본이 제거되면 다시 다운로드할 수 없습니다.
  
1. **키**에서, 방금 만든 키(또는 대신 사용하도록 선택한 경우 기존 키)를 클릭합니다.
1. **Key ID** 값을 기록해 둡니다.
1. [**Visual Studio Code**](https://code.visualstudio.com)와 같이 적절한 애플리케이션에서 p8 인증서를 엽니다. 키 값( **-----시작 프라이빗 키-----** ~ **-----끝 프라이빗 키-----** 사이의 값)을 적어 둡니다.

    -----시작 프라이빗 키-----  
    <key_value>  
    -----끝 프라이빗 키-----

    > [!NOTE]
    > 이는 나중에 **알림 허브**를 구성하는 데 사용되는 **토큰 값**입니다.

이 단계를 마치면 나중에 [APNS 정보를 사용하여 알림 허브 구성](#configure-your-notification-hub-with-apns-information) 단계에서 사용할 수 있는 다음 정보가 확보됩니다.

* **팀 ID**(1단계 참조)
* **번들 ID**(1단계 참조)
* **키 ID**(7단계 참조)
* **토큰 값**(8단계에서 얻은 p8 키 값)

### <a name="create-a-provisioning-profile-for-the-app"></a>앱용 프로비저닝 프로필 만들기

1. [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456)로 돌아가서 **Certificates, Identifiers & Profiles**(인증서, 식별자 및 프로필)를 선택하고, 왼쪽 메뉴에서 **Profiles**(프로필)를 선택한 다음, **+** 를 선택하여 새 프로필을 만듭니다. **Register a New Provisioning Profile**(새 프로비저닝 프로필 등록) 화면이 표시됩니다.

1. **Development**(개발) 아래에서 프로비저닝 프로필 유형으로 **iOS App Development**(iOS 앱 개발)를 선택한 다음, **Continue**(계속)를 선택합니다.

    ![프로비전 프로필 목록](../media/notification-hubs-new-provisioning-profile.png)

1. 다음으로 **App ID** 드롭다운 목록에서 이전에 만든 앱 ID를 선택하고, **계속**을 선택합니다.

    ![앱 ID 선택](../media/notification-hubs-select-appid-for-provisioning.png)

1. **Select certificates**(인증서 선택) 창에서 코드 서명에 사용하는 개발 인증서를 선택하고, **Continue**(계속)를 선택합니다.

    > [!NOTE]
    > 이 인증서는 [이전 단계](#creating-a-certificate-for-notification-hubs)에서 만든 푸시 인증서가 아니라 개발 인증서입니다. 인증서는 이 자습서의 [필수 구성 요소](#prerequisites)이므로 인증서가 없으면 만들어야 합니다. [Apple 개발자 포털](https://developer.apple.com), [Xcode](https://developer.apple.com/library/archive/documentation/ToolsLanguages/Conceptual/YourFirstAppStoreSubmission/ProvisionYourDevicesforDevelopment/ProvisionYourDevicesforDevelopment.html) 또는 [Visual Studio](/xamarin/ios/get-started/installation/device-provisioning/)에서 개발자 인증서를 만들 수 있습니다.

1. **Certificates, Identifiers & Profiles**(인증서, 식별자 및 프로필) 페이지로 돌아가고, 왼쪽 메뉴에서 **Profiles**(프로필)를 선택한 다음, **+** 를 선택하여 새 프로필을 만듭니다. **Register a New Provisioning Profile**(새 프로비저닝 프로필 등록) 화면이 표시됩니다.

1. **Select certificates**(인증서 선택) 창에서, 방금 만든 개발 인증서를 선택합니다. 그런 다음, **계속**을 선택합니다.

1. 다음으로 테스트에 사용할 디바이스를 선택하고, **계속**을 선택합니다.

1. 마지막으로, **Provisioning Profile Name**(프로비저닝 프로필 이름)에서 프로필 이름을 선택하고, **Generate**(생성)를 선택합니다.

    ![프로비전 프로필 이름 선택](../media/notification-hubs-provisioning-name-profile.png)

1. 새 프로비저닝 프로필이 만들어지면 **다운로드**를 선택합니다. 저장된 위치를 기억합니다.

1. 프로비저닝 프로필의 위치를 찾은 다음, 해당 프로필을 두 번 클릭하여 개발 머신에 설치합니다.