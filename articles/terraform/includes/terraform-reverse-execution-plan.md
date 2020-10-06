---
title: 포함 파일
description: 포함 파일
author: tomarchermsft
ms.service: terraform
ms.topic: include
ms.date: 09/27/2020
ms.author: tarcher
ms.openlocfilehash: 718ac8c480c5d366e985f9488d81ab626a82b586
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401563"
---
## <a name="reverse-a-terraform-execution-plan"></a>Terraform 실행 계획 되돌리기

1. 실행 계획을 되돌리거나 취소하려면 [terraform plan](https://www.terraform.io/docs/commands/plan.html)을 실행하고 다음과 같이 `destroy` 플래그를 지정합니다.

    ```cmd
    terraform plan -destroy -out <terraform_plan>.destroy.tfplan
    ```

    [!INCLUDE [terraform-plan-notes.md](terraform-plan-notes.md)]

1. [terraform apply](https://www.terraform.io/docs/commands/apply.html)를 실행하여 실행 계획을 적용합니다.

    ```cmd
    terraform apply <terraform_plan>.destroy.tfplan
    ```
