---
title: 웹앱에 사용자 지정 도메인 이름 추가
description: Azure CLI를 사용하여 Azure 웹앱에 사용자 지정 도메인 이름을 추가합니다.
ms.topic: how-to
ms.date: 01/29/2021
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 977376fda6e7c93390c45196ae5baae751f5ad64
ms.sourcegitcommit: 3f8aa923e4626b31cc533584fe3b66940d384351
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99230189"
---
# <a name="configuring-a-custom-domain-name"></a>사용자 지정 도메인 이름 구성

Azure CLI를 사용하여 Azure 웹앱에 사용자 지정 도메인 이름을 추가합니다. 

앱 서비스에는 테스트에 적합한 편리한 DNS 이름이 `YOUR-RESOURCE-NAME.azurewebsites.net` 형식으로 있습니다. 어느 시점에서 사용자 지정 도메인 이름을 웹앱에 추가할 수 있습니다. 

## <a name="purchase-a-domain-name-and-configure-dns-record"></a>도메인 이름 구매 및 DNS 레코드 구성

1. 도메인 이름 등록자로부터 도메인 이름을 구입합니다. 
1. DNS 레코드의 경우 웹앱의 외부 IP(실제로는 부하 분산 장치)를 가리키는 DNS 레코드에 `A` 레코드를 추가합니다. 다음 섹션의 절차를 사용하여 외부 IP를 가져옵니다.

    `A` 레코드를 추가하는 것 외에도, 지금까지 사용 중인 `*.azurewebsites.net` 도메인을 가리키는 `TXT` 레코드를 도메인에 추가해야 합니다. `A`과 `TXT` 레코드의 조합을 사용하면 Azure에서 사용자가 해당 도메인을 소유하고 있음을 확인할 수 있습니다.

## <a name="get-web-app-external-ip"></a>웹앱 외부 IP 가져오기

다음 명령을 실행하여 이 IP를 검색할 수 있습니다.

```azurecli
az webapp config hostname get-external-ip --name
```

<a name="register-a-domain-name-with-your-azure-app"></a>

## <a name="configure-web-app-domain-name"></a>웹앱 도메인 이름 구성 

일단 레코드가 만들어지고 DNS 변경 내용이 전파되면 들어오는 트래픽을 올바르게 예상하는지 파악할 수 있도록 Azure에 사용자 지정 도메인을 등록합니다.

[az webapp config hostname add](/cli/azure/webapp/config/hostname) 명령을 사용합니다.

```azurecli
az webapp config hostname add \
    --hostname YOUR-DOMAIN-NAME
    --webapp-name YOUR-WEBAPP-NAME
    --resource-group YOUR-RESOURCE-GROUP-NAME
```

> [!NOTE]
> 이 명령이 작동하려면 먼저 DNS 변경 내용이 전파되어야 합니다.

브라우저를 열고 사용자 지정 도메인으로 이동하여 이제 Azure에서 배포된 앱으로 확인하는지 알아봅니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 레지스트리 리소스 만들기](create-container-registry-resource.md)