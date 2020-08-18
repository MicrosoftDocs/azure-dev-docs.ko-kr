---
author: edburns
ms.author: edburns
ms.date: 08/09/2020
ms.openlocfilehash: e006f2ec9d6f0d3b2d83a6653d65da19489dc221
ms.sourcegitcommit: b923aee828cd4b309ef92fe1f8d8b3092b2ffc5a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88052265"
---
### <a name="account-for-authentication-and-authorization"></a>인증 및 권한 부여 고려

대부분의 애플리케이션에는 일종의 인증 및 권한 부여가 있습니다.  LDAP를 인증에 사용하면 Azure 기반 WebLogic Server가 자동 통합을 지원합니다. 마켓플레이스 솔루션에서는 Azure AD DS(Azure Active Directory Domain Services)를 보안 LDAP와 함께 사용합니다.  이 솔루션은 Azure AD DS의 데이터로 WebLogic Server의 기본 보안영역을 만듭니다.  자세한 내용은 [WebLogic Server의 Java 앱을 Azure로 마이그레이션하기 위한 최종 사용자 권한 부여 및 인증](../migrate-weblogic-with-aad-ldap.md)을 참조하세요.
