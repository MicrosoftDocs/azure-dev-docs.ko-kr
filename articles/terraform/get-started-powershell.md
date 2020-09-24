---
title: 빠른 시작 - Azure PowerShell을 사용하여 Terraform 구성
description: 이 빠른 시작에서는 Terraform을 설치하고 구성하여 Azure 리소스를 만드는 방법을 알아봅니다.
keywords: azure devops terraform install configure windows init plan apply execution login rbac service principal automated script powershell
ms.topic: quickstart
ms.date: 08/18/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 401a6c4cc8827e48858a936a10c9c7f62af15aab
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90830059"
---
# <a name="quickstart-configure-terraform-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Terraform 구성
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

이 문서에서는 PowerShell을 사용하여 [Azure에서 Terraform을 시작](https://www.terraform.io/docs/providers/azurerm/index.html)하는 방법에 대해 설명합니다.

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
> * PowerShell 최신 버전 설치
> * 새로운 PowerShell Az 모듈 설치
> * Azure CLI 설치
> * Terraform 설치
> * 인증용 Azure 서비스 주체 만들기
> * 서비스 주체를 사용하여 Azure에 로그인 
> * Terraform이 Azure 구독에 올바르게 인증하도록 환경 변수 설정
> * Terraform 스크립트를 작성하여 Azure 리소스 그룹 만들기
> * Terraform 실행 계획 만들기 및 적용
> * `terraform plan -destroy` 플래그를 사용하여 실행 계획 되돌리기

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-your-environment"></a>환경 구성

1. Azure 리소스와 상호 작용하도록 허용하는 최신 PowerShell 모듈은 [Azure PowerShell Az 모듈](/powershell/azure/new-azureps-module-az)이라고 합니다. Azure PowerShell Az 모듈을 사용하는 경우 모든 플랫폼에서 추천되는 버전은 PowerShell 7 이상입니다. PowerShell이 설치되어 있으면 PowerShell 프롬프트에서 다음 명령을 입력하여 버전을 확인할 수 있습니다.

    ```powershell
    $PSVersionTable.PSVersion
    ```

1. [PowerShell을 설치](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-7)합니다. 이 데모는 Windows 10에서 PowerShell 7.0.2를 사용하여 테스트되었습니다.

1. [Terraform을 Azure에 인증](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html)하려면 [Azure CLI를 설치](/cli/azure/install-azure-cli-windows?view=azure-cli-latest)해야 합니다. 이 데모는 Azure CLI 버전 2.9.1을 사용하여 테스트되었습니다.

1. [Terraform을 다운로드](https://www.terraform.io/downloads.html)합니다.

1. 다운로드에서 실행 파일의 압축을 선택한 디렉터리에 풉니다.

1. [시스템의 전역 경로를 실행 파일로 업데이트](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)합니다.

1. `terraform` 명령을 사용하여 글로벌 경로 구성을 확인합니다.

    ```powershell
    terraform
    ```

    **참고**:
    - Terraform 실행 파일이 있으면 구문 및 사용 가능한 명령이 나열됩니다.

## <a name="create-an-azure-service-principal"></a>Azure 서비스 주체 만들기

PowerShell 및 Terraform을 사용하는 경우 서비스 주체를 사용하여 로그인해야 합니다.

서비스 주체를 사용하여 Azure 구독에 로그인하려면 서비스 주체에 대한 액세스 권한이 필요합니다. 서비스 주체가 이미 있는 경우 이 섹션을 건너뛸 수 있습니다.

[PowerShell을 사용하여 서비스 주체를 만드는 경우](/powershell/azure/create-azure-service-principal-azureps) 많은 옵션이 있습니다. 이 문서에서는 **기여자** 역할을 사용하여 서비스 주체를 만듭니다. **기여자** 역할(기본 역할)에는 Azure 계정에서 읽고 쓸 수 있는 모든 권한이 있습니다. RBAC(역할 기반 액세스 제어)와 역할에 대한 자세한 내용은 [RBAC: 기본 제공 역할](/azure/active-directory/role-based-access-built-in-roles)을 참조하세요.

[New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal)을 호출하면 지정된 구독에 대한 서비스 주체를 만듭니다. 성공적으로 완료되면 서비스 주체 이름 및 표시 이름과 같은 서비스 주체의 정보가 표시됩니다. 인증 자격 증명을 지정하지 않고 `New-AzADServicePrincipal`을 호출하면 암호가 자동으로 생성됩니다. 그러나 이 암호는 `SecureString` 형식으로 반환되므로 표시되지 않습니다. 따라서 변수로 이동하는 결과가 포함된 `New-AzADServicePrincipal`을 호출해야 합니다. 그런 다음, 변수를 일반 텍스트로 변환하여 표시할 수 있습니다.

1. 사용하려는 Azure 구독에 대한 구독 ID를 가져옵니다. 구독 ID를 모르는 경우 [Azure Portal](https://portal.azure.com/)에서 해당 값을 가져올 수 있습니다.

    1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
    1. **Azure 서비스** 아래에서 **구독**을 선택합니다.
    1. 구독 테이블 목록에는 각 구독 ID가 포함된 열이 있습니다.

1. PowerShell을 시작합니다.

1. [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)을 사용하여 새 서비스 주체를 만듭니다. `<azure_subscription_id>`를 사용하려는 Azure 구독의 ID로 바꿉니다.

    ```powershell
    $sp = New-AzADServicePrincipal -Scope /subscriptions/<azure_subscription_id>
    ```

1. 서비스 주체의 이름을 표시합니다.

    ```powershell
    $sp.ServicePrincipalNames
    ```

1. 자동으로 생성된 암호를 텍스트로 표시합니다([ConvertFrom-SecureString](/powershell/module/microsoft.powershell.security/convertfrom-securestring)).

    ```powershell
    $UnsecureSecret = ConvertFrom-SecureString -SecureString $sp.Secret -AsPlainText
    ```

**참고**:

- 서비스 주체 이름 및 암호 값은 서비스 주체를 사용하여 구독에 로그인하는 데 필요합니다.
- 분실한 암호는 복구할 수 없습니다. 따라서 암호를 안전한 장소에 저장해야 합니다. 암호를 잊어버린 경우 [서비스 주체 자격 증명을 다시 설정](/powershell/azure/create-azure-service-principal-azureps#reset-credentials)해야 합니다.

## <a name="log-in-to-azure-using-a-service-principal"></a>서비스 주체를 사용하여 Azure에 로그인

서비스 주체를 사용하여 Azure 구독에 로그인하려면 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount)를 호출하고 [PsCredential](/dotnet/api/system.management.automation.pscredential) 형식의 개체를 지정합니다.

1. PowerShell을 시작합니다.

1. 다음 기술 중 하나를 사용하여 [PsCredential](/dotnet/api/system.management.automation.pscredential) 개체를 가져옵니다.

    1. [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)을 호출하고, 요청 시 서비스 주체 이름과 암호를 입력합니다.

        ```powershell
        $spCredential = Get-Credential
        ```

    1. 메모리에서 `PsCredential` 개체를 생성합니다. 자리 표시자를 서비스 주체에 적절한 값으로 바꿉니다. 이 패턴은 스크립트에서 로그인하는 방법입니다.

        ```powershell
        $spName = "<service_principal_name>"
        $spPassword = ConvertTo-SecureString "<service_principal_password>" -AsPlainText -Force
        $spCredential = New-Object System.Management.Automation.PSCredential($spName , $spPassword)
        ```

1. `Connect-AzAccount`를 호출하여 `PsCredential` 개체를 전달합니다. `<azure_subscription_tenant_id>` 자리 표시자를 Azure 구독 테넌트 ID로 바꿉니다.

    ```powershell
    Connect-AzAccount -Credential $spCredential -Tenant "<azure_subscription_tenant_id>" -ServicePrincipal
    ```

## <a name="set-environment-variables"></a>환경 변수 설정

Terraform에서 의도한 Azure 구독을 사용하도록 환경 변수를 설정합니다. 환경 변수는 Windows 시스템 수준 또는 특정 PowerShell 세션 내에서 설정할 수 있습니다. 특정 세션에 대한 환경 변수를 설정하려면 다음 코드를 사용합니다. 자리 표시자를 사용자 환경에 적절한 값으로 바꿉니다.

```powershell
$env:ARM_CLIENT_ID="<service_principal_app_id>"
$env:ARM_SUBSCRIPTION_ID="<azure_subscription_id>"
$env:ARM_TENANT_ID="<azure_subscription_tenant_id>"
```

## <a name="create-a-terraform-configuration-file"></a>Terraform 구성 파일 만들기

이 섹션에서는 Azure 리소스 그룹을 만드는 Terraform 구성 파일을 코딩합니다.

1. 이 데모에 대한 Terraform 파일을 저장할 디렉터리를 만듭니다.

    ```powershell
    mkdir QuickstartTerraformTest
    ```

1. 디렉터리를 데모 디렉터리로 변경합니다.

    ```powershell
    cd QuickstartTerraformTest
    ```

1. 선호하는 편집기를 사용하여 Terraform 구성 파일을 만듭니다. 이 문서에서는 [Visual Studio Code](https://code.visualstudio.com/Download)를 사용합니다.

    ```powershell
    code QuickstartTerraformTest.tf
    ```

1. 다음 HCL 코드를 새 파일에 붙여넣습니다. 자세한 내용은 코드 목록 뒤에 나오는 참고 사항을 참조하세요.

    ```hcl
    provider "azurerm" {
      # The "feature" block is required for AzureRM provider 2.x.
      # If you're using version 1.x, the "features" block isn't allowed.
      version = "~>2.0"
      features {}
    }

    resource "azurerm_resource_group" "rg" {
      name     = "QuickstartTerraformTest-rg"
      location = "eastus"
    }
    ```

    **참고**:
    - 공급자 블록은 [Azure 공급자(azurerm)](https://www.terraform.io/docs/providers/azurerm/index.html)를 사용하도록 지정합니다.
    - `azurerm` 공급자 블록 내에서 `version` 및 `features` 특성이 설정됩니다. 주석에 언급된 대로, 용도는 버전에 따라 다릅니다. 이러한 특성을 설정하는 방법에 대한 자세한 내용은 [AzureRM Provider v2.0](https://www.terraform.io/docs/providers/azurerm/guides/2.0-upgrade-guide.html)을 참조하세요.
    - [resource declaration](https://www.terraform.io/docs/configuration/resources.html)은 [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html) 리소스 종류에 대해서만 적용됩니다. azure_resource_group에 대한 두 가지 필수 인수는 name 및 location입니다.

## <a name="create-and-apply-a-terraform-execution-plan"></a>Terraform 실행 계획 만들기 및 적용

이 섹션에서는 *실행 계획*을 만들어 클라우드 인프라에 적용합니다.

1. [terraform init](https://www.terraform.io/docs/commands/init.html)를 사용하여 Terraform 배포를 초기화합니다. 이 단계에서는 Azure 리소스 그룹을 만드는 데 필요한 Azure 모듈을 다운로드합니다.

    ```powershell
    terraform init
    ```

1. [terraform plan](https://www.terraform.io/docs/commands/plan.html)을 실행하여 Terraform 구성 파일에서 실행 계획을 만듭니다.

    ```powershell
    terraform plan -out QuickstartTerraformTest.tfplan
    ```

    **참고:**
    - `terraform plan` 명령은 실행 계획을 만들지만 실행하지는 않습니다. 대신 구성 파일에 지정된 구성을 만드는 데 필요한 작업을 결정합니다. 이 패턴을 사용하면 실제 리소스를 변경하기 전에 실행 계획이 예상과 일치하는지 확인할 수 있습니다.
    - 선택 사항인 `-out` 매개 변수를 사용하여 계획의 출력 파일을 지정할 수 있습니다. `-out` 매개 변수를 사용하면 검토한 계획이 정확하게 적용됩니다.
    - 실행 계획 및 보안을 유지하는 방법에 대한 자세한 내용은 [보안 경고 섹션](https://www.terraform.io/docs/commands/plan.html#security-warning)을 참조하세요.

1. [terraform apply](https://www.terraform.io/docs/commands/apply.html)를 실행하여 실행 계획을 적용합니다.

    ```powershell
    terraform apply QuickstartTerraformTest.tfplan
    ```

1. 실행 계획이 적용되면 [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)을 사용하여 리소스 그룹이 성공적으로 만들어졌는지 테스트할 수 있습니다.

    ```powershell
    Get-AzResourceGroup -Name QuickstartTerraformTest-rg
    ```

    **참고**:

    - 성공하면 명령은 새로 만들어진 리소스 그룹의 다양한 특성을 표시합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 이 문서에서 만든 리소스를 삭제합니다.

1. [terraform plan](https://www.terraform.io/docs/commands/plan.html)을 실행하여 Terraform 구성 파일에 표시된 리소스를 삭제하는 실행 계획을 만듭니다.

    ```powershell
    terraform plan -destroy -out QuickstartTerraformTest.destroy.tfplan
    ```

    **참고:**
    - `terraform plan` 명령은 실행 계획을 만들지만 실행하지는 않습니다. 대신 구성 파일에 지정된 구성을 만드는 데 필요한 작업을 결정합니다. 이 패턴을 사용하면 실제 리소스를 변경하기 전에 실행 계획이 예상과 일치하는지 확인할 수 있습니다.
    - `-destroy` 매개 변수는 리소스를 삭제하는 계획을 생성합니다.
    - 선택 사항인 `-out` 매개 변수를 사용하여 계획의 출력 파일을 지정할 수 있습니다. `-out` 매개 변수를 사용하면 검토한 계획이 정확하게 적용됩니다.
    - 실행 계획 및 보안을 유지하는 방법에 대한 자세한 내용은 [보안 경고 섹션](https://www.terraform.io/docs/commands/plan.html#security-warning)을 참조하세요.

1. [terraform apply](https://www.terraform.io/docs/commands/apply.html)를 실행하여 실행 계획을 적용합니다.

    ```powershell
    terraform apply QuickstartTerraformTest.destroy.tfplan
    ```

1. [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)을 사용하여 리소스 그룹이 삭제되었는지 확인합니다.

    ```powershell
    Get-AzResourceGroup -Name QuickstartTerraformTest-rg
    ```

    **참고**:
    - 성공하면 `Get-AzResourceGroup`에서 리소스 그룹이 없다는 사실을 표시합니다.

1. 디렉터리를 상위 디렉터리로 변경하고 데모 디렉터리를 제거합니다. `-r` 매개 변수는 디렉터리를 제거하기 전에 디렉터리 콘텐츠를 제거합니다. 디렉터리 콘텐츠에는 이전에 만든 구성 파일과 Terraform 상태 파일이 포함됩니다.

    ```powershell
    cd .. && rm -r QuickstartTerraformTest
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Terraform으로 Azure VM 만들기](create-linux-virtual-machine-with-infrastructure.md)