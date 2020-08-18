---
title: WebLogic Server의 Java 앱을 Azure로 마이그레이션하기 위해 Azure Active Directory를 사용하여 최종 사용자 권한 부여 및 인증
description: 이 가이드에서는 LDAP를 통해 Azure Active Directory Domain Services에 연결하도록 Oracle WebLogic Server를 구성하는 방법을 설명합니다.
author: edburns
ms.author: edburns
ms.topic: tutorial
ms.date: 08/10/2020
ms.openlocfilehash: b828fc2bc41b0e4e557472e7efd00498e68933db
ms.sourcegitcommit: b923aee828cd4b309ef92fe1f8d8b3092b2ffc5a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88052220"
---
# <a name="end-user-authorization-and-authentication-for-migrating-java-apps-on-weblogic-server-to-azure"></a>WebLogic Server의 Java 앱을 Azure로 마이그레이션하기 위한 최종 사용자 권한 부여 및 인증

이 가이드는 Azure Active Directory를 사용하여 WebLogic Server에서 엔터프라이즈급 최종 사용자 인증 및 권한 부여를 Java 앱에 사용하도록 설정하는 데 도움이 됩니다.

Java EE 개발자는 워크로드를 Azure로 이동하는 경우에도 [표준 플랫폼 보안 메커니즘](https://javaee.github.io/tutorial/security-intro.html#BNBWJ)이 "틀림없이 작동"하는 것으로 간주합니다.  [Oracle WLS(WebLogic Server) Azure 애플리케이션](/azure/virtual-machines/workloads/oracle/oracle-weblogic)을 사용하면 Azure AD DS(Azure Active Directory Domain Services)의 사용자로 기본 제공 보안 영역을 채울 수 있습니다.  Azure 애플리케이션의 Java EE에서 표준 `<security-role>` 요소를 사용합니다. 사용자 정보는 Azure AD DS에서 LDAP(Lightweight Directory Access Protocol)를 통해 이동합니다.

이 가이드는 두 부분으로 나뉘어 있습니다.  보안 LDAP가 공개된 Azure AD DS가 이미 있는 경우 두 번째 부분으로 바로 건너뛸 수 있습니다.

* [Azure Active Directory 구성](#azure-active-directory-configuration)
* [WLS 구성](#wls-configuration)

이 가이드에서는 다음을 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Active Directory Domain Services 관리형 도메인 만들기 및 구성
> * Azure AD DS 관리형 도메인에 대한 보안 LDAP(Lightweight Directory Access Protocol) 구성
> * WebLogic Server에서 기본 보안 영역으로 LDAP에 액세스하도록 설정

이 가이드는 기존 Azure AD 배포를 다시 구성하는 데 도움이 되지 않지만, 이 가이드와 함께 수행하고 건너뛸 수 있는 단계를 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성화된 Azure 구독.
  * Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/).
* [Oracle WebLogic Server Azure 애플리케이션](/azure/virtual-machines/workloads/oracle/oracle-weblogic)에 나와 있는 WLS Azure 애플리케이션 중 하나를 배포하는 기능

## <a name="migration-context"></a>마이그레이션 컨텍스트

온-프레미스 WLS 설치 및 Azure AD 마이그레이션에 대해 고려해야 할 몇 가지 사항은 다음과 같습니다.

* LDAP를 통해 Domain Services를 공개하지 않은 Azure AD 테넌트가 이미 있는 경우 이 가이드에서는 LDAP 기능을 공개하고 WLS와 통합하는 방법을 보여 줍니다.
* 온-프레미스 Active Directory 포리스트와 관련된 시나리오인 경우 Azure AD를 사용하여 하이브리드 ID 솔루션을 구현하는 것이 좋습니다.  자세한 내용은 [하이브리드 ID 설명서](/azure/active-directory/hybrid/)를 참조하세요.
* 온-프레미스 AD DS(Active Directory Domain Services) 배포가 이미 있는 경우 [자체 관리형 Active Directory Domain Services, Azure Active Directory 및 관리형 Azure Active Directory Domain Services 비교](/azure/active-directory-domain-services/compare-identity-solutions)를 방문하여 마이그레이션 경로를 살펴봅니다.
* 클라우드에 맞게 최적화하는 경우 이 가이드에서는 Azure AD DS LDAP 및 WLS를 사용하여 처음부터 시작하는 방법을 보여 줍니다.
* WebLogic Server를 Azure Virtual Machines로 마이그레이션하는 과정에 대한 포괄적인 설문 조사는 [WebLogic Server 애플리케이션을 Azure Virtual Machines로 마이그레이션](migrate-weblogic-to-virtual-machines.md)을 참조하세요.

## <a name="azure-active-directory-configuration"></a>Azure Active Directory 구성

이 섹션에서는 WLS와 통합된 Azure AD DS 인스턴스를 구성하는 모든 단계를 안내합니다.  Azure Active Directory는 LDAP(Lightweight Directory Access Protocol) 프로토콜 또는 보안 LDAP를 직접 지원하지 않습니다. 대신 Azure AD 테넌트 내에서 Azure AD DS(Azure Active Directory Domain Services) 인스턴스를 통해 지원을 사용하도록 설정됩니다.

>[!NOTE]
> 이 가이드에서는 Azure AD DS의 "클라우드 전용" 사용자 계정 기능을 사용합니다.  다른 사용자 계정 유형도 지원되지만 이 가이드에서 설명하지는 않습니다.

### <a name="create-and-configure-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services 관리형 도메인 만들기 및 구성

이 섹션에서는 Azure AD DS 관리형 도메인을 구성하는 별도의 자습서를 안내합니다.

[Azure Active Directory Domain Services 관리형 도메인 만들기 및 구성](/azure/active-directory-domain-services/tutorial-create-instance) 자습서를 완료하지만, [Azure AD DS에서 사용자 계정을 사용하도록 설정](/azure/active-directory-domain-services/tutorial-create-instance#enable-user-accounts-for-azure-ad-ds) 섹션은 포함되지 않습니다.  이 섹션에서는 다음 섹션에서 설명한 대로 이 자습서의 컨텍스트에서 특별한 처리가 필요합니다.  DNS 작업을 완전히 정상적으로 완료해야 합니다.

"관리되는 도메인에 대한 DNS 도메인 이름 입력" 단계를 완료할 때 지정한 값을 적어 둡니다.  이 문서의 뒷부분에서 사용합니다.

### <a name="create-users-and-reset-passwords"></a>사용자 만들기 및 암호 다시 설정

이 섹션에는 사용자를 만들고, 이 사용자가 LDAP를 통해 성공적으로 전파되는 데 필요한 해당 암호를 변경하는 단계가 포함되어 있습니다.  기존 Azure AD DS 설치가 있는 경우 이 단계가 필요하지 않을 수 있습니다.

1. Azure Portal 내에서 Azure AD 테넌트에 해당하는 디렉터리가 현재 활성 디렉터리인지 확인합니다.  올바른 디렉터리를 선택하는 방법에 대한 자세한 내용은 [Azure Active Directory 테넌트에 Azure 구독 연결 또는 추가](/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory)를 참조하세요.  잘못된 디렉터리를 선택하면 사용자를 만들 수 없거나 잘못된 디렉터리에 사용자를 만들 수 있습니다.
1. Azure Portal 위쪽의 검색 상자에서 "사용자"를 입력합니다.
1. **새 사용자**를 선택합니다.
1. **사용자 만들기**가 선택되어 있는지 확인합니다.
1. 사용자 이름, 이름, 이름 및 성에 대한 값을 입력합니다.  나머지 필드는 기본값으로 둡니다.
1. **만들기**를 선택합니다.
1. 테이블에서 새로 만든 사용자를 선택합니다.
1. **암호 다시 설정**을 선택합니다.
1. 표시되는 패널에서 **암호 다시 설정**을 선택합니다.
1. 임시 암호를 적어둡니다.
1. "incognito" 브라우저 창에서 [Azure Portal](https://portal.azure.com/)을 방문하여 사용자의 자격 증명과 암호를 사용하여 로그인합니다.
1. 메시지가 표시되면 암호를 변경합니다.  새 암호를 적어 둡니다.  이는 나중에 사용합니다.
1. 로그아웃하고 "incognito" 창을 닫습니다.

사용하도록 설정하려는 각 사용자에 대해 "**새 사용자 선택**"에서 "로그아웃 후 닫기"까지의 단계를 반복합니다.

### <a name="allow-ldap-in-azure-ad-ds"></a>Azure AD DS에서 LDAP 허용

이 섹션에서는 WLS를 구성하는 데 사용할 값을 추출하는 별도의 자습서를 안내합니다.

먼저 자습서를 실행할 때 아래와 같은 변형을 볼 수 있도록 별도의 브라우저 창에서 [Azure Active Directory Domain Services 관리형 도메인에 대한 보안 LDAP 구성](/azure/active-directory-domain-services/tutorial-configure-ldaps) 자습서를 엽니다.  

[클라이언트 컴퓨터에 대한 인증서 내보내기](/azure/active-directory-domain-services/tutorial-configure-ldaps#export-a-certificate-for-client-computers) 섹션에 도달하면 *.cer*로 끝나는 인증서 파일을 저장하는 위치를 적어 둡니다.  이 인증서는 WLS 구성에 대한 입력으로 사용됩니다.

[인터넷을 통한 보안 LDAP 액세스 잠금](/azure/active-directory-domain-services/tutorial-configure-ldaps#lock-down-secure-ldap-access-over-the-internet) 섹션에 도달하면 **모두**를 원본으로 지정합니다.  이 가이드의 뒷부분에서 특정 IP 주소를 사용하여 보안 규칙을 강화합니다.

[관리되는 도메인에 대한 쿼리 테스트](/azure/active-directory-domain-services/tutorial-configure-ldaps#test-queries-to-the-managed-domain)의 단계를 실행하기 전에 다음 단계를 수행하여 테스트가 성공할 수 있도록 합니다.

   1. 포털에서 Azure AD Domain Services 인스턴스에 대한 개요 페이지를 방문합니다.
   1. [설정] 영역에서 **속성**을 선택합니다.
   1. 페이지 오른쪽에서 **관리자 그룹**이 표시될 때까지 아래로 스크롤합니다.  이 제목 아래에는 **AAD DC 관리자**에 대한 링크가 있습니다.  해당 링크를 선택합니다.
   1. **관리 섹션**에서 **멤버**를 선택합니다.
   1. **구성원 추가**를 선택합니다.
   1. **검색** 텍스트 필드에서 일부 문자를 입력하여 이전 단계에서 만든 사용자 중 하나를 찾습니다.
   1. 사용자를 선택한 다음, **선택** 단추를 활성화합니다.
   1. 이 사용자는 **관리되는 도메인에 대한 쿼리 테스트** 섹션의 단계를 실행할 때 사용해야 합니다.
   >[!NOTE]
   >
   > WLS 구성에 필요한 일부 값을 수집하기 위해 수행해야 하는 LDAP 데이터 쿼리에 대한 몇 가지 팁은 다음과 같습니다.
   >
   > * 이 자습서에서는 *LDP.exe* Windows 프로그램을 사용하는 것이 좋습니다.  동일한 목적으로 [Apache Directory Studio](https://directory.apache.org/studio/downloads.html)를 사용할 수도 있습니다.
   > * *LDP.exe*를 사용하여 LDAP에 로그인하는 경우 사용자 이름은 단지 @ 앞에 있는 부분입니다.  예를 들어 사용자가 `alice@contoso.onmicrosoft.com`인 경우 *LDP.exe* 바인딩 작업에 대한 사용자 이름은 `alice`입니다.  또한 이후 단계에서 사용하기 위해 *LDP.exe*를 실행하여 로그인한 상태로 둡니다.
   >
[외부 액세스를 위한 DNS 영역 구성](/azure/active-directory-domain-services/tutorial-configure-ldaps#configure-dns-zone-for-external-access) 섹션에서 **보안 LDAP 외부 IP 주소**에 대한 값을 적어 둡니다.  이는 나중에 사용합니다.

이 가이드에서 수행하도록 지시할 때까지 [리소스 정리](/azure/active-directory-domain-services/tutorial-configure-ldaps#clean-up-resources)의 단계를 실행하지 마세요.

위의 변형을 염두에 두고 [Azure Active Directory Domain Services 관리형 도메인에 대한 보안 LDAP 구성](/azure/active-directory-domain-services/tutorial-configure-ldaps)을 완료합니다.  이제 WLS 구성에 제공하는 데 필요한 값을 수집할 수 있습니다.

### <a name="disable-weak-tls-v1"></a>약한 TLS v1 사용 안 함

기본적으로 Azure AD DS(Azure Active Directory Domain Services)는 TLS v1을 사용하도록 설정합니다. 이는 약한 것으로 간주되며 WebLogic Server 14 이상에서 지원되지 않습니다. 

이 섹션에서는 TLS v1 암호화를 사용하지 않도록 설정하는 방법을 안내합니다.

먼저 LDAP를 사용하도록 설정하는 Azure Domain Service 인스턴스의 리소스 ID를 가져옵니다. 다음 예에서는 `aadds-rg`라는 리소스 그룹에서 `aaddscontoso.com`이라는 Azure Domain Service 인스턴스의 ID를 가져옵니다.

```azurecli
AADDS_ID=$(az resource show --resource-group aadds-rg --resource-type "Microsoft.AAD/DomainServices" --name aaddscontoso.com --query "id" --output tsv)
```

다음 명령을 실행하여 TLS v1을 사용하지 않도록 설정하세요.

```azurecli
az resource update --ids $AADDS_ID --set properties.domainSecuritySettings.tlsV1=Disabled
```

출력에는 다음 예와 같이 `domainSecuritySettings`가 `"tlsV1": "Disabled"`로 표시됩니다.

```text
"domainSecuritySettings": {
      "ntlmV1": "Enabled",
      "syncKerberosPasswords": "Enabled",
      "syncNtlmPasswords": "Enabled",
      "syncOnPremPasswords": "Enabled",
      "tlsV1": "Disabled"
}
```

자세한 내용은 [약한 암호화 및 암호 해시 동기화를 사용하지 않도록 설정하여 Azure Active Directory Domain Services 관리되는 도메인 보호](/azure/active-directory-domain-services/secure-your-domain)를 참조하세요.

## <a name="wls-configuration"></a>WLS 구성

이 섹션은 이전에 배포한 Azure AD DS에서 매개 변수 값을 수집하는 데 도움이 됩니다.

[Oracle WebLogic Server Azure 애플리케이션](/azure/virtual-machines/workloads/oracle/oracle-weblogic)에 나와 있는 Azure 애플리케이션을 배포할 때 배포가 기존 LDAP 서버에 자동으로 연결되도록 선택할 수 있습니다.  또는 나중에 Active Directory 통합 하위 템플릿을 호출하여 LDAP 연결을 구성할 수 있습니다.  이 방법은 [공식 설명서](https://wls-eng.github.io/arm-oraclelinux-wls/)의 부록 A에서 설명하고 있습니다.  어느 경우이든 ARM 템플릿에 전달하는 데 필요한 매개 변수 값이 있어야 합니다.

| 매개 변수 이름 | Description   | 세부 정보 | 
|----------------|---------------|---------|
| `aadsServerHost` | 서버 호스트 | 이 값은 [Azure Active Directory Domain Services 관리형 도메인 만들기 및 구성](/azure/active-directory-domain-services/tutorial-create-instance)을 완료할 때 저장한 공용 DNS 이름입니다. |
| `aadsPublicIP` | 보안 LDAP 외부 IP 주소 | 이 값은 [외부 액세스를 위한 DNS 영역 구성](/azure/active-directory-domain-services/tutorial-configure-ldaps#configure-dns-zone-for-external-access) 섹션에서 저장한 **보안 LDAP 외부 IP 주소**입니다.|
| `wlsLDAPPrincipal` | 주 서버   | *LDP.exe*로 돌아갑니다.  `wlsLDAPPrincipal`에 대한 추가 값을 얻으려면 다음 단계를 수행합니다. <ol><li>**보기** 메뉴에서 **트리**를 선택합니다.</li><li>**트리 보기** 대화 상자에서 **BaseDN**을 비워 두고 **확인**을 선택합니다.</li><li>오른쪽 창에서 마우스 오른쪽 단추를 클릭하고 **출력 지우기**를 선택합니다.</li><li>왼쪽의 트리 보기를 펼치고, "OU=AADDC 사용자"로 시작하는 항목을 선택합니다.</li><li>**찾아보기** 메뉴에서 **검색**을 선택합니다.</li><li>표시되는 대화 상자에서 기본값을 적용하고 **실행**을 선택합니다.</li><li>오른쪽 창에 출력이 표시되면 **실행** 옆에 있는 **닫기**를 선택합니다.</li><li>"AAD DC 관리자" 그룹에 추가한 사용자에 해당하는 **Dn** 항목에 대한 출력을 검사합니다.  **Dn: CN=&lt;사용자 이름&gt;OU=AADDC 사용자"** 로 시작합니다.</li></ol> |
| `wlsLDAPGroupBaseDN` 및 `wlsLDAPUserBaseDN` | 사용자 기본 DN 및 그룹 기본 DN | 이 자습서에서는 이러한 두 속성의 값이 모두 동일합니다(첫 번째 쉼표 뒤의 **wlsLDAPPrincipal** 부분).|
| `wlsLDAPPrincipalPassword` | 보안 주체 암호 | 이 값은 **AAD DC 관리자** 그룹에 추가된 사용자에 대한 암호입니다. |
| `wlsLDAPProviderName` | Provider Name | 이 값은 기본값으로 둘 수 있습니다.  WLS에서 인증 공급자의 이름으로 사용됩니다. |
| `wlsLDAPSSLCertificate` | SSL 구성에 대한 신뢰 키 저장소 | 이 값은 [클라이언트 컴퓨터에 대한 인증서 내보내기](/azure/active-directory-domain-services/tutorial-configure-ldaps#export-a-certificate-for-client-computers) 단계를 완료할 때 별도로 저장하도록 요청받은 Base 64로 인코딩된 *.cer* 파일입니다.  이 값은 다음 UNIX 또는 PowerShell 명령을 사용하여 가져올 수 있습니다. <br /> bash: <br /> `base64 your-certificate.cer -w 0 >temp.txt` <br /> PowerShell: <br /> `$Content = Get-Content -Path .\your-certificate.cer -Encoding Byte`<br /> `$Base64 = [System.Convert]::ToBase64String($Content)` <br /> `$Base64 | Out-File .\temp.txt`

### <a name="integrating-azure-ad-ds-ldap-with-wls"></a>WLS와 Azure AD DS LDAP 통합

위의 구성 값을 직접 사용하고 Azure AD DS LDAP가 배포되면 이제 구성을 시작할 수 있습니다.  이 프로세스를 완료하는 두 가지 방법이 있습니다.

#### <a name="during-wls-deployment"></a>WLS 배포 중

[Oracle WebLogic Server Azure 애플리케이션](/azure/virtual-machines/workloads/oracle/oracle-weblogic)을 방문하여 관리자 또는 클러스터 제안 중 하나를 선택합니다.  제안을 배포하는 동안 배포 프로세스의 탭 중 하나는 **Azure Active Directory**입니다.  **Azure Active Directory에 연결**을 **예**로 전환합니다.  이전 섹션에서 수집한 정보를 사용하여 값을 채웁니다.  인증서의 경우 `.cer` 파일을 직접 업로드해야 합니다.

#### <a name="after-wls-deployment"></a>WLS 배포 후

배포 시 **Azure Active Directory에 연결**을 **예**로 전환하지 않은 경우 나중에 이전 섹션에서 수집한 값을 사용하여 구성을 수행할 수 있습니다.  자세한 내용은 [공식 설명서](https://wls-eng.github.io/arm-oraclelinux-wls/)에 나와 있습니다.

### <a name="validate-the-deployment"></a>배포 유효성 검사

위의 두 가지 방법 중 하나를 사용하여 WLS가 배포되고 LDAP가 구성되면 다음 단계에 따라 통합이 성공적으로 수행되었는지 확인합니다.

1. WLS 관리 콘솔을 방문합니다.
1. 왼쪽 탐색기에서 트리를 펼쳐 **보안 영역** -> **myrealm** -> **공급자**를 차례로 선택합니다.
1. 성공적으로 통합되면 `AzureActiveDirectoryProvider`와 같은 Azure AD 공급자를 찾을 수 있습니다.
1. 왼쪽 탐색기에서 트리를 펼쳐 **보안 영역** -> **myrealm** -> **사용자 및 그룹**을 차례로 선택합니다.
1. 성공적으로 통합되면 Azure AD 공급자의 사용자를 찾을 수 있습니다.

### <a name="lock-down-and-secure-ldap-access-over-the-internet"></a>인터넷을 통한 보안 LDAP 액세스 잠금

이전 단계에서 보안 LDAP를 구성하는 동안 네트워크 보안 그룹의 `AllowLDAPS` 규칙에 대해 원본을 **모두**로 설정했습니다.  이제 WLS 관리 서버가 배포되어 LDAP에 연결되었으므로 Azure Portal을 사용하여 공용 IP 주소를 가져옵니다.  [인터넷을 통한 보안 LDAP 액세스 잠금](/azure/active-directory-domain-services/tutorial-configure-ldaps?branch=pr-en-us-778#lock-down-secure-ldap-access-over-the-internet)을 다시 방문하여 **모두**를 WLS 관리 서버의 특정 IP 주소로 변경합니다.

## <a name="clean-up-resources"></a>리소스 정리

이제 [Azure Active Directory Domain Services 관리형 도메인에 대한 보안 LDAP 구성](/azure/active-directory-domain-services/tutorial-configure-ldaps#clean-up-resources)에 있는 [리소스 정리](/azure/active-directory-domain-services/tutorial-configure-ldaps#clean-up-resources) 섹션의 단계를 수행합니다.

## <a name="next-steps"></a>다음 단계

WebLogic Server 앱을 Azure로 마이그레이션하는 과정의 측면을 살펴보세요.

> [!div class="nextstepaction"]
> [WebLogic 애플리케이션을 Azure Virtual Machines로 마이그레이션](/azure/developer/java/migration/migrate-weblogic-to-virtual-machines)
