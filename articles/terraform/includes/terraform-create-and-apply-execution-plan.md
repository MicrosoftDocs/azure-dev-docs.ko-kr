---
title: 포함 파일
description: 포함 파일
author: tomarchermsft
ms.service: terraform
ms.topic: include
ms.date: 09/27/2020
ms.author: tarcher
ms.openlocfilehash: 23a5bbc2e6a7e364b3c6eab38bfd5e98b97348a6
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401565"
---
## <a name="create-and-apply-a-terraform-execution-plan"></a>Terraform 실행 계획 만들기 및 적용

이 섹션에서는 *실행 계획*을 만들고 클라우드 인프라에 적용하는 방법을 알아봅니다.

1. Terraform 배포를 초기화하려면 [terraform init](https://www.terraform.io/docs/commands/init.html)를 실행합니다. 이 명령은 Azure 리소스 그룹을 만드는 데 필요한 Azure 모듈을 다운로드합니다.

    ```cmd
    terraform init
    ```

1. 초기화 후에는 [terraform plan](https://www.terraform.io/docs/commands/plan.html)을 실행하여 실행 계획을 만듭니다.

    ```cmd
    terraform plan -out <terraform_plan>.tfplan
    ```

    [!INCLUDE [terraform-plan-notes.md](terraform-plan-notes.md)]

1. 실행 계획을 클라우드 인프라에 적용할 준비가 되면 [terraform apply](https://www.terraform.io/docs/commands/apply.html)를 실행합니다.

    ```cmd
    terraform apply <terraform_plan>.tfplan
    ```
