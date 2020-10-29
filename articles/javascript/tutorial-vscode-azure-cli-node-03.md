---
title: Azure CLI에서 앱을 호스트하는 Azure App Service 만들기
description: '자습서 3부: Azure CLI - App Service 만들기'
ms.topic: tutorial
ms.date: 09/24/2019
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: c22896484e07c8657582265e70dda41b12efb155
ms.sourcegitcommit: 1ddcb0f24d2ae3d1f813ec0f4369865a1c6ef322
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92688653"
---
# <a name="create-the-app-service"></a>App Service 만들기

[이전 단계: 앱 만들기](tutorial-vscode-azure-cli-node-02.md)

이 단계에서는 Azure CLI를 사용하여 앱 코드를 호스트하는 Azure App Service를 만듭니다.

1. 터미널 또는 명령 프롬프트에서 다음 명령을 사용하여 App Service에 대한 **리소스 그룹** 을 만듭니다. 리소스 그룹은 기본적으로 Azure에 있는 앱 리소스의 명명된 컬렉션(예: 웹 사이트, 데이터베이스, Azure Functions 등)입니다.

    ```azurecli
    az group create --name myResourceGroup --location westus
    ```

    위의 명령은 `westus` 데이터 센터에 `myResourceGroup`이라는 리소스 그룹을 만듭니다. 이 값은 원하는 대로 변경할 수 있습니다.

    명령이 성공적으로 실행되면 리소스 그룹의 세부 사항과 함께 JSON 출력이 표시됩니다.

1. 다음 명령을 실행하여 후속 명령에 대한 기본 리소스 그룹과 지역을 설정합니다. 이렇게 하면 해당 값을 매번 지정할 필요가 없습니다. (이 명령은 성공에 대한 출력이 없습니다.)

    ```azurecli
    az configure --defaults group=myResourceGroup location=westus
    ```

1. 다음 명령을 실행하여 App Service에 사용되는 기본 가상 머신을 정의하는 **App Service 계획** 을 만듭니다.

    ```azurecli
    az appservice plan create --name myPlan --sku F1
    ```

    위의 명령은 공유 가상 머신을 사용하는 무료 호스팅 계획(`--sku F1`)을 지정하고 계획 이름을 `myPlan`으로 지정합니다. 다시, 이 명령은 성공 시 JSON 출력을 표시합니다.

1. 다음 명령을 실행하여 `<your_app_name>`을 고유 이름으로 대체하여 URL `http://<your_app_name>.azurewebsites.net`이 되는 App Service를 만듭니다. PowerShell 명령은 약간 다릅니다. `--runtime "node|6.9"` 인수는 서버에서 노드 버전 6.9.x를 사용하도록 Azure에 알려줍니다.

    ```azurecli
    az webapp create --name <your_app_name> --plan myPlan --runtime "node|6.9"
    ```

    > [!TIP]
    > `package.json`에 원하는 노드 버전을 명시할 수도 있습니다. 이 설정은 Azure가 배포 중에 적용합니다. 예를 들어 다음 `package.json` 항목은 Azure에 노드 7.0.0 이상을 사용하도록 알려줍니다.
    >
    > ``` json
    > "engines": {
    >     "node": ">7.0.0"
    > },
    > ```

1. 다음 명령을 실행하여 새로 생성된 App Service를 브라우저에서 열고 `<your_app_name>`을 사용한 이름으로 다시 대체합니다.

    ```azurecli
    az webapp browse --name <your_app_name>
    ```

1. 앱에 대한 사용자 지정 코드를 배포하지 않았으므로 브라우저에 기본 페이지가 표시됩니다.

    ![기본 App Service 페이지](media/azure-cli/azure-default-page.png)

> [!div class="nextstepaction"]
> [App Service를 만들었습니다.](tutorial-vscode-azure-cli-node-04.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=create-website)
