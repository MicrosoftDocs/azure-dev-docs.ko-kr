---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: a0b7faea4c122012ddb296af339ff95ef6f41353
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401656"
---
#### <a name="no-response-from-the-backend-service"></a>백 엔드 서비스에서 응답 없음

로컬로 테스트하는 경우 백 엔드 서비스가 실행 중이고 올바른 포트를 사용하고 있는지 확인합니다.

**Azure API 앱**을 대상으로 테스트하는 경우 서비스가 실행 중이고 배포되었으며 오류 없이 시작되었는지 확인합니다.

클라이언트를 통해 테스트할 때 **[Postman](https://www.postman.com/downloads)** 또는 모바일 앱 구성에서 기본 주소를 올바르게 지정했는지 확인해야 합니다. 로컬로 테스트하는 경우에는 기본 주소가 명확하게 `https://<api_name>.azurewebsites.net/` 또는 `https://localhost:5001/`이어야 합니다.

#### <a name="not-receiving-notifications-on-android-after-starting-or-stopping-a-debug-session"></a>디버그 세션을 시작 또는 중지한 후 Android에서 알림이 수신되지 않음

디버그 세션을 시작 또는 중지한 후 다시 등록해야 합니다. 그러면 디버거가 새 **Firebase** 토큰을 생성합니다. 알림 허브 설치도 업데이트해야 합니다.

#### <a name="receiving-a-401-status-code-from-the-backend-service"></a>백 엔드 서비스에서 401 상태 코드 수신

**apikey** 요청 헤더를 설정 중이며 이 값이 백 엔드 서비스에 대해 구성한 값과 일치하는지 확인합니다.

로컬로 테스트할 때 이 오류가 발생하는 경우 클라이언트 구성에서 정의한 키 값이 [API](#create-the-api-app)에서 사용하는 **Authentication:ApiKey** 사용자 설정 값과 일치하는지 확인합니다.

**API 앱**을 사용하여 테스트하는 경우 클라이언트 구성 파일의 키 값이 [API 앱](#create-the-api-app)에서 사용 중인 **Authentication:ApiKey** 애플리케이션 설정과 일치하는지 확인합니다.

> [!NOTE]
> 백 엔드 서비스를 배포한 후 이 설정을 만들거나 변경한 경우 서비스를 다시 시작해야 바뀐 내용이 적용됩니다.

[API 키를 사용하여 클라이언트 인증](#authenticate-clients-using-an-api-key-optional) 섹션을 완료하지 않기로 선택한 경우 **NotificationsController** 클래스에 **Authorize** 특성을 적용하지 않았는지 확인합니다.

#### <a name="receiving-a-404-status-code-from-the-backend-service"></a>백 엔드 서비스에서 404 상태 코드 수신

엔드포인트 및 HTTP 요청 메서드가 올바른지 확인합니다. 예를 들어 엔드포인트는 명확하게 다음과 같아야 합니다.

- **[PUT]** `https://<api_name>.azurewebsites.net/api/notifications/installations`
- **[DELETE]** `https://<api_name>.azurewebsites.net/api/notifications/installations/<installation_id>`
- **[POST]** `https://<api_name>.azurewebsites.net/api/notifications/requests`

또는 로컬로 테스트하는 경우에는 다음과 같아야 합니다.

- **[PUT]** `https://localhost:5001/api/notifications/installations`
- **[DELETE]** `https://localhost:5001/api/notifications/installations/<installation_id>`
- **[POST]** `https://localhost:5001/api/notifications/requests`

클라이언트 앱에서 기본 주소를 지정할 때 주소가 `/`로 끝나야 합니다. 로컬로 테스트하는 경우에는 기본 주소가 명확하게 `https://<api_name>.azurewebsites.net/` 또는 `https://localhost:5001/`이어야 합니다.

#### <a name="unable-to-register-and-a-notification-hub-error-message-is-displayed"></a>등록할 수 없으며 알림 허브 오류 메시지가 표시됨

테스트 디바이스가 네트워크에 연결되었는지 확인합니다. 그런 다음, **HttpResponse**에서 **StatusCode** 속성 값을 검사하는 중단점을 설정하여 Http 응답 상태 코드를 확인합니다.

상태 코드에 따라 적용 가능한 이전 문제 해결 제안 사항을 검토합니다.

각 API에 대해 이러한 특정 상태 코드를 반환하는 중단점을 줄에서 설정합니다. 그런 다음, 로컬로 디버그할 때 백 엔드 서비스를 호출해 봅니다.

적절한 페이로드를 사용하여 **[Postman](https://www.postman.com/downloads)** 를 통해 백 엔드 서비스가 예상대로 작동하는지 확인합니다. 의심스러운 플랫폼에 대한 클라이언트 코드에서 만든 실제 페이로드를 사용합니다.

플랫폼 관련 구성 섹션을 검토하여 누락된 단계는 없는지 확인합니다. 적절한 플랫폼의 `installation id` 및 `token` 변수에 대한 적절한 값이 확인되는지 검사합니다.

#### <a name="unable-to-resolve-an-id-for-the-device-error-message-is-displayed"></a>디바이스의 ID를 확인할 수 없다는 오류 메시지가 표시됨

플랫폼 관련 구성 섹션을 검토하여 누락된 단계는 없는지 확인합니다.
