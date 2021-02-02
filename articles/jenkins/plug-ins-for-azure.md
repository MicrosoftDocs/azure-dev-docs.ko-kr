---
title: Azure용 Jenkins 플러그 인
description: Azure에서 사용할 수 있는 Jenkins 플러그 인에 대해 알아봅니다.
keywords: Jenkins, plugis, Azure, DevOps
ms.date: 04/25/2020
ms.topic: article
ms.custom: devx-track-jenkins
ms.openlocfilehash: 3051492f4278c2b5702fdc20932fa47928dea7a5
ms.sourcegitcommit: 3d906f265b748fbc0a070fce252098675674c8d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98699851"
---
# <a name="jenkins-plug-ins-for-azure"></a>Azure용 Jenkins 플러그 인

[!INCLUDE [jenkins-integration-with-azure.md](includes/jenkins-integration-with-azure.md)]

많은 Azure 서비스와 기능은 Jenkins 플러그 인을 통해 액세스할 수 있습니다. 이러한 서비스는 DevOps 환경에 대한 CI/CD(연속 통합 및 지속적인 배포)와 관련된 다양한 가능성을 지원합니다. Jenkins 플러그 인을 클릭하면 해당 플러그 인에 대한 자세한 내용을 확인할 수 있습니다.

| Jenkins 플러그 인 | Description                                   |
|------------------------------------------------------------------------------|
| [Azure App Service 플러그 인](https://plugins.jenkins.io/azure-app-service)     | Azure App Service를 배포하는 Jenkins 플러그 인입니다(현재 Web App만 지원). |
| [Azure AD 플러그 인](https://plugins.jenkins.io/azure-ad)                       | Azure Active Directory를 통한 인증 및 권한 부여를 지원하는 Jenkins 플러그 인입니다. |
| [Azure Artifact Manager 플러그 인](https://plugins.jenkins.io/azure-artifact-manager) | Azure Artifact Manager 플러그 인은 아티팩트를 Azure Blob Storage에 저장할 수 있는 Artifact Manager입니다. Azure Artifact Manager 플러그 인은 Jenkins 및 사용자의 작업에서 투명하게 작동하며, 기본 Artifact Manager와 같습니다. |
| [Azure Container Agents 플러그 인](https://plugins.jenkins.io/azure-container-agents) | Azure Container Agents 플러그 인을 사용하면 Jenkins에서 컨테이너를 에이전트로 실행할 수 있습니다. |
| [Azure Container Registry Task 플러그 인](https://plugins.jenkins.io/azure-container-registry-tasks)       | Docker 빌드 요청을 [Azure Container Registry](/azure/container-registry/container-registry-tasks-overview)에 보내는 Jenkins 플러그 인입니다. |
| [Azure Container Service 플러그 인](https://plugins.jenkins.io/azure-acs)       | 구성을 Azure Container Service(AKS)에 배포하는 Jenkins 플러그 인입니다. |
| [Azure Credential 플러그 인](https://plugins.jenkins.io/azure-credentials)      | Azure 자격 증명을 관리하는 Jenkins 플러그 인입니다. |
| [Azure 함수 플러그 인](https://plugins.jenkins.io/azure-function)           | Azure 함수를 배포하는 Jenkins 플러그 인입니다. |
| [Azure IoT Edge 플러그 인](https://plugins.jenkins.io/azure-iot-edge)           | Azure IoT Edge 플러그 인을 사용하면 Jenkins에서 IoT Edge 솔루션을 개발하기 위한 CI/CD 파이프라인을 쉽게 설정할 수 있습니다. |
| [Azure Service Fabric 플러그 인](https://plugins.jenkins.io/service-fabric)     | Linux Azure Service Fabric 프로젝트용 Jenkins 플러그 인입니다. |
| [Azure Storage 플러그 인](https://plugins.jenkins.io/windows-azure-storage)     | 빌드 아티팩트를 Microsoft Azure Blob Storage에 업로드하거나 이 스토리지에서 빌드 종속성을 다운로드하는 플러그 인입니다. |
| [Azure VM Agents 플러그 인](https://plugins.jenkins.io/azure-vm-agents)         | Azure 가상 머신(Azure Resource Manager 템플릿을 통해)에서 Jenkins 에이전트를 만드는 Jenkins 플러그 인입니다. |
| [Azure Virtual Machine Scale Set 플러그 인](https://plugins.jenkins.io/azure-vmss)           | VM 이미지를 Azure 가상 머신 확장 집합에 배포하는 Jenkins 플러그 인입니다. |