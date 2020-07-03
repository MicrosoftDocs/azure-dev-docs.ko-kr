---
title: Visual Studio App Center 및 Azure 서비스를 사용하여 모바일 앱에 인증 추가
description: 사용자 인증을 설정하고 모바일 애플리케이션이 소셜 계정, Azure Active Directory 및 사용자 지정 인증을 통해 인증을 받도록 하는 Visual Studio App Center와 같은 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 782123b4b567010a43ce72c622f7db5a65679db9
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632585"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>모바일 앱에서 인증 추가 및 사용자 ID 관리

애플리케이션에서 사용자 및 해당 동작을 표시하면 개발자는 사용자에게 적합한 환경을 만들어 사용자의 참여를 높일 수 있습니다. 조직 내 사용자를 위해 공동 작업 애플리케이션을 빌드하는 애플리케이션 개발자이거나 차기 소셜 네트워크 플랫폼을 만들려는 경우 사용자를 인증하고 사용자 ID를 관리하는 방법이 필요할 것입니다. ID 관리 서비스는 모바일 백 엔드 서비스의 가장 중요한 기능 중 하나입니다.

다음 서비스를 사용하여 모바일 앱에서 사용자 인증을 사용하도록 설정합니다.

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)는 개발자가 고객을 인증하는 데 사용할 수 있는 B2C(비즈니스 대 소비자) ID 관리 서비스입니다. 이 화이트 라벨 서비스를 사용하여 개발자는 웹, 데스크톱, 모바일 또는 단일 페이지 애플리케이션과 안전하게 상호 작용하는 방식을 사용자 지정하고 제어할 수 있습니다. Azure AD B2C를 사용하여 사용자는 등록, 로그인, 암호 재설정 및 프로필 편집을 수행할 수 있습니다. Azure AD B2C는 OpenID Connect 및 OAuth 2.0 프로토콜의 형태를 구현합니다. 

### <a name="azure-active-directory-b2c-features"></a>Azure Active Directory B2C 기능

- 기본 설정 ID 공급자를 사용하여 고객을 안전하게 인증합니다.
- 고객 ID 및 액세스를 관리합니다.
- Facebook, GitHub, Google, LinkedIn, Twitter, WeChat, Weibo 등의 소셜 미디어에 대한 로그인 지원을 받습니다.
- OpenID Connect 또는 SAML 같은 산업 표준 프로토콜을 사용하여 사용자 계정에 연결함으로써 다양한 플랫폼에서 ID를 관리할 수 있도록 합니다.
- 브랜드 등록 및 로그인 환경을 제공합니다.
- CRM 데이터베이스, 마케팅 분석 도구 및 계정 확인 시스템과 쉽게 연결합니다.
- 고객의 로그인, 기본 설정 및 전환 데이터를 캡처합니다.

### <a name="azure-active-directory-b2c-references"></a>Azure Active Directory B2C 참조

- [Azure Portal](https://portal.azure.com/)
- [Azure AD B2C 설명서](/azure/active-directory-b2c/)
- [빠른 시작](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [샘플](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)는 직원들이 로그인하여 다음에 액세스할 수 있게 해주는 Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스입니다.

- Microsoft Office 365, Azure Portal, 수천 개의 기타 SaaS(Software as a Service) 애플리케이션을 비롯한 외부 리소스
- 조직에서 자체 개발한 클라우드 앱과 함께 회사 네트워크와 인트라넷의 앱 같은 내부 리소스.

### <a name="azure-active-directory-features"></a>Azure Active Directory 기능

- 사용자를 필요한 애플리케이션에 연결하여 원활하고 안전한 액세스 지원
- 사용자, 위치, 디바이스, 데이터 및 애플리케이션 컨텍스트를 기준으로 ID 및 액세스를 위한 포괄적인 ID 보호 및 보안 강화
- 상업용 애플리케이션 및 사용자 지정 애플리케이션(예: Office 365, Salesforce.com 및 Box)을 위한 수천 개의 사전 통합 앱
- 대규모로 액세스를 관리하는 기능

### <a name="azure-active-directory-references"></a>Azure Active Directory 참조

- [Azure Portal](https://portal.azure.com/)
- [Azure AD란?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Azure Active Directory 시작](/azure/active-directory/fundamentals/active-directory-whatis)
- [빠른 시작](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)