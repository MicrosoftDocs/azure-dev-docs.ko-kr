---
title: Azure 개발을 위한 로컬 서비스 주체 관리
description: Azure Portal 또는 Azure CLI를 사용하여 로컬 개발용으로 만든 서비스 주체를 관리하는 방법입니다.
ms.date: 08/18/2020
ms.topic: conceptual
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: b6d3ffbb7e78b7c4f2405e5363446c1906913aa9
ms.sourcegitcommit: 800c5e05ad3c0b899295d381964dd3d47436ff90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88614511"
---
# <a name="how-to-manage-service-principals"></a>서비스 주체를 관리하는 방법

[앱 인증 방법](azure-sdk-authenticate.md)의 설명대로 관리 ID를 사용하는 경우를 제외하면 Azure에서 앱을 식별하기 위해 서비스 주체를 사용하는 경우가 많습니다.

시간이 지나면 이러한 서비스 주체를 삭제하거나 이름을 바꾸거나 관리해야 하는 경우가 일반적이며, 이러한 작업은 Azure Portal 또는 Azure CLI를 통해 수행할 수 있습니다.

## <a name="manage-service-principals-using-the-azure-portal"></a>Azure Portal을 사용하여 서비스 주체 관리

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 포털 홈 페이지의 아이콘을 사용하거나 포털 검색 창에서 "Azure Active Directory"를 검색하여 **Azure Active Directory** 페이지로 이동합니다.

    ![Azure Portal에서 Azure Active Directory 검색](media/how-to-manage-service-principals/azure-ad-portal-search.png)

1. 왼쪽 탐색 메뉴에서 **관리** > **앱 등록**을 선택합니다. 로컬 개발 서비스 주체가 목록에 표시됩니다.

    ![Azure Active Directory에서 앱 등록](media/how-to-manage-service-principals/azure-ad-app-registrations.png)

1. 서비스 주체를 선택하여 해당 속성 페이지로 이동한 후 ID 값을 검사하고, 서비스 주체를 삭제하거나 이름을 바꾸고, 다양한 엔드포인트 URL을 얻을 수 있습니다.

1. 특정 리소스에 액세스할 수 있도록 서비스 주체에 권한을 부여하는 프로세스는 일반적으로 해당 서비스에 따라 달라집니다. 자세한 내용은 해당 서비스에 대한 설명서를 참조하세요. 예를 들어 [Blob Storage에 대한 권한 부여](/azure/storage/common/storage-auth-aad-rbac-portal) 및 [Queue Storage에 대한 권한 부여](/azure/storage/common/storage-auth-aad-rbac-portal) 문서는 Azure Storage의 일부 프로세스를 설명합니다.

## <a name="manage-service-principals-using-the-azure-cli"></a>Azure CLI를 사용하여 서비스 주체 관리

Azure CLI를 사용하면 Azure Portal을 통해 서비스 주체에 수행할 수 있는 동일한 작업을 많이 수행할 수 있습니다.

- 서비스 주체 생성, 보기, 업데이트 및 삭제: [az ad sp](/cli/azure/ad/sp?view=azure-cli-latest) 명령. [Azure CLI를 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)도 참조하세요.
- 역할 할당 관리: [az role assignment](/cli/azure/role/assignment?view=azure-cli-latest) 명령.

참고 항목:

- [Azure 라이브러리를 사용하여 Azure에서 인증](azure-sdk-authenticate.md)
