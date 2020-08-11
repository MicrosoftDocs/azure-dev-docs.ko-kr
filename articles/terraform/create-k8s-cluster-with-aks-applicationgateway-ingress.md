---
title: Azure Kubernetes Service에 Application Gateway 수신 컨트롤러 만들기
description: Azure Kubernetes Service를 사용하여 수신 컨트롤러로 Application Gateway를 사용하는 Kubernetes 클러스터를 만드는 방법을 알아봅니다.
keywords: azure devops terraform 애플리케이션 게이트웨이 수신 aks kubernetes
ms.topic: how-to
ms.date: 03/09/2020
ms.openlocfilehash: 4e34942257baa1e530a7ed0990a6d30a5297dadf
ms.sourcegitcommit: e451e4360d9c5956cc6a50880b3a7a55aa4efd2f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87478523"
---
# <a name="create-an-application-gateway-ingress-controller-in-azure-kubernetes-service"></a>Azure Kubernetes Service에 Application Gateway 수신 컨트롤러 만들기

[AKS(Azure Kubernetes Service)](/azure/aks/)는 호스트된 Kubernetes 환경을 관리합니다. AKS를 사용하면 컨테이너 오케스트레이션 전문 지식 없이도 컨테이너화된 애플리케이션을 쉽고 빠르게 배포 및 관리할 수 있습니다. AKS는 운영 및 유지 관리 작업을 위해 애플리케이션을 오프라인으로 전환하는 부담을 없애줍니다. AKS를 사용하면 이러한 작업(예: 리소스 프로비저닝, 업그레이드 및 크기 조정)을 요청 시 수행할 수 있습니다.

수신 컨트롤러는 Kubernetes 서비스에 대해 다양한 기능을 제공합니다. 이러한 기능에는 역방향 프록시, 구성 가능한 트래픽 라우팅 및 TLS 종료가 포함됩니다. Kubernetes 수신 리소스는 개별 Kubernetes 서비스에 대한 수신 규칙을 구성하는 데 사용됩니다. 수신 컨트롤러 및 수신 규칙을 사용하면 단일 IP 주소를 사용하여 Kubernetes 클러스터의 여러 서비스에 트래픽을 라우팅할 수 있습니다. 이러한 모든 기능이 Azure [Application Gateway](/azure/Application-Gateway/)에서 제공되기 때문에 Azure의 Kubernetes에 이상적인 수신 컨트롤러입니다. 

이 문서에서는 다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * AKS를 Application Gateway에서 수신 컨트롤러로 사용하여 [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) 클러스터 만들기
> * HCL(HashiCorp Language)을 사용하여 Kubernetes 클러스터 정의
> * Terraform을 사용하여 Application Gateway 리소스 만들기
> * Terraform 및 AKS를 사용하여 Kubernetes 클러스터 만들기
> * kubectl 도구를 사용하여 Kubernetes 클러스터의 가용성 테스트

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

- **Terraform 구성**: [Terraform 및 Azure에 액세스 구성](get-started-cloud-shell.md) 문서의 지침을 따릅니다.

