---
ms.openlocfilehash: 4215099ae39963448b7a94d389ded0c9096b1c67
ms.sourcegitcommit: 69933dcce571b2686897b295b7822e207d944617
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2020
ms.locfileid: "90772683"
---
[Azure Portal](https://portal.azure.com) 또는 Azure CLI를 사용하여 리소스 그룹을 삭제할 수 있습니다.

- 포털의 왼쪽 탐색 창에서 **리소스 그룹**을 선택하고 이 자습서의 프로세스에서 만든 리소스 그룹을 선택한 다음, **리소스 그룹 삭제** 명령을 사용합니다.

- 다음 Azure CLI 명령(로컬 또는 [Cloud Shell](/azure/cloud-shell/overview) 사용)을 실행하고 `<resource_group>`을 이 자습서에 사용된 그룹의 이름으로 바꿉니다.

    ```azurecli
    az group delete --no-wait --name <resource_group>
    ```
