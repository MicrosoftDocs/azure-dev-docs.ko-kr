---
title: Azure 용어 참고 자료
description: Microsoft Azure를 사용할 때 알아야 하는 가장 중요한 용어와 개념을 정리한 목록입니다.
ms.date: 12/07/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 0be44d246af0f34b60b9ca1403f1889cc5708e9c
ms.sourcegitcommit: 1901759f41adfac3c3f2ff135bcf72206543b639
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96934298"
---
# <a name="azure-terminology-in-brief"></a>간단한 Azure 용어

| 용어 | 간단한 설명 |
| --- | --- |
| [계정 및 구독](#account-and-subscriptions) | Azure에서 리소스를 관리하기 위한 청구 정보 및 기본 조직 구조입니다. [자세히...](#account-and-subscriptions)
| [리소스](#resource) | Azure 데이터 센터 내부에서 이루어지는 특정 기능 할당의 일반적인 이름입니다. [자세히...](#resource) |
| [리소스 그룹](#resource-group) | 한 단위로 관리할 수 있는 다른 리소스에 대한 논리적 컨테이너입니다. [자세히...](#resource-group) |
| [지역](#region-location) | 리소스가 할당된 특정 Azure 데이터 센터에 대한 참조입니다. [자세히...](#region-location) |
| [Azure App Service](#azure-app-service) | 웹 애플리케이션을 위한 Azure의 관리형 호스팅 서비스입니다. [자세히...](#azure-app-service) |
| [App Service 계획](#app-service-plan) | Azure App Service에서 사용하는 가상 머신을 정의하는 리소스입니다. [자세히...](#app-service-plan) |
| [Azure Portal](#azure-portal) | Azure 리소스를 만들고 관리하는 데 사용되는 웹 기반 UI입니다. [자세히...](#azure-portal) |
| [Azure CLI](#azure-command-line-interface-cli) | Azure 리소스를 만들고 관리하는 데 사용되는 텍스트 기반 명령 세트입니다. [자세히...](#azure-command-line-interface-cli) |
| [az webapp, az webapp up](#az-webapp-az-webapp-up) | Azure App Service를 작업하는 Azure CLI 명령입니다. [자세히...](#az-webapp-az-webapp-up) |

## <a name="account-and-subscriptions"></a>계정 및 구독

**Azure 계정** 에는 기본 연락처 정보(전화 번호, 이메일 주소) 및 청구 정보(신용 카드)가 포함되어 있습니다.

단일 청구 계정 내에서 활동을 하나 이상의 **구독** 에 구성할 수 있습니다. 구독마다 자체 권한이 있으므로 동일한 계정 아래에 유지되는 개인 또는 부서에 대한 별도의 구독을 만들 수 있습니다. 개인이 액세스 권한을 가질 수 있는 구독 수에는 제한이 없습니다.

Azure에서 리소스 만들기는 항상 구독의 컨텍스트에서 수행됩니다. [구독 간에 리소스를 이동](/azure/azure-resource-manager/management/move-resource-group-and-subscription)할 수 있지만 계정 간에는 이동할 수 없습니다.

## <a name="resource"></a>리소스

**리소스** 는 Azure 데이터 센터 내부에서 이루어지는 특정 기능 할당의 일반적인 이름입니다. 리소스가 될 수 있는 것은 가상 머신, 가상 네트워크, 다양한 수준의 스토리지, 데이터베이스, 기계 학습 모델, IoT 수집 허브 등입니다.

리소스는 실제 컴퓨팅 기능을 할당하기 때문에 필요한 성능 수준에 따라 각 리소스에서 지속적으로 비용이 발생할 수 있습니다. 개발 및 테스트가 목적인 경우 무료 비용 계층으로 여러 리소스를 만들 수 있습니다. 자세한 내용은 [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 참조하세요.

## <a name="resource-group"></a>Resource group

구독 내부에서 *리소스 그룹* 은 한 단위로 관리할 수 있는 다른 리소스에 대한 논리적 컨테이너입니다.

일반적으로 리소스 그룹은 특정 프로젝트와 관련이 있으며, 리소스를 프로비저닝할 때 항상 리소스 그룹을 지정해야 합니다. 새 프로젝트를 만들 때 첫 번째 단계는 일반적으로 적절한 리소스 그룹을 만드는 것입니다.

리소스 그룹을 삭제하면 그 안에 들어 있는 리소스도 할당 취소 및 삭제되며, 각 리소스를 개별적으로 삭제하는 것보다 편리한 방법입니다.

## <a name="region-location"></a>지역(위치)

**지역** 은 Azure 데이터 센터에서 리소스가 프로비저닝되는 특정 위치를 식별합니다.

서로 다른 지역에 있는 리소스가 언제든지 서로 통신할 수 있지만, 리소스가 같은 지역에 있을 때 보다 효율적인 통신이 이루어집니다.

글로벌 고객에게 서비스를 제공하는 애플리케이션의 경우 Azure가 여러 지역에 리소스를 자동으로 복제하도록 설정하면 애플리케이션의 전반적인 응답성과 성능을 향상시킬 수 있습니다.

## <a name="azure-app-service"></a>Azure App Service

**App Service** 는 웹 애플리케이션을 위한 Azure의 관리형 호스팅 서비스입니다. 모든 기본 하드웨어와 서버 인프라를 Azure가 관리하며, 개발자는 코드와 구성을 제공합니다.

App Service를 사용하여 App Service **웹앱** 이라는 호스트를 프로비저닝하고 코드를 업로드합니다. 부하 분산, 크기 조정, 서버 쪽 환경 변수 등과 같은 호스트의 다양한 특성도 구성합니다.

App Service 웹앱의 직접 URL은 항상 `<web-app-name>.azurewebsites.net`입니다. 또한 사용자 지정 도메인을 사용하도록 웹앱을 구성할 수 있습니다.

## <a name="app-service-plan"></a>App Service 플랜

**App Service 요금제** 는 Azure App Service에 사용되는 가상 머신을 정의하는 리소스이며, 이 요금제에 따라 애플리케이션을 호스트하는 핵심 비용이 결정됩니다. 코드를 배포하기 전에 App Service를 프로비저닝할 때 App Service 요금제를 정의합니다.

## <a name="azure-portal"></a>Azure portal

[Azure Portal](https://portal.azure.com)은 Azure 계정, 구독 및 리소스를 사용할 수 있는 웹 기반 사용자 인터페이스입니다.

## <a name="azure-command-line-interface-cli"></a>Azure CLI(명령줄 인터페이스)

[Azure CLI](/azure/what-is-azure-cli)는 Azure 리소스를 만들고 관리하는 데 사용되는 명령 세트이며, 특히 자동화에 유용합니다. Azure CLI는 모든 운영 체제에서 사용할 수 있으며 대부분의 Azure 서비스에서 작동합니다.

PowerShell을 선호하는 경우 [Azure PowerShell 모듈](/powershell/azure)을 사용해도 됩니다.

## <a name="az-webapp-az-webapp-up"></a>az webapp, az webapp up

Azure CLI에서 **az webapp** 명령을 사용하여 Azure App Service의 모든 것을 조작할 수 있습니다.

**az webapp up** 명령은 특히 웹 애플리케이션 배포를 간소화합니다. 이 명령을 한 번만 전달하여 리소스 그룹, App Service 요금제 및 App Service 웹앱을 프로비저닝한 다음, 코드를 업로드할 수 있습니다.

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [클라우드 개발 개요 >>>](cloud-development-overview.md)
