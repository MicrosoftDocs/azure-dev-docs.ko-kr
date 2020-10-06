---
title: 포함 파일
description: 포함 파일
author: tomarchermsft
ms.service: terraform
ms.topic: include
ms.date: 09/27/2020
ms.author: tarcher
ms.openlocfilehash: 9fcde450a19515c38eb6653d75e644ca23d08964
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401564"
---
## <a name="create-a-base-terraform-configuration-file"></a>기본 Terraform 구성 파일 만들기

공급자를 지정하면 Terraform 구성 파일이 시작됩니다. Azure를 사용하는 경우 `provider` 블록에 [Azure 공급자(azurerm)](https://www.terraform.io/docs/providers/azurerm/index.html)를 지정합니다.

```terraform
provider "azurerm" {
  version = "~>2.0"
  features {}
}

resource "azurerm_resource_group" "rg" {
  name = "<your_resource_group_name>"
  location = "<your_resource_group_location>"
}

# Your Terraform code goes here...

```

**참고**:

- `version` 특성은 선택 사항이지만 HashiCorp에서는 지정된 버전의 공급자로 고정할 것을 권장합니다. 
- Azure 공급자 1.x를 사용 중인 경우 `features` 블록을 사용할 수 없습니다.
- Azure 공급자 2.x를 사용 중인 경우 `features` 블록이 필요합니다.
- [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html)의 [리소스 선언](https://www.terraform.io/docs/configuration/resources.html)에는 `name` 및 `location`이라는 두 인수가 있습니다. 자리 표시자는 사용자 환경에 적절한 값으로 설정하세요.
- 리소스 그룹의 [로컬 명명 값](https://www.terraform.io/docs/configuration/expressions.html#references-to-named-values) `rg`는 방법 및 자습서 문서 전체에서 리소스 그룹을 참조할 때 사용됩니다. 이는 리소스 그룹 이름과 독립적이며, 코드의 변수 이름만 참조합니다. 리소스 그룹 정의에서 이 값을 변경하는 경우 이 값을 참조하는 코드에서도 변경해야 합니다.
