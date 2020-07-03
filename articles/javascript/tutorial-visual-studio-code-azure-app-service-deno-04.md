---
title: Azure App Service에 배포 후 리소스 정리
description: 자습서 4부, 리소스 정리
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 1f452b054aa41c714105e79bd3dc971c88acd260
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85792473"
---
# <a name="clean-up"></a>정리

[이전 단계: 앱 배포](tutorial-visual-studio-code-azure-app-service-deno-03.md)

이 섹션에서는 생성된 모든 리소스를 제거하고 정리합니다.

## <a name="remove-your-resources"></a>리소스 제거

생성한 App Service에는 체험 가격 책정 계층에서 실행되는 지원 App Service 계획이 포함되므로 지속적인 비용은 발생하지 않습니다.

리소스를 정리하려면 [Azure Portal](https://portal.azure.com)을 방문하여 **리소스 그룹**을 선택하고 이 자습서의 프로세스에서 만든 리소스 그룹(예: `deno-quickstart`)을 찾아서 선택한 다음, **리소스 그룹 삭제** 명령을 사용합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [완료했습니다.](node-howto-deploy-web-app.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=clean-up-resources)
