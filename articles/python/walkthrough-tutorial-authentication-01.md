---
title: '연습: Azure 서비스를 사용하여 Python 앱 인증'
description: Azure Python SDK azure-identity 라이브러리를 사용하여 Azure Active Directory, Azure Key Vault 및 Azure Queue Storage로 Python 앱을 인증하는 방법에 대한 자세한 연습입니다.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 7f716f3276c0b93ec37ba0941f4029b2ee817fa0
ms.sourcegitcommit: 324da872a9dfd4c55b34739824fc6a6598f2ae12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379545"
---
# <a name="walkthrough-integrated-authentication-for-python-apps-with-azure-services"></a>연습: Azure 서비스로 Python 앱에 대한 통합 인증

Azure Key Vault와 함께 Azure AD(Azure Active Directory)는 애플리케이션이 Azure 서비스는 물론 액세스 키가 포함된 타사 서비스를 사용하여 인증할 수 있는 포괄적이고 편리한 수단을 제공합니다.

이 연습에서는 몇 가지 배경 지식을 제공한 후 샘플([github.com/Azure-Samples/python-integrated-authentication](https://github.com/Azure-Samples/python-integrated-authentication)) 컨텍스트에서 이러한 인증 기능을 설명합니다.

편의를 위해 Azure에 샘플이 이미 배포되어 있으므로 작동 중인 것을 볼 수 있습니다. 자체 Azure 구독에 샘플을 배포하는 경우를 위해, 리포지토리에 Azure CLI 배포 스크립트도 포함됩니다.

## <a name="part-1-background"></a>1부: 배경

많은 Azure 서비스가 역할 기반 액세스 제어에만 의존하여 권한 부여를 수행하지만 특정 서비스는 비밀 또는 키를 사용하여 해당 리소스에 대한 액세스를 제어합니다. 이러한 서비스에는 Azure Storage, 데이터베이스, Cognitive Services, Key Vault 및 Event Hubs가 포함됩니다.

이러한 서비스 내에서 리소스를 사용하는 클라우드 앱을 만들 때는 Azure Portal, Azure CLI 또는 Azure PowerShell을 사용하여 특정 액세스 정책에 연결된 앱에 대한 키를 만들고 구성합니다. 이러한 키는 권한이 없는 다른 코드가 해당 앱 관련 리소스에 액세스하는 것을 막습니다.

이러한 일반적인 디자인 내에서는 일반적으로 클라우드 앱이 이러한 키를 관리하고 각 서비스를 개별적으로 인증해야 하므로 프로세스가 지루하고 오류가 발생하기 쉽습니다. 앱 코드에서 직접 키를 관리하면 키가 소스 제어에 노출될 위험이 있고 보안이 설정되지 않은 개발자 워크스테이션에 키가 저장될 수 있습니다.

다행히, 프로세스를 간소화하고 보안을 강화하는 두 가지 특정 서비스가 Azure에 제공됩니다.

- Azure Key Vault는 액세스 키를 위한 안전한 클라우드 기반 스토리지를(암호화 키 및 인증서와 함께 - 이 문서에 설명되어 있지 않음) 제공합니다. Key Vault를 사용하면 앱이 런타임에만 키에 액세스하기 때문에 소스 코드에 직접 표시되지 않습니다.

- Azure AD(Azure Active Directory) 관리 ID를 사용하는 경우 앱은 Active Directory로 한 번만 인증하면 됩니다. 그런 다음, 앱이 Key Vault를 비롯한 다른 Azure 서비스에서 자동으로 인증됩니다. 따라서 해당 Azure 서비스에 대한 키 또는 기타 자격 증명에 코드가 관여할 필요가 없습니다. 더 좋은 점은 최소한의 구성 요구 사항으로 로컬과 클라우드 모두에서 동일한 코드를 실행할 수 있습니다.

Azure AD와 Key Vault를 함께 사용하면 앱이 개별 Azure 서비스로 자체 인증할 필요가 없으며 타사 서비스에 필요한 모든 키에 쉽고 안전하게 액세스할 수 있습니다.

> [!IMPORTANT]
> 이 문서에서는 공통적인 일반 용어인 "키"를 사용하여 Azure Key Vault에 "비밀"로 저장되는 항목(예: REST API에 대한 액세스 키)을 언급합니다. 이것을 Key Vault의 *secret*와 다른 별도의 기능인 Key Vault의 *crytographic* 키 관리와 혼동해서는 안 됩니다.

## <a name="example-cloud-app-scenario"></a>클라우드 앱 시나리오 예

Azure의 인증 프로세스를 더 자세히 이해하려면 다음 시나리오를 고려하세요.

- 기본 앱은 HTTP 요청에 JSON 데이터로 응답하는 공용(인증되지 않은) API 엔드포인트를 노출합니다. 이 문서에 표시된 예제 엔드포인트는 Azure App Service에 배포된 간단한 Flask 앱으로 구현됩니다.

- 응답을 생성하기 위해 API는 액세스 키가 필요한 타사 API를 호출합니다. 앱은 런타임에 Azure Key Vault에서 해당 액세스 키를 검색합니다.

- 응답을 반환하기 전에 API는 나중에 처리할 수 있도록 Azure Storage Queue에 메시지를 씁니다. (이러한 메시지의 구체적인 처리는 기본 시나리오와 관련이 없습니다.)

![애플리케이션 시나리오 다이어그램](media/walkthrough-tutorial-authentication/scenario-diagram.png)

> [!NOTE]
> 공용 API 엔드포인트는 일반적으로 자체 액세스 키로 보호되지만, 이 문서에서는 엔드포인트가 열려 있고 인증되지 않은 것으로 가정합니다. 이렇게 가정하면, 앱의 인증 요구 사항과 이 엔드포인트의 외부 호출자 요구 사항 간의 혼동을 피할 수 있습니다. 이 시나리오는 외부 호출자를 보여주지 않습니다.

> [!div class="nextstepaction"]
> [2부 - 인증 요구 사항 >>>](walkthrough-tutorial-authentication-02.md)
