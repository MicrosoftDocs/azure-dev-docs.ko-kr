---
title: 자습서 - Terratest를 사용하여 Azure에서 Terraform 모듈 테스트
description: Terratest를 사용하여 Terraform 모듈을 테스트하는 방법을 알아보세요.
ms.topic: tutorial
ms.date: 10/26/2019
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: a8e5f674e28ed3ba7265b3f97541f49f27013bdd
ms.sourcegitcommit: dc74b60217abce66fe6cc93923e869e63ac86a8f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94872854"
---
# <a name="tutorial-test-terraform-modules-in-azure-using-terratest"></a>자습서: Terratest를 사용한 Azure 내 Terraform 모듈 테스트

> [!NOTE]
> 이 문서의 샘플 코드는 버전 0.12 이상에서 작동하지 않습니다.

Azure Terraform 모듈은 재사용 가능하고, 구성 가능하고, 테스트 가능한 구성 요소를 만드는 데 사용할 수 있습니다. Terraform 모듈은 인프라를 코드 프로세스로 구현하는 데 유용한 캡슐화를 통합합니다.

Terraform 모듈을 만들 때는 품질 보증을 구현해야 합니다. 아쉽게도 Terraform 모듈에서 단위 테스트와 통합 테스트를 작성하는 방법을 설명하는 데 사용할 수 있는 문서는 제한되어 있습니다. 이 자습서에서는 [Azure Terraform 모듈](https://registry.terraform.io/browse?provider=azurerm)을 작성하는 동안 적용한 테스트 인프라와 모범 사례를 소개합니다.

가장 인기 있는 모든 테스트 인프라를 살펴보고 Terraform 모듈을 테스트하기 위해 [Terratest](https://github.com/gruntwork-io/terratest)를 선택했습니다. Terratest는 Go 라이브러리로 구현됩니다. Terratest는 HTTP를 요청하고 SSH를 사용하여 특정 가상 머신에 액세스하는 것과 같은 일반적인 인프라 테스트 작업에 대한 도우미 함수 및 패턴 컬렉션을 제공합니다. 다음 목록에서는 Terratest를 사용할 때의 주요 이점 중 일부에 대해 설명합니다.

- **인프라를 점검할 수 있는 편리한 도우미** - 이 기능은 실제 환경에서 실제 인프라를 확인하려는 경우에 유용합니다.
- **구성된 폴더 구조** - 테스트 사례가 명확하게 구성되며, [표준 Terraform 모듈 폴더 구조](https://www.terraform.io/docs/modules/create.html#standard-module-structure)를 따릅니다.
- **Go로 작성되는 테스트 사례** - Terraform을 사용하는 개발자는 대부분 Go 개발자입니다. Go 개발자라면 Terratest를 사용하기 위해 다른 프로그래밍 언어를 학습할 필요가 없습니다.
- **확장 가능한 인프라** - Azure 관련 기능을 포함하여 Terratest에 기반한 함수를 추가로 확장할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Go 프로그래밍 언어**: 모든 테스트 사례가 [Go](https://golang.org/dl/)로 작성됩니다.
- **dep**: [dep](https://github.com/golang/dep#installation)는 Go의 종속성 관리 도구입니다.
- **Azure CLI**: [Azure CLI](/cli/azure/install-azure-cli)는 Azure 리소스를 관리하는 데 사용할 수 있는 명령줄 도구입니다. (Terraform은 서비스 주체 또는 [Azure CLI](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html)를 통해 Azure를 인증하도록 지원합니다.)
- **mage**: [mage 실행 파일](https://github.com/magefile/mage/releases)을 사용하여 Terratest 사례의 실행을 간소화하는 방법을 보여 줍니다. 

## <a name="create-a-static-webpage-module"></a>정적 웹 페이지 모듈 만들기

이 자습서에서는 단일 HTML 파일을 Azure Storage Blob에 업로드하여 정적 웹 페이지를 프로비전하는 Terraform 모듈을 만듭니다. 이 모듈은 전 세계 사용자가 모듈에서 반환하는 URL을 통해 웹 페이지에 액세스할 수 있게 합니다.

> [!NOTE]
> 이 섹션에서 설명하는 모든 파일은 [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH) 위치 아래에 만드세요.

먼저 GoPath의 `src` 폴더 아래에 `staticwebpage`라는 새 폴더를 만듭니다. 이 자습서의 전체 폴더 구조는 다음 예와 같습니다. 별표(`(*)`)가 표시된 파일은 이 섹션에서 중점적으로 사용하는 것입니다.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

정적 웹 페이지 모듈은 세 개의 입력을 허용합니다. 입력은 `./variables.tf`에서 다음과 같이 선언됩니다.

```hcl
variable "location" {
  description = "The Azure region in which to create all resources."
}

variable "website_name" {
  description = "The website name to use to create related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static home page HTML in your local file system."
  default     = "index.html"
}
```

문서의 앞부분에서 설명한 대로 이 모듈은 `./outputs.tf`에 선언된 URL도 출력합니다.

```hcl
output "homepage_url" {
  value = azurerm_storage_blob.homepage.url
}
```

모듈의 주요 논리에서 프로비전하는 네 가지 리소스는 다음과 같습니다.
- **리소스 그룹**: 리소스 그룹의 이름은 `-staging-rg`에 추가된 `website_name` 입력입니다.
- **스토리지 계정**: 스토리지 계정의 이름은 `data001`에 추가된 `website_name` 입력입니다. 스토리지 계정 이름에 대한 제한 사항에 맞게 모듈에서는 전체 스토리지 계정 이름에서 특수 문자를 모두 제거하고 소문자만 사용합니다.
- **고정 이름 컨테이너**: 컨테이너의 이름은 `wwwroot`이고, 스토리지 계정에 만들어집니다.
- **단일 HTML 파일**: HTML 파일을 `html_path` 입력에서 읽어 `wwwroot/index.html`로 업로드합니다.

정적 웹 페이지 모듈 논리가 `./main.tf`에 구현되었습니다.

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = var.location
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = azurerm_resource_group.main.name
  location                 = azurerm_resource_group.main.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = azurerm_resource_group.main.name
  storage_account_name  = azurerm_storage_account.main.name
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = azurerm_resource_group.main.name
  storage_account_name   = azurerm_storage_account.main.name
  storage_container_name = azurerm_storage_container.main.name
  source                 = var.html_path
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>단위 테스트

Terratest는 통합 테스트를 위해 설계되었습니다. 이를 위해 Terratest는 실제 환경에서 실제 리소스를 프로비전합니다. 특히 프로비전할 리소스가 많은 경우 통합 테스트 작업이 매우 커질 수 있는 경우도 있습니다. 앞 섹션에서 언급한 스토리지 계정 이름을 변환하는 논리가 좋은 예입니다. 

하지만 어떤 리소스도 실제로 프로비전할 필요가 없습니다. 여기서는 명명 변환 논리가 올바른지 확인하려고 합니다. Terratest의 유연성 덕분에 단위 테스트를 사용할 수 있습니다. 단위 테스트는 로컬 실행 테스트 사례입니다(인터넷 액세스가 필요함). 단위 테스트 사례는 `terraform init` 및 `terraform plan` 명령을 실행하여 `terraform plan`의 출력을 구문 분석하고 비교할 특성 값을 찾습니다.

이 섹션의 나머지 부분에서는 Terratest를 사용하여 스토리지 계정 이름을 변환하는 데 사용되는 논리가 올바른지 확인하도록 단위 테스트를 구현하는 방법에 대해 설명합니다. 별표`(*)`가 있는 파일에만 관심이 있습니다.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

먼저 `./test/fixtures/storage-account-name/empty.html`이라는 비어 있는 HTML 파일을 자리 표시자로 사용합니다.

`./test/fixtures/storage-account-name/main.tf` 파일은 테스트 사례 프레임입니다. 단위 테스트의 입력이기도 한 하나의 입력, `website_name`을 허용합니다. 논리는 다음과 같습니다.

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = var.website_name
  html_path    = "empty.html"
}
```

주요 구성 요소는 `./test/storage_account_name_unit_test.go`의 단위 테스트에 대한 구현입니다.

Go 개발자는 아마도 단위 테스트가 `*testing.T` 형식의 인수를 허용하여 클래식 Go 테스트 함수의 시그니처와 일치한다는 것을 알 수 있습니다.

단위 테스트의 본문에는 `testCases` 변수(입력으로 `key`, 예상 출력으로 `value`)에 정의된 총 5개의 사례가 있습니다. 각 단위 테스트 사례에 대해 먼저 `terraform init`를 실행하고 테스트 픽스쳐 폴더(`./test/fixtures/storage-account-name/`)를 대상으로 합니다. 

다음으로, 특정 테스트 사례 입력을 사용하는 `terraform plan` 명령(`tfOptions`에서 `website_name` 정의 참조)은 결과를 `./test/fixtures/storage-account-name/terraform.tfplan`(전체 폴더 구조에 나와 있지 않음)에 저장합니다.

이 결과 파일은 공식 Terraform 계획 파서를 사용하여 코드를 읽을 수 있는 구조로 구문 분석됩니다.

이제 관심 있는 특성(이 경우 `azurerm_storage_account`의 `name`)을 찾고, 결과를 예상 출력과 비교합니다.

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify the test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

단위 테스트를 실행하려면 명령줄에서 다음 단계를 완료합니다.

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestUT_StorageAccountName
```

기존의 Go 테스트 결과는 약 1분 이내에 반환됩니다.

### <a name="integration-test"></a>통합 테스트

단위 테스트와는 달리 통합 테스트는 엔드투엔드 관점에서 실제 환경에 리소스를 프로비전해야 합니다. Terratest는 이러한 종류의 작업을 효율적으로 수행합니다. 

Terraform 모듈의 모범 사례는 `examples` 폴더를 설치하는 것입니다. `examples` 폴더에는 몇 가지 엔드투엔드 샘플이 포함되어 있습니다. 실제 데이터를 사용하지 않으려면 이러한 샘플을 통합 테스트로 테스트하는 것은 어떨까요? 이 섹션에서는 다음 폴더 구조에서 별표`(*)`가 있는 세 개의 파일에 중점을 둡니다.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

먼저 샘플을 사용하여 시작해 보겠습니다. `hello-world/`라는 새 샘플 폴더가 `./examples/` 폴더에 만들어집니다. 여기서는 업로드할 간단한 HTML 페이지(`./examples/hello-world/index.html`)를 제공합니다.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demonstrate Terratest.</p>
</body>
</html>
```

`./examples/hello-world/main.tf` Terraform 샘플은 단위 테스트에 표시된 것과 비슷합니다. 하나의 중요한 차이점이 있습니다. 즉 이 샘플은 업로드된 HTML의 URL을 `homepage`라는 웹 페이지로도 출력합니다.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = var.website_name
}

output "homepage" {
  value = module.staticwebpage.homepage_url
}
```

`./test/hello_world_example_test.go` 통합 테스트 파일에서 Terratest 및 클래식 Go 테스트 함수를 다시 사용합니다.

단위 테스트와는 달리 통합 테스트는 Azure에서 실제 리소스를 만듭니다. 이로 인해 이름이 충돌하지 않도록 주의해야 합니다. (스토리지 계정 이름과 같이 글로벌로 고유한 이름에 특히 주의하세요.) 따라서 테스트 논리의 첫 번째 단계는 Terratest에서 제공하는 `UniqueId()` 함수를 사용하여 임의의 `websiteName`을 생성하는 것입니다. 이 함수는 소문자, 대문자 또는 숫자가 있는 임의의 이름을 생성합니다. `tfOptions`는 `./examples/hello-world/` 폴더를 대상으로 하는 모든 Terraform 명령을 수행합니다. 또한 `website_name`이 임의로 추출된 `websiteName`으로 설정되어 있는지 확인합니다.

그런 다음, `terraform init`, `terraform apply` 및 `terraform output`이 하나씩 실행됩니다. Terratest에서 제공하는 또 다른 `HttpGetWithCustomValidation()` 도우미 함수를 사용합니다. 도우미 함수를 사용하여 HTML이 `terraform output`에서 반환되는 출력 `homepage` URL에 업로드되는지 확인합니다. HTTP GET 상태 코드를 `200`과 비교하여 HTML 콘텐츠에서 일부 키워드를 찾습니다. 마지막으로, `terraform destroy`는 Go의 `defer` 기능을 활용하여 실행되도록 "약속"되어 있습니다.

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent a naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify the test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output, and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demonstrate Terratest.")
    })
}
```

통합 테스트를 실행하려면 명령줄에서 다음 단계를 완료합니다.

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestIT_HelloWorldExample
```

기존의 Go 테스트 결과는 약 2분 이내에 반환됩니다. 또한 다음 명령을 실행하여 단위 테스트와 통합 테스트를 모두 실행할 수도 있습니다.

```shell
go fmt
go test
```

통합 테스트는 단위 테스트보다 훨씬 더 오래 걸립니다 (하나의 통합 사례에 대해 2분, 5개의 단위 사례에 대해 1분). 그러나 시나리오에서 단위 테스트 또는 통합 테스트 중에서 어느 것을 사용할지는 사용자의 결정에 달려 있습니다. 일반적으로 Terraform HCL 함수를 사용하여 복잡한 논리에 대한 단위 테스트를 사용하는 것을 선호합니다. 일반적으로 사용자의 엔드투엔드 관점에서 통합 테스트를 사용합니다.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>mage를 사용하여 Terratest 사례 실행 단순화 
Azure Cloud Shell에서 테스트 사례를 실행하려면 다양한 디렉터리에서 다른 명령을 실행해야 합니다. 이 프로세스를 보다 효율적으로 수행하기 위해 프로젝트에 빌드 시스템을 도입했습니다. 이 섹션에서는 작업에 Go 빌드 시스템인 mage를 사용합니다.

mage에서 요구되는 유일한 것은 프로젝트의 루트 디렉터리에 있는 `magefile.go`(다음 예에서 `(+)`로 표시됨)입니다.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

`./magefile.go`의 예제는 다음과 같습니다. Go로 작성된 이 빌드 스크립트에는 5개의 빌드 단계가 구현되어 있습니다.
- `Clean`: 테스트 실행 중에 생성된 모든 생성 및 임시 파일을 제거합니다.
- `Format`: 이 단계는 `terraform fmt` 및 `go fmt`를 실행하여 코드 베이스의 형식을 지정합니다.
- `Unit`: 이 단계는 `./test/` 폴더 아래의 모든 단위 테스트를 실행합니다(함수 이름 규칙 `TestUT_*` 사용).
- `Integration`: 이 단계는 `Unit`와 비슷하지만, 단위 테스트 대신 통합 테스트(`TestIT_*`)를 실행합니다.
- `Full`: 이 단계는 `Clean`, `Format`, `Unit` 및 `Integration`을 순서대로 실행합니다.

```go
// +build mage

// Build a script to format and run tests of a Terraform module project
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// The default target when the command executes `mage` in Cloud Shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build and test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

다음 명령을 사용하여 전체 테스트 도구 모음을 실행할 수 있습니다. 이 코드는 이전 섹션에서 사용한 실행 단계와 비슷합니다. 

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in magefile or test cases
go fmt      # Only required when you change the magefile
mage
```

마지막 명령줄을 추가 mage 단계로 바꿀 수 있습니다. 예를 들어 `mage unit` 또는 `mage clean`을 사용할 수 있습니다. `dep` 명령과 `az login`을 magefile에 포함시키는 것이 좋습니다. 여기서는 코드를 보여 주지 않습니다. 

mage에서는 Go 패키지 시스템을 사용하여 단계를 공유할 수도 있습니다. 이 경우 일반적인 구현만 참조하고 종속성(`mg.Deps()`)을 선언하여 모든 모듈의 magefile을 간소화할 수 있습니다.

**선택 사항: 수용 테스트를 실행하도록 서비스 주체 환경 변수 설정**
 
테스트하기 전에 `az login`을 실행하는 대신, 서비스 주체 환경 변수를 설정하여 Azure 인증을 수행할 수 있습니다. Terraform은 [환경 변수 이름 목록](https://www.terraform.io/docs/providers/azurerm/index.html#testing)을 게시합니다. (이러한 환경 변수 중 처음 4개만 필요합니다.) 또한 Terraform은 [이러한 환경 변수의 값을 가져오는 방법](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html)을 설명하는 자세한 지침도 게시합니다.

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Terratest GitHub 페이지](https://github.com/gruntwork-io/terratest).
