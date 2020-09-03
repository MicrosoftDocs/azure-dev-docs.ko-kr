---
author: yevster
ms.author: yebronsh
ms.date: 7/17/2020
ms.openlocfilehash: 1e7957a03cb96254a0318774a1620c7143ae6fc4
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89062065"
---
### <a name="migrate-all-certificates-to-keyvault"></a>모든 인증서를 KeyVault로 마이그레이션

Azure Spring Cloud는 JRE 키 저장소에 대한 액세스를 제공하지 않으므로 인증서를 Azure KeyVault로 마이그레이션하고 KeyVault의 인증서에 액세스하도록 애플리케이션 코드를 변경해야 합니다. 자세한 내용은 [Key Vault 인증서 시작](/azure/key-vault/certificates/certificate-scenarios) 및 [Java용 Azure Key Vault 인증서 클라이언트 라이브러리](/java/api/overview/azure/security-keyvault-certificates-readme)를 참조하세요.