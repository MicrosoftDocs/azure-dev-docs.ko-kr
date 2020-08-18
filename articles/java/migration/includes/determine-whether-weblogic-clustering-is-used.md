---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: f50115be65be8e746527b3d4ee833a738109ddbc
ms.sourcegitcommit: b923aee828cd4b309ef92fe1f8d8b3092b2ffc5a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88052264"
---
### <a name="determine-whether-weblogic-clustering-is-used"></a>WebLogic 클러스터링이 사용되는지 확인

아마도 많은 분들이 고가용성을 얻기 위해 여러 WebLogic 서버에 애플리케이션을 배포하셨을 것입니다. 이러한 클러스터를 온-프레미스 설치에서, Azure Virtual Machines에서 실행되는 WebLogic으로 직접 마이그레이션할 수 있습니다. 자세한 내용은 Oracle 설명서의 [도메인 구성 파일](https://docs.oracle.com/middleware/12213/wls/DOMCF/config_files.htm#DOMCF127)을 참조하세요.

### <a name="account-for-load-balancing-requirements"></a>부하 분산 요구 사항 고려

부하 분산은 Oracle WebLogic Server 클러스터를 Azure로 마이그레이션할 때 필수적인 과정입니다.  가장 쉬운 방법은 Oracle WebLogic Server 클러스터용 Azure Marketplace 솔루션에 제공되는 [Azure Application Gateway](/azure/application-gateway/overview)에 대한 기본 제공 지원을 사용하는 것입니다.  이 항목에 대한 자습서는 [자습서: Azure Application Gateway를 부하 분산 장치로 사용하여 WebLogic Server 클러스터를 Azure로 마이그레이션](../migrate-weblogic-with-app-gateway.md)을 참조하세요.

Azure Application Gateway의 기능을 다른 Azure 부하 분산 솔루션과 비교한 요약 정보는 [Azure의 부하 분산 옵션 개요](/azure/architecture/guide/technology-choices/load-balancing-overview)를 참조하세요.
