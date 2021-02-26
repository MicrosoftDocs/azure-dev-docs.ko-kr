---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/08/2021
ms.openlocfilehash: b2877a43eda86868eb3ee2841cedfd8c4d242340
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100019365"
---
## <a name="create-a-cosmos-db-resource-for-mongodb"></a>MongoDB용 Cosmos DB 리소스 만들기

[Azure Cloud Shell](https://shell.azure.com)에서 다음 Azure CLI [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) 명령을 사용하여 MongoDB 데이터베이스에 대한 새 CosmosDB 리소스를 만듭니다. 

```azurecli
az cosmosdb create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE_NAME \
    --locations regionName=eastus \
    --kind MongoDB \
    --enable-public-network true \
    --ip-range-filter 123.123.123.123 
```

`123.123.123.123`을 자신의 클라이언트 IP로 바꾸거나 매개 변수를 완전히 제거합니다. 

이 명령은 완료하는 데 몇 분 정도 걸릴 수 있으며 `eastus` 지역에서 공개적으로 사용 가능한 리소스를 만듭니다. 

```text
{
  "apiProperties": {
    "serverVersion": "3.6"
  },
  "capabilities": [
    {
      "name": "EnableMongo"
    }
  ],
  "connectorOffer": null,
  "consistencyPolicy": {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "cors": [],
  "databaseAccountOfferType": "Standard",
  "disableKeyBasedMetadataWriteAccess": false,
  "documentEndpoint": "https://mongo-2.documents.azure.com:443/",
  "enableAnalyticalStorage": false,
  "enableAutomaticFailover": false,
  "enableCassandraConnector": null,
  "enableFreeTier": false,
  "enableMultipleWriteLocations": false,
  "failoverPolicies": [
    {
      "failoverPriority": 0,
      "id": "mongodb-2",
      "locationName": "East US"
    }
  ],
  "id": "/subscriptions/.../resourceGroups/my-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/mongo-2",
  "ipRules": [
    {
      "ipAddressOrRange": "123.123.123.123"
    }
  ],
  "isVirtualNetworkFilterEnabled": false,
  "keyVaultKeyUri": null,
  "kind": "MongoDB",
  "location": "Central US",
  "locations": [
    {
      "documentEndpoint": "https://mongodb-2.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "mongodb-2",
      "isZoneRedundant": false,
      "locationName": "East US",
      "provisioningState": "Succeeded"
    }
  ],
  "name": "mongo-2",
  "privateEndpointConnections": null,
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "readLocations": [
    {
      "documentEndpoint": "https://mongodb-2.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "mongodb-2",
      "isZoneRedundant": false,
      "locationName": "East US",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "my-resource-group",
  "systemData": {
    "createdAt": "2021-02-08T20:21:05.9519342Z"
  },
  "tags": {},
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "virtualNetworkRules": [],
  "writeLocations": [
    {
      "documentEndpoint": "https://mongodb-2.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "mongodb-2",
      "isZoneRedundant": false,
      "locationName": "East US",
      "provisioningState": "Succeeded"
    }
  ]
}
```

## <a name="add-firewall-rule-for-your-client-ip-address-to-mongodb-resource"></a>MongoDB 리소스에 클라이언트 IP 주소에 대한 방화벽 규칙 추가

기본적으로 방화벽 규칙은 구성되지 않습니다. JavaScript를 사용하여 클라이언트가 서버에 성공적으로 연결되도록 클라이언트 IP 주소를 추가해야 합니다.

[az cosmosdb update](/cli/azure/cosmosdb#az_cosmosdb_update) 명령을 사용하여 방화벽 규칙을 업데이트합니다.

```azurecli
az cosmosdb update \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE_NAME \
    --ip-range-filter 123.123.123.123
```

여러 IP 주소를 구성하려면 쉼표로 구분된 목록을 사용합니다.

```azurecli
az cosmosdb update \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE_NAME \
    --ip-range-filter 123.123.123.123,456.456.456.456
```

## <a name="get-the-mongodb-connection-string-for-your-resource"></a>리소스에 대한 MongoDB 연결 문자열 가져오기

[az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) 명령을 사용하여 이 인스턴스에 대한 MongoDB 연결 문자열을 검색합니다.

```azurecli
az cosmosdb keys list \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE-NAME \
    --type connection-strings 
```

그러면 4개의 연결 문자열(읽기-쓰기 2개와 읽기 전용 2개)이 반환됩니다. 두 개의 다른 시스템 또는 개발자에게 개별적으로 사용할 연결 문자열을 제공할 수 있습니다. 

연결 문자열을 사용하여 MongoDB 데이터베이스에 연결합니다. 다음 중 하나를 사용하여 서비스를 사용할 수 있는지 확인합니다.

* 공개적으로 사용 가능
* 클라이언트의 IP 주소에 대한 방화벽 설정

## <a name="configure-your-azure-web-app-with-the-connection-string"></a>연결 문자열을 사용하여 Azure 웹앱 구성

웹앱이 Cosmos DB 리소스에 연결되도록 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set)를 사용하여 Azure 웹앱 **MONGODB_URL** 환경 변수를 추가합니다.

```azurecli
az webapp config appsettings set \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE_NAME \
    --settings MONGODB_URL=YOUR-CONNECTION-STRING
```
