---
title: Azure 개발 흐름
description: 프로비저닝, 코딩, 테스트, 배포 및 관리로 구성되는 Azure의 클라우드 개발 주기에 대한 개요입니다.
ms.date: 02/16/2021
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: c2187f43da3159cc623e2ace671561ece2cf1758
ms.sourcegitcommit: b882128a763f81dba83913bfff1e9cd1ec70818f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642289"
---
# <a name="the-azure-development-flow-provision-code-test-deploy-and-manage"></a>Azure 개발 흐름: 프로비저닝, 코딩, 테스트, 배포 및 관리

[이전 문서: 리소스 프로비저닝, 액세스 및 관리](cloud-development-provisioning.md)

Azure의 서비스 및 리소스 모델을 알아보았으므로, Azure에서 클라우드 애플리케이션을 개발하는 **프로비저닝**, **코딩**, **테스트**, **배포** 및 **관리** 흐름을 전체적으로 이해할 수 있습니다.

| 단계 | 기본 도구 | 활동 |
| --- | --- | --- |
| 프로비전 | Azure 관리 라이브러리를 사용하는 Azure CLI, Azure Portal, Cloud Shell, Python 스크립트 | 리소스 그룹을 프로비저닝하고, 특정 리소스를 해당 그룹에 프로비저닝하고, 앱 코드에서 사용할 수 있고/있거나 배포 시 Python 코드를 수신하도록 리소스를 구성합니다. |
| 코드 | 코드 편집기(예: Visual Studio Code), Azure 라이브러리, 참조 설명서 | Azure 클라이언트 라이브러리를 사용하여 프로비저닝된 리소스와 상호 작용하는 Python 코드를 작성합니다. |
| 테스트 | Python 런타임, 디버거 | 활성 클라우드 리소스(일반적으로 프로덕션 리소스 대신 개발 또는 테스트 리소스)에 대해 로컬로 Python 코드를 실행합니다. 코드 자체는 아직 Azure에 호스트되지 않았으므로 신속하게 디버그하고 반복할 수 있습니다. |
| 배포 | Azure CLI, GitHub, DevOps | 코드를 로컬로 테스트한 후에는 코드 자체가 클라우드에서 실행될 수 있는 적절한 Azure 호스팅 서비스에 코드를 배포합니다. 배포된 코드는 일반적으로 준비 또는 프로덕션 리소스에 대해 실행됩니다. |
| 관리 | Azure CLI, Azure Portal, Python 스크립트, Azure Monitor | 앱 성능과 응답성을 모니터링하고, 프로덕션 환경을 조정하고, 다음 프로비저닝과 개발을 위해 개선된 내용을 개발 환경으로 마이그레이션합니다. |

## <a name="step-1-provision-and-configure-resources"></a>1단계: 리소스 프로비저닝 및 구성

[이 시리즈의 이전 문서](cloud-development-provisioning.md)에서 설명했듯이, 애플리케이션 개발의 첫 번째 단계는 애플리케이션의 대상 환경을 구성하는 리소스를 프로비저닝하고 구성하는 것입니다.

프로비저닝은 적절한 Azure 지역에 리소스 그룹을 만드는 것으로 시작합니다. Azure Portal, Azure CLI 또는 Azure 라이브러리(또는 REST API)를 사용하는 사용자 지정 스크립트를 통해 리소스 그룹을 만들 수 있습니다.

그런 다음, 해당 리소스 그룹 내에서 다시 한 번 포털, CLI 또는 Azure 라이브러리를 사용하여 필요한 개별 리소스를 프로비저닝하고 구성합니다. (다시 말하지만, 사용 가능한 리소스 종류에 대한 개요를 보려면 [Azure 개발자 가이드](/azure/guides/developer/azure-developer-guide)를 살펴보세요.)

구성에는 이러한 리소스에 액세스할 수 있는 ID(서비스 주체 및/또는 애플리케이션 ID)를 제어하는 액세스 정책 설정이 포함됩니다. 액세스 정책은 일반적으로 [RBAC(역할 기반 액세스 제어)](/azure/role-based-access-control/overview)를 통해 관리됩니다. 일부 서비스에는 보다 구체적인 액세스 제어도 있습니다. Azure를 사용하는 클라우드 개발자는 RBAC를 보안 문제가 있는 리소스에만 사용하기 때문에 RBAC에 대해 잘 알고 있어야 합니다.

