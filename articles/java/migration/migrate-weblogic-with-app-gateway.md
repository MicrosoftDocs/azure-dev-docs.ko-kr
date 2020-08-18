---
title: Azure Application Gateway를 부하 분산 장치로 사용하여 WebLogic Server 클러스터를 Azure로 마이그레이션 자습서
description: 이 자습서에서는 Azure Application Gateway를 부하 분산 장치로 사용하여 WebLogic Server를 Azure로 배포하는 방법을 안내합니다.
author: edburns
ms.author: edburns
ms.topic: tutorial
ms.date: 08/05/2020
ms.openlocfilehash: 166e6f90218eb519242da0d89ae6146a2d589863
ms.sourcegitcommit: b923aee828cd4b309ef92fe1f8d8b3092b2ffc5a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057548"
---
# <a name="tutorial-migrate-a-weblogic-server-cluster-to-azure-with-azure-application-gateway-as-a-load-balancer"></a>자습서: Azure Application Gateway를 부하 분산 장치로 사용하여 WebLogic Server 클러스터를 Azure로 마이그레이션

이 자습서에서는 Azure Application Gateway로 WebLogic Server(WLS)를 배포하는 과정을 안내합니다.  Key Vault를 만들고, Key Vault에 SSL 인증서를 저장하고, SSL 종료를 위해 해당 인증서를 사용하는 구체적인 단계를 설명합니다.  이러한 요소는 모두 각각 적절히 문서화되어 있지만 이 자습서에서는 이러한 모든 요소를 결합하여 Azure 기반 WLS에 대한 간단하면서도 강력한 부하 분산 솔루션을 만드는 구체적인 방법을 보여 줍니다.

:::image type="content" border="false" source="media/migrate-weblogic-with-app-gateway/weblogic-app-gateway-key-vault.png" alt-text="WLS, App Gateway 및 Key Vault 간의 관계를 보여 주는 다이어그램":::

부하 분산은 Oracle WebLogic Server 클러스터를 Azure로 마이그레이션할 때 필수적인 과정입니다.  가장 쉬운 방법은 [Azure Application Gateway](/azure/application-gateway/overview)에서 기본 제공하는 지원을 사용하는 것입니다.  App Gateway는 Azure 기반 WebLogic Cluster 지원의 일부로 포함됩니다.  Azure 기반 WebLogic Cluster 지원에 대한 개요는 [Azure 기반 Oracle WebLogic Server란?](/azure/virtual-machines/workloads/oracle/oracle-weblogic)을 참조하세요.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * Azure Key Vault 만들기
> * SSL 인증서 만들기
> * Key Vault에 SSL 인증서 저장
> * Azure Application Gateway를 사용하여 WebLogic Server를 Azure에 배포
> * WLS 및 App Gateway의 성공적인 배포 확인

## <a name="prerequisites"></a>전제 조건

* UNIX와 비슷한 명령줄 환경을 실행하는 컴퓨터의 [OpenSSL](https://www.openssl.org/)

   인증서 관리에 사용할 수 있는 다른 도구가 있을 수 있지만 이 자습서에서는 OpenSSL을 사용합니다. Ubuntu와 같은 많은 GNU/Linux 배포판에 번들로 제공되는 OpenSSL을 찾을 수 있습니다.
* 활성화된 Azure 구독.
  * Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/).
* [Oracle WebLogic Server Azure 애플리케이션](/azure/virtual-machines/workloads/oracle/oracle-weblogic)에 나와 있는 WLS Azure 애플리케이션 중 하나를 배포하는 기능

## <a name="migration-context"></a>마이그레이션 컨텍스트

온-프레미스 WLS 설치 및 Azure Application Gateway 마이그레이션 시 고려해야 할 몇 가지 사항은 다음과 같습니다.  이 자습서에서는 Azure 기반 WebLogic Server 클러스터 앞에 부하 분산 장치를 구성하는 가장 쉬운 방법을 설명하지만 이외에도 여러 가지 방법이 있습니다.  이 목록에는 몇 가지 다른 고려 사항이 나와 있습니다.

