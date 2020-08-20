---
title: 자습서 - Terraform을 사용하여 Azure에서 허브 및 스포크 네트워크 유효성 검사
description: 모든 가상 네트워크가 서로 연결된 허브 및 스포크 네트워크 토폴로지의 유효성을 검사하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 10/26/2019
ms.custom: devx-track-terraform
ms.openlocfilehash: ec21091b45dfe11839e4bfe0f4fe2f37261f16cc
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88241115"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-in-azure-using-terraform"></a>자습서: Terraform을 사용하여 Azure에서 허브 및 스포크 네트워크 유효성 검사

이 문서에서는 이 시리즈의 이전 문서에서 만든 terraform 파일을 실행한다. 결과는 데모 가상 네트워크 간의 연결에 대한 유효성 검사입니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * HCL(HashiCorp Language)을 사용하여 허브 스포크 토폴로지에서 Hub VNet을 구현
> * Terraform 계획을 사용하여 배포할 리소스 확인
> * Terraform 적용을 사용하여 Azure에서 리소스 만들기
> * 서로 다른 네트워크 간의 연결 유효성 확인
> * Terraform을 사용하여 모든 리소스 삭제

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>사전 요구 사항

1. [Azure에서 Terraform을 사용하여 허브 및 스포크 하이브리드 네트워크 토폴로지를 만듭니다](./hub-spoke-introduction.md).
1. [Azure에서 Terraform을 사용하여 온-프레미스 가상 네트워크를 만듭니다](./hub-spoke-on-prem.md).
1. [Azure에서 Terraform을 사용하여 허브 가상 네트워크를 만듭니다](./hub-spoke-hub-network.md).
1. [Azure에서 Terraform을 사용하여 허브 가상 네트워크 어플라이언스를 만듭니다](./hub-spoke-hub-nva.md).
1. [Azure에서 Terraform을 사용하여 스포크 가상 네트워크를 만듭니다](./hub-spoke-spoke-network.md).

## <a name="verify-your-configuration"></a>구성 확인

[필수 구성 요소](#prerequisites)를 완료한 후 적절한 구성 파일이 있는지 확인합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. [Azure Cloud Shell](/azure/cloud-shell/overview)을 엽니다. 이전에 환경을 선택하지 않은 경우 환경으로 **Bash**를 선택합니다.

    ![Cloud Shell 프롬프트](./media/common/azure-portal-cloud-shell-button-min.png)

1. 디렉터리를 `clouddrive` 디렉터리로 변경합니다.

    ```bash
    cd clouddrive
    ```

1. 디렉터리를 새 디렉터리로 변경합니다.

    ```bash
    cd hub-spoke
    ```

1. `ls` 명령을 실행하여 이전 자습서에서 작성한 `.tf` 구성 파일이 나열되는지 확인합니다.

    ![Terraform 데모 구성 파일](./media/hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>리소스 배포

1. Terraform 공급자 초기화:
    
    ```bash
    terraform init
    ```
    
    !["terraform init" 명령에 대한 결과의 예](./media/hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. `terraform plan` 명령을 실행하여 실행 전에 배포 효과 확인:

    ```bash
    terraform plan
    ```
    
    !["terraform plan" 명령에 대한 결과의 예](./media/hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. 솔루션 배포:

    ```bash
    terraform apply
    ```
    
    배포를 확인하라는 메시지가 표시되면 `yes`를 입력합니다.

    !["terraform apply" 명령에 대한 결과의 예](./media/hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>허브 VNet 및 스포크 VNet 테스트

이 섹션에서는 시뮬레이션된 온-프레미스 환경에서 허브 VNet으로 연결을 테스트하는 방법을 보여줍니다.

1. Azure Portal에서 **onprem-vnet-rg** 리소스 그룹으로 이동합니다.

1. **onprem-vnet-rg** 탭에서 **onprem-vm**이라는 VM을 선택합니다.

1. **연결**을 선택합니다.

1. **VM 로컬 계정을 사용하여 로그인** 텍스트 옆에 있는 **ssh** 명령을 클립보드에 복사합니다.

1. Linux 프롬프트에서 `ssh`를 실행하여 시뮬레이션된 온-프레미스 환경에 연결합니다. `on-prem.tf` 매개 변수 파일에 지정된 암호를 사용합니다.

1. `ping` 명령을 실행하여 허브 VNet에서 jumpbox VM에 대한 연결을 테스트합니다.

   ```bash
   ping 10.0.0.68
   ```

1. `ping` 명령을 실행하여 각 스포크에서 jumpbox VM에 대한 연결을 테스트합니다.

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. **onprem-vm** 가상 머신에서 ssh 세션을 종료하려면 `exit`을 입력하고 &lt;Enter> 키를 누릅니다.

## <a name="troubleshoot-vpn-issues"></a>VPN 문제 해결

VPN 오류 해결에 대한 자세한 내용은 [하이브리드 VPN 연결 문제 해결](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn) 문서를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 이 자습서에서 만든 Azure 리소스를 삭제합니다.

1. 계획에 선언된 리소스 제거:

    ```bash
    terraform destroy
    ```

    리소스 제거를 확인하는 메시지가 표시되면 `yes`를 입력합니다.

1. 디렉터리를 상위 디렉터리로 변경:

    ```bash
    cd ..
    ```

1. `hub-scope` 디렉터리(모든 파일 포함) 삭제:

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure에서 Terraform을 사용하는 방법에 대해 자세히 알아보기](/azure/terraform)