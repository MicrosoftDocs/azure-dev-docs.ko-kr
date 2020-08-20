---
title: Terraform을 사용하여 AKS(Azure Kubernetes Service)에서 Kubernetes 클러스터 만들기
description: Azure Kubernetes Service 및 Terraform을 사용하여 Kubernetes 클러스터를 만드는 방법을 알아봅니다.
keywords: azure devops terraform aks kubernetes
ms.topic: how-to
ms.date: 03/09/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 726f4544f4c417792d784dfaddf93a3d79eaec9e
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88241255"
---
# <a name="create-a-kubernetes-cluster-with-azure-kubernetes-service-using-terraform"></a>Terraform을 사용하여 Azure Kubernetes Service로 Kubernetes 클러스터 만들기

[AKS(Azure Kubernetes Service)](/azure/aks/)는 호스트된 Kubernetes 환경을 관리합니다. AKS를 사용하면 컨테이너 오케스트레이션 전문 지식 없이도 컨테이너화된 애플리케이션을 배포 및 관리할 수 있습니다. AKS를 사용하면 앱을 오프라인 상태로 전환하지 않고도 많은 일반적인 유지 관리 작업을 수행할 수 있습니다. 이러한 작업에는 주문형 리소스 프로비저닝, 업그레이드 및 확장이 포함됩니다.

이 문서에서는 다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * HCL(HashiCorp Language)을 사용하여 Kubernetes 클러스터 정의
> * Terraform 및 AKS를 사용하여 Kubernetes 클러스터 만들기
> * kubectl 도구를 사용하여 Kubernetes 클러스터의 가용성 테스트

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

- **Terraform 구성**: [Terraform 및 Azure에 액세스 구성](get-started-cloud-shell.md) 문서의 지침을 따릅니다.

- **Azure 서비스 주체**: [Azure CLI를 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) 문서의 **서비스 주체 만들기** 섹션에 있는 지침을 따릅니다. `appId`, `displayName`, `password` 및 `tenant` 값을 적어 둡니다.

## <a name="create-the-directory-structure"></a>디렉터리 구조 만들기

