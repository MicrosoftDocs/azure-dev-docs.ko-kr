---
ms.openlocfilehash: f644d66895b7b8bcce345cd42c6efedfaddb4240
ms.sourcegitcommit: 29b161c450479e5d264473482d31e8d3bf29c7c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91764468"
---
이 코드는 Azure CLI에서 직접 수행할 수 있는 작업을 보여주므로 CLI 기반 인증(`AzureCliCredential` 사용)을 사용합니다. 두 경우 모두 인증에 동일한 ID를 사용합니다.

프로덕션 스크립트에서 이러한 코드를 사용하려면(예: VM 관리를 자동화하려면) [Azure 서비스로 Python 앱을 인증하는 방법](../azure-sdk-authenticate.md)에 설명된 대로 `DefaultAzureCredential`(권장) 또는 서비스 주체 기반 메서드를 사용합니다.