- **Azure 리소스 그룹**: 데모에 사용할 Azure 리소스 그룹이 없는 경우 [Azure 리소스 그룹을 만듭니다](/azure/azure-resource-manager/manage-resource-groups-portal#create-resource-groups). 해당 값이 데모에 사용되므로 리소스 그룹 이름과 위치를 기록해 둡니다.

- **Azure 서비스 주체**: [Azure CLI를 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) 문서의 **서비스 주체 만들기** 섹션에 나온 지침을 따릅니다. `appId`, `displayName` 및 `password` 값을 적어 둡니다.

- **서비스 사용자 개체 ID 가져오기**: Cloud Shell에서 다음 명령을 실행합니다. `az ad sp list --display-name <displayName>`

## <a name="create-the-directory-structure"></a>디렉터리 구조 만들기

첫 번째 단계는 연습용 Terraform 구성 파일을 넣을 디렉터리를 만드는 것입니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. [Azure Cloud Shell](/azure/cloud-shell/overview)을 엽니다.

1. 디렉터리를 `clouddrive` 디렉터리로 변경합니다.

    ```bash
    cd clouddrive
    ```

1. 이름이 `terraform-aks-appgw-ingress`인 디렉터리를 만듭니다.

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. 디렉터리를 새 디렉터리로 변경합니다.

    ```bash
    cd terraform-aks-appgw-ingress
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

## <a name="define-input-variables"></a>입력 변수 정의

이 배포에 필요한 모든 변수를 나열하는 Terraform 구성 파일을 만듭니다.

1. Cloud Shell에서 이름이 `variables.tf`인 파일을 만듭니다.

    ```bash
    code variables.tf
    ```

1. 다음 코드를 편집기에 붙여 넣습니다.
    
    ```hcl
    variable "resource_group_name" {
      description = "Name of the resource group."
    }

    variable "location" {
      description = "Location of the cluster."
    }

    variable "aks_service_principal_app_id" {
      description = "Application ID/Client ID  of the service principal. Used by AKS to manage AKS related resources on Azure like vms, subnets."
    }

    variable "aks_service_principal_client_secret" {
      description = "Secret of the service principal. Used by AKS to manage Azure."
    }

    variable "aks_service_principal_object_id" {
      description = "Object ID of the service principal."
    }

    variable "virtual_network_name" {
      description = "Virtual network name"
      default     = "aksVirtualNetwork"
    }

    variable "virtual_network_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/8"
    }

    variable "aks_subnet_name" {
      description = "AKS Subnet Name."
      default     = "kubesubnet"
    }

    variable "aks_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/16"
    }

    variable "app_gateway_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.1.0.0/16"
    }

    variable "app_gateway_name" {
      description = "Name of the Application Gateway."
      default = "ApplicationGateway1"
    }

    variable "app_gateway_sku" {
      description = "Name of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "app_gateway_tier" {
      description = "Tier of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "aks_name" {
      description = "Name of the AKS cluster."
      default     = "aks-cluster1"
    }
    variable "aks_dns_prefix" {
      description = "Optional DNS prefix to use with hosted Kubernetes API server FQDN."
      default     = "aks"
    }

    variable "aks_agent_os_disk_size" {
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 applies the default disk size for that agentVMSize."
      default     = 40
    }

    variable "aks_agent_count" {
      description = "The number of agent nodes for the cluster."
      default     = 3
    }

    variable "aks_agent_vm_size" {
      description = "The size of the Virtual Machine."
      default     = "Standard_D3_v2"
    }

    variable "kubernetes_version" {
      description = "The version of Kubernetes."
      default     = "1.11.5"
    }

    variable "aks_service_cidr" {
      description = "A CIDR notation IP range from which to assign service cluster IPs."
      default     = "10.0.0.0/16"
    }

    variable "aks_dns_service_ip" {
      description = "Containers DNS server IP address."
      default     = "10.0.0.10"
    }

    variable "aks_docker_bridge_cidr" {
      description = "A CIDR notation IP for Docker bridge."
      default     = "172.17.0.1/16"
    }

    variable "aks_enable_rbac" {
      description = "Enable RBAC on the AKS cluster. Defaults to false."
      default     = "false"
    }

    variable "vm_user_name" {
      description = "User name for the VM"
      default     = "vmuser1"
    }

    variable "public_ssh_key_path" {
      description = "Public key path for SSH."
      default     = "~/.ssh/id_rsa.pub"
    }

    variable "tags" {
      type = map(string)

      default = {
        source = "terraform"
      }
    }
    ```

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

## <a name="define-the-resources"></a>리소스 정의 
모든 리소스를 만드는 Terraform 구성 파일을 만듭니다. 

1. Cloud Shell에서 이름이 `resources.tf`인 파일을 만듭니다.

    ```bash
    code resources.tf
    ```

1. 다음 코드 블록을 붙여넣어 계산 변수가 재사용할 로컬 블록을 만듭니다.

    ```hcl
    # # Locals block for hardcoded names. 
    locals {
        backend_address_pool_name      = "${azurerm_virtual_network.test.name}-beap"
        frontend_port_name             = "${azurerm_virtual_network.test.name}-feport"
        frontend_ip_configuration_name = "${azurerm_virtual_network.test.name}-feip"
        http_setting_name              = "${azurerm_virtual_network.test.name}-be-htst"
        listener_name                  = "${azurerm_virtual_network.test.name}-httplstn"
        request_routing_rule_name      = "${azurerm_virtual_network.test.name}-rqrt"
        app_gateway_subnet_name = "appgwsubnet"
    }
    ```

1. 다음 코드 블록을 붙여넣어 리소스 그룹, 새 사용자 ID에 대한 데이터 원본을 만듭니다.

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = var.resource_group_name
    }

    # User Assigned Identities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      name = "identity1"

      tags = var.tags
    }
    ```

