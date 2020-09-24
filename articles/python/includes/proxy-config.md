---
ms.openlocfilehash: 9245a256aba53e5ffd891227e867a7fc363a3d33
ms.sourcegitcommit: 69933dcce571b2686897b295b7822e207d944617
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2020
ms.locfileid: "90772766"
---
> [!NOTE]
> **"이름이 [구독 ID]인 구독을 찾을 수 없습니다"** 오류가 표시되면 프록시를 사용하고 Azure API에 연결할 수 없기 때문일 수 있습니다. 터미널의 프록시 정보를 사용하여 `HTTP_PROXY` 및 `HTTPS_PROXY` 환경 변수를 구성합니다.
>
> ```cmd
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```

