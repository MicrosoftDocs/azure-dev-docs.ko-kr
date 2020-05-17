---
title: Azure CLI를 사용하여 클라우드를 선택합니다.
description: Azure CLI를 사용하여 여러 클라우드를 만들고, 로그인하고, 관리합니다.
author: dbradish-microsoft
manager: barbkess
ms.author: dbradish
ms.date: 09/09/2018
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 8e24a4740d97ddf67f81e60fef9217a4e72daab0
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82031225"
---
# <a name="select-clouds-with-the-azure-cli"></a>Azure CLI를 사용하여 클라우드를 선택합니다.

서로 다른 영역에서 작업하거나 [Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/)을 사용할 경우 하나 이상의 클라우드를 사용해야 할 수도 있습니다. Microsoft는 제품 사용에 적용될 수 있는 지역 법을 준수하기 위해 클라우드를 제공합니다. 이 문서에서는 클라우드에 대한 정보를 얻고 현재 클라우드를 변경하고 새 클라우드를 등록 또는 등록 취소하는 방법을 설명합니다.

## <a name="list-available-clouds"></a>사용할 수 있는 클라우드 목록

[az cloud list](/cli/azure/cloud#az-cloud-list) 명령을 사용하여 사용 가능한 클라우드를 나열할 수 있습니다. 이 명령으로 현재 활성 클라우드, 현재 프로필, 지역별 접미사 및 호스트 이름에 대한 정보를 알 수 있습니다.

다음과 같이 활성 클라우드와 모든 사용 가능한 클라우드 목록을 가져옵니다.

```azurecli-interactive
az cloud list --output table
```

```output
IsActive    Name               Profile
----------  -----------------  ---------
True        AzureCloud         latest
            AzureChinaCloud    latest
            AzureUSGovernment  latest
            AzureGermanCloud   latest
```

현재 활성 클라우드에는 `IsActive` 열에 `True`가 있습니다. 언제든지 하나의 클라우드만 활성화할 수 있습니다. Azure 서비스에 사용하는 엔드포인트를 포함하여 클라우드에 대한 자세한 정보를 알아보려면 `cloud show` 명령을 사용하세요.

```azurecli-interactive
az cloud show --name AzureChinaCloud --output json
```

```json
{
  "endpoints": {
    "activeDirectory": "https://login.chinacloudapi.cn",
    "activeDirectoryDataLakeResourceId": null,
    "activeDirectoryGraphResourceId": "https://graph.chinacloudapi.cn/",
    "activeDirectoryResourceId": "https://management.core.chinacloudapi.cn/",
    "batchResourceId": "https://batch.chinacloudapi.cn/",
    "gallery": "https://gallery.chinacloudapi.cn/",
    "management": "https://management.core.chinacloudapi.cn/",
    "resourceManager": "https://management.chinacloudapi.cn",
    "sqlManagement": "https://management.core.chinacloudapi.cn:8443/",
    "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json"
  },
  "isActive": false,
  "name": "AzureChinaCloud",
  "profile": "latest",
  "suffixes": {
    "azureDatalakeAnalyticsCatalogAndJobEndpoint": null,
    "azureDatalakeStoreFileSystemEndpoint": null,
    "keyvaultDns": ".vault.azure.cn",
    "sqlServerHostname": ".database.chinacloudapi.cn",
    "storageEndpoint": "core.chinacloudapi.cn"
  }
}
```

## <a name="switch-the-active-cloud"></a>활성 클라우드 전환

구성 파일을 사용하여 기본 클라우드를 설정하려면 [CLI 구성 값 및 환경 변수](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables)를 참조하세요.  활성 클라우드를 전환하려면 [az cloud set](/cli/azure/cloud#az-cloud-set) 명령을 실행합니다. 이 명령은 하나의 필수 인수인 클라우드 이름을 사용합니다.

```azurecli-interactive
az cloud set --name AzureChinaCloud
```

> [!IMPORTANT]
> 활성화된 클라우드에 대한 인증이 만료된 경우에 다른 CLI 작업을 수행하기 전에 다시 인증해야 합니다. 새로운 클라우드로 처음 전환하는 경우도 활성 구독을 설정해야 합니다.
> 인증에 대한 지침은 [Azure CLI를 사용하여 로그인](authenticate-azure-cli.md)을 참조하세요. 구독 관리에 대한 자세한 내용은 [Azure CLI를 사용하여 Azure 구독 관리](manage-azure-subscriptions-azure-cli.md)를 참조하세요.

## <a name="register-a-new-cloud"></a>새 클라우드를 등록합니다.

Azure Stack에 대한 사용자 고유의 엔드포인트가 있는 경우 새 클라우드를 등록합니다. 클라우드를 만들려면 [az cloud register](/cli/azure/cloud#az-cloud-register) 명령을 사용합니다. 이 명령에는 이름 및 서비스 엔드포인트 집합이 필요합니다. Azure Stack과 함께 사용할 클라우드를 등록하는 방법에 대해 알아보려면 [Azure Stack에서 Azure CLI와 함께 API 버전 프로필 사용](/azure/azure-stack/user/azure-stack-version-profiles-azurecli2#connect-to-azure-stack)을 참조하세요.

중국, US Government 또는 독일 지역에 대한 정보는 등록할 필요가 없습니다. 이들 클라우드는 Microsoft에서 관리하며 기본적으로 사용할 수 있습니다.  사용 가능한 모든 엔드포인트 설정에 대한 자세한 내용은 [에 대한 설명서를 참조하세요`az cloud register`](/cli/azure/cloud#az-cloud-register).

클라우드를 등록하더라도 자동으로 해당 클라우드로 전환되지 않습니다. `az cloud set` 명령을 사용하여 새로 만든 클라우드를 선택합니다.

## <a name="update-an-existing-cloud"></a>기존 클라우드 업데이트

권한이 있는 경우 기존 클라우드를 업데이트할 수도 있습니다. 클라우드를 업데이트하면 다른 Azure 서비스 프로필로 전환하거나 연결 엔드포인트를 수정합니다.
`az cloud register`와 동일한 인수를 사용하여 [az cloud update](/cli/azure/cloud#az-cloud-update) 명령으로 클라우드를 업데이트합니다.

## <a name="unregister-a-cloud"></a>클라우드 등록 취소

만든 클라우드가 더 이상 필요하지 않으면 [az cloud unregister](/cli/azure/cloud#az-cloud-unregister) 명령을 사용하여 등록을 취소할 수 있습니다.

```azurecli-interactive
az cloud unregister --name MyCloud
```
