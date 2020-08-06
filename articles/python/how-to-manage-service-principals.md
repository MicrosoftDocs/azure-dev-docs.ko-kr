---
title: Azure 개발을 위한 로컬 서비스 주체 관리
description: Azure Portal 또는 Azure CLI를 사용하여 로컬 개발용으로 만든 서비스 주체를 관리하는 방법입니다.
ms.date: 05/12/2020
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: e29ee999472d0d9d141cfad728e62f1fe518e85b
ms.sourcegitcommit: e451e4360d9c5956cc6a50880b3a7a55aa4efd2f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87478362"
---
# <a name="how-to-manage-service-principals"></a>서비스 주체를 관리하는 방법

보안을 위해, Azure 리소스에 액세스하고 수정할 수 있도록 앱 코드에 권한을 부여하는 방식을 항상 신중하게 관리해야 합니다. 코드를 로컬에서 테스트하는 경우에는 [로컬 Python 개발 환경 구성 - 인증](configure-local-development-environment.md#configure-authentication)의 설명대로 전체 권한이 있는 사용자로 실행하기 보다는 로컬 *서비스 주체*를 사용해야 합니다.

시간이 지나면 이러한 서비스 주체를 삭제하거나 이름을 바꾸거나 관리해야 할 수도 있으며, 이러한 작업은 Azure Portal 또는 Azure CLI를 통해 수행할 수 있습니다.

## <a name="basics-of-azure-authorization"></a>Azure 인증의 기본 사항

코드가 Azure 리소스에서 작업을 수행하려고 시도할 때마다(Azure 라이브러리의 클래스를 통해 수행함), Azure는 애플리케이션에 해당 작업을 수행할 권한이 있는지 확인합니다. [Azure Portal](https://portal.azure.com)이나 Azure CLI를 사용하여 애플리케이션 ID에 특정 역할 또는 리소스 기반 권한을 부여합니다. (이 절차는 애플리케이션의 보안이 손상될 경우 악용될 수 있는 애플리케이션에 대해 과도한 권한을 부여하지 않도록 방지합니다.)

Azure에 배포할 때, 애플리케이션의 ID는 대개 애플리케이션을 호스트하는 서비스 내에서 앱에 부여한 이름과 동일합니다(예: 관리 ID가 활성화된 경우 Azure App Service, Azure Functions, 가상 머신 등). 하지만 코드를 로컬에서 실행할 때는 호스팅 서비스가 포함되지 않기 때문에 Azure에 적절한 대안을 제공해야 합니다.

이런 용도로 로컬 서비스 주체를 사용합니다. 서비스 주체는 앱 ID의 다른 이름이며 사용자 ID가 아닙니다. 서비스 주체에는 이름, "테넌트" 식별자(본질적으로 조직의 ID), 앱 또는 "클라이언트" 식별자, 비밀/암호가 있습니다. 이러한 자격 증명은 Azure에서 ID를 인증하기에 충분하며, 해당 ID가 지정된 리소스에 액세스할 권한이 있는지 여부를 확인할 수 있습니다.

각 개발자는 자신의 서비스 주체를 가지고 있어야 하며, 이 주체는 워크스테이션의 사용자 계정으로 보호되고 소스 제어 리포지토리에는 저장되지 않습니다. 서비스 주체 하나가 도난되거나 손상되면 Azure Portal에서 해당 주체를 간편하게 삭제하고 모든 권한을 해지한 다음, 해당 개발자의 서비스 주체를 다시 만들 수 있습니다.

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
