---
author: yevster
ms.author: yebronsh
ms.date: 5/19/2020
ms.openlocfilehash: 51ed106fde71e37467571baab2b327b092dddc15
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507639"
---
### <a name="migrate-and-enable-the-identity-provider"></a>ID 공급자 마이그레이션 및 사용

애플리케이션에 인증 또는 권한 부여가 필요한 경우 다음 지침을 사용하여 ID 공급자에 액세스하도록 구성되어 있는지 확인합니다.

* ID 공급자가 Azure Active Directory인 경우 변경할 필요가 없습니다.
* ID 공급자가 온-프레미스 Active Directory 포리스트인 경우 Azure Active Directory를 사용하여 하이브리드 ID 솔루션을 구현하는 것이 좋습니다. 자세한 내용은 [하이브리드 ID 설명서](/azure/active-directory/hybrid/)를 참조하세요.
* ID 공급자가 다른 온-프레미스 솔루션(예: PingFederate)인 경우 [Azure AD Connect 설치 사용자 지정](/azure/active-directory/hybrid/how-to-connect-install-custom) 항목을 참조하여 Azure Active Directory와 페더레이션하도록 구성합니다. 또는 Spring Security를 사용하여 [OAuth2/OpenID Connect](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#oauth2) 또는 [SAML](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-saml2)을 통해 ID 공급자를 사용하는 것이 좋습니다.