대부분의 애플리케이션 시나리오에서는 일반적으로 Azure CLI 및/또는 Azure 라이브러리를 사용하는 Python 코드로 프로비저닝 스크립트를 만듭니다. 이러한 스크립트는 애플리케이션의 모든 리소스 요구 사항을 설명합니다(기본적으로 애플리케이션이 배포되는 사용자 지정 클라우드 컴퓨터를 정의함). 스크립트를 사용하면 Azure Portal에서 반복되는 여러 단계를 수동으로 수행할 필요 없이 여러 개발, 테스트, 준비 및 프로덕션 환경 내에서 동일한 리소스 세트를 쉽게 다시 만들 수 있습니다. 또한 이러한 스크립트를 사용하면 간단하게 다른 지역에서 환경을 프로비저닝하거나 다른 리소스 그룹을 사용할 수 있습니다. 또한 소스 제어 리포지토리에 이러한 스크립트를 유지하면 전체 감사 및 변경 기록을 확보할 수 있습니다.

## <a name="step-2-write-your-app-code-to-use-resources"></a>2단계: 리소스를 사용하는 앱 코드 작성

애플리케이션에 필요한 리소스를 프로비저닝한 후에는 해당 리소스의 런타임 요소와 함께 작동하도록 애플리케이션 코드를 작성합니다.

예를 들어 프로비저닝 단계에서 Azure 스토리지 계정을 만들고 해당 계정 내에 BLOB 컨테이너를 만든 다음, 해당 컨테이너에서 애플리케이션에 대한 액세스 정책을 설정할 수 있습니다. 이 프로비저닝 프로세스는 [예제 - Azure Storage 프로비저닝](azure-sdk-example-storage.md)에 설명되어 있습니다. 이제 코드에서 해당 스토리지 계정으로 인증한 다음, 해당 컨테이너 내에서 BLOB을 생성, 업데이트 또는 삭제할 수 있습니다. 이 런타임 프로세스는 [예제 - Azure Storage 사용](azure-sdk-example-storage.md)에 설명되어 있습니다. 마찬가지로, 애플리케이션 코드에서 데이터베이스에 연결하고 일상적인 만들기-읽기-업데이트-삭제 쿼리를 수행할 수 있도록 스키마와 적절한 권한을 사용하여 데이터베이스를 프로비저닝할 수 있습니다(자세한 내용은 [예제 - Azure Storage 사용](azure-sdk-example-database.md) 참조).

앱 코드는 일반적으로 환경 변수를 통해 사용하려는 리소스의 이름과 URL을 식별합니다. 환경 변수를 사용하면 코드를 변경하지 않고 클라우드 환경(개발, 테스트, 준비 및 프로덕션) 간에 쉽게 전환할 수 있습니다. 애플리케이션 코드를 호스팅하는 다양한 Azure 서비스는 필요한 변수를 정의하는 수단을 제공합니다. 예를 들어 웹앱을 호스팅하는 Azure App Service 및 Azure Functions(Azure의 서버리스 호스트)에서는 Azure Portal 또는 Azure CLI를 통해 *애플리케이션 설정* 을 정의합니다. 그러면 코드에 환경 변수로 표시됩니다.

Python 개발자는 대부분 Python에서 Python용 Azure 라이브러리를 사용하여 애플리케이션 코드를 작성합니다. 즉, 클라우드 애플리케이션의 독립적인 부분은 지원되는 언어로 작성할 수 있습니다. 예를 들어 다양한 언어 전문가들이 있는 팀에서 일하는 경우 애플리케이션의 일부는 Python, 일부는 JavaScript, 일부는 Java로 작성하고 나머지는 C#으로 작성하는 것이 가능합니다.

애플리케이션 코드에서는 필요할 때 Azure 라이브러리를 사용하여 프로비저닝 및 관리 작업을 수행할 수 있습니다. 마찬가지로 프로비저닝 스크립트에서는 라이브러리를 사용하여 특정 데이터가 포함된 리소스를 초기화하거나, 해당 스크립트가 로컬로 실행될 때 클라우드 리소스에 대한 정리 작업을 수행할 수 있습니다.

## <a name="step-3-test-and-debug-your-app-code-locally"></a>3단계: 로컬로 앱 코드 테스트 및 디버그

개발자는 일반적으로 로컬 워크스테이션에서 앱 코드를 테스트한 후 클라우드에 배포하는 것을 좋아합니다. 앱 코드를 로컬로 테스트한다는 것은 스토리지, 데이터베이스 등과 같이 이미 클라우드에서 프로비저닝한 다른 리소스에 주로 액세스한다는 의미입니다. 차이점은 클라우드 서비스 내에서 앱 코드 자체를 아직 실행하지 않는다는 것입니다.

