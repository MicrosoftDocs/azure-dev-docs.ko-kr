---
title: 앱 ID 또는 서비스 주체에 역할 권한 할당
description: Azure CLI를 사용하여 서비스 주체 또는 앱 ID에 권한을 부여하는 방법
ms.date: 05/12/2020
ms.topic: conceptual
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: facfa1663e6f62a7458f99ee20c86f66ee67b17d
ms.sourcegitcommit: 800c5e05ad3c0b899295d381964dd3d47436ff90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88614485"
---
# <a name="how-to-assign-role-permissions-to-an-app-identity-or-service-principal"></a>앱 ID 또는 서비스 주체에 역할 권한을 할당하는 방법

Azure의 RBAC(역할 기반 액세스 제어) 시스템은 다양한 리소스에 대한 권한을 관리합니다. 기본적으로 *역할*은 같이 있어야 하는 관련 권한 컬렉션입니다. 권한을 사용하려면 역할이 적용되는 특정 *범위*를 사용하여 *보안 주체*(사용자, 그룹, 서비스 주체 또는 앱 ID)에 역할을 할당합니다.

실제로는 항상 가장 구체적인 범위에서 보안 주체에 꼭 필요한 역할만 할당합니다. 처음에는 더 넓은 범위에서 더 넓은 역할을 할당하는 것이 더 편리하게 보이겠지만, 이렇게 하지 마세요. 역할 및 범위를 제한하면 보안 주체가 손상될 때(즉, 해당 보안 주체의 자격 증명이 데이터 위반 또는 기타 보안 인시던트에 노출될 때) 위험에 노출되는 리소스를 제한할 수 있습니다.

