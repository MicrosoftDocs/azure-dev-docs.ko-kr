---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 6d9f12534c8bd5ab4fac8a1e337196fcc7ad559e
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401671"
---
### <a name="configure-your-notification-hub-with-apns-information"></a>APNS 정보를 사용하여 Notification Hub 구성

**Notification Services**에서 **Apple**을 선택한 다음, 이전에 [Notification Hubs에 대한 인증서 만들기](#creating-a-certificate-for-notification-hubs) 섹션에서 선택한 접근 방식에 따라 적절한 단계를 수행합니다.  

> [!NOTE]
> 스토어에서 앱을 구매한 사용자에게 푸시 알림을 보내려는 경우에만 **애플리케이션 모드**에서 **프로덕션** 모드를 사용합니다.

### <a name="option-1-using-a-p12-push-certificate"></a>옵션 1: .p12 푸시 인증서 사용

1. **인증서**를 선택합니다.

1. 파일 아이콘을 선택합니다.

1. 이전에 내보낸 .p12 파일을 선택한 다음, **열기**를 선택합니다.

1. 필요한 경우 올바른 암호를 지정합니다.

1. **샌드박스** 모드를 선택합니다.

1. **저장**을 선택합니다.

### <a name="option-2-using-token-based-authentication"></a>옵션 2: 토큰 기반 인증 사용

1. **토큰**을 선택합니다.
1. 앞서 얻은 다음 값을 입력합니다.

    - **키 ID**
    - **번들 ID**
    - **팀 ID**
    - **토큰**

1. **샌드박스**를 선택합니다.
1. **저장**을 선택합니다.
