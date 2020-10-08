---
title: '연습, 2부: Azure 서비스를 사용하여 Python 앱 인증'
description: 예제 시나리오의 다양한 인증 요구 사항 및 과제와 Azure 통합 인증으로 이러한 문제를 충족하는 방법에 대해 설명합니다.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 2e3755f7049fd091c05cd2aca5ddf8276cebff95
ms.sourcegitcommit: 29b161c450479e5d264473482d31e8d3bf29c7c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91764431"
---
# <a name="part-2-authentication-needs-in-the-scenario"></a>2부: 시나리오의 인증 요구 사항

[이전 파트: 소개 및 배경](walkthrough-tutorial-authentication-01.md)

이 예제 시나리오의 기본 앱에는 다음과 같은 인증 요구 사항이 있습니다.

- Azure Key Vault로 인증하고 저장된 타사 API 키에 액세스합니다.
- API 키를 사용하여 타사 API로 인증합니다.
- 스토리지 계정에 필요한 자격 증명을 사용하여 Azure Queue Storage로 인증합니다.

이러한 세 가지 별개의 요구 사항으로 인해 애플리케이션은 세 가지 자격 증명 집합 즉, Azure 리소스(Key Vault 및 Queue Storage)용 두 개와 외부 리소스(타사 API)용 하나를 관리해야 합니다.

앞서 언급했듯이 Key Vault 자체에 필요한 자격 증명을 제외하고 Key Vault의 모든 자격 증명을 안전하게 관리할 수 있습니다. Key Vault로 인증되면 애플리케이션은 런타임에 다른 키를 검색하여 Queue Storage와 같은 서비스로 인증할 수 있습니다.

단, 이 방법을 사용하려면 앱에서 Key Vault에 대한 자격 증명을 별도로 관리해야 합니다. 그렇다면 이러한 자격 증명을 안전하게 관리하고 로컬 개발과 클라우드의 프로덕션 배포 모두에서 작동하도록 하는 방법은 무엇일까요?

부분적인 솔루션은 서버 쪽 환경 변수에(즉, Azure App Service 및 Azure Functions를 사용하는 애플리케이션 설정을 통해) 키를 저장하는 것입니다. 그러면 적어도 소스 제어에서 키가 제외됩니다. 하지만 워크스테이션에서 코드를 실행하려면 해당 환경 변수를 로컬로 복제해야 하기 때문에 자격 증명이 노출되거나 소스 제어에 실수로 포함될 위험이 있습니다. 코드의 개발 버전에서 특수 프로시저를 구현하여 문제를 어느 정도 해결할 수 있지만 그렇게 하면 개발 프로세스가 복잡해집니다.

다행히 Azure AD(Active Directory)와의 통합 인증을 사용하면 앱에서 Azure 자격 증명을 전혀 처리하지 않아도 됩니다.

## <a name="integrated-authentication-with-managed-identity"></a>관리 ID와 통합 인증

Storage 및 Key Vault와 같은 많은 Azure 서비스가 Azure AD(Azure Active Directory)와 통합되기 때문에 [관리 ID](/azure/active-directory/managed-identities-azure-resources/overview)를 사용하여 Azure AD로 애플리케이션을 인증하는 경우 다른 연결된 리소스로 자동 인증됩니다. ID에 대한 권한 부여는 [RBAC(역할 기반 액세스 제어)](/azure/role-based-access-control/role-assignments-steps)를 통해 처리되며 다른 액세스 정책을 통해 처리되는 경우도 있습니다.

이렇게 통합하면 앱 코드에서 Azure 관련 자격 증명을 처리할 필요가 없으며 개발자 워크스테이션이나 소스 제어에 자격 증명이 나타나지 않습니다. 또한 타사 API 및 서비스에 대한 키 처리는 전적으로 런타임에 수행되므로 해당 키를 안전하게 유지할 수 있습니다.

관리 ID는 특히 Azure에 배포된 앱에서 작동합니다. 로컬 개발을 위해, 로컬에서 실행할 때 앱 ID로 사용할 별도의 서비스 주체를 만듭니다. [로컬 개발 환경 구성 - 인증 구성](configure-local-development-environment.md#configure-authentication)의 설명대로 환경 변수를 사용하여 Azure 라이브러리에서 이 서비스 주체를 사용할 수 있도록 설정합니다. 이 서비스 주체에 클라우드에서 사용되는 관리 ID와 함께 역할 권한도 할당합니다.

로컬 서비스 주체에 대해 이러한 단계를 수행하고 나면 동일한 코드가 로컬과 클라우드에서 모두 작동하여 Azure 리소스로 앱을 인증합니다. 이러한 세부 정보는 [앱 인증 및 권한 부여 방법](azure-sdk-authenticate.md)에서 설명되어 있지만 짧은 버전은 다음과 같습니다.

1. 코드에서, Azure에서 실행하는 경우 관리 ID를 자동으로 사용하고 로컬에서 실행하는 경우 별도의 서비스 주체를 사용하는 `DefaultAzureCredential` 개체를 만듭니다

1. 액세스하려는 리소스(Key Vault, Queue Storage 등)에 적절한 클라이언트 개체를 만들 때 이 자격 증명을 사용합니다.

1. 그러면 클라이언트 개체를 통해 작업 메서드를 호출할 때 인증이 수행되고, 이로 인해 리소스에 대한 REST API 호출이 생성됩니다.

1. 앱 ID가 유효하면 Azure는 해당 ID가 특정 작업에 대해 권한이 있는지도 확인합니다.

이 자습서의 나머지 부분에서는 예제 시나리오 및 함께 제공되는 샘플 코드의 컨텍스트에서 프로세스의 모든 세부 사항을 보여줍니다.

샘플의 프로비저닝 스크립트에서 모든 리소스는 `auth-scenario-rg`라는 리소스 그룹에 만들어집니다. 이 그룹은 Azure CLI [`az group create`](/cli/azure/group#az-group-create) 명령을 사용하여 생성됩니다.

> [!div class="nextstepaction"]
> [3부 - 타사 API 구현 예 >>>](walkthrough-tutorial-authentication-03.md)
