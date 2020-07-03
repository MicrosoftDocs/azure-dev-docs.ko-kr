---
author: judubois
ms.date: 05/18/2020
ms.author: judubois
ms.openlocfilehash: 32444e17932c2b86c58d1ac44ab95ce9bd1fa49c
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507514"
---
## <a name="prepare-the-working-environment"></a>작업 환경 준비

먼저 다음 명령을 사용하여 몇 가지 환경 변수를 설정합니다.

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_POSTGRESQL_USERNAME=spring
AZ_POSTGRESQL_PASSWORD=<YOUR_POSTGRESQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

자리 표시자를 이 문서 전체에서 사용되는 다음 값으로 바꿉니다.

- `<YOUR_DATABASE_NAME>`: PostgreSQL 서버의 이름입니다. Azure에서 고유해야 합니다.
- `<YOUR_AZURE_REGION>`: 사용할 Azure 지역. 기본적으로 `eastus`를 사용할 수 있지만 거주지와 더 가까운 지역을 구성하는 것이 좋습니다. `az account list-locations`를 입력하면 사용 가능한 지역의 전체 목록을 나열할 수 있습니다.
- `<YOUR_POSTGRESQL_PASSWORD>`: PostgreSQL 데이터베이스 서버의 암호입니다. 암호의 길이는 8자 이상이어야 합니다. 다음 범주 중 세 가지 범주의 문자여야 합니다. 영문 대문자, 영문 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등).
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

## <a name="create-an-azure-database-for-postgresql-instance"></a>Azure Database for PostgreSQL 인스턴스 만들기

먼저 관리형 PostgreSQL 서버를 만듭니다.

> [!NOTE]
> PostgreSQL 서버 만들기에 관한 자세한 정보는 [Azure portal을 사용하여 Azure Database for PostgreSQL 서버 만들기](/azure/postgresql/quickstart-create-server-database-portal)에서 확인할 수 있습니다.

[Azure Cloud Shell](https://shell.azure.com/)에서 다음 명령을 실행합니다.

```azurecli
az postgres server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name B_Gen5_1 \
    --storage-size 5120 \
    --admin-user $AZ_POSTGRESQL_USERNAME \
    --admin-password $AZ_POSTGRESQL_PASSWORD \
    | jq
```

이 명령은 작은 PostgreSQL 서버를 만듭니다.

### <a name="configure-a-firewall-rule-for-your-postgresql-server"></a>PostgreSQL 서버에 대한 방화벽 규칙 구성

Azure Database for PostgreSQL 인스턴스는 기본적으로 보호됩니다. 들어오는 연결을 허용하지 않는 방화벽이 있습니다. 데이터베이스를 사용하려면 로컬 IP 주소에서 데이터베이스 서버에 액세스할 수 있도록 하는 방화벽 규칙을 추가해야 합니다.

이 문서의 시작 부분에서 로컬 IP 주소를 구성했으므로 다음 명령을 실행하여 서버의 방화벽을 열 수 있습니다.

```azurecli
az postgres server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
    | jq
```

### <a name="configure-a-postgresql-database"></a>PostgreSQL 데이터베이스 구성

이전에 만든 PostgreSQL 서버가 비어 있습니다. Spring Boot 애플리케이션에서 사용할 수 있는 데이터베이스가 없습니다. 다음 명령을 사용하여 `demo`라는 새 데이터베이스를 만듭니다.

```azurecli
az postgres db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server-name $AZ_DATABASE_NAME \
    | jq
```
