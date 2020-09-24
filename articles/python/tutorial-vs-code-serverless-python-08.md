---
title: '8단계: Azure Functions에서 Python 코드에 사용된 리소스 정리'
description: 자습서 8단계, 지속적인 변경을 방지하기 위해 Azure 리소스 정리
ms.topic: conceptual
ms.date: 09/17/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 885ff8b79ac219d4adaf4760e31fe439d010f9d5
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90832009"
---
# <a name="8-clean-up-azure-resources-for-azure-functions"></a>8: Azure Functions에 대한 Azure 리소스 정리

[이전 단계: 스토리지 바인딩 추가](tutorial-vs-code-serverless-python-07.md)

이 문서에서는 이 자습서에서 만든 Azure 리소스를 제거하는 방법을 보여줍니다. 사용자가 Visual Studio Code로 만든 Azure 함수 앱에는 최소한의 비용을 발생시킬 수 있는 리소스가 포함되어 있습니다. (자세한 내용은 [Functions 가격 책정](https://azure.microsoft.com/pricing/details/functions/)을 참조하세요.)

리소스를 정리하는 가장 좋은 방법은 이 자습서에서 사용되는 모든 개별 리소스가 포함된 리소스 그룹을 삭제하는 것입니다. 리소스에는 함수 앱, 스토리지 계정 및 지원 App Service 계획이 포함됩니다.

[!INCLUDE [delete-resource-group](includes/delete-resource-group.md)]

Visual Studio Code에서 **Azure: Functions** 탐색기의 함수 앱에 대한 컨텍스트 메뉴에 **함수 앱 삭제** 명령이 있음을 알 수 있습니다. 그러나 이 명령은 함수 앱만 삭제하고 다른 리소스는 그대로 유지하여 지속적인 비용이 발생될 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Functions에 Python 코드를 배포하는 연습을 완료했습니다. 이제 더 많은 서버리스 함수를 만들 준비가 되었습니다.

앞에서 설명한 것처럼 해당 GitHub 리포지토리 [vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions)를 방문하여 함수 확장에 대해 자세히 알아볼 수 있습니다. 문제 및 기여도 환영합니다.

사용할 수 있는 다른 트리거를 살펴보려면 [Azure Functions 개요](/azure/azure-functions/functions-overview)를 참조하세요.

AI 및 Machine Learning Service와 함께 데이터 스토리지를 포함하여 Python에서 사용할 수 있는 Azure 서비스에 대해 자세히 알아보려면 [Azure Python 개발자 센터](./index.yml)를 참조하세요.

도움이 될 수 있는 Visual Studio Code에 대한 다른 Azure 확장도 있습니다. 확장 탐색기에서 "Azure"를 검색하기만 하면 됩니다.

![Visual Studio Code용 Azure 확장](media/tutorial-vs-code-serverless-python/azure-extensions-for-visual-studio-code.png)

몇 가지 인기 있는 확장은 다음과 같습니다.

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). [App Service에 배포 자습서](tutorial-deploy-app-service-on-linux-01.md) 참조
- [Azure CLI 도구](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Azure Resource Manager 도구](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

> [!div class="nextstepaction"]
> [완료했습니다!](/python/azure/?preserve-view=true&view=azure-python)