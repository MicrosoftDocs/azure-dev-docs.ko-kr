---
title: Azure Portal에서 로그 보기
description: Azure Monitor 및 Application Insights를 사용하여 로깅을 보는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: fd0741cd58e8d2e882a352035cd3c4f9bca956d1
ms.sourcegitcommit: a2a51e0c6530eb5794a2fe667cf4c9a60b2a7470
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625072"
---
# <a name="6-view-logs-in-azure-portal"></a>6. Azure Portal에서 로그 보기

자습서의 이 섹션에서는 Azure Monitor 및 Application Insights를 사용하여 로깅을 보는 방법을 알아봅니다. 

## <a name="count-of-traces-in-logs-with-azure-cli"></a>Azure CLI를 사용하여 로그의 추적 수 보기

Azure CLI를 사용하여 로그의 중요한 부분을 신속하게 확인할 수 있습니다. 예를 들어 다음 명령을 사용하여 로그에 있는 추적 수를 확인할 수 있습니다. 

추적은 `/trace` 경로에만 추가됩니다. 웹앱의 루트에 대한 호출은 추적 로그를 생성하지 않습니다. 

```azurecli
az monitor app-insights metrics show \
    --resource-group rg-demo-vm-eastus \
    --app demoWebAppMonitor \
    --metric traces/count
```

응답은 다음과 비슷하며, 이 예제의 총 추적 수는 2입니다. 

```console
{
  "value": {
    "end": "2020-11-11T21:33:40.311000+00:00",
    "interval": null,
    "segments": null,
    "start": "2020-11-11T20:33:40.311000+00:00",
    "traces/count": {
      "sum": 2
    }
  }
}
```

## <a name="view-application-traces-in-azure-portal"></a>Azure Portal에서 애플리케이션 추적 보기

추적을 목록으로 보는 가장 쉬운 방법은 Azure Portal을 사용하는 것입니다. 

1. 웹 브라우저에서 [Azure Portal](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll)을 엽니다.
1. 리소스 목록을 리소스 그룹 `rg-demo-vm-eastus`로 필터링합니다. 
1. `demoWebAppMonitor` 리소스를 선택합니다. 
1. **모니터링** 섹션에서 **로그** 를 선택합니다. 팝업에 선택 가능한 쿼리가 표시되면 모서리에서 **X** 를 선택하여 팝업을 닫습니다.
1. **추적** 이라는 **Application Insights** 항목을 두 번 클릭하여 선택합니다. 그러면 쿼리 창에 이름이 추가됩니다. 
1. **실행** 단추를 선택하여 쿼리를 실행합니다.
1. 웹앱에서 Azure Monitor 애플리케이션 인사이트 사용자 지정 추적이 목록에 표시됩니다.

    :::image type="content" source="../../media/tutorial-vm/azure-portal-application-insights-custom-trace.png" alt-text="웹앱에서 Azure Monitor 애플리케이션 인사이트 사용자 지정 추적이 목록에 표시됩니다.":::

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 리소스 정리](clean-up-resources.md) 