* 기존 부하 분산 솔루션이 있는 경우 Azure Application Gateway가 해당 기능을 포함하거나 초과하는지 확인하세요.  Azure Application Gateway의 기능을 다른 Azure 부하 분산 솔루션과 비교한 요약 정보는 [Azure의 부하 분산 옵션 개요](/azure/architecture/guide/technology-choices/load-balancing-overview)를 참조하세요.
* 기존 부하 분산 솔루션에서 일반적인 악용 및 취약점에 대한 보안을 제공한다면 Application Gateway로 충분히 대체할 수 있습니다. Application Gateway에서 기본 제공하는 WAF(Web Application Firewall)는 [OWASP(Open Web Application Security Project) 코어 규칙 집합](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)을 구현합니다.  Application Gateway의 WAF 지원에 대한 자세한 내용은 [Application Gateway 기능](/azure/application-gateway/features#web-application-firewall)을 참조하세요.
* 기존 부하 분산 솔루션에 엔드투엔드 SSL 암호화가 필요한 경우 이 가이드의 단계를 진행한 후 추가 구성을 수행해야 합니다.  [Application Gateway를 사용한 TLS 종료 및 엔드투엔드 TLS 개요](/azure/application-gateway/ssl-overview#end-to-end-tls-encryption) 및 [Oracle Fusion 미들웨어에서 SSL 구성](https://docs.oracle.com/en/middleware/fusion-middleware/12.2.1.3/asadm/configuring-ssl1.html#GUID-623906C0-B1FD-423F-AE51-061B5800E927)에 대한 Oracle 설명서를 참조하세요.
* 클라우드에 맞게 최적화하는 경우 이 가이드에서는 Azure App Gateway 및 WLS를 사용하여 처음부터 시작하는 방법을 보여 줍니다.
* WebLogic Server를 Azure Virtual Machines로 마이그레이션하는 과정에 대한 포괄적인 설문 조사는 [WebLogic Server 애플리케이션을 Azure Virtual Machines로 마이그레이션](migrate-weblogic-to-virtual-machines.md)을 참조하세요.

## <a name="create-an-azure-key-vault"></a>Azure Key Vault 만들기

이 섹션에서는 Azure Portal을 사용하여 Azure Key Vault를 만드는 방법을 보여 줍니다.

1. Azure Portal 메뉴 또는 **홈**페이지에서 **리소스 만들기**를 선택합니다.
1. 검색 상자에 **Key Vault**를 입력합니다.
1. 결과 목록에서 **Key Vault**를 선택합니다.
1. Key Vault 섹션에서 **만들기**를 선택합니다.
1. **Key Vault 만들기** 섹션에서 다음 정보를 제공합니다.
    * **구독**: 구독을 선택합니다.
    * **리소스 그룹**에서 **새로 만들기**를 선택하고 리소스 그룹 이름을 입력합니다.  키 자격 증명 모음 이름을 적어 둡니다.  *나중에 WLS를 배포할 때 필요합니다.*
    * **Key Vault 이름**: 고유 이름은 필수입니다.  키 자격 증명 모음 이름을 적어 둡니다.  *나중에 WLS를 배포할 때 필요합니다.*
    > [!NOTE]
    > **리소스 그룹**과 **키 자격 증명 모음 이름** 모두에 동일한 이름을 사용할 수 있습니다.
    * **위치** 풀 다운 메뉴에서 위치를 선택합니다.
    * 다른 옵션은 기본값으로 그대로 둡니다.
1. 완료되면 **다음: 액세스 정책**을 선택합니다.
1. **액세스 허용 대상**에서 **Azure Resource Manager에서 템플릿 배포**를 선택합니다.
1. **검토 + 만들기**를 선택합니다.
1. **만들기**를 선택합니다.

키 자격 증명 모음 만들기는 매우 간단합니다. 일반적으로 2분 이내에 완료됩니다.  배포가 완료되면 **리소스로 이동**을 선택하고 다음 섹션을 진행합니다.

## <a name="create-an-ssl-certificate"></a>SSL 인증서 만들기

이 섹션에서는 WebLogic을 사용하여 Azure에 배포된 Application Gateway에서 사용하기에 적합한 형식으로 자체 서명 SSL 인증서를 만드는 방법을 보여 줍니다.  인증서 암호는 비어 있지 않아야 합니다.  암호가 비어 있지 않은 *.pfx* 형식의 유효한 SSL 인증서가 이미 있는 경우 이 섹션을 건너뛰고 다음 단계로 이동할 수 있습니다.  암호가 비어 있지 않은 기존의 유효한 SSL 인증서가 *.pfx* 형식이 아닌 경우 먼저 *.pfx* 파일로 변환한 후에 다음 섹션으로 건너뛰세요.  그렇지 않으면 명령 셸을 열고 다음 명령을 입력합니다.

> [!NOTE]
> 이 섹션에서는 인증서를 Base64로 인코딩한 후 Key Vault에 비밀로 저장하는 방법을 보여 줍니다.  이는 WebLogic Server 및 Application Gateway를 만드는 기본 Azure 배포에 필요합니다.

다음 단계를 수행하여 인증서를 만들고 Base64로 인코딩하세요.

1. `RSA PRIVATE KEY` 만들기

   ```bash
   openssl genrsa 2048 > private.pem
   ```
1. 해당 공개 키를 만듭니다.

   ```bash
   openssl req -x509 -new -key private.pem -out public.pem
   ```

   OpenSSL 도구에서 메시지가 표시되면 몇 가지 질문에 답변해야 합니다.  이러한 값은 인증서에 포함됩니다.  이 자습서에서는 자체 서명 인증서를 사용하므로 이 값은 관련이 없습니다.  다음 리터럴 값은 괜찮습니다.
     1. **국가 이름**에 2자 코드를 입력합니다.
     1. **시/도 이름**에 WA를 입력합니다.
     1. **조직 이름**에 Contoso를 입력합니다.  조직 구성 단위 이름에 billing을 입력합니다.
     1. **일반 이름**에 Contoso를 입력합니다.
     1. **이메일 주소**에 billing@contoso.com을 입력합니다.

1. 인증서를 *.pfx* 파일로 내보냅니다.

   ```bash
   openssl pkcs12 -export -in public.pem -inkey private.pem -out mycert.pfx
   ```

   암호를 두 번 입력합니다.  암호를 기록해 둡니다.  *나중에 WLS를 배포할 때 필요합니다.*

1. *mycert.pfx* 파일을 Base64로 인코딩

   ```bash
   base64 mycert.pfx > mycert.txt
   ```

Key Vault를 만들었고 암호가 비어 있지 않은 유효한 SSL 인증서가 있으므로 Key Vault에 인증서를 저장할 수 있습니다.

## <a name="store-the-ssl-certificate-in-the-key-vault"></a>Key Vault에 SSL 인증서 저장

이 섹션에서는 이전 섹션에서 만든 Key Vault에 인증서와 해당 암호를 저장하는 방법을 보여 줍니다.

인증서를 저장하려면 다음 단계를 따르세요.

1. Azure Portal에서 페이지 맨 위에 있는 검색 창에 커서를 놓고 이 자습서의 앞 부분에서 만든 Key Vault의 이름을 입력합니다.
1. **리소스** 제목 아래에 Key Vault가 나타납니다.  이 폴더를 선택합니다.
1. **설정** 섹션에서 **비밀**을 선택합니다.
1. **생성/가져오기**를 선택합니다.
1. **업로드 옵션**에서 기본값을 그대로 둡니다.
1. **이름**에서 `myCertSecretData` 또는 원하는 이름을 입력합니다.
1. **값**에서 *mycert.txt* 파일의 내용을 입력합니다.  텍스트 필드에서 값의 길이와 줄바꿈 여부는 문제가 되지 않습니다.
1. 나머지 값은 기본값으로 두고 **만들기**를 선택합니다.

인증서의 암호를 저장하려면 다음 단계를 따르세요.

1. **비밀** 페이지로 돌아갑니다.  **생성/가져오기**를 선택합니다.
1. **업로드 옵션**에서 기본값을 그대로 둡니다.
1. **이름**에서 `myCertSecretPassword` 또는 원하는 이름을 입력합니다.
1. **값**에서 인증서에 사용할 암호를 입력합니다.
1. 나머지 값은 기본값으로 두고 **만들기**를 선택합니다.
1. **비밀** 페이지로 돌아갑니다.

## <a name="deploy-weblogic-server-with-application-gateway-to-azure"></a>Application Gateway를 사용하여 WebLogic Server를 Azure에 배포

이 섹션에서는 이전 섹션에서 만든 Key Vault, SSL 인증서 및 해당 암호를 사용하는 방법을 보여 줍니다.  클러스터 노드의 부하 분산 장치로 자동 생성된 Azure Application Gateway를 사용하여 WLS 클러스터를 프로비저닝합니다.  Application Gateway는 제공된 SSL 인증서를 SSL 종료에 사용합니다.  Application Gateway를 사용한 SSL 종료에 대한 더 자세한 내용은 [Application Gateway를 사용한 TLS 종료 및 엔드투엔드 TLS 개요](/azure/application-gateway/ssl-overview)를 참조하세요.

WLS 클러스터 및 Application Gateway를 만들려면 다음 단계를 수행합니다.

1. [Oracle 설명서](https://aka.ms/arm-oraclelinux-wls-cluster-oracle-docs)에 설명된 대로 WebLogic Server 클러스터를 프로비저닝하는 단계를 시작합니다. 하지만 여기에 표시된 **Azure Application Gateway** 블레이드에 도달하면 이 페이지로 돌아갑니다.

   :::image type="content" source="media/migrate-weblogic-with-app-gateway/weblogic-app-gateway-blade.png" alt-text="WLS, App Gateway 및 Key Vault 간의 관계를 보여 주는 다이어그램":::

1. **Azure Application Gateway** 블레이드에서 **예**를 선택합니다.
1. **KeyVault를 포함하는 현재 구독의 리소스 그룹 이름** 아래에서 앞서 만든 Key Vault를 포함하는 리소스 그룹의 이름을 입력합니다.
1. **SSL 종료를 위한 인증서의 비밀을 포함하는 Azure KeyVault의 이름** 아래에서 Key Vault의 이름을 입력합니다.
1. **SSL 인증서 데이터가 값으로 지정된 해당 KeyVault의 비밀 이름** 아래에서 `myCertSecretData` 또는 이전에 입력한 이름을 입력합니다.
1. **SSL 인증서 암호가 값으로 지정된 해당 KeyVault의 비밀 이름** 아래에서 `myCertSecretData` 또는 이전에 입력한 이름을 입력합니다.
1. **검토 + 만들기**를 선택합니다.
1. **만들기**를 선택합니다.  그러면 Key Vault에서 인증서를 가져올 수 있으며 해당 암호가 Key Vault의 암호에 저장한 값과 일치하는지 유효성 검사가 수행됩니다.  이 유효성 검사 단계가 실패하면 Key Vault의 속성을 검토하고 인증서가 올바르게 입력되었는지 확인한 후 암호를 올바르게 입력했는지 확인합니다.
1. **유효성 검사 통과**가 표시되면 **만들기**를 선택합니다.

그러면 WLS 클러스터 및 프런트 엔드 Application Gateway를 만드는 프로세스가 시작됩니다. 이 프로세스는 약 15분 정도 걸릴 수 있습니다.  배포가 완료되면 **리소스 그룹으로 이동**을 선택합니다. 리소스 그룹의 리소스 목록에서 **myAppGateway**를 선택합니다.

## <a name="validate-successful-deployment-of-wls-and-app-gateway"></a>WLS 및 App Gateway의 성공적인 배포 확인

이 섹션에서는 WLS 클러스터 및 Application Gateway의 성공적인 배포를 신속하게 확인하는 방법을 보여 줍니다.

이전 섹션의 끝 부분에서 **리소스 그룹으로 이동**, **myAppGateway**를 차례로 선택한 경우, Application Gateway에 대한 개요 페이지가 표시됩니다.  표시되지 않을 경우 Azure Portal 맨 위에 있는 텍스트 상자에 `myAppGateway`를 입력한 다음, 표시되는 올바른 항목을 선택하면 이 페이지를 찾을 수 있습니다.  WLS 클러스터에 대해 만든 리소스 그룹 내 항목을 선택해야 합니다.  그런 다음, 다음 단계를 완료합니다.

1. **myAppGateway**에 대한 개요 페이지의 왼쪽 창에서 아래의 **모니터링** 섹션으로 스크롤하고 **백 엔드 상태**를 선택합니다.
1. **로드 중** 메시지가 사라지면 화면 중간에 백 엔드 풀의 노드로 구성된 클러스터의 노드를 보여 주는 테이블이 표시됩니다.
1. 각 노드의 상태가 **정상**으로 표시되는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

WLS 클러스터를 계속 사용할 계획이 없으면 다음 단계에 따라 Key Vault 및 WLS 클러스터를 삭제합니다.

1. 이전 섹션에 나와 있는 것처럼 **myAppGateway** 개요 페이지를 방문하세요.
1. 페이지 상단의 **리소스 그룹** 텍스트 아래에서 리소스 그룹을 선택합니다.
1. **리소스 그룹 삭제**를 선택합니다.
1. **리소스 그룹 이름 입력** 레이블이 지정된 필드로 입력 포커스가 설정됩니다.  요청에 따라 리소스 그룹 이름을 입력합니다.
1. 이렇게 하면 **삭제** 단추가 활성화됩니다.  **삭제** 단추를 선택합니다.  이 작업은 약간의 시간이 걸리지만 삭제를 처리하는 동안 다음 단계를 진행할 수 있습니다.
1. [Key Vault에 SSL 인증서 저장]() 섹션의 첫 번째 단계에 따라 Key Vault를 찾습니다.
1. **삭제**를 선택합니다.
1. 표시되는 창에서 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure 기반 WLS를 실행하는 옵션을 계속 살펴봅니다.
> [!div class="nextstepaction"]
> [Azure 기반 Oracle WebLogic에 대한 자세한 정보](/azure/virtual-machines/workloads/oracle/oracle-weblogic)
