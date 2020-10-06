---
title: Azure에서 Terraform 사용
description: Terraform을 통해 Azure에서 인프라를 배포 및 버전 관리하는 방법을 알아봅니다.
ms.topic: overview
ms.date: 10/26/2019
ms.custom: devx-track-terraform
ms.openlocfilehash: 0b38c7c2d518087f7a49c0d7e4f462d7da7eaf76
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401753"
---
# <a name="terraform-with-azure"></a>Azure의 Terraform

[Hashicorp Terraform](https://www.terraform.io/)은 클라우드 인프라를 프로비전하고 관리하기 위한 오픈 소스 도구입니다. 클라우드 리소스의 토폴로지를 설명하는 구성 파일의 인프라를 체계화합니다. 이러한 리소스에는 가상 머신, 스토리지 계정 및 네트워킹 인터페이스가 포함됩니다. Terraform CLI는 Azure에 구성 파일을 배포하고 버전을 지정하는 간단한 메커니즘을 제공합니다.

이 문서에서는 Terraform을 사용하여 Azure 인프라를 관리하는 이점에 대해 설명합니다.

## <a name="automate-infrastructure-management"></a>인프라 관리 자동화

Terraform의 템플릿 기반 구성 파일을 사용하면 반복 가능하고 예측 가능한 방식으로 Azure 리소스를 정의하고, 프로비전하고 구성할 수 있습니다. 인프라 자동화에는 다음과 같은 몇 가지 이점이 있습니다.

- 인프라를 배포하고 관리하는 동안 사람의 실수 가능성을 줄입니다.
- 동일한 템플릿을 여러 번 배포하여 동일한 개발, 테스트 및 프로덕션 환경을 만듭니다.
- 개발 및 테스트 환경을 요청 시 만들어서 비용을 줄입니다.

## <a name="understand-infrastructure-changes-before-being-applied"></a>적용하기 전에 인프라 변경 내용 이해

리소스 토폴로지가 복잡해지면서 인프라 변경의 의미와 영향을 이해하는 것이 어려울 수 있습니다.

Terraform CLI를 통해 사용자는 애플리케이션 전에 인프라 변경 내용을 확인하고 미리 볼 수 있습니다. 인프라 변경 사항을 안전한 방식으로 미리 보면 다음과 같은 여러 가지 이점이 있습니다.
- 제안된 변경 사항과 그 영향을 팀 멤버가 신속하게 이해하기 때문에 보다 효과적으로 협업할 수 있습니다.
- 의도하지 않은 변경을 개발 프로세스 초기에 파악할 수 있습니다.

## <a name="deploy-infrastructure-to-multiple-clouds"></a>여러 클라우드에 인프라 배포

Terraform은 여러 클라우드 공급자에 걸쳐 인프라를 배포하는 데 능숙합니다. 이를 통해 개발자는 일관된 도구를 사용하여 각 인프라 정의를 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Terraform에 대한 개요와 이점에 대해 간략히 살펴본 후 권장되는 다음 단계는 아래와 같습니다.

사용자 환경에 따라 Terraform을 설치하고 구성합니다.

- [Azure Cloud Shell 및 Azure CLI를 사용하여 Terraform 구성](get-started-cloud-shell.md)
- [Azure PowerShell을 사용하여 Terraform 구성](get-started-powershell.md)