1. 다음 코드 블록을 붙여넣어 기본 네트워킹 리소스를 만듭니다.

    ```hcl
    resource "azurerm_virtual_network" "test" {
      name                = var.virtual_network_name
      location            = data.azurerm_resource_group.rg.location
      resource_group_name = data.azurerm_resource_group.rg.name
      address_space       = [var.virtual_network_address_prefix]

      subnet {
        name           = var.aks_subnet_name
        address_prefix = var.aks_subnet_address_prefix
      }

      subnet {
        name           = "appgwsubnet"
        address_prefix = var.app_gateway_subnet_address_prefix
      }

      tags = var.tags
    }

    data "azurerm_subnet" "kubesubnet" {
      name                 = var.aks_subnet_name
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = data.azurerm_resource_group.rg.location
      resource_group_name          = data.azurerm_resource_group.rg.name
      allocation_method            = "Static"
      sku                          = "Standard"

      tags = var.tags
    }
    ```

1. 다음 코드 블록을 붙여넣어 Application Gateway 리소스를 만듭니다.

    ```hcl
    resource "azurerm_application_gateway" "network" {
      name                = var.app_gateway_name
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      sku {
        name     = var.app_gateway_sku
        tier     = "Standard_v2"
        capacity = 2
      }

      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = data.azurerm_subnet.appgwsubnet.id
      }

      frontend_port {
        name = local.frontend_port_name
        port = 80
      }

      frontend_port {
        name = "httpsPort"
        port = 443
      }

      frontend_ip_configuration {
        name                 = local.frontend_ip_configuration_name
        public_ip_address_id = azurerm_public_ip.test.id
      }

      backend_address_pool {
        name = local.backend_address_pool_name
      }

      backend_http_settings {
        name                  = local.http_setting_name
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }

      http_listener {
        name                           = local.listener_name
        frontend_ip_configuration_name = local.frontend_ip_configuration_name
        frontend_port_name             = local.frontend_port_name
        protocol                       = "Http"
      }

      request_routing_rule {
        name                       = local.request_routing_rule_name
        rule_type                  = "Basic"
        http_listener_name         = local.listener_name
        backend_address_pool_name  = local.backend_address_pool_name
        backend_http_settings_name = local.http_setting_name
      }

      tags = var.tags

      depends_on = [azurerm_virtual_network.test, azurerm_public_ip.test]
    }
    ```

1. 다음 코드 블록을 붙여넣어 역할 할당을 만듭니다.

    ```hcl
    resource "azurerm_role_assignment" "ra1" {
      scope                = data.azurerm_subnet.kubesubnet.id
      role_definition_name = "Network Contributor"
      principal_id         = var.aks_service_principal_object_id 

      depends_on = [azurerm_virtual_network.test]
    }

    resource "azurerm_role_assignment" "ra2" {
      scope                = azurerm_user_assigned_identity.testIdentity.id
      role_definition_name = "Managed Identity Operator"
      principal_id         = var.aks_service_principal_object_id
      depends_on           = [azurerm_user_assigned_identity.testIdentity]
    }

    resource "azurerm_role_assignment" "ra3" {
      scope                = azurerm_application_gateway.network.id
      role_definition_name = "Contributor"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = [azurerm_user_assigned_identity.testIdentity, azurerm_application_gateway.network]
    }

    resource "azurerm_role_assignment" "ra4" {
      scope                = data.azurerm_resource_group.rg.id
      role_definition_name = "Reader"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = [azurerm_user_assigned_identity.testIdentity, azurerm_application_gateway.network]
    }
    ```

