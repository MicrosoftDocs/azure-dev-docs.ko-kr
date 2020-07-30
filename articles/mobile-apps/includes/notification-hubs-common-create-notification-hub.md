---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: b5eb04656e4e8e4623e4ca2fe22e75010e1ae1f6
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401684"
---
### <a name="create-a-notification-hub"></a>알림 허브 만들기 

이 섹션에서는 알림 허브를 만들고 **APNS**를 사용하여 인증을 구성합니다. p12 푸시 인증서 또는 토큰 기반 인증을 사용할 수 있습니다. 이미 만든 알림 허브를 사용하려는 경우 5단계로 건너뛸 수 있습니다.

1. [Azure](https://portal.azure.com)에 로그인합니다.

1. **리소스 만들기**를 클릭한 후, **Notification Hub**를 검색하여 선택한 다음, **만들기**를 클릭합니다.

1. 다음 필드를 업데이트한 다음, **만들기**를 클릭합니다.

    **기본 세부 정보**  

    **구독:** 드롭다운 목록에서 대상 **구독**을 선택합니다.  
    **리소스 그룹:** 새 **리소스 그룹**을 만들거나 기존 그룹을 선택합니다.  

    **네임스페이스 세부 정보**  

    **Notification Hub 네임스페이스:** **Notification Hub** 네임스페이스에 대해 전역적으로 고유한 이름을 입력합니다.  

    > [!NOTE]
    > 이 필드에 대해 **새로 만들기** 옵션을 선택했는지 확인합니다.

    **알림 허브 세부 정보**  

    **Notification Hub:** **Notification Hub**의 이름을 입력합니다.  
    **위치:** 드롭다운 목록에서 적절한 위치를 선택합니다.  
    **가격 책정 계층:** 기본 **무료** 옵션 유지  

    > [!NOTE]
    > 무료 계층의 최대 허브 수에 도달하지 않은 경우

1. **Notification Hub**가 프로비저닝되면 해당 리소스로 이동합니다.
1. 새 **Notification Hub**로 이동합니다.
1. 목록(**관리** 아래)에서 **액세스 정책**을 선택합니다.
1. **정책 이름** 값과 해당 **연결 문자열** 값을 기록해 둡니다.
