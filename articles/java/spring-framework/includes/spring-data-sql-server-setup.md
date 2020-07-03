---
author: judubois
ms.date: 05/18/2020
ms.author: judubois
ms.openlocfilehash: b1dd65024158f622d6b202dc2ad83a31f8d98296
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507717"
---
## <a name="prepare-the-working-environment"></a>작업 환경 준비

먼저 다음 명령을 사용하여 몇 가지 환경 변수를 설정합니다.

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_SQL_SERVER_USERNAME=spring
AZ_SQL_SERVER_PASSWORD=<YOUR_AZURE_SQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

자리 표시자를 이 문서 전체에서 사용되는 다음 값으로 바꿉니다.

- `<YOUR_DATABASE_NAME>`: Azure SQL Database 서버의 이름입니다. Azure에서 고유해야 합니다.
- `<YOUR_AZURE_REGION>`: 사용할 Azure 지역. 기본적으로 `eastus`를 사용할 수 있지만 거주지와 더 가까운 지역을 구성하는 것이 좋습니다. `az account list-locations`를 입력하면 사용 가능한 지역의 전체 목록을 나열할 수 있습니다.
- `<AZ_SQL_SERVER_PASSWORD>`: Azure SQL Database 서버의 암호입니다. 암호의 길이는 8자 이상이어야 합니다. 다음 범주 중 세 가지 범주의 문자여야 합니다. 영문 대문자, 영문 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등).
- `<YOUR_LOCAL_IP_ADDRESS>`: Spring Boot 애플리케이션을 실행할 로컬 컴퓨터의 IP 주소. 이를 확인하는 간편한 방법 중 하나는 브라우저에서 [whatismyip.akamai.com](http://whatismyip.akamai.com/)으로 이동하는 것입니다.

다음으로, 다음 명령을 사용하여 리소스 그룹을 만듭니다.

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
    | jq
```

> [!NOTE]
> `jq` 유틸리티를 사용하여 JSON 데이터를 표시하고 더 읽기 쉽게 만듭니다. 이 유틸리티는 [Azure Cloud Shell](https://shell.azure.com/)에 기본적으로 설치됩니다. 이 유틸리티가 마음에 들지 않을 경우 여기서 사용하게 될 모든 명령의 `| jq` 부분을 안전하게 제거하면 됩니다.

## <a name="create-an-azure-sql-database-instance"></a>Azure SQL Database 인스턴스 만들기

먼저 관리형 Azure SQL Database 서버를 만듭니다.

> [!NOTE]
> [빠른 시작: Azure SQL Database 단일 데이터베이스 만들기](/azure/sql-database/sql-database-single-database-get-started)에서 Azure SQL Database 서버를 만드는 방법에 대한 자세한 정보를 읽을 수 있습니다.

[Azure Cloud Shell](https://shell.azure.com/)에서 다음 명령을 실행합니다.

```azurecli
az sql server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --admin-user $AZ_SQL_SERVER_USERNAME \
    --admin-password $AZ_SQL_SERVER_PASSWORD \
    | jq
```

이 명령은 Azure SQL Database 서버를 만듭니다.

### <a name="configure-a-firewall-rule-for-your-azure-sql-database-server"></a>Azure SQL Database 서버용 방화벽 규칙 구성

Azure SQL Database 인스턴스는 기본적으로 보호됩니다. 들어오는 연결을 허용하지 않는 방화벽이 있습니다. 데이터베이스를 사용하려면 로컬 IP 주소에서 데이터베이스 서버에 액세스할 수 있도록 하는 방화벽 규칙을 추가해야 합니다.

이 문서의 시작 부분에서 로컬 IP 주소를 구성했으므로 다음 명령을 실행하여 서버의 방화벽을 열 수 있습니다.

```azurecli
az sql server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
    | jq
```

### <a name="configure-a-azure-sql-database"></a>Azure SQL Database 구성

이전에 만든 Azure SQL Database 서버가 비어 있습니다. Spring Boot 애플리케이션에서 사용할 수 있는 데이터베이스가 없습니다. 다음 명령을 실행하여 `demo`라는 새 데이터베이스를 만듭니다.

```azurecli
az sql db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server $AZ_DATABASE_NAME \
    | jq
```