1. 다음 코드 블록을 붙여넣어 Kubernetes 클러스터를 만듭니다.

    ```hcl
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = var.aks_name
      location   = data.azurerm_resource_group.rg.location
      dns_prefix = var.aks_dns_prefix

      resource_group_name = data.azurerm_resource_group.rg.name

      linux_profile {
        admin_username = var.vm_user_name

        ssh_key {
          key_data = file(var.public_ssh_key_path)
        }
      }

      addon_profile {
        http_application_routing {
          enabled = false
        }
      }

      default_node_pool {
        name            = "agentpool"
        node_count      = var.aks_agent_count
        vm_size         = var.aks_agent_vm_size
        os_disk_size_gb = var.aks_agent_os_disk_size
        vnet_subnet_id  = data.azurerm_subnet.kubesubnet.id
      }

      service_principal {
        client_id     = var.aks_service_principal_app_id
        client_secret = var.aks_service_principal_client_secret
      }

      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = var.aks_dns_service_ip
        docker_bridge_cidr = var.aks_docker_bridge_cidr
        service_cidr       = var.aks_service_cidr
      }

      depends_on = [azurerm_virtual_network.test, azurerm_application_gateway.network]
      tags       = var.tags
    }

    ```

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

이 섹션에 제시된 코드는 클러스터의 이름, 위치 및 resource_group_name을 설정합니다. 클러스터에 액세스하는 데 사용되는 FQDN(정규화된 도메인 이름)의 일부를 형성하는 `dns_prefix` 값이 설정됩니다.

`linux_profile` 레코드를 사용하면 SSH를 사용하여 작업자 노드에 로그인할 수 있는 설정을 구성할 수 있습니다.

AKS를 사용하면 작업자 노드에 대해서만 지불합니다. `agent_pool_profile` 레코드는 이러한 작업자 노드에 대한 세부 정보를 구성합니다. `agent_pool_profile record`에는 만들 작업자 노드 수와 작업자 노드 유형이 포함됩니다. 나중에 클러스터를 확장하거나 축소해야 하는 경우, 이 레코드에서 `count` 값을 수정합니다.

## <a name="create-a-terraform-output-file"></a>Terraform 출력 파일 만들기

