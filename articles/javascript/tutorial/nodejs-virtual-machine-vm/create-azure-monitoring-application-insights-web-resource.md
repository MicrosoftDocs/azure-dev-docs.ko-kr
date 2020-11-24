---
title: Azure Monitor 리소스 만들기
description: 모든 Azure 리소스에 대한 Azure 리소스 그룹 하나와 Azure 클라우드로 웹앱의 로그 파일을 수집하기 위한 Monitor 리소스 하나를 만듭니다. Azure Monitor는 Azure 서비스의 이름이고, Application Insights는 자습서에서 사용하는 클라이언트 라이브러리의 이름입니다.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: b401953c0e1c972efa0f5d90817f461b858cf04b
ms.sourcegitcommit: a2a51e0c6530eb5794a2fe667cf4c9a60b2a7470
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625042"
---
# <a name="2-create-application-insights-resource-for-web-pages"></a>2. 웹 페이지에 대한 Application Insights 리소스 만들기

자습서의 이 단계에서는 모든 Azure 리소스에 대한 Azure 리소스 그룹 하나와 Azure 클라우드로 웹앱의 로그 파일을 수집하기 위한 Monitor 리소스 하나를 만듭니다. Azure Monitor는 Azure 서비스의 이름이고, Application Insights는 자습서에서 사용하는 클라이언트 라이브러리의 이름입니다. 

## <a name="create-a-resource-group-for-your-virtual-machine-resources"></a>가상 머신 리소스에 대한 리소스 그룹 만들기

이 자습서에는 여러 Azure 리소스가 포함되어 있습니다. 리소스 그룹을 만들면 리소스를 쉽게 찾고, 작업을 마친 후 쉽게 삭제할 수 있습니다.

1. 터미널 또는 bash 셸에서 [Azure 리소스 그룹을 만드는 Azure CLI 명령을 입력](/cli/azure/group?view=azure-cli-latest#az_group_create)하고, 이름을 `rg-demo-vm-eastus`로 지정합니다.

    ```azurecli
    az group create \
        --location eastus \
        --name rg-demo-vm-eastus 
    ```

## <a name="create-azure-monitor-resource-with-azure-cli"></a>Azure CLI를 사용하여 Azure Monitor 리소스 만들기

1. Azure CLI의 Application Insights 확장을 설치합니다.

    ```azurecli
    az extension add -n application-insights
    ```

1. 다음 명령을 사용하여 [모니터링 리소스 그룹을 만듭니다](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext_application_insights_az_monitor_app_insights_component_create).


    ```azurecli
    az monitor app-insights component create \
      --app demoWebAppMonitor \
      --location eastus \
      --resource-group rg-demo-vm-eastus
    ```

    결과에서 `instrumentationKey`를 찾아 복사합니다. 나중에 필요합니다. 

1. 터미널을 계속 열어 둡니다. 다음 단계에서도 사용할 것입니다.

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [Linux 가상 머신 만들기](create-linux-virtual-machine-azure-cli.md) 