개발 환경과 프로덕션 환경에서 서로 다른 보안 주체를 사용하기 때문에 개발자는 환경마다 역할 할당을 반복합니다. 즉, 개발 과정에서 개발자는 일반적으로 워크스테이션에서 만든 로컬 서비스 사용자에게 역할을 할당합니다([로컬 Python 개발 환경 구성 - 인증](configure-local-development-environment.md#configure-authentication) 참조). 프로덕션 환경에서는 애플리케이션이 시작할 때 액세스 권한을 갖도록 배포 전에 애플리케이션의 관리 ID 또는 서비스 주체에 역할을 할당합니다. 자세한 내용은 [인증 - Azure에서 실행하는 경우 앱 ID](azure-sdk-authenticate.md#identity-when-running-the-app-on-azure)를 참조하세요.

RBAC에 대한 일반적인 내용은 [Azure 역할 기반 액세스 제어란?](/azure/role-based-access-control/overview)을 참조하세요.

## <a name="role-assignment-process"></a>역할 할당 처리

다음 세 단계에 따라 역할을 할당합니다.

1. 관련 리소스 유형과 권한을 부여하려는 작업에 [적절한 역할을 찾습니다](#find-the-roles-you-need).

1. 해당 역할에 필요한 범위를 확인합니다. 이 범위는 작업에 권한이 부여된 리소스의 범위를 설명합니다.

1. 보안 주체에 역할을 할당합니다.

1단계는 Azure Portal과 Azure CLI가 동일합니다. 2단계와 3단계는 포털과 CLI가 서로 다르며, 이어지는 [Azure Portal에서 범위를 식별하고 역할 할당](#azure-portal) 및 [Azure CLI를 통해 범위 식별 및 역할 할당](#azure-cli) 섹션에서는 결합되었습니다.

> [!NOTE]
> 사용자 계정에 구독 내에서 역할을 할당할 수 있는 권한이 없으면 계정에 "'Microsoft.Authorization/roleAssignments/write' 작업을 수행할 수 있는 권한이 없습니다"라는 오류 메시지가 표시됩니다. 이 경우 개발자 대신 권한을 할당할 수 있는 구독 관리자에게 문의하세요.

## <a name="find-the-roles-you-need"></a>필요한 역할 찾기

1. 포괄적인 내용을 다루는 [Azure 기본 제공 역할](/azure/role-based-access-control/built-in-roles) 문서부터 시작합니다. 문서 맨 위에 있는 표는 이 문서의 뒷부분에 있는 세부 정보의 인덱스입니다.

1. 이 문서에서 권한을 부여하려는 리소스의 서비스 범주(컴퓨팅, 스토리지, 데이터베이스 등)로 이동합니다. 원하는 항목을 찾는 가장 쉬운 방법은 일반적으로 페이지에서 "BLOB", "가상 머신" 등의 관련 키워드를 검색하는 것입니다.

1. 서비스 범주에 대해 나열된 역할을 검토하고 필요한 작업을 찾습니다. 다시 말씀드리지만, 항상 가장 제한된 역할로 시작합니다.

    예를 들어 보안 주체가 Azure Storage 계정의 BLOB을 읽어야 하지만 쓰기 권한은 필요 없는 경우 "Storage Blob 데이터 기여자" 대신(관리자 수준 "Storage Blob 데이터 소유자" 역할은 절대 아님) "Storage Blob 데이터 읽기 권한자"를 선택합니다. 나중에 필요에 따라 언제든지 역할 할당을 업데이트할 수 있습니다.

    또한 보안 주체가 큐 스토리지에 대한 액세스 권한이 필요한 경우 "Storage 큐 데이터 읽기 권한자" 및 "Storage 큐 데이터 기여자" 같은 역할을 사용할 수 있습니다. 다시 강조하지만, 보안 주체가 스토리지 계정의 모든 항목에 액세스할 수 있는 계정 키에 대한 액세스 권한을 제공하는 "읽기 권한자 및 데이터 액세스"처럼 광범위한 역할을 할당하지 말고 최대한 제한적인 역할을 할당하세요.

1. 적당한 역할을 찾을 수 없으면 [사용자 지정 역할](/azure/role-based-access-control/custom-roles)을 만들면 됩니다.

## <a name="identify-scope-and-assign-a-role-on-the-azure-portal"></a><a name="azure-portal"></a>Azure Portal에서 범위를 식별하고 역할 할당

1. [Azure Portal](https://portal.azure.com)에서 역할을 할당하려는 리소스로 이동합니다. 리소스 범위에 따라 할당 범위가 결정됩니다.

    예를 들어 스토리지 계정으로 이동하면 모든 역할 할당이 전체 스토리지 계정에 적용됩니다. 해당 스토리지 계정 내에서 특정 BLOB 컨테이너로 이동하면 역할 할당이 해당 컨테이너에만 적용됩니다.

1. **Access Control(IAM)** 블레이드를 선택합니다(IAM는 "ID 및 액세스 관리"를 의미).

1. 이 블레이드 내에는 보안 주체에 역할을 추가하고 할당된 역할을 제거할 수 있는 **역할 할당** 섹션이 있습니다.

전체 세부 정보와 UI 연습은 Azure RBAC 설명서의 [Azure 역할 할당 추가 또는 제거](/azure/role-based-access-control/role-assignments-portal)를 참조하세요.

## <a name="identify-scope-and-assign-a-role-through-the-azure-cli"></a><a name="azure-cli"></a>Azure CLI를 통해 범위를 식별하고 역할 할당

Azure CLI를 통해 역할을 할당할 때에는 [`az role assignment`](/cli/azure/role/assignment?view=azure-cli-latest) 명령을 사용합니다. `az role assignment create` 명령을 사용하여 할당을 추가하고 `az role assignment delete` 명령을 사용하여 할당을 제거합니다.

전체 프로세스는 [Azure CLI를 사용하여 Azure 역할 할당 추가 또는 제거](/azure/role-based-access-control/role-assignments-cli)에 설명되어 있으며, 다음 요약에서는 이 개발자 센터의 다른 문서와 관련된 특정 예제를 제공합니다.

`az role assignment create` 명령의 구문은 다음과 같습니다.

```azurecli
az role assignment create --assignee <assignee> --role <role> --scope <scope>
```

- `<assignee>`는 보안 주체를 식별합니다. 로컬 개발 중에 사용하는 서비스 주체의 경우 담당자는 해당 보안 주체의 클라이언트 ID입니다. 클라우드에 배포된 애플리케이션의 경우 담당자는 애플리케이션의 이름입니다.
- `<role>`은 "Storage Blob 데이터 기여자" 또는 기여자의 GUID(예: "ba92f5b4-2d11-453d-a403-e96b0029c9fe")처럼 할당할 역할의 이름입니다.
- `<scope>`는 할당의 정확한 범위를 식별하는 잠재적으로 긴 문자열입니다.

범위는 / 문자로 구분된 일련의 식별자로 구성됩니다. 이 문자열을 다음 계층 구조의 표현으로 간주할 수 있으며, 여기서 자리 표시자(`<>`) 없는 텍스트는 고정 식별자입니다.

<pre>
/subscriptions
  /&lt;subscription_id&gt;
    /resourcegroups
      /&lt;resource_group_name&gt;
        /providers
          /&lt;provider_name&gt;
            /&lt;resource_type&gt;
              /&lt;resource_sub_type_1&gt;
                /&lt;resource_sub_type_2&gt;
                  /&lt;resource_name&gt;
</pre>

- `<subscription_id>`는 사용할 구독의 ID(GUID)입니다.
- `<resources_group_name>`은 포함하는 리소스 그룹의 이름입니다.
- `<provider_name>`은 리소스를 처리하는 서비스의 이름이고, `<resource_type>` 및 `<resource_sub_type_*>`은 해당 서비스 내의 추가 수준을 식별합니다.
  
    이러한 이름과 형식은 [Azure 기본 제공 역할](/azure/role-based-access-control/built-in-roles) 참조를 조회하여 찾을 수 있습니다. 위쪽 테이블에서 역할을 찾아 선택하면 역할과 관련된 설명으로 이동됩니다. 여기서 공급자 이름, 리소스 종류 및 리소스 하위 종류가 포함된 문자열을 찾을 수 있습니다. 예를 들어 "Storage Blob 데이터 기여자"는 "Microsoft.Storage/storageAccounts/blobServices/containers/"가 표시됩니다. "Cosmos DB 계정 읽기 권한자 역할"은 하위 형식 하나만 있는 "Microsoft.DocumentDB/databaseAccounts/readonlykeys"가 표시됩니다.

- `<resource_name>`은 특정 리소스를 식별하는 문자열의 마지막 부분입니다.

가장 광범위한(가장 덜 구체적인) 범위는 전체 구독에 할당을 적용하는 `/subscriptions/<subscription_id>`입니다. 계층 구조의 각 추가 수준은 범위를 보다 구체적으로 만듭니다.

### <a name="examples"></a>예제

다음 예에서는 아래 조건을 가정합니다([예: Azure Storage 프로비저닝 및 사용](azure-sdk-example-storage.md)):

- Azure 구독 ID가 `AZURE_SUBSCRIPTION_ID`라는 환경 변수에 포함되어 있습니다.
- 역할을 할당하려는 서비스 주체의 클라이언트 ID가 `AZURE_CLIENT_ID`라는 환경 변수에 포함되어 있습니다.
- 구독에 "PythonAzureExample-Storage-rg"라는 리소스 그룹이 있습니다.
- 리소스 그룹에는 "pythonazurestorage-12345"라는 Azure Storage 계정이 포함되어 있습니다.
- 해당 스토리지 계정에 "blob-container-01"이라는 BLOB 컨테이너가 있습니다.
- 서비스 주체에게 "Storage Blob 데이터 기여자"를 할당하려 합니다.

> [!TIP]
> 변경된 역할 할당이 Azure 내에서 전파될 때까지 1분 정도 걸릴 수 있습니다. 따라서 권한을 제거한 후에도 코드가 잠시 동안 작동할 수 있습니다. 예기치 않은 동작이 발생하면 1~2분 정도 기다렸다가 다시 시도합니다.

#### <a name="grant-permissions-for-the-specific-container-only"></a>특정 컨테이너에 대한 권한만 부여

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345/blobServices/default/containers/blob-container-01"
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345/blobServices/default/containers/blob-container-01"
```

---

#### <a name="grant-permissions-for-all-blob-containers-in-the-storage-account"></a>스토리지 계정의 모든 BLOB 컨테이너에 대한 권한 부여

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345"
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345"
```

---

#### <a name="grant-permissions-for-all-blob-containers-in-the-resource-group"></a>리소스 그룹의 모든 BLOB 컨테이너에 대한 권한 부여

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonAzureExample-Storage-rg"
```

또는 `--resource-group` 매개 변수를 사용하여 리소스 그룹을 지정할 수 있습니다.

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --resource-group "PythonAzureExample-Storage-rg"
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonAzureExample-Storage-rg"
```

또는 `--resource-group` 매개 변수를 사용하여 리소스 그룹을 지정할 수 있습니다.

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --resource-group "PythonAzureExample-Storage-rg"
```

---

#### <a name="grant-permissions-to-all-blob-containers-in-the-subscription"></a>구독의 모든 BLOB 컨테이너에 대한 권한 부여

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%"
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID"
```

---
