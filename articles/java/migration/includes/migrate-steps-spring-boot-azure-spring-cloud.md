---
author: yevster
ms.author: yebronsh
ms.date: 8/25/2020
ms.openlocfilehash: 7970cae2b9ac39f7012e74b1334d12b2c5006af0
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89062064"
---
### <a name="create-an-azure-spring-cloud-instance-and-apps"></a>Azure Spring Cloud 인스턴스 및 앱 만들기

Azure 구독에 아직 Azure Spring Cloud 인스턴스가 없다면 지금 프로비저닝합니다. 그런 다음, 애플리케이션 템플릿을 만듭니다. 자세한 내용은 [빠른 시작: Azure Portal을 사용하여 기존 Azure Spring Cloud 애플리케이션 시작](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal)의 지침을 따릅니다.

[!INCLUDE [ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud](ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud.md)]

[!INCLUDE [configure-persistent-storage-azure-spring-cloud](configure-persistent-storage-azure-spring-cloud.md)]

[!INCLUDE [migrate-all-certificates-to-keyvault-azure-spring-cloud](migrate-all-certificates-to-keyvault-azure-spring-cloud.md)]

### <a name="remove-application-performance-management-apm-integrations"></a>APM(애플리케이션 성능 관리) 통합 제거

APM 도구/에이전트와의 통합을 제거합니다. Azure Monitor를 사용하여 성능 관리를 구성하는 방법에 대한 자세한 내용은 [마이그레이션 후](#post-migration) 섹션을 참조하세요.

### <a name="disable-metrics-clients-and-endpoints-in-your-applications"></a>애플리케이션에서 메트릭 클라이언트 및 엔드포인트 사용 안 함

사용된 메트릭 클라이언트 또는 애플리케이션에 공개된 메트릭 엔드포인트를 모두 제거합니다.

### <a name="deploy-the-application"></a>애플리케이션 배포

마이그레이션된 각 마이크로서비스(Spring Cloud Config 및 Registry 서버는 제외)를 배포합니다. 자세한 지침은 [빠른 시작: Azure Portal을 사용하여 기존 Azure Spring Cloud 애플리케이션 시작](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal)을 참조하세요.

### <a name="configure-per-service-secrets-and-externalized-settings"></a>서비스별 비밀 및 외부화된 설정 구성

서비스별 구성 설정을 환경 변수로 각 서비스에 주입할 수 있습니다. Azure Portal에서 다음 단계를 사용합니다.

1. Azure Spring Cloud 인스턴스로 이동하여 **Apps(앱)** 를 선택합니다.
1. 구성할 서비스를 선택합니다.
1. **Configuration(구성)** 을 선택합니다.
1. 구성할 변수를 입력합니다.
1. **저장**을 선택합니다.

![Spring Cloud 앱 구성 설정](../media/migrate-spring-cloud-to-azure-spring-cloud/spring-cloud-app-configuration-settings.png)

### <a name="migrate-and-enable-the-identity-provider"></a>ID 공급자 마이그레이션 및 사용

Spring Cloud 애플리케이션 중에서 인증 또는 권한 부여가 필요한 애플리케이션이 있는 경우 ID 공급자에 액세스하도록 구성되어 있는지 확인합니다.

* ID 공급자가 Azure Active Directory인 경우 변경할 필요가 없습니다.
* ID 공급자가 온-프레미스 Active Directory 포리스트인 경우 Azure Active Directory를 사용하여 하이브리드 ID 솔루션을 구현하는 것이 좋습니다. 자세한 내용은 [하이브리드 ID 설명서](/azure/active-directory/hybrid/)를 참조하세요.
* ID 공급자가 다른 온-프레미스 솔루션(예: PingFederate)인 경우 [Azure AD Connect 설치 사용자 지정](/azure/active-directory/hybrid/how-to-connect-install-custom) 항목을 참조하여 Azure Active Directory와 페더레이션하도록 구성합니다. 또는 Spring Security를 사용하여 [OAuth2/OpenID Connect](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#oauth2) 또는 [SAML](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-saml2)을 통해 ID 공급자를 사용하는 것이 좋습니다.

### <a name="expose-the-application"></a>애플리케이션 공개

기본적으로 Azure Spring Cloud에 배포된 애플리케이션은 외부에서 볼 수 없습니다. 다음 명령을 사용하여 애플리케이션을 공개로 설정하면 애플리케이션을 공개할 수 있습니다.

```azurecli
az spring-cloud app update -n <application name> --is-public true
```

Spring Cloud 게이트웨이를 사용 중이거나 사용할 생각이라면 이 단계를 건너뜁니다(자세한 내용은 다음 섹션에서 설명).
