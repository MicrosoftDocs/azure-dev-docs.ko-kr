---
title: 포함 파일
description: 포함 파일
author: tomarchermsft
ms.service: terraform
ms.topic: include
ms.date: 05/31/2020
ms.author: tarcher
ms.openlocfilehash: 43e684a44c657ab44f3f8f13719e08f0e339f498
ms.sourcegitcommit: db56786f046a3bde1bd9b0169b4f62f0c1970899
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329901"
---
[Terraform on Azure](https://www.terraform.io/docs/providers/azurerm/index.html)를 구성하고 기본 Azure 리소스 그룹을 만들어 [Terraform](https://www.terraform.io)을 시작합니다.

Terraform은 클라우드 인프라의 정의, 미리 보기 및 배포를 사용합니다. Terraform을 사용하는 경우 [HCL 구문](https://www.terraform.io/docs/configuration/syntax.html)를 사용하여 구성 파일을 만듭니다. HCL 구문을 사용하면 클라우드 공급자(예: Azure) 그리고 클라우드 인프라를 구성하는 요소를 지정할 수 있습니다. 구성 파일을 만든 후 배포되기 전에 인프라 변경을 미리 볼 수 있는 *실행 계획*를 만듭니다. 변경 내용을 확인 한 후에는 실행 계획을 적용하여 인프라를 배포합니다.
