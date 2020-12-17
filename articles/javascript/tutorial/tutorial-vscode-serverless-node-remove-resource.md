---
title: Azure Functions 애플리케이션을 배포한 후에는 많은 비용이 드는 원격 Azure 리소스 삭제
description: 더 이상 비용이 발생하지 않도록 원격 Azure 리소스를 제거(정리)합니다. 리소스를 정리하려면 Azure Functions 탐색기에서 함수 앱을 마우스 오른쪽 단추로 클릭하고 **함수 앱 삭제** 를 선택합니다.
ms.topic: tutorial
ms.date: 08/31/2020
ms.custom: devx-track-js, contperf-fy21q2
ms.openlocfilehash: 428330b34b3c315d01c2209840de62001caa747a
ms.sourcegitcommit: c8330128d5d6a71859933a890ecdf047cb950996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97522051"
---
# <a name="5-clean-up-azure-resources-for-azure-functions-tutorial"></a>5. Azure Functions 자습서에 사용되는 Azure 리소스 정리

[이전 단계: Functions 앱 배포](tutorial-vscode-serverless-node-deploy-hosting.md)

## <a name="remove-remote-azure-resources"></a>원격 Azure 리소스 제거

사용자가 만든 Functions 앱에는 최소한의 비용을 발생시킬 수 있는 리소스가 포함되어 있습니다([Functions 가격 책정](https://azure.microsoft.com/pricing/details/functions/) 참조). 리소스를 정리하려면 **Azure: Functions** 탐색기에서 함수 앱을 마우스 오른쪽으로 클릭하고 **함수 앱 삭제** 를 선택합니다.

또한 [Azure Portal](https://portal.azure.com)을 방문하여 왼쪽 탐색 창에서 **리소스 그룹** 을 선택하고, 이 자습서의 프로세스에서 만든 리소스 그룹을 선택한 다음, **리소스 그룹 삭제** 명령을 사용합니다.

[!INCLUDE [Next steps for using VSCode extensions](../includes/tutorial-next-steps-vscode-extensions.md)]

[!INCLUDE [Next steps for using JavaScript on Azure](../includes/tutorial-next-steps-js-azure.md)]

## <a name="learn-more-about-azure-functions"></a>Azure Functions에 대한 자세한 정보

* [Azure Functions 개발자 가이드](/azure/azure-functions/functions-reference)
* [Azure Functions JavaScript 개발자 가이드](/azure/azure-functions/functions-reference-node)
* [Azure Functions 보안 설정](/azure/azure-functions/security-concepts)
* [스토리지](/azure/azure-functions/storage-considerations) 및 [성능](/azure/azure-functions/functions-best-practices) 고려 사항

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [완료했습니다.](../how-to/develop-serverless-apps.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=clean-up-resources)
