---
title: 자습서 - Terraform을 사용하여 Azure에서 허브 및 스포크 하이브리드 네트워크 토폴로지 만들기
description: Terraform을 사용하여 Azure에서 전체 하이브리드 네트워크 참조 아키텍처를 만드는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 10/26/2019
ms.custom: devx-track-terraform
ms.openlocfilehash: 7fd6de18404f6c71184f17510699c6eb2a7be16b
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88241125"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-in-azure-using-terraform"></a>자습서: Terraform을 사용하여 Azure에서 허브 및 스포크 하이브리드 네트워크 토폴로지 만들기

이 자습서 시리즈에서는 Terraform을 사용하여 Azure에서 [허브 및 스포크 네트워크 토폴로지](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)를 구현하는 방법을 보여줍니다. 

허브 및 스포크 토폴로지는 공통 서비스를 공유하면서 워크로드를 격리하는 방법입니다. 이러한 서비스에는 ID 및 보안이 포함됩니다. 허브는 온-프레미스 네트워크에 대한 중앙 연결 지점으로 작동하는 VNet(가상 네트워크)입니다. 스포크는 허브와 피어링된 VNet입니다. 공유 서비스는 허브에 배포되는 반면 개별 워크로드는 스포크 네트워크 내부에 배포됩니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * HCL(HashiCorp Language)을 사용하여 허브 및 스포크 하이브리드 네트워크 참조 아키텍처 리소스 배치
> * Terraform을 사용하여 허브 네트워크 어플라이언스 리소스 만들기
> * Terraform을 사용하여 Azure에 모든 리소스에 대한 공통 지점으로 작동할 허브 네트워크 만들기
> * Terraform을 사용하여 Azure에서 개별 워크로드를 스포크 VNets으로 만들기
> * Terraform을 사용하여 온-프레미스와 Azure 네트워크 간의 게이트웨이 및 연결 설정
> * Terraform을 사용하여 스포크 네트워크에 대한 VNet 피어링 만들기

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

- **Terraform 설치 및 구성**: Azure에 VM 및 기타 인프라를 프로비저닝하려면 [Terraform을 설치 및 구성](get-started-cloud-shell.md)합니다.

## <a name="hub-and-spoke-topology-architecture"></a>허브 및 스포크 토폴로지 아키텍처

허브 및 스포크 토폴로지에서 허브는 VNet입니다. VNet은 온-프레미스 네트워크에 대한 연결의 중심 지점으로 작동합니다. 스포크는 허브와 피어링하는 Vnet이며 워크로드를 격리하는 데 사용할 수 있습니다. 트래픽은 ExpressRoute 또는 VPN 게이트웨이 연결을 통해 온-프레미스 데이터 센터와 허브 사이를 흐릅니다. 다음 이미지는 허브 및 스포크 토폴로지의 구성 요소를 보여줍니다.

