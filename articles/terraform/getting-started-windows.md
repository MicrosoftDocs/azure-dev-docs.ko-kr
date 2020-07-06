---
title: 빠른 시작 - Windows를 사용하여 Terraform 시작
description: 이 빠른 시작에서는 Terraform을 설치하고 구성하여 Azure 리소스를 만드는 방법을 알아봅니다.
keywords: azure devops terraform install configure windows init plan apply execution login rbac service principal automated script cli powershell
ms.topic: quickstart
ms.date: 06/12/2020
ms.openlocfilehash: d28a79af9a59551153f297cebfb0c51ed2e72838
ms.sourcegitcommit: 2d6c9687b39e33a6b5e980d9a375c9f8f1f2cab7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84783805"
---
# <a name="quickstart-getting-started-with-terraform-using-windows"></a>빠른 시작: Windows를 사용하여 Terraform 시작
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-your-environment"></a>환경 구성

1. PowerShell 7 이상은 Windows를 포함한 모든 플랫폼에서 Azure PowerShell을 통해 사용하는 데 추천되는 PowerShell 버전입니다. PowerShell이 설치되어 있으면 PowerShell 프롬프트에서 다음을 입력하여 버전을 확인할 수 있습니다.

    ```powershell
    $PSVersionTable.PSVersion
    ```
    
1. [Windows에 PowerShell 설치](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-7) 문서의 지침에 따라 최신 버전의 PowerShell을 설치합니다.

1. 이 문서에서는 [Azure PowerShell Az 모듈](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)을 사용합니다. [Windows에 Azure CLI 설치](/cli/azure/install-azure-cli-windows?view=azure-cli-latest) 문서의 지침을 따릅니다.

1. 방금 설치한 PowerShell 버전의 인스턴스를 엽니다.

## <a name="log-into-azure"></a>Azure에 로그인

Azure 구독에 로그인할 수 있는 몇 가지 옵션이 있습니다.