코드를 로컬로 실행하면 Visual Studio Code 같은 도구에서 제공하는 디버깅 기능을 모두 활용하고 소스 제어 리포지토리의 코드를 관리할 수 있습니다.

로컬 테스트를 위해 코드를 수정할 필요가 전혀 없습니다. Azure는 개발자가 클라우드에 배포하는 코드와 동일한 코드를 사용한 로컬 개발 및 디버깅을 완벽하게 지원합니다. 환경 변수는 키입니다. 클라우드에서 코드는 호스팅 리소스의 설정에 환경 변수로 액세스할 수 있습니다. 동일한 환경 변수를 로컬로 만들면 동일한 코드가 수정 없이 실행됩니다. 이 패턴은 인증 자격 증명, 리소스 URL, 연결 문자열 및 기타 여러 설정에서 작동하므로, 코드를 로컬로 실행할 때 개발 환경에서 리소스를 쉽게 사용할 수 있고 코드가 클라우드에 배포된 후 프로덕션 리소스를 쉽게 사용할 수 있습니다.

## <a name="step-4-deploy-your-app-code-to-azure"></a>4단계: Azure에 앱 코드 배포

로컬로 코드를 테스트한 후에는 코드를 호스팅하기 위해 프로비저닝한 Azure 리소스에 코드를 배포할 수 있습니다. 예를 들어 Django 웹 애플리케이션을 작성하는 경우 해당 코드를 가상 머신(개발자 고유의 웹 서버를 제공하는 위치) 또는 Azure App Service(개발자 대신 웹 서비스 제공)에 배포합니다. 배포된 코드는 로컬 머신이 아닌 서버에서 실행되며, 권한이 부여된 모든 Azure 리소스에 액세스할 수 있습니다.

이전 섹션에서 설명했듯이, 일반적인 개발 프로세스에서는 개발 환경에서 프로비저닝한 리소스에 코드를 먼저 배포합니다. 테스트를 마친 후에는 테스트 팀과 경우에 따라 미리 보기 고객이 애플리케이션을 사용할 수 있도록 스테이징 환경의 리소스에 코드를 배포합니다. 애플리케이션 성능이 만족스러우면 프로덕션 환경에 코드를 배포합니다. Azure DevOps를 사용하여 지속적인 통합 및 지속적인 배포를 통해 이 모든 배포 과정을 자동화할 수도 있습니다.

그러나 이렇게 하면 일단 코드가 클라우드에 배포되고 나면 완전한 클라우드 애플리케이션이 되어 Azure 데이터 센터의 서버 컴퓨터에서만 실행됩니다.

## <a name="step-5-manage-monitor-and-revise"></a>5단계: 관리, 모니터링 및 수정

배포 후에는 애플리케이션이 정상적으로 작동하면서 고객 요청에 응답하고 리소스를 효율적으로(그리고 가장 저렴한 비용으로) 사용하는지 확인해야 합니다. Azure가 필요에 따라 배포를 자동으로 스케일링하는 방식을 관리할 수 있으며 Azure Portal, Azure CLI 또는 Azure 라이브러리로 작성된 사용자 지정 스크립트를 통해 성능 데이터를 수집하고 모니터링할 수 있습니다. 그런 다음, 다시 한 번 같은 도구를 사용하여 프로비저닝된 리소스를 실시간으로 조정하면서 성능을 최적화할 수 있습니다.

모니터링을 통해 클라우드 애플리케이션을 어떻게 재구성해야 하는지 인사이트를 얻을 수 있습니다. 예를 들어 웹앱의 특정 부분(예: API 엔드포인트 그룹)이 주요 부분에 비해 아주 가끔 사용되는 것을 발견하는 경우가 있습니다. 이 경우 해당 API를 서버리스 Azure Functions로 별도로 배포할 수 있으며, 이렇게 하면 주 애플리케이션과 경쟁하지 않는 고유의 지원 컴퓨팅 리소스를 사용할 수 있고 그 비용은 한 달에 동전 몇 개에 불과합니다. 주 애플리케이션은 더 많은 비용이 드는 계층으로 스케일 업할 필요 없이 더 많은 고객에게 더 빠르게 응답할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure의 기본 구조와 리소스 프로비저닝, 코드 작성 및 테스트, Azure에 코드 배포, 리소스 모니터링 및 관리로 이어지는 전반적인 개발 흐름을 알아보았습니다.

다음 단계는 흐름의 여러 부분에서 사용되는 Python용 Azure 라이브러리를 익히는 것입니다.

> [!div class="nextstepaction"]
> [Python용 Azure 라이브러리 사용 방법 알아보기 >>>](azure-sdk-overview.md)
