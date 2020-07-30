---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: fb2311fe9256daa53b2687c43b508c4af4f97bd2
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401692"
---
### <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging-for-android"></a>Firebase 프로젝트 생성 및 Android용 Firebase Cloud Messaging 사용

1. [Firebase 콘솔](https://firebase.google.com/console/)에 로그인합니다. **프로젝트 이름**으로 **PushDemo**를 입력하는 새 Firebase 프로젝트를 만듭니다.

    > [!NOTE]
    > 고유한 이름이 생성됩니다. 기본적으로 이는 사용자가 제공한 이름의 소문자 변형과 대시로 구분되는 생성된 숫자로 구성됩니다. 계속 전역적으로 고유하게 지정하려는 경우 이를 변경할 수 있습니다.

1. 프로젝트를 만든 후 **Android 앱에 Firebase 추가**를 선택합니다.

    ![Android 앱에 Firebase 추가](../media/notification-hubs-add-firebase-to-android-app.png)

1. **Android 앱에 Firebase 추가** 페이지에서 다음 단계를 수행합니다.
    1. **Android 패키지 이름**에는 패키지의 이름을 입력합니다. 예: `com.<organization_identifier>.<package_name>`

        ![패키지 이름 지정](../media/specify-package-name-firebase-cloud-messaging-settings.png)
    1. **앱 등록**을 선택합니다.  
    1. **google-services.json 다운로드**를 선택합니다. 그런 다음, 나중에 사용할 수 있도록 파일을 로컬 폴더에 저장하고 **다음**을 선택합니다.  

        ![google-services.json 다운로드](../media/download-google-service-button.png)
    1. **다음**을 선택합니다.
    1. **콘솔에서 계속 진행**을 선택합니다.

        > [!NOTE]
        > *설치 확인*으로 인해 **콘솔에서 계속 진행** 단추를 사용할 수 없는 경우 **이 단계 건너뛰기**를 선택합니다.

1. Firebase 콘솔에서 프로젝트의 톱니바퀴를 선택합니다. 그런 후 **프로젝트 설정**을 선택합니다.

    ![프로젝트 설정 선택](../media/notification-hubs-firebase-console-project-settings.png)

    > [!NOTE]
    > **google-services.json** 파일을 다운로드하지 않은 경우 이 페이지에서 다운로드할 수 있습니다.

1. 맨 위에 있는 **클라우드 메시징** 탭으로 전환합니다. 나중에 사용하기 위해 **서버 키**를 복사하고 저장합니다. 이 값은 알림 허브를 구성하는 데 사용됩니다.

    ![서버 키 복사](../media/server-key.png)
