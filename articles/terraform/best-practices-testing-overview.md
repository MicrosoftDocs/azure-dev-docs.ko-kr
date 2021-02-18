---
title: 자습서 - Terraform 테스트 개요
description: Terraform 프로젝트의 유효성을 검사하기 위해 구성할 수 있는 다양한 테스트 옵션에 대해 알아봅니다.
ms.topic: overview
ms.date: 07/31/2020
ms.custom: devx-track-terraform
adobe-target: true
ms.openlocfilehash: ede7fdfc1dafedacfdc74f657fee0a4ae11feb85
ms.sourcegitcommit: b380f6e637b47e6e3822b364136853e1d342d5cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100395328"
---
# <a name="tutorial-terraform-testing-overview"></a>자습서: Terraform 테스트 개요

[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

Terraform은 IaC(Infrastructure as Code) 도구입니다. 이 도구의 범주는 프로젝트의 소스 코드와 마찬가지로 Terraform 파일을 처리한다는 사실을 나타냅니다. 이 프로세스의 일부에는 버전 관리 및 소스 코드 제어가 포함됩니다. 또한 테스트도 프로세스의 일부여야 합니다. 이 문서에서는 Terraform 프로젝트에 대해 실행할 수 있는 다양한 유형의 테스트에 대해 간략히 설명합니다.

## <a name="integration-testing"></a>통합 테스트

통합 테스트를 통해 새로 도입된 코드 변경이 기존 코드를 손상시키지 않는지 확인합니다. DevOps에서 CI(연속 통합)는 PR을 Git 리포지토리에 병합하려는 사용자처럼 코드베이스가 변경될 때마다 전체 시스템을 빌드하는 프로세스를 나타냅니다. 다음 목록에는 통합 테스트의 일반적인 예가 나와 있습니다.

- lint 및 형식과 같은 정적 코드 분석 도구입니다.
- [terraform validate](https://www.terraform.io/docs/commands/validate.html)를 실행하여 구성 파일의 구문을 확인합니다.
- [terraform plan](https://www.terraform.io/docs/commands/validate.html)을 실행하여 구성이 예상대로 작동하는지 확인합니다.

> [!div class="nextstepaction"]
> [통합 테스트에 대한 자세한 정보](best-practices-integration-testing.md)

## <a name="unit-testing"></a>단위 테스트

단위 테스트는 프로그램의 특정 부분 또는 기능이 올바르게 작동하는지 확인합니다. 단위 테스트는 기능 개발자가 작성합니다. TDD 또는 테스트 기반 개발이라고도 하는 이 유형의 테스트에는 지속적인 짧은 개발 주기가 포함됩니다. Terraform 프로젝트의 컨텍스트에서 단위 테스트는 `terraform plan`을 사용하는 형식을 통해 생성된 계획에서 사용할 수 있는 실제 값이 예상 값과 동일한지 확인할 수 있습니다. 

다음과 같이 Terraform 모듈이 더 복잡해지는 경우 단위 테스트가 특히 유용할 수 있습니다.

- 동적 블록 생성
- 반복 사용
- 로컬 변수 계산

통합 테스트와 마찬가지로 단위 테스트가 연속 통합 프로세스에 포함되는 경우가 많습니다.

## <a name="compliance-testing"></a>호환성 테스트

호환성 테스트는 구성에서 프로젝트에 대해 정의한 정책을 따르는지 확인하는 데 사용됩니다. 예를 들어 Azure 리소스에 대한 지정학적 명명 규칙을 정의할 수 있습니다. 또는 정의된 이미지 하위 집합에서 가상 머신을 만들려고 할 수도 있습니다. 호환성 테스트는 이러한 규칙을 적용하는 데 사용됩니다.

또한 호환성 테스트는 일반적으로 연속 통합 프로세스의 일부로 정의됩니다.

> [!div class="nextstepaction"]
> [호환성 테스트에 대한 자세한 정보](best-practices-compliance-testing.md)

## <a name="end-to-end-e2e-testing"></a>E2E(엔드투엔드) 테스트

E2E 테스트는 프로덕션에 배포하기 전에 프로그램 작동의 유효성을 검사합니다. 시나리오 예제는 두 개의 가상 머신을 가상 네트워크에 배포하는 Terraform 모듈일 수 있습니다. 두 대의 컴퓨터에서 서로 ping하지 못하도록 방지하는 것이 좋습니다. 이 예제에서는 배포하기 전에 의도한 결과를 확인하는 테스트를 정의할 수 있습니다.

E2E 테스트는 일반적으로 3단계 프로세스입니다. 먼저, 구성이 테스트 환경에 적용됩니다. 다음으로, 코드를 실행하여 결과를 확인합니다. 마지막으로, 테스트 환경이 다시 초기화되거나 제거됩니다(예: 가상 머신 할당 취소).

> [!div class="nextstepaction"]
> [엔드투엔드 테스트에 대한 자세한 정보](best-practices-end-to-end-testing.md)

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]