![Azure의 허브 및 스포크 토폴로지 아키텍처](./media/hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>허브 및 스포크 토폴로지의 이점

허브 및 스포크 네트워크 토폴로지는 공통 서비스를 공유하면서 워크로드를 격리하는 방법입니다. 이러한 서비스에는 ID 및 보안이 포함됩니다. 허브는 온-프레미스 네트워크에 대한 중앙 연결 지점으로 작동하는 VNet니다. 스포크는 허브와 피어링된 VNet입니다. 공유 서비스는 허브에 배포되는 반면 개별 워크로드는 스포크 네트워크 내부에 배포됩니다. 다음은 허브 및 스포크 네트워크 토폴로지의 몇 가지 이점입니다.

- **비용 절약** 여러 워크로드가 공유할 수 있는 단일 위치에 서비스를 중앙 집중화합니다. 이러한 워크로드에는 네트워크 가상 어플라이언스 및 DNS 서버가 있습니다.
- **구독 제한 극복**: 여러 구독의 VNet을 중앙 허브로 피어링하여 구독의 제한을 극복합니다.
- **문제 구분**: 중앙 IT(SecOps, InfraOps)와 워크로드(DevOps)를 문제를 분리합니다.

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>허브 및 스포크 아키텍처의 일반적인 용도

허브 및 스포크 아키텍처의 일반적인 용도는 다음과 같습니다.

- 서로 다른 환경에 배포된 워크로드가 많은 고객에게 있습니다. 이러한 환경에는 개발, 테스트 및 프로덕션이 있습니다. 이러한 워크로드는 DNS, IDS, NTP 또는 AD DS와 같은 서비스를 공유해야 하는 경우가 많습니다. 이러한 공유 서비스를 허브 VNet에 배치할 수 있습니다. 이런 방식으로 격리를 유지하기 위해 각 환경이 스포크에 배포됩니다.
- 서로 연결할 필요는 없지만 공유 서비스에 액세스해야 하는 워크로드.
- 보안 측면을 중앙에서 제어해야 하는 기업.
- 각 스포크의 워크로드에 대해 분리된 관리가 필요한 기업.

## <a name="preview-the-demo-components"></a>데모 구성 요소 미리 보기

이 시리즈의 각 자습서를 진행하다 보면 다양한 구성 요소가 고유한 Terraform 스크립트에 정의됩니다. 생성 및 배포된 데모 아키텍처는 다음과 같은 구성 요소로 이루어져 있습니다.

- **온-프레미스 네트워크**. 조직에서 실행되는 프라이빗 로컬 영역 네트워크입니다. 허브 및 스포크 참조 아키텍처의 경우 Azure의 VNet은 온-프레미스 네트워크를 시뮬레이션하는 데 사용됩니다.

- **VPN 디바이스**. VPN 디바이스나 서비스는 온-프레미스 네트워크에 외부 연결을 제공합니다. VPN 디바이스는 하드웨어 어플라이언스 또는 소프트웨어 솔루션일 수 있습니다. 

- **허브 VNet**. 허브는 온-프레미스 네트워크에 대한 중앙 연결 지점이며 서비스를 호스팅하는 장소입니다. 이러한 서비스는 스포크 VNets에서 호스팅되는 다른 워크로드에서 사용할 수 있습니다.

- **게이트웨이 서브넷**. VNet 게이트웨이는 동일한 서브넷에 유지됩니다.

- **스포크 VNet**. 스포크는 다른 스포크와 별도로 관리되는 자체 VNet에서 워크로드를 격리하는 데 사용할 수 있습니다. 각 워크로드에는 Azure Load Balancer를 통해 여러 서브넷이 연결된 여러 계층이 포함될 수 있습니다. 

- **VNet 피어링**. 피어링 연결을 사용하여 두 개의 VNet을 연결할 수 있습니다. 피어링 연결은 VNet 사이에 적용되는 비전이적이고 대기 시간이 낮은 연결입니다. 피어링이 적용되면 VNet은 라우터가 필요 없이 Azure 백본을 사용하여 트래픽을 교환합니다. 허브 및 스포크 네트워크 토폴로지에서는 VNet 피어링은 허브를 각 스포크에 연결하는 데 사용됩니다. 동일한 지역 또는 다른 지역의 VNet을 피어링할 수 있습니다.

## <a name="create-the-directory-structure"></a>디렉터리 구조 만들기

데모용 Terraform 구성 파일을 저장할 디렉터리를 만듭니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. [Azure Cloud Shell](/azure/cloud-shell/overview)을 엽니다. 이전에 환경을 선택하지 않은 경우 환경으로 **Bash**를 선택합니다.

    ![Cloud Shell 프롬프트](./media/common/azure-portal-cloud-shell-button-min.png)

1. 디렉터리를 `clouddrive` 디렉터리로 변경합니다.

    ```bash
    cd clouddrive
    ```

1. 이름이 `hub-spoke`인 디렉터리를 만듭니다.

    ```bash
    mkdir hub-spoke
    ```

1. 디렉터리를 새 디렉터리로 변경합니다.

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-azure-provider"></a>Azure 공급자 선언

Azure 공급자를 선언하는 Terraform 구성 파일을 만듭니다.

1. Cloud Shell에서 `main.tf`라는 새 파일을 엽니다.

    ```bash
    code main.tf
    ```

1. 다음 코드를 편집기에 붙여 넣습니다.

    ```hcl
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. 파일을 저장하고 편집기를 종료합니다.

## <a name="create-the-variables-file"></a>변수 파일 만들기

다양한 스크립트에 사용되는 일반적인 변수에 대한 Terraform 구성 파일을 만듭니다.

1. Cloud Shell에서 `variables.tf`라는 새 파일을 엽니다.

    ```bash
    code variables.tf
    ```

1. 다음 코드를 편집기에 붙여 넣습니다.

    ```hcl
    variable "location" {
      description = "Location of the network"
      default     = "centralus"
    }
    
    variable "username" {
      description = "Username for Virtual Machines"
      default     = "testadmin"
    }
    
    variable "password" {
      description = "Password for Virtual Machines"
      default     = "Password1234!"
    }
    
    variable "vmsize" {
      description = "Size of the VMs"
      default     = "Standard_DS1_v2"
    }
    ```

1. 파일을 저장하고 편집기를 종료합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure에서 Terraform으로 온-프레미스 가상 네트워크 만들기](./hub-spoke-on-prem.md)