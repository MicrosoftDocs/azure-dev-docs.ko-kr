---
title: 자습서 - Azure Container Instances에 Azure Cosmos DB 배포
description: Terraform을 사용하여 Azure Cosmos DB를 Azure Container Instances에 배포하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 04/26/2020
ms.openlocfilehash: d0a175d3b465de640c151bd2b8ce339f0b5ade5b
ms.sourcegitcommit: 8cd0ddf1651c3b64bb72dedc2890108c2cfe3bcb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87334368"
---
# <a name="tutorial-deploy-an-azure-cosmos-db-to-azure-container-instances"></a>자습서: Azure Container Instances에 Azure Cosmos DB 배포

이 자습서에서는 Terraform을 사용하여 Azure 리소스를 만듭니다. 이 문서의 단계에서는 다음 리소스를 만드는 과정을 안내합니다.

> [!div class="checklist"]
> * Azure Cosmos DB 인스턴스
> * Azure Container Instance
> * 이러한 두 리소스에서 작동하는 앱

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="create-first-configuration"></a>첫 번째 구성 만들기

이 섹션에서는 Azure Cosmos DB 인스턴스에 대한 구성을 만듭니다.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. Azure Cloud Shell을 엽니다.

1. Cloud Shell 편집기를 시작합니다.

    ```bash
    code main.tf
    ```

1. 이 단계의 구성은 몇 가지 Azure 리소스를 모델링합니다. 이러한 리소스에는 Azure 리소스 그룹 및 Azure Cosmos DB 인스턴스가 포함됩니다. 임의의 정수는 고유한 Cosmos DB 인스턴스 이름을 만드는 데 사용됩니다. 또한 여러 Cosmos DB 설정이 구성됩니다. 자세한 내용은 [Cosmos DB Terraform 참조](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html)를 참조하세요. 다음 Terraform 구성을 복사하여 편집기에 붙여 넣습니다.

    ```hcl
    resource "azurerm_resource_group" "vote-resource-group" {
      name     = "vote-resource-group"
      location = "westus"
    }

    resource "random_integer" "ri" {
      min = 10000
      max = 99999
    }

    resource "azurerm_cosmosdb_account" "vote-cosmos-db" {
      name                = "tfex-cosmos-db-${random_integer.ri.result}"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
      offer_type          = "Standard"
      kind                = "GlobalDocumentDB"

      consistency_policy {
        consistency_level       = "BoundedStaleness"
        max_interval_in_seconds = 10
        max_staleness_prefix    = 200
      }

      geo_location {
        location          = "westus"
        failover_priority = 0
      }
    }
    ```

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

## <a name="run-the-configuration"></a>구성 실행

이 섹션에서는 여러 Terraform 명령을 사용하여 구성을 실행합니다.

1. [terraform init](https://www.terraform.io/docs/commands/init.html) 명령은 작업 디렉터리를 초기화합니다. Cloud Shell에서 다음 명령을 실행합니다.

    ```bash
    terraform init
    ```

1. [terraform plan](https://www.terraform.io/docs/commands/plan.html) 명령은 구성 구문의 유효성을 검사하는 데 사용할 수 있습니다. `-out` 매개 변수는 결과를 파일로 보냅니다. 나중에 이 출력 파일을 사용하여 구성을 적용할 수 있습니다. Cloud Shell에서 다음 명령을 실행합니다.

    ```bash
    terraform plan --out plan.out
    ```

1. [terraform apply](https://www.terraform.io/docs/commands/apply.html) 명령은 구성을 적용하는 데 사용됩니다. 이전 단계의 출력 파일이 지정되었습니다. 이 명령을 수행하면 Azure 리소스가 생성됩니다. Cloud Shell에서 다음 명령을 실행합니다.

    ```bash
    terraform apply plan.out
    ```

1. Azure Portal 내에서 결과를 확인하려면 새 리소스 그룹으로 이동합니다. 새 Azure Cosmos DB 인스턴스는 새 리소스 그룹에 있습니다.

## <a name="update-configuration"></a>구성 업데이트

이 섹션에서는 Azure Container 인스턴스를 포함하도록 구성을 업데이트하는 방법을 보여 줍니다. 컨테이너는 Cosmos DB에 데이터를 읽고 쓰는 애플리케이션을 실행합니다.

1. Cloud Shell 편집기를 시작합니다.

    ```bash
    code main.tf
    ```

1. 이 단계의 구성은 `COSMOS_DB_ENDPOINT` 및 `COSMOS_DB_MASTERKEY`의 두 환경 변수를 설정합니다. 이러한 변수는 데이터베이스에 액세스하기 위한 위치 및 키를 보관합니다. 이러한 변수의 값은 이전 단계에서 만든 데이터베이스 인스턴스에서 가져옵니다. 이 프로세스를 보간이라고 합니다. Terraform 보간에 대한 자세한 내용은 [보간 구문](https://www.terraform.io/docs/configuration/interpolation.html)을 참조하세요. 구성에는 컨테이너 인스턴스의 FQDN(정규화된 도메인 이름)을 반환하는 출력 블록도 포함됩니다. 다음 코드를 복사하여 편집기에 붙여 넣습니다.

    ```hcl
    resource "azurerm_container_group" "vote-aci" {
      name                = "vote-aci"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
      ip_address_type     = "public"
      dns_name_label      = "vote-aci"
      os_type             = "linux"

      container {
        name   = "vote-aci"
        image  = "microsoft/azure-vote-front:cosmosdb"
        cpu    = "0.5"
        memory = "1.5"
        ports {
          port     = 80
          protocol = "TCP"
        }

        secure_environment_variables = {
          "COSMOS_DB_ENDPOINT"  = azurerm_cosmosdb_account.vote-cosmos-db.endpoint
          "COSMOS_DB_MASTERKEY" = azurerm_cosmosdb_account.vote-cosmos-db.primary_master_key
          "TITLE"               = "Azure Voting App"
          "VOTE1VALUE"          = "Cats"
          "VOTE2VALUE"          = "Dogs"
        }
      }
    }

    output "dns" {
      value = azurerm_container_group.vote-aci.fqdn
    }
    ```

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

1. 이전 섹션에서 수행한 것처럼 다음 명령을 실행하여 변경 내용을 시각적으로 표시합니다.

    ```bash
    terraform plan --out plan.out
    ```

1. `terraform apply` 명령을 실행하여 구성을 적용합니다.

    ```bash
    terraform apply plan.out
    ```

1. 컨테이너 인스턴스 FQDN을 적어 둡니다.

## <a name="test-application"></a>애플리케이션 테스트

애플리케이션을 테스트하려면 컨테이너 인스턴스의 FQDN으로 이동합니다. 다음 출력과 유사한 결과가 표시됩니다.

![Azure 투표 애플리케이션](media/deploy-azure-cosmos-db-to-azure-container-instances/azure-vote.jpg)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 이 문서에서 만든 리소스를 삭제합니다.

[terraform destroy](https://www.terraform.io/docs/commands/destroy.html) 명령을 실행하여 이 자습서에서 만든 Azure 리소스를 제거합니다.

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Terraform을 설치하고 구성하여 Azure 리소스를 프로비저닝합니다](get-started-cloud-shell.md).