---
title: Azure에서 Ansible 사용
description: Ansible을 사용하여 클라우드 프로비전, 구성 관리 및 애플리케이션 배포를 자동화하는 방법을 소개합니다.
keywords: Ansible, Azure, DevOps, 개요, 클라우드 프로비전, 구성 관리, 애플리케이션 배포, Ansible 모듈, Ansible 플레이북
ms.topic: overview
ms.date: 08/13/2020
ms.custom: devx-track-ansible
ms.openlocfilehash: 9eb90921a0d44e138c331eb716700feb85e8aa9d
ms.sourcegitcommit: 717e32b68fc5f4c986f16b2790f4211967c0524b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91586104"
---
# <a name="using-ansible-with-azure"></a>Azure에서 Ansible 사용

[Ansible](https://www.ansible.com)은 클라우드 프로비전, 구성 관리 및 애플리케이션 배포를 자동화하는 오픈 소스 제품입니다. Ansible을 사용하면 가상 머신, 컨테이너 및 네트워크를 프로비저닝하고 클라우드 인프라를 완성할 수 있습니다. 또한 Ansible을 사용하면 사용자 환경에서 리소스의 배포 및 구성을 자동화할 수 있습니다.

이 문서에서는 Azure에서 Ansible을 사용할 때의 이점에 대한 기본적인 개요를 제공합니다.

## <a name="ansible-playbooks"></a>Ansible 플레이북

[Ansible 플레이북](https://docs.ansible.com/ansible/latest/playbooks.html)을 사용하여 환경을 구성하도록 Ansible에 지시할 수 있습니다. 플레이북은 사람이 읽을 수 있도록 하기 위해 YAML을 사용하여 코딩합니다. 자습서 섹션에는 플레이북을 사용한 Azure 리소스 설치 및 구성에 대한 많은 예제가 나와 있습니다. 

## <a name="ansible-modules"></a>Ansible 모듈

Ansible에는 원격 호스트에서 직접 실행하거나 [플레이북](https://docs.ansible.com/ansible/latest/playbooks.html)을 통해 실행하는 [Ansible 모듈](https://docs.ansible.com/ansible/2.9/modules/modules_by_category.html) 모음이 포함되어 있습니다. 사용자는 자신의 모듈을 만들 수 있습니다. 모듈은 서비스, 패키지 또는 파일과 같은 시스템 리소스를 제어하거나 시스템 명령을 실행하는 데 사용합니다.

Azure 서비스와 상호 작용하기 위해 Ansible에는 [Ansible 클라우드 모듈](https://docs.ansible.com/ansible/2.9/modules/list_of_cloud_modules.html#azure) 모음이 포함되어 있습니다. 이러한 모듈을 사용하여 Azure에서 인프라를 만들고 오케스트레이션할 수 있습니다. 

## <a name="migrate-existing-workload-to-azure"></a>기존 작업을 Azure로 마이그레이션

인프라를 정의하기 위해 Ansible을 사용하면, 애플리케이션의 플레이북을 적용하여 필요에 따라 Azure에서 사용자 환경의 크기를 자동으로 조정하도록 할 수 있습니다. 

## <a name="automate-cloud-native-application-in-azure"></a>Azure에서 클라우드 네이티브 애플리케이션 자동화

Ansible을 사용하면 Azure 마이크로 서비스(예: [Azure Functions](https://azure.microsoft.com//services/functions/)와 [Azure의 Kubernetes](https://azure.microsoft.com/services/container-service/kubernetes/))를 사용하여 Azure에서 클라우드 네이티브 애플리케이션을 자동화할 수 있습니다.  

## <a name="manage-deployments-with-dynamic-inventory"></a>동적 인벤토리를 사용하여 배포 관리

Ansible은 [동적 인벤토리](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) 기능을 통해 Azure 리소스에서 인벤토리를 끌어오는 기능을 제공합니다. 그런 다음, 기존 Azure 배포에 태그를 지정하고, Ansible을 통해 이러한 태그가 지정된 배포를 관리할 수 있습니다.

## <a name="additional-azure-marketplace-options"></a>추가 Azure Marketplace 옵션

[Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower)는 Red Hat의 Azure Marketplace 이미지입니다. 

Ansible Tower는 다음 기능을 포함하는 Ansible용 웹 기반 UI 및 대시보드입니다.

* 역할 기반 액세스 제어, 작업, 예약 및 그래픽 인벤토리 관리를 정의할 수 있습니다. 
* Tower를 기존 도구 및 프로세스에 삽입할 수 있도록 하기 위해 REST API 및 CLI를 포함합니다. 
* 플레이북 실행의 실시간 출력을 지원합니다. 
* 자격 증명을 노출하지 않고 작업을 위임할 수 있도록 Azure 및 SSH 키와 같은 자격 증명을 암호화합니다.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Azure용 Ansible 모듈 및 버전 매트릭스

Ansible에는 Azure 리소스 프로비저닝 및 구성에 사용할 모듈 모음이 포함되어 있습니다. 이러한 리소스에는 가상 머신포함, 확장 집합, 네트워킹 서비스 및 컨테이너 서비스가 포함됩니다. [Ansible 매트릭스](./module-version-matrix.md)에는 Azure용 Ansible 모듈과 해당 모듈이 제공하는 Ansible 버전이 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [빠른 시작: Azure Cloud Shell을 사용하여 Ansible 구성](getting-started-cloud-shell.md)
- [빠른 시작: Azure CLI를 사용하여 Ansible 구성](install-on-linux-vm.md)
