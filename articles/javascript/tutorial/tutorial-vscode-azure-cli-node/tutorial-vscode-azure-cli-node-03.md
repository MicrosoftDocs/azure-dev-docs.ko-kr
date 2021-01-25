---
title: Azure CLI에서 앱을 호스트하는 Azure App Service 만들기
description: '자습서 3부: Azure CLI - App Service 만들기'
ms.topic: tutorial
ms.date: 01/13/2021
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: ad8e0836d62102521b557fd45b24291d52db447c
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561519"
---
# <a name="create-the-app-service"></a>App Service 만들기

[이전 단계: 앱 만들기](tutorial-vscode-azure-cli-node-02.md)

이 단계에서는 Azure CLI를 사용하여 앱 코드를 호스트하는 Azure App Service를 만듭니다.

<a name="create-resource-group"></a>

## <a name="create-resource-group-and-set-as-default-value"></a>리소스 그룹 만들기 및 기본값으로 설정

1. 터미널 또는 명령 프롬프트에서 다음 명령을 사용하여 App Service에 대한 **리소스 그룹** 을 만듭니다. 리소스 그룹은 기본적으로 Azure에 있는 앱 리소스의 명명된 컬렉션(예: 웹 사이트, 데이터베이스, Azure Functions 등)입니다.

    ```azurecli
    az group create --name myResourceGroup --location westus
    ```

    위의 Azure CLI 명령([`az group create`](/cli/azure/group#az_group_create))은 `westus` 데이터 센터에 `myResourceGroup`이라는 리소스 그룹을 만듭니다. 이 값은 원하는 대로 변경할 수 있습니다.

    명령이 성공적으로 실행되면 리소스 그룹의 세부 사항과 함께 JSON 출력이 표시됩니다.

1. 다음 Azure CLI 명령([`az configure`](/cli/azure/config))을 실행하여 후속 명령에 대한 기본 리소스 그룹과 지역을 설정합니다. 이렇게 하면 해당 값을 매번 지정할 필요가 없습니다. (이 명령은 성공에 대한 출력이 없습니다.)

    ```azurecli
    az configure --defaults group=myResourceGroup location=westus
    ```

## <a name="create-and-deploy-web-app-service-with-azure-cli-command"></a>Azure CLI 명령을 사용하여 웹앱 서비스 만들기 및 배포

다음 Azure CLI 명령인 [`az webapp up`](/cli/azure/webapp#az_webapp_up)을 실행하여 App Service 앱을 배포합니다. `<your_app_name>`을 URL `http://<your_app_name>.azurewebsites.net`이 되는 고유한 이름으로 바꿉니다. 

```azurecli
az webapp up --name <your_app_name> --logs --launch-browser
```

`--logs` 명령은 웹앱을 시작한 직후 로그 스트림을 표시합니다. `--launch-browser` 명령은 새 앱에 대한 기본 브라우저를 엽니다. 동일한 명령을 사용하여 전체 앱을 다시 재배포할 수 있습니다. 

## <a name="troubleshooting"></a>문제 해결

* 필수 매개 변수(`--resource-group`)가 누락되었다는 오류가 표시되면 문서 맨 위로 돌아가 기본값을 설정하거나 매개 변수와 값을 제공하세요. 

## <a name="next-steps"></a>다음 단계

Azure [웹앱](/cli/azure/webapp) 명령 그룹 또는 Azure [App Service](/cli/azure/appservice) 명령 그룹을 사용하여 웹앱에 대한 자세한 명령을 알아보세요. 

> [!div class="nextstepaction"]
> [App Service를 만들었습니다.](tutorial-vscode-azure-cli-node-04.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=create-website)