[Terraform 출력](https://www.terraform.io/docs/configuration/outputs.html)을 사용하면 Terraform이 계획을 적용할 때 사용자에게 강조 표시되고 `terraform output` 명령을 사용하여 쿼리할 수 있는 값을 정의할 수 있습니다. 이 섹션에서는 [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) 도구를 사용하여 클러스터 액세스를 허용하는 출력 파일을 만듭니다.

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

    output "identity_resource_id" {
        value = azurerm_user_assigned_identity.testIdentity.id
    }

    output "identity_client_id" {
        value = azurerm_user_assigned_identity.testIdentity.client_id
    }
    ```

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

## <a name="configure-azure-storage-to-store-terraform-state"></a>Terraform 상태를 저장하도록 Azure Storage 구성

Terraform은 `terraform.tfstate` 파일을 통해 로컬로 상태를 추적합니다. 이 패턴은 단일 작업자 환경에서 잘 작동합니다. 그러나 실제의 다중 작업자 환경에서는 [Azure 스토리지](/azure/storage/)를 사용하여 서버의 상태를 추적해야 합니다. 이 섹션에서는 필요한 스토리지 계정 정보를 검색하고 스토리지 컨테이너를 만드는 방법을 알아봅니다. 그런 다음, Terraform 상태 정보를 해당 컨테이너에 저장합니다.

1. Azure Portal의 **Azure 서비스**에서 **스토리지 계정**을 선택합니다. (**스토리지 계정** 옵션이 주 페이지에 표시되지 않으면 **더 많은 서비스**를 선택한 다음, 이를 찾아서 선택합니다.)

1. **스토리지 계정** 페이지에서 Terraform가 상태를 저장하도록 설정할 스토리지 계정의 이름을 선택합니다. 예를 들어, Cloud Shell을 처음 열 때 만들어진 스토리지 계정을 사용할 수 있습니다.  Cloud Shell에서 만든 스토리지 계정 이름은 일반적으로 `cs`로 시작되고 그 뒤에 숫자 및 문자로 이루어진 임의의 문자열이 나옵니다. 

    선택한 스토리지 계정이 나중에 필요하므로 적어 둡니다.

1. [스토리지 계정] 페이지에서 **액세스 키**를 선택합니다.

    ![스토리지 계정 메뉴](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. **key1** **key** 값을 기록해 둡니다. (키 오른쪽에 있는 아이콘을 선택하면 값이 클립보드에 복사됩니다.)

    ![스토리지 계정 액세스 키](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. Cloud Shell에서 Azure 스토리지 계정에 컨테이너를 만듭니다. 자리 표시자를 Azure 스토리지 계정에 대한 적절한 값으로 바꿉니다.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Kubernetes 클러스터 만들기
이 섹션에서는 `terraform init` 명령을 사용하여 이전 섹션에서 만든 구성 파일을 정의하는 리소스를 만드는 방법을 보여 줍니다.

1. Cloud Shell에서 Terraform을 초기화합니다. 자리 표시자를 Azure 스토리지 계정에 대한 적절한 값으로 바꿉니다.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
  
    `terraform init` 명령은 백 엔드 및 공급자 플러그 인 초기화 성공을 표시합니다.

    ![“terraform init” 결과의 예](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. Cloud Shell에서 이름이 `terraform.tfvars`인 파일을 만듭니다.

    ```bash
    code terraform.tfvars
    ```

1. 앞에서 만든 다음 변수를 편집기에 붙여넣습니다. 환경의 위치 값을 가져오려면 `az account list-locations`를 사용합니다.

    ```hcl
    resource_group_name = "<Name of the Resource Group already created>"

    location = "<Location of the Resource Group>"
      
    aks_service_principal_app_id = "<Service Principal AppId>"
      
    aks_service_principal_client_secret = "<Service Principal Client Secret>"
      
    aks_service_principal_object_id = "<Service Principal Object Id>"
        
    ```

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

1. `terraform plan` 명령을 실행하여 인프라 요소를 정의하는 Terraform 계획을 만듭니다. 

    ```bash
    terraform plan -out out.plan
    ```

    `terraform plan` 명령은 `terraform apply` 명령을 실행하면 생성되는 리소스를 표시합니다.

    ![“terraform plan” 결과의 예](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. `terraform apply` 명령을 실행하여 Kubernetes 클러스터를 만들 계획에 적용합니다. Kubernetes 클러스터를 만드는 프로세스는 몇 분 정도 소요될 수도 있으며 이 경우 Cloud Shell 세션 시간이 초과됩니다. Cloud Shell 세션의 시간이 초과되면 "Cloud Shell 시간 초과에서 복구" 섹션의 단계에 따라 프로세스를 완료할 수 있습니다.

    ```bash
    terraform apply out.plan
    ```

    `terraform apply` 명령은 구성 파일에 정의된 리소스 만들기의 결과를 표시합니다.

    ![“terraform apply” 결과의 예](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. Azure Portal에서 왼쪽 메뉴에 있는 **리소스 그룹**을 선택하여 선택한 리소스 그룹에서 새 Kubernetes 클러스터용으로 생성된 리소스를 확인합니다.

    ![Cloud Shell 프롬프트](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Cloud Shell 시간 초과에서 복구

Cloud Shell 세션의 시간이 초과되면 다음 단계를 사용하여 복구할 수 있습니다.

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

    ![kubectl 도구를 사용하여 Kubernetes 클러스터의 상태 확인](./media/terraform-k8s-cluster-appgw-with-tf-aks/kubectl-get-nodes.png)

## <a name="install-azure-ad-pod-identity"></a>Azure AD Pod ID 설치

Azure Active Directory Pod ID는 [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)에 대한 토큰 기반 액세스를 제공합니다.

[Azure AD Pod ID](https://github.com/Azure/aad-pod-identity)는 Kubernetes 클러스터에 다음 구성 요소를 추가합니다.

  - Kubernetes [CRD](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`, `AzureAssignedIdentity`, `AzureIdentityBinding`
  - [MIC(Managed Identity Controller)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic) 구성 요소
  - [NMI(Node Managed Identity)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi) 구성 요소

RBAC를 **사용하도록 설정**한 경우 다음 명령을 실행하여 Azure AD Pod ID를 클러스터에 설치합니다.

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
```

RBAC를 **사용하지 않도록 설정**한 경우 다음 명령을 실행하여 Azure AD Pod ID를 클러스터에 설치합니다.

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
```

## <a name="install-helm"></a>Helm 설치

이 섹션의 코드는 [Helm](/azure/aks/kubernetes-helm)(Kubernetes 패키지 관리자)을 사용하여 `application-gateway-kubernetes-ingress` 패키지를 설치합니다.

1. RBAC를 **사용하도록 설정**한 경우 다음 명령 집합을 실행하여 Helm을 설치하고 구성합니다.

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

1. RBAC를 **사용하지 않도록 설정**한 경우 다음 명령을 실행하여 Helm을 설치하고 구성합니다.

    ```bash
    helm init
    ```

1. AGIC 리포지토리를 추가합니다.

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="install-ingress-controller-helm-chart"></a>수신 컨트롤러 Helm 차트 설치

1. `helm-config.yaml`을 다운로드하여 AGIC를 구성합니다.

    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```

1. `helm-config.yaml`을 편집하고 `appgw`와 `armAuth` 섹션에 적절한 값을 입력합니다.

    ```bash
    code helm-config.yaml
    ```

    값에 대한 설명은 다음과 같습니다.

    - `verbosityLevel`: AGIC 로깅 인프라의 세부 정보 표시 수준을 설정합니다. 가능한 값은 [로깅 수준](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels)을 참조하세요.
    - `appgw.subscriptionId`: App Gateway의 Azure 구독 ID입니다. 예: `a123b234-a3b4-557d-b2df-a0bc12de1234`
    - `appgw.resourceGroup`: App Gateway가 생성된 Azure Resource Group의 이름입니다. 
    - `appgw.name`: Application Gateway의 이름입니다. 예: `applicationgateway1`.
    - `appgw.shared`: 이 부울 플래그는 기본적으로 `false`로 설정되어야 합니다. [공유 App Gateway](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)가 필요하면 `true`로 설정합니다.
    - `kubernetes.watchNamespace`: AGIC가 조사할 네임스페이스를 지정합니다. 네임스페이스는 단일 문자열 값 또는 쉼표로 구분된 네임스페이스 목록일 수 있습니다. 이 변수를 주석으로 처리하거나 공백 또는 빈 문자열로 설정하면 수신 컨트롤러에서 액세스 가능한 모든 네임스페이스를 관찰합니다.
    - `armAuth.type`: 값은 `aadPodIdentity` 또는 `servicePrincipal`입니다.
    - `armAuth.identityResourceID`: 관리 ID의 리소스 ID입니다.
    - `armAuth.identityClientId`: ID의 클라이언트 ID입니다.
    - `armAuth.secretJSON`: 서비스 주체 비밀 유형을 선택한 경우(`armAuth.type`을 `servicePrincipal`로 설정한 경우)에만 필요합니다.

    주요 참고 사항:
    - `identityResourceID` 값은 terraform 스크립트에서 생성되고 `echo "$(terraform output identity_resource_id)"`를 실행하여 찾을 수 있습니다.
    - `identityClientID` 값은 terraform 스크립트에서 생성되고 `echo "$(terraform output identity_client_id)"`를 실행하여 찾을 수 있습니다.
    - `<resource-group>` 값은 App Gateway의 리소스 그룹입니다.
    - `<identity-name>` 값은 생성된 ID의 이름입니다.
    - 지정된 구독에 대한 모든 ID는 `az identity list`를 사용하여 나열할 수 있습니다.

1. Application Gateway 수신 컨트롤러 패키지 설치

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

### <a name="install-a-sample-app"></a>샘플 앱 설치

App Gateway, AKS 및 AGIC가 설치되면, [Azure Cloud Shell](https://shell.azure.com/)을 통해 샘플 앱을 설치할 수 있습니다.

1. curl 명령을 사용하여 YAML 파일을 다운로드합니다.

    ```bash
    curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
    ```

2. YAML 파일을 적용합니다.

    ```bash
    kubectl apply -f aspnetapp.yaml
    ```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 이 문서에서 만든 리소스를 삭제합니다.  

자리 표시자를 적절한 값으로 바꿉니다. 지정된 리소스 그룹 내의 모든 리소스가 삭제됩니다.

```azurecli
az group delete -n <resource-group>
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Application Gateway 수신 컨트롤러](https://azure.github.io/application-gateway-kubernetes-ingress/)