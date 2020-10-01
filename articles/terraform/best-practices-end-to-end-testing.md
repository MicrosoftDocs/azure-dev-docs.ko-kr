---
title: 자습서 - Terraform 프로젝트에서 엔드투엔드 Terratest 테스트 설정
description: Terraform 프로젝트에서 Terratest를 사용한 엔드투엔드 테스트에 대해 자세히 알아봅니다.
ms.topic: tutorial
ms.date: 07/31/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: b760908bf1950751b93ba1787f444ca37ee8bf83
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401713"
---
# <a name="tutorial-setup-end-to-end-terratest-testing-on-terraform-projects"></a>자습서: Terraform 프로젝트에서 엔드투엔드 Terratest 테스트 설정

[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

이 문서에서는 다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * [Terratest](https://github.com/gruntwork-io/terratest)를 사용한 엔드투엔드 테스트의 기본 사항 이해
> * Golang을 사용하여 엔드투엔드 테스트를 작성하는 방법 알아보기
> * 코드가 리포지토리에 커밋될 때 Azure DevOps를 사용하여 엔드투엔드 테스트를 자동으로 트리거하는 방법 알아보기

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Terraform 설치**: 사용자 환경에 따라 [Terraform을 다운로드하여 설치](https://www.terraform.io/downloads.html)합니다.
- **테스트 샘플 포크:** 빠르게 시작하려면 [이 리포지토리](https://github.com/Azure/terraform)를 사용자 고유의 GitHub 조직으로 포크하는 것이 좋습니다.
- **Go 프로그래밍 언어**: Terraform 테스트 사례는 [Go](https://golang.org/dl/)로 작성됩니다. 이 문서의 샘플에서는 [Go 모듈](https://blog.golang.org/using-go-modules)을 사용합니다. 이 문서에는 Go 1.13 이상이 권장됩니다.

## <a name="what-is-end-to-end-testing"></a>엔드투엔드 테스트 정의

엔드투엔드 테스트는 시스템이 전체적으로 작동하는지 확인합니다. 이러한 유형의 테스트는 특정 모듈을 테스트하는 것과 반대입니다. Terraform 프로젝트의 경우 엔드투엔드 테스트를 통해 배포된 항목의 유효성을 검사할 수 있습니다. 이 유형의 테스트는 배포 전 시나리오를 테스트하는 다른 여러 유형과 다릅니다. 엔드투엔드 테스트는 여러 모듈을 포함하고 여러 리소스에 대해 작동하는 복잡한 시스템을 테스트하는 데 중요합니다. 이러한 시나리오에서 엔드투엔드 테스트는 다양한 모듈이 올바르게 상호 작용하는지 확인하는 유일한 방법입니다.

이 문서에서는 [Terratest](https://github.com/gruntwork-io/terratest)를 사용하여 엔드투엔드 테스트를 구현하는 데 중점을 둡니다. Terratest는 다음 작업을 수행하는 데 필요한 모든 통로를 제공합니다.

- Terraform 구성 배포
- Go 언어를 사용하여 배포된 항목의 유효성을 검사하는 테스트를 작성할 수 있도록 함
- 테스트를 단계로 오케스트레이션
- 배포된 인프라 분해

## <a name="tutorial-scenario"></a>자습서 시나리오

이 자습서에서는 [Azure/terraform 샘플 리포지토리](https://github.com/Azure/terraform/blob/master/samples/end-to-end-testing/README.md)에서 제공하는 샘플을 사용합니다.

이 샘플에는 두 개의 Linux 가상 머신을 동일한 가상 네트워크에 배포하는 Terraform 구성이 정의되어 있습니다. 하나의 VM(`vm-linux-1`)에는 공용 IP 주소가 있습니다. SSH 연결을 허용하는 22 포트만 열립니다. 두 번째 VM(`vm-linux-2`)에는 정의된 공용 IP 주소가 없습니다.

테스트는 다음 시나리오의 유효성을 검사해야 합니다.

- 인프라가 올바르게 배포됨
- 22 포트를 사용하여 `vm-linux-1`에 대한 SSH 세션을 열 수 있음
- `vm-linux-1`에 대한 SSH 세션을 사용하여 `vm-linux-2`를 ping할 수 있음

![엔드투엔드 테스트 시나리오 샘플](media/best-practices-end-to-end-testing/scenario.png)

[샘플을 다운로드](#prerequisites)한 경우 이 시나리오에 대한 Terraform 구성은 `src/main.tf` 파일에서 찾을 수 있습니다. 이 파일에는 위의 그림에 표시된 Azure 인프라를 배포하는 데 필요한 모든 항목이 포함되어 있습니다.

가상 머신을 만드는 데 익숙하지 않은 경우 [Terraform을 사용하여 Azure에서 인프라를 갖춘 Linux VM 만들기](create-linux-virtual-machine-with-infrastructure.md)를 참조하세요.

> [!CAUTION]
> 이 문서에 제공된 시나리오 샘플은 설명을 위한 목적으로만 제공됩니다. 엔드투엔드 테스트의 단계에 집중하기 위해 의도적으로 간단하게 유지했습니다. 공용 IP 주소를 통해 SSH 포트를 공개하는 프로덕션 가상 머신은 사용하지 않는 것이 좋습니다.

## <a name="end-to-end-test"></a>엔드투엔드 테스트

엔드투엔드 테스트는 Go 언어로 작성되며 Terratest 프레임워크를 사용합니다. [샘플을 다운로드](#prerequisites)한 경우 이는 `src/test/end2end_test.go` 파일에 정의되어 있습니다.

다음 소스 코드에서는 Terratest를 사용하는 Golang 테스트의 표준 구조를 보여 줍니다.

```Go
package test

import (
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    test_structure "github.com/gruntwork-io/terratest/modules/test-structure"
)

func TestEndToEndDeploymentScenario(t *testing.T) {
    t.Parallel()

    fixtureFolder := "../"

    // User Terratest to deploy the infrastructure
    test_structure.RunTestStage(t, "setup", func() {
        terraformOptions := &terraform.Options{
            // Indicate the directory that contains the Terraform configuration to deploy
            TerraformDir: fixtureFolder,
        }

        // Save options for later test stages
        test_structure.SaveTerraformOptions(t, fixtureFolder, terraformOptions)

        // Triggers the terraform init and terraform apply command
        terraform.InitAndApply(t, terraformOptions)
    })

    test_structure.RunTestStage(t, "validate", func() {
        // run validation checks here
        terraformOptions := test_structure.LoadTerraformOptions(t, fixtureFolder)
            publicIpAddress := terraform.Output(t, terraformOptions, "public_ip_address")
    })

    // When the test is completed, teardown the infrastructure by calling terraform destroy
    test_structure.RunTestStage(t, "teardown", func() {
        terraformOptions := test_structure.LoadTerraformOptions(t, fixtureFolder)
        terraform.Destroy(t, terraformOptions)
    })
}
```

위의 코드 조각에서 알 수 있듯이 테스트는 아래 세 단계로 구성됩니다.

- **setup**: Terraform을 실행하여 구성을 배포합니다.
- **validate**`: 유효성 검사 및 어설션을 수행합니다.
- **teardown**: 테스트가 실행된 후 인프라를 정리합니다.

다음 목록에서는 Terratest 프레임워크에서 제공하는 몇 가지 주요 함수를 보여 줍니다.

- **terraform.InitAndApply**: Go 코드에서 `terraform init` 및 `terraform apply`를 실행하도록 설정합니다.
- **terraform.Output**: 배포 출력 변수의 값을 검색합니다.
- **terraform.Destroy**: Go 코드에서 `terraform destroy` 명령을 실행합니다.
- **test_structure.LoadTerraformOptions**: 해당 상태에서 Terraform 옵션(예: 구성 및 변수)을 로드합니다.
- **test_structure.SaveTerraformOptions**: Terraform 옵션(예: 구성 및 변수)을 상태에 저장합니다.

## <a name="run-the-end-to-end-test"></a>엔드투엔드 테스트 실행

이 섹션에서는 샘플 구성 및 배포에 대해 테스트를 실행합니다. 

1. bash/터미널 창을 엽니다.

1. Azure 계정에 로그인 Azure 구독에 로그인하는 방법에 대한 자세한 내용은 [Azure 인증 섹션](get-started-cloud-shell.md#authenticate-to-azure)을 참조하세요.

1. 이 샘플 테스트를 실행하려면 홈 디렉터리에 SSH 프라이빗/공개 키 쌍 이름(`id_rsa` 및 `id_rsa.pub`)이 필요합니다. `USER`를 홈 디렉터리의 이름으로 바꿉니다.

```bash
export TEST_SSH_KEY_PATH="/home/USER/.ssh/id_rsa"
```

1. 디렉터리를 `test` 디렉터리로 변경합니다.

1. 테스트를 실행합니다.

```go
go test -v ./ -timeout 10m
```

테스트에서 다음 출력과 비슷한 결과를 표시합니다.

```output
--- PASS: TestEndToEndDeploymentScenario (390.99s)
PASS
ok      test    391.052s
```

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Terraform 테스트 개요](best-practices-testing-overview.md)