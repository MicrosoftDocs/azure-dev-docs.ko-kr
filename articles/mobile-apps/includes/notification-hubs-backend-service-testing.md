---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 95dd432dfae61346c5a3129be12c534eba045c10
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401636"
---
### <a name="send-a-test-notification"></a>테스트 알림 보내기

1. [Postman](https://www.postman.com/downloads/)에서 새 탭을 엽니다.

1. 요청을 **POST**로 설정하고 다음 주소를 입력합니다.

    ```xml
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

1. [API 키를 사용하여 클라이언트 인증](#authenticate-clients-using-an-api-key-optional) 섹션을 완료하도록 선택한 경우 **apikey** 값을 포함하도록 요청 헤더를 구성해야 합니다.

   | 키                            | 값                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. **본문**에 대해 **raw(원시)** 옵션을 선택하고, 형식 옵션 목록에서 **JSON**을 선택한 다음, 자리 표시자 **JSON** 콘텐츠를 포함시킵니다.

    ```json
    {
        "text": "Message from Postman!",
        "action": "action_a"
    }
    ```

1. **코드** 단추를 선택합니다. 이 단추는 창의 오른쪽 위에 **저장** 단추 아래에 있습니다. 요청은 **HTML**에 대해 표시되는 경우 다음 예제와 비슷합니다(**apikey** 헤더를 포함했는지 여부에 따라 다름).

    ```html
    POST /api/notifications/requests HTTP/1.1
    Host: https://<app_name>.azurewebsites.net
    apikey: <your_api_key>
    Content-Type: application/json

    {
        "text": "Message from backend service",
        "action": "action_a"
    }
    ```

1. 대상 플랫폼(**Android** 및 **iOS**) 중 하나 또는 둘 다에서 **PushDemo** 애플리케이션을 실행합니다.

    > [!NOTE]
    > **Android**에서 테스트하는 경우 **디버그** 모드에서 실행하고 있지 않은지 확인합니다. 또는 애플리케이션을 실행하여 앱을 배포한 경우에는 앱을 실행하고 강제로 종료한 후 시작 관리자에서 앱을 다시 시작합니다.

1. **PushDemo** 앱에서 **등록** 단추를 탭합니다.

1. **[Postman](https://www.postman.com/downloads)** 으로 돌아가서 **코드 조각 생성** 창을 닫고(아직 닫지 않은 경우) **보내기** 단추를 클릭합니다.

1. **[Postman](https://www.postman.com/downloads)** 에서 **200 OK** 응답을 수신하고, **ActionA 작업 수신됨** 경고가 앱에 표시되는지 확인합니다.  

1. **PushDemo** 앱을 닫고, **[Postman](https://www.postman.com/downloads)** 에서 **보내기** 단추를 다시 클릭합니다.

1. **[Postman](https://www.postman.com/downloads)** 에서 **200 OK** 응답을 수신하는지 다시 한 번 확인합니다. **PushDemo** 앱의 알림 영역에 올바른 메시지와 함께 알림이 표시되는지 확인합니다.

1. 알림을 탭하여 앱이 열리고 **ActionA 작업 수신됨** 경고가 표시되는지 확인합니다.

1. **[Postman](https://www.postman.com/downloads)** 으로 돌아가서, **action** 값으로 *action_a* 대신 *action_b*를 지정하는 자동 알림을 보내도록 이전 요청 본문을 수정합니다.

    ```json
    {
        "action": "action_b",
        "silent": true
    }
    ```

1. 앱이 열려 있는 상태로 **[Postman](https://www.postman.com/downloads)** 에서 **보내기** 단추를 클릭합니다.

1. **[Postman](https://www.postman.com/downloads)** 에서 **200 OK** 응답을 수신하고, **ActionA 작업 수신됨** 대신 **ActionB 작업 수신됨** 경고가 앱에 표시되는지 확인합니다.

1. **PushDemo** 앱을 닫고, **[Postman](https://www.postman.com/downloads)** 에서 **보내기** 단추를 다시 클릭합니다.

1. **[Postman](https://www.postman.com/downloads)** 에서 **200 확인** 응답을 수신하고, 알림 영역에 자동 알림이 표시되지 않는지 확인합니다.
