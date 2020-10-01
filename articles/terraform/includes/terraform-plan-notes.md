---
title: 포함 파일
description: 포함 파일
author: tomarchermsft
ms.service: terraform
ms.topic: include
ms.date: 09/27/2020
ms.author: tarcher
ms.openlocfilehash: 9f3d916bc37cc9d5f86c1f6b2738f419414e5816
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401562"
---
  **참고:**

  - `terraform plan` 명령은 실행 계획을 만들지만 실행하지는 않습니다. 대신 구성 파일에 지정된 구성을 만드는 데 필요한 작업을 결정합니다. 이 패턴을 사용하면 실제 리소스를 변경하기 전에 실행 계획이 예상과 일치하는지 확인할 수 있습니다.
  - 선택 사항인 `-out` 매개 변수를 사용하여 계획의 출력 파일을 지정할 수 있습니다. `-out` 매개 변수를 사용하면 검토한 계획이 정확하게 적용됩니다.
  - 실행 계획 및 보안을 유지하는 방법에 대한 자세한 내용은 [보안 경고 섹션](https://www.terraform.io/docs/commands/plan.html#security-warning)을 참조하세요.