첫 번째 단계는 연습용 Terraform 구성 파일을 넣을 디렉터리를 만드는 것입니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. [Azure Cloud Shell](/azure/cloud-shell/overview)을 엽니다. 이전에 환경을 선택하지 않은 경우 환경으로 **Bash**를 선택합니다.

    ![Cloud Shell 프롬프트](./media/create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. 디렉터리를 `clouddrive` 디렉터리로 변경합니다.

    ```bash
    cd clouddrive
    ```

1. 이름이 `terraform-aks-k8s`인 디렉터리를 만듭니다.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. 디렉터리를 새 디렉터리로 변경합니다.

    ```bash
    cd terraform-aks-k8s
    ```

## <a name="declare-the-azure-provider"></a>Azure 공급자 선언

Azure 공급자를 선언하는 Terraform 구성 파일을 만듭니다.

1. Cloud Shell에서 이름이 `main.tf`인 파일을 만듭니다.

    ```bash
    code main.tf
    ```

1. 다음 코드를 편집기에 붙여 넣습니다.

    ```hcl
    provider "azurerm" {
        # The "feature" block is required for AzureRM provider 2.x. 
        # If you are using version 1.x, the "features" block is not allowed.
        version = "~>2.0"
        features {}
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

## <a name="define-a-kubernetes-cluster"></a>Kubernetes 클러스터 정의

Kubernetes 클러스터용 리소스를 선언하는 Terraform 구성 파일을 만듭니다.

1. Cloud Shell에서 이름이 `k8s.tf`인 파일을 만듭니다.

    ```bash
    code k8s.tf
    ```

1. 다음 코드를 편집기에 붙여 넣습니다.

    ```hcl
    resource "azurerm_resource_group" "k8s" {
        name     = var.resource_group_name
        location = var.location
    }
    
    resource "random_id" "log_analytics_workspace_name_suffix" {
        byte_length = 8
    }

    resource "azurerm_log_analytics_workspace" "test" {
        # The WorkSpace name has to be unique across the whole of azure, not just the current subscription/tenant.
        name                = "${var.log_analytics_workspace_name}-${random_id.log_analytics_workspace_name_suffix.dec}"
        location            = var.log_analytics_workspace_location
        resource_group_name = azurerm_resource_group.k8s.name
        sku                 = var.log_analytics_workspace_sku
    }

    resource "azurerm_log_analytics_solution" "test" {
        solution_name         = "ContainerInsights"
        location              = azurerm_log_analytics_workspace.test.location
        resource_group_name   = azurerm_resource_group.k8s.name
        workspace_resource_id = azurerm_log_analytics_workspace.test.id
        workspace_name        = azurerm_log_analytics_workspace.test.name

        plan {
            publisher = "Microsoft"
            product   = "OMSGallery/ContainerInsights"
        }
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = var.cluster_name
        location            = azurerm_resource_group.k8s.location
        resource_group_name = azurerm_resource_group.k8s.name
        dns_prefix          = var.dns_prefix

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
                key_data = file(var.ssh_public_key)
            }
        }

        default_node_pool {
            name            = "agentpool"
            node_count      = var.agent_count
            vm_size         = "Standard_D2_v2"
        }

        service_principal {
            client_id     = var.client_id
            client_secret = var.client_secret
        }

        addon_profile {
            oms_agent {
            enabled                    = true
            log_analytics_workspace_id = azurerm_log_analytics_workspace.test.id
            }
        }
        
        network_profile {
        load_balancer_sku = "Standard"
        network_plugin = "kubenet"
        }

        tags = {
            Environment = "Development"
        }
    }
    ```

    위의 코드는 클러스터의 이름, 위치 및 리소스 그룹 이름을 설정합니다. FQDN(정규화된 도메인 이름)의 접두사도 설정됩니다. FQDN은 클러스터에 액세스하는 데 사용됩니다.

    `linux_profile` 레코드를 사용하면 SSH를 사용하여 작업자 노드에 로그인할 수 있는 설정을 구성할 수 있습니다.

    AKS를 사용하면 작업자 노드에 대해서만 지불합니다. `default_node_pool` 레코드는 이러한 작업자 노드에 대한 세부 정보를 구성합니다. `default_node_pool record`에는 만들 작업자 노드 수와 작업자 노드 유형이 포함됩니다. 나중에 클러스터를 확장하거나 축소해야 하는 경우, 이 레코드에서 `count` 값을 수정합니다.

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

## <a name="declare-the-variables"></a>변수 선언

1. Cloud Shell에서 이름이 `variables.tf`인 파일을 만듭니다.

    ```bash
    code variables.tf
    ```

1. 다음 코드를 편집기에 붙여 넣습니다.

    ```hcl
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }

    variable log_analytics_workspace_name {
        default = "testLogAnalyticsWorkspaceName"
    }

    # refer https://azure.microsoft.com/global-infrastructure/services/?products=monitor for log analytics available regions
    variable log_analytics_workspace_location {
        default = "eastus"
    }

   # refer https://azure.microsoft.com/pricing/details/monitor/ for log analytics pricing 
   variable log_analytics_workspace_sku {
        default = "PerGB2018"
   }
    ```

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

## <a name="create-a-terraform-output-file"></a>Terraform 출력 파일 만들기

[Terraform 출력](https://www.terraform.io/docs/configuration/outputs.html)을 사용하면 Terraform이 계획을 적용할 때 사용자에게 강조 표시되는 값 및 `terraform output` 명령을 사용하여 쿼리할 수 있는 값을 정의할 수 있습니다. 이 섹션에서는 [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) 도구를 사용하여 클러스터 액세스를 허용하는 출력 파일을 만듭니다.

1. Cloud Shell에서 이름이 `output.tf`인 파일을 만듭니다.

    ```bash
    code output.tf
    ```

1. 다음 코드를 편집기에 붙여 넣습니다.

    ```hcl
    output "client_key" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_key
    }

    output "client_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate
    }

    output "cluster_ca_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate
    }

    output "cluster_username" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.username
    }

    output "cluster_password" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.password
    }

    output "kube_config" {
        value = azurerm_kubernetes_cluster.k8s.kube_config_raw
    }

    output "host" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.host
    }
    ```

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Terraform 상태를 저장하도록 Azure Storage 설정

Terraform은 `terraform.tfstate` 파일을 통해 로컬로 상태를 추적합니다. 이 패턴은 단일 작업자 환경에서 잘 작동합니다. 다중 사용자 환경에서 [Azure 스토리지](/azure/storage/)는 상태를 추적하는 데 사용됩니다.

이 섹션에서는 다음 작업을 수행하는 방법을 보여줍니다.
- 스토리지 계정 정보 검색(계정 이름 및 계정 키)
- Terraform 상태 정보가 저장되는 스토리지 컨테이너를 만듭니다.

1. Azure Portal에서 왼쪽 메뉴에 있는 **모든 서비스**를 선택합니다.

1. **스토리지 계정**을 선택합니다.

1. **스토리지 계정** 탭에서 Terraform가 상태를 저장하도록 설정할 스토리지 계정의 이름을 선택합니다. 예를 들어, Cloud Shell을 처음 열 때 만들어진 스토리지 계정을 사용할 수 있습니다.  Cloud Shell에서 만든 스토리지 계정 이름은 일반적으로 `cs`로 시작되고 그 뒤에 숫자 및 문자로 이루어진 임의의 문자열이 나옵니다. 선택한 스토리지 계정을 기록해 둡니다. 이 값은 나중에 필요합니다.

1. [스토리지 계정] 탭에서 **액세스 키**를 선택합니다.

    ![스토리지 계정 메뉴](./media/create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. **key1** **key** 값을 기록해 둡니다. (키 오른쪽에 있는 아이콘을 선택하면 값이 클립보드에 복사됩니다.)

    ![스토리지 계정 액세스 키](./media/create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. Cloud Shell에서 Azure 스토리지 계정에 컨테이너를 만듭니다. 자리 표시자를 사용자 환경에 적합한 값으로 바꿉니다.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Kubernetes 클러스터 만들기

이 섹션에서는 `terraform init` 명령을 사용하여 이전 섹션에서 만든 구성 파일에 정의된 리소스를 만드는 방법을 보여줍니다.

1. Cloud Shell에서 Terraform을 초기화합니다. 자리 표시자를 사용자 환경에 적합한 값으로 바꿉니다.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    `terraform init` 명령은 백 엔드 및 공급자 플러그 인 초기화 성공을 표시합니다.

    ![“terraform init” 결과의 예](./media/create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. 서비스 주체 자격 증명 내보내기 자리 표시자를 서비스 주체의 적절한 값으로 바꿉니다.

    ```bash
    export TF_VAR_client_id=<service-principal-appid>
    export TF_VAR_client_secret=<service-principal-password>
    ```

1. `terraform plan` 명령을 실행하여 인프라 요소를 정의하는 Terraform 계획을 만듭니다. 

    ```bash
    terraform plan -out out.plan
    ```

    `terraform plan` 명령은 `terraform apply` 명령 실행 시 만들어질 리소스를 표시합니다.

    ![“terraform plan” 결과의 예](./media/create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. `terraform apply` 명령을 실행하여 Kubernetes 클러스터를 만들 계획에 적용합니다. Kubernetes 클러스터를 만드는 프로세스는 몇 분 정도 소요될 수도 있으며 이 경우 Cloud Shell 세션 시간이 초과됩니다. Cloud Shell 세션의 시간이 초과되면 "Cloud Shell 시간 초과에서 복구" 섹션의 단계에 따라 프로세스를 완료할 수 있습니다.

    ```bash
    terraform apply out.plan
    ```

    `terraform apply` 명령은 구성 파일에 정의된 리소스 만들기의 결과를 표시합니다.

    ![“terraform apply” 결과의 예](./media/create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. Azure Portal에서 왼쪽 메뉴에 있는 **모든 리소스**를 선택하여 새 Kubernetes 클러스터에 대해 만든 리소스가 표시됩니다.

    ![Azure Portal의 모든 리소스](./media/create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Cloud Shell 시간 초과에서 복구

Cloud Shell 세션의 시간이 초과되면 다음 단계를 수행하여 복구할 수 있습니다.

1. Cloud Shell 세션을 시작합니다.

1. Terraform 구성 파일이 있는 디렉터리로 변경합니다.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. 다음 명령 실행:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>Kubernetes 클러스터 테스트

새로 만든 클러스터를 Kubernetes 도구를 사용해 확인할 수 있습니다.

1. Terraform 상태에서 Kubernetes 구성을 가져오고 이를 kubectl이 읽을 수 있는 파일에 저장합니다.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. kubectl이 올바른 구성을 선택할 수 있도록 환경 변수를 설정합니다.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. 클러스터 상태를 확인합니다.

    ```bash
    kubectl get nodes
    ```

    작업자 노드의 세부 정보가 표시됩니다. 노드는 다음 이미지에 나온 대로 모두 **Ready**상태여야 합니다.

    ![kubectl 도구를 사용하여 Kubernetes 클러스터의 상태 확인](./media/create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="monitor-health-and-logs"></a>상태 및 로그 모니터링

AKS 클러스터가 생성될 때 클러스터 노드와 Pod의 상태 메트릭을 캡처하기 위해 모니터링이 설정되었습니다. 이 상태 메트릭은 Azure Portal에서 사용할 수 있습니다. 컨테이너 상태 모니터링에 대한 자세한 내용은 [Azure Kubernetes Service 상태 모니터링](/azure/azure-monitor/insights/container-insights-overview)을 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure에서 Terraform을 사용하는 방법에 대해 자세히 알아보기](/azure/terraform)