- [Microsoft 계정에 로그인](#log-into-your-microsoft-account)
- [Azure 서비스 주체를 사용하여 로그인](#log-into-azure-using-an-azure-service-principal)

### <a name="log-into-your-microsoft-account"></a>Microsoft 계정으로 로그인

매개 변수 없이 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount)를 호출하면 URL과 코드가 표시됩니다. URL을 찾아 코드를 입력하고 지시에 따라 Microsoft 계정을 사용하여 Azure에 로그인합니다. 로그인되면 포털로 돌아갑니다.

```powershell
Connect-AzAccount
```

참고:
- 로그인에 성공하면 `Connect-AzAccount`에서 기록된 Microsoft 계정과 연결된 기본 Azure 구독을 표시합니다. 다른 Azure 구독으로 전환하는 방법을 알아보려면 [현재 Azure 구독 지정](#specify-the-current-azure-subscription) 섹션을 참조하세요.

### <a name="log-into-azure-using-an-azure-service-principal"></a>Azure 서비스 주체를 사용하여 Azure에 로그인

**Azure 서비스 주체 만들기**: 서비스 주체를 사용하여 Azure 구독에 로그인하려면 먼저 서비스 주체에 액세스할 수 있어야 합니다. 서비스 주체가 이미 있는 경우 섹션의 이 부분을 건너뛸 수 있습니다.

Azure 서비스를 배포하거나 사용하는 자동화된 도구(예: Terraform)에는 항상 제한된 권한이 있어야 합니다. Azure는 애플리케이션에서 모든 권한이 있는 사용자로 로그인하도록 하는 대신 서비스 주체를 제공합니다. 하지만 로그인할 서비스 주체가 없는 경우 어떻게 될까요? 이 시나리오에서는 사용자 자격 증명을 사용하여 로그인한 다음, 서비스 주체를 만들 수 있습니다. 서비스 주체를 만든 후에는 나중에 로그인을 시도할 때 해당 정보를 사용할 수 있습니다.

[PowerShell을 사용하여 서비스 주체를 만드는 경우](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps) 많은 옵션이 있습니다. 이 문서에서는 **기여자** 역할을 사용하여 서비스 주체를 만듭니다. **기여자** 역할(기본 역할)에는 Azure 계정에서 읽고 쓸 수 있는 모든 권한이 있습니다. RBAC(역할 기반 액세스 제어)와 역할에 대한 자세한 내용은 [RBAC: 기본 제공 역할](/azure/active-directory/role-based-access-built-in-roles)을 참조하세요.

[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADServicePrincipal)을 호출하면 지정된 구독에 대한 서비스 주체를 만듭니다. 성공적으로 완료되면 서비스 주체 이름 및 표시 이름과 같은 서비스 주체의 정보가 표시됩니다. 인증 자격 증명을 지정하지 않고 `New-AzADServicePrincipal`을 호출하면 암호가 자동으로 생성됩니다. 그러나 이 암호는 `SecureString` 형식으로 반환되므로 표시되지 않습니다. 따라서 변수로 이동하는 결과가 포함된 `New-AzADServicePrincipal`을 호출해야 합니다. 그런 다음, 변수에 암호를 쿼리할 수 있습니다.

1. 다음 명령을 입력하고, `<subscription_id>`를 사용하려는 구독 계정의 ID로 바꿉니다.

    ```powershell
    $sp = New-AzADServicePrincipal -Scope /subscriptions/<subscription_id>
    ```

1. 다음을 입력하여 서비스 주체의 이름을 표시합니다.

    ```powershell
    $sp.ServicePrincipalNames
    ```

1. `ConvertFrom-SecureString`을 호출하여 암호를 텍스트로 표시합니다.

    ```powershell
    $UnsecureSecret = ConvertFrom-SecureString -SecureString $sp.Secret -AsPlainText
    ```

참고:
- 이 시점에서 서비스 주체 이름과 암호를 알고 있습니다. 이러한 값은 서비스 주체를 사용하여 구독에 로그인하는 데 필요합니다.
- 분실한 암호는 복구할 수 없습니다. 따라서 암호를 안전한 장소에 저장해야 합니다. 암호를 잊어버린 경우 [서비스 주체 자격 증명을 다시 설정](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps#reset-credentials)해야 합니다.

**Azure 서비스 주체를 사용하여 로그인**: 서비스 주체를 사용하여 Azure 구독에 로그인하려면 `Connect-AzAccount`를 호출하고 [PsCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential) 형식의 개체를 전달합니다. 대화형 및 스크립트의 두 가지 옵션이 있습니다.

- **대화형 패턴**: [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential)을 호출하고, 요청 시 자격 증명을 입력합니다. `Get-Credential`을 호출하면 `Connect-AzAccount`에 전달되는 `PsCredential` 개체가 반환됩니다.

    1. `Get-Credential`을 호출하고, 서비스 사용자 이름과 암호를 수동으로 입력합니다.

        ```powershell
        $Credential = Get-Credential
        ```

    2. `Connect-AzAccount`를 호출하여 `PsCredential` 개체를 전달합니다. (`<azureSubscriptionTenantId>` 자리 표시자를 Azure 구독 테넌트 ID로 바꿉니다.)

        ```powershell
        Connect-AzAccount -Credential $Credential -Tenant <azureSubscriptionTenantId> -ServicePrincipal
        ```

- **스크립트 패턴**: `PsCredential` 개체를 생성하여 `Connect-AzConnect`에 전달합니다.

    1. `Get-Credential`을 생성합니다. (자리 표시자를 Azure 구독 및 서비스 주체에 적합한 값으로 바꿉니다.)

        ```powershell
        $spName = "<servicePrincipalName>"
        $spPassword = ConvertTo-SecureString "<servicePrincipalPassword>" -AsPlainText -Force
        $psCredential = New-Object System.Management.Automation.PSCredential($spName , $spPassword)
        ```

    1. `Connect-AzAccount`를 호출하여 생성된 `PsCredential` 개체를 전달합니다.

        ```powershell
        Connect-AzAccount -Credential $psCredential -TenantId "<azureSubscriptionTenantId>"  -ServicePrincipal
        ```

## <a name="specify-the-current-azure-subscription"></a>현재 Azure 구독 지정

이전 섹션에서 설명한 것처럼 Azure에 로그인하는 두 가지 방법은 다음과 같습니다.

- **Microsoft 계정을 사용한 로그인**: Microsoft 계정은 여러 Azure 구독과 연결될 수 있으며,이 중 하나는 기본 구독입니다. 기본 구독은 로그인할 때 사용하는 구독이며 다른 구독으로 전환되지 않습니다.
- **Azure 서비스 주체를 사용한 로그인**: 서비스 주체는 Azure 구독에 한정됩니다. 로그인하면 구독 정보가 표시됩니다.

다음 단계는 다음 작업을 수행하는 첫 번째 시나리오를 해결합니다.

- 현재 Azure 구독 보기
- 현재 Microsoft 계정에 대해 사용 가능한 모든 Azure 구독을 나열합니다.
- 다른 Azure 구독으로 전환

1. 현재 Azure 구독을 보려면 [Get-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext)를 사용합니다.

    ```powershell
    Get-AzContext
    ```

1. 사용 가능한 여러 Azure 구독에 액세스할 수 있는 경우 `Get-AzContext -ListAvailable`을 사용합니다.

    ```powershell
    Get-AzContext -ListAvailable | Select Name
    ```

1. 자동 생성된 컨텍스트 이름은 다루기 어려울 수 있습니다. 컨텍스트 이름을 더 쉽게 읽을 수 있고 매개 변수로 쉽게 사용할 수 있도록 컨텍스트의 이름을 바꿀 수 있습니다. 컨텍스트 이름 바꾸기는 [Rename-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/rename-azcontext)를 통해 수행됩니다.

    ```powershell
    Rename-AzContext -SourceName <current_context_name> -TargetName <new_context_Name>
    ```

1. 특정 Azure 구독을 현재 PowerShell 세션에 사용하려면 [Select-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/select-azcontext)를 사용합니다.

    ```powershell
    Select-AzContext <context_name>
    ```

## <a name="configure-terraform"></a>Terraform 구성

1. [Terraform을 다운로드](https://www.terraform.io/downloads.html)합니다.

1. 다운로드에서 실행 파일의 압축을 선택한 디렉터리에 풉니다.

1. [시스템의 전역 경로를 실행 파일로 업데이트](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)합니다.

1. `terraform` 명령을 사용하여 글로벌 경로 구성을 확인합니다. Terraform 실행 파일이 있으면 사용 가능한 Terraform 옵션 목록이 표시됩니다.

    ```powershell
    terraform
    ```

    Terraform 실행 파일이 있으면 구문 및 사용 가능한 명령이 나열됩니다.

    ```output
    Usage: terraform [-version] [-help] <command> [args]

    The available commands for execution are listed below.
    The most common, useful commands are shown first, followed by
    less common or more advanced commands. If you're just getting
    started with Terraform, stick with the common commands. For the
    other commands, please read the help and docs before usage.
    ...
    ```

## <a name="create-a-terraform-configuration-file"></a>Terraform 구성 파일 만들기

이 섹션에서는 Azure 리소스 그룹을 만드는 Terraform 구성 파일을 만드는 방법에 대해 알아봅니다.

1. 이 데모에 대한 Terraform 파일을 저장할 디렉터리를 만듭니다.

1. 디렉터리를 데모 디렉터리로 변경합니다.

1. 즐겨찾는 편집기를 사용하여 `QuickstartTerraformTest.tf`라는 Terraform 구성 파일을 만듭니다.

1. 다음 HCL을 새 파일에 붙여 넣습니다.

    ```hcl
    provider "azurerm" {
      # The "feature" block is required for AzureRM provider 2.x.
      # If you are using version 1.x, the "features" block is not allowed.
      version = "~>2.0"
      features {}
    }
    resource "azurerm_resource_group" "rg" {
            name = "QuickstartTerraformTest-rg"
            location = "eastus"
    }
    ```

    참고:
    - 공급자 블록은 [Azure 공급자(azurerm)](https://www.terraform.io/docs/providers/azurerm/index.html)를 사용하도록 지정합니다.
    - azurerm 공급자 블록 내에서 버전 및 기능 특성이 설정됩니다. 주석에 언급된 대로, 용도는 버전에 따라 다릅니다. 환경에 이러한 특성을 설정하는 방법에 대한 자세한 내용은 [AzureRM 공급자의 v2.0](https://www.terraform.io/docs/providers/azurerm/guides/2.0-upgrade-guide.html)을 참조하세요.
    - [resource declaration](https://www.terraform.io/docs/configuration/resources.html)은 [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html) 리소스 종류에 대해서만 적용됩니다. azure_resource_group에 대한 두 가지 필수 인수는 name 및 location입니다.

## <a name="create-and-apply-a-terraform-execution-plan"></a>Terraform 실행 계획 만들기 및 적용

구성 파일이 만들어지면 이 섹션에서 *실행 계획*을 만들고 클라우드 인프라에 적용하는 방법에 대해 설명합니다.

1. [terraform init](https://www.terraform.io/docs/commands/init.html)를 사용하여 Terraform 배포를 초기화합니다. 이 단계에서는 Azure 리소스 그룹을 만드는 데 필요한 Azure 모듈을 다운로드합니다.

    ```powershell
    terraform init
    ```
    
1. Terraform을 사용하면 [terraform plan](https://www.terraform.io/docs/commands/plan.html)을 통해 완료되는 작업을 미리 볼 수 있습니다.

    ```powershell
    terraform plan
    ```

    **참고:**
    - `terraform plan` 명령은 실행 계획을 만들지만 실행하지는 않습니다. 대신 구성 파일에 지정된 구성을 만드는 데 필요한 작업을 결정합니다.
    - `terraform plan` 명령을 사용하면 실제 리소스를 변경하기 전에 실행 계획이 예상과 일치하는지 확인할 수 있습니다.
    - 선택 사항인 `-out` 매개 변수를 사용하여 계획의 출력 파일을 지정할 수 있습니다. `-out` 매개 변수를 사용하는 방법에 대한 자세한 내용은 [이후 배포를 위해 실행 계획 유지](#persist-an-execution-plan-for-later-deployment) 섹션을 참조하세요.

1. [terraform apply](https://www.terraform.io/docs/commands/apply.html)를 사용하여 실행 계획을 적용합니다.

    ```powershell
    terraform apply
    ```
    
1. Terraform은 실행 계획을 적용하고 실행을 확인해야 할 경우 어떻게 되는지 보여줍니다. `yes`를 입력하고 **Enter** 키를 눌러 명령을 확인합니다.

1. 재생 실행이 확인되면 [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show)를 사용하여 리소스 그룹이 성공적으로 만들어지는지 테스트합니다.

    ```powershell
    Get-AzResourceGroup -Name QuickstartTerraformTest-rg
    ```

    성공하면 명령은 새로 만들어진 리소스 그룹의 다양한 특성을 표시합니다.

## <a name="persist-an-execution-plan-for-later-deployment"></a>이후 배포를 위해 실행 계획 유지

이전 섹션에서는 [terraform plan](https://www.terraform.io/docs/commands/plan.html)을 실행하여 실행 계획을 만드는 방법을 살펴보았습니다. 그런 다음, [terraform apply](https://www.terraform.io/docs/commands/apply.html)를 사용하여 해당 계획을 적용하는 것을 살펴보았습니다. 이 패턴은 단계가 대화식이고 순차적일 때 효과적입니다.

더 복잡한 시나리오의 경우 실행 계획을 파일에 유지할 수 있습니다. 이후 또는 다른 머신에서 실행하는 경우에도 해당 실행 계획을 적용할 수 있습니다.

이 기능을 사용하는 경우 [자동으로 Terraform 실행](https://learn.hashicorp.com/terraform/development/running-terraform-in-automation) 문서를 읽을 것을 권장합니다.

다음 단계에서는 이 기능을 사용하기 위한 기본 패턴을 보여줍니다.

1. [terraform init](https://www.terraform.io/docs/commands/init.html)를 실행합니다.

    ```powershell
    terraform init
    ```

1. `-out` 매개 변수를 사용하여 `terraform plan`을 실행합니다.

    ```powershell
    terraform plan -out QuickstartTerraformTest.tfplan
    ```

1. 이전 단계에서 파일 이름을 지정하여 `terraform apply`를 실행합니다.

    ```powershell
    terraform apply QuickstartTerraformTest.tfplan
    ```

참고:
- 자동화와 함께 사용하면 `terraform apply <filename>`을 실행할 때 확인이 필요하지 않습니다.
- 이 기능을 사용하려면 [보안 경고 섹션](https://www.terraform.io/docs/commands/plan.html#security-warning)을 읽습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 이 문서에서 만든 리소스를 삭제합니다.

1. 현재 실행 계획을 취소할 [terraform destroy](https://www.terraform.io/docs/commands/destroy.html)를 실행합니다.

    ```powershell
    terraform destroy
    ```

1. Terraform은 실행 계획을 취소하고 확인해야 할 경우 어떻게 되는지 보여줍니다. `yes`를 입력하고 **Enter** 키를 눌러 명령을 확인합니다.

1. 재생 실행이 확인되고 출력이 다음 예제와 비슷하면 [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show)를 사용하여 리소스 그룹이 삭제되었는지 확인합니다.

    ```powershell
    Get-AzResourceGroup -Name QuickstartTerraformTest-rg
    ```

    참고:
    - 성공하면 `Get-AzResourceGroup`에서 리소스 그룹이 없다는 사실을 표시합니다.

1. 디렉터리를 상위 디렉터리로 변경하고 데모 디렉터리를 제거합니다. `-r` 매개 변수는 디렉터리를 제거하기 전에 디렉터리 콘텐츠를 제거합니다. 디렉터리 콘텐츠에는 이전에 만든 구성 파일과 Terraform 상태 파일이 포함됩니다.

    ```bash
    cd .. && rm -r QuickstartTerraformTest
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Terraform으로 Azure VM 만들기](create-linux-virtual-machine-with-infrastructure.md)