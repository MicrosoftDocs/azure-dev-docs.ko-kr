---
title: Azure CLI에서 앱을 호스트하는 Azure App Service 만들기
description: '자습서 3부: Azure CLI - App Service 만들기'
ms.topic: tutorial
ms.date: 12/18/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: abd434d2222e5bdd758be42856a569e24def08f8
ms.sourcegitcommit: 1c508f5ba73a12e4baeacc88ad9a8359301acb50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97687439"
---
# <a name="create-the-app-service"></a>App Service 만들기

[이전 단계: 앱 만들기](tutorial-vscode-azure-cli-node-02.md)

이 단계에서는 Azure CLI를 사용하여 앱 코드를 호스트하는 Azure App Service를 만듭니다.

1. 터미널 또는 명령 프롬프트에서 다음 명령을 사용하여 App Service에 대한 **리소스 그룹** 을 만듭니다. 리소스 그룹은 기본적으로 Azure에 있는 앱 리소스의 명명된 컬렉션(예: 웹 사이트, 데이터베이스, Azure Functions 등)입니다.

    ```azurecli
    az group create --name myResourceGroup --location westus
    ```

    위의 Azure CLI 명령([`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create))은 `westus` 데이터 센터에 `myResourceGroup`이라는 리소스 그룹을 만듭니다. 이 값은 원하는 대로 변경할 수 있습니다.

    명령이 성공적으로 실행되면 리소스 그룹의 세부 사항과 함께 JSON 출력이 표시됩니다.

1. 다음 Azure CLI 명령([`az configure`](/cli/azure/config?view=azure-cli-latest))을 실행하여 후속 명령에 대한 기본 리소스 그룹과 지역을 설정합니다. 이렇게 하면 해당 값을 매번 지정할 필요가 없습니다. (이 명령은 성공에 대한 출력이 없습니다.)

    ```azurecli
    az configure --defaults group=myResourceGroup location=westus
    ```

1. 다음 Azure CLI 명령([`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create))을 실행하여 App Service에 사용되는 기본 가상 머신을 정의하는 **App Service 요금제** 를 만듭니다.

    ```azurecli
    az appservice plan create --name myPlan --sku F1
    ```

    위의 명령은 공유 가상 머신을 사용하는 [무료 호스팅 계획](../../core/what-is-azure-for-javascript-development.md#free-tier-resources)(`--sku F1`)을 지정하고 계획 이름을 `myPlan`으로 지정합니다. 

1. 다음 Azure CLI 명령([`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create))을 실행하여 App Service를 만들고 `<your_app_name>`을 URL(`http://<your_app_name>.azurewebsites.net`)이 될 고유 이름으로 대체하고, [최신 Node.js 런타임](/cli/azure/webapp?view=azure-cli-latest#az_webapp_list_runtimes&preserve-view=false)을 지정합니다. 

    ```azurecli
    az webapp create --name <your_app_name> --plan myPlan -g --runtime "node|12-lts"
    ```


1. 다음 Azure CLI 명령([`az webapp browse`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_browse))을 실행하여 새로 생성된 App Service를 브라우저에서 열고 `<your_app_name>`을 사용한 이름으로 다시 대체합니다.

    ```azurecli
    az webapp browse --name <your_app_name>
    ```

1. 앱에 대한 사용자 지정 코드를 배포하지 않았으므로 브라우저에 기본 페이지가 표시됩니다.

    ![기본 App Service 페이지](../../media/azure-cli/azure-default-page.png)

## <a name="troubleshooting"></a>문제 해결

* 필수 매개 변수(`--resource-group`)가 누락되었다는 오류가 표시되면 아티클 맨 위로 돌아가 기본값을 설정하세요. 

> [!div class="nextstepaction"]
> [App Service를 만들었습니다.](tutorial-vscode-azure-cli-node-04.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=create-website)
