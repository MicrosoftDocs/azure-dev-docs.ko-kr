---
title: 포함 파일
description: 포함 파일
author: tomarchermsft
ms.service: terraform
ms.topic: include
ms.date: 09/27/2020
ms.author: tarcher
ms.openlocfilehash: daa57dfc18cae3250c42265ebe8cbf7722e4235b
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401591"
---
## <a name="configure-your-environment"></a>환경 구성

사용자 환경에 따라 Terraform을 설치하고 구성합니다.

- [Azure Cloud Shell 및 Azure CLI를 사용하여 Terraform 구성](../get-started-cloud-shell.md)
- [Azure PowerShell을 사용하여 Terraform 구성](../get-started-powershell.md)

구성 문서에서는 다음 작업을 수행하는 방법에 대해서도 설명합니다.

- 기본 Terraform 구성 파일을 만듭니다. 이 파일은 `provider` 블록에 [Azure 공급자(azurerm)](https://www.terraform.io/docs/providers/azurerm/index.html)가 포함되어 있으며 [Azure 리소스 그룹](/azure/azure-resource-manager/management/manage-resource-groups-portal#what-is-a-resource-group)을 정의합니다.
- Terraform 실행 계획을 만들고 적용하여 코드를 "실행"합니다.
- 리소스 사용을 완료하고 삭제하려는 경우 실행 계획을 되돌립니다.
