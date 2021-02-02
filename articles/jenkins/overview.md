---
title: Jenkins 및 Azure 개요
description: Azure에서 Jenkins 빌드 및 배포 자동화 서버를 호스트하고 Azure 컴퓨팅 및 스토리지 리소스를 사용하여 CI/CD(지속적인 통합 및 배포) 파이프라인을 확장합니다.
keywords: Jenkins, Azure, DevOps, 개요
ms.topic: overview
ms.date: 11/10/2020
ms.custom: devx-track-jenkins
ms.openlocfilehash: 38703bbd857c1990571f6255ab3a4d9089ad7a46
ms.sourcegitcommit: 3d906f265b748fbc0a070fce252098675674c8d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98699831"
---
# <a name="azure-and-jenkins"></a>Azure 및 Jenkins

[Jenkins](https://jenkins.io/)는 소프트웨어 프로젝트에 대해 CI/CD(지속적인 통합 및 배포)를 설정하는 데 사용되는 인기 있는 오픈 소스 자동화 서버입니다. Azure에서 Jenkins 배포를 호스트하거나 Azure 리소스를 사용하여 기존 Jenkins 구성을 확장할 수 있습니다. 애플리케이션의 CI/CD를 Azure로 간소화하기 위해 Jenkins 플러그 인도 사용할 수 있습니다.

이 문서는 Azure와 Jenkins를 함께 사용하는 것을 소개하며 Jenkins 사용자에게 제공되는 주요 Azure 기능에 대해 자세히 설명합니다. Azure에서 사용자 고유의 Jenkins 서버를 시작하는 방법에 대한 자세한 내용은 [Azure에서 Jenkins 서버 만들기](configure-on-linux-vm.md)를 참조하세요.

## <a name="host-your-jenkins-servers-in-azure"></a>Azure에서 Jenkins 서버 호스트

Azure에서 Jenkins를 호스트하여 빌드 자동화를 중앙 집중화하고 소프트웨어 프로젝트 규모가 확장됨에 따라 필요에 따라 배포 규모를 조정합니다. Linux VM에서 Jenkins를 설치하고 구성하는 방법을 알아보려면 [빠른 시작 - Jenkins 시작](configure-on-linux-vm.md)을 참조하세요. [Azure Monitor 로그](/azure/log-analytics/log-analytics-overview) 및 [Azure CLI](/cli/azure)를 사용하여 Azure Jenkins 배포를 모니터링 및 관리합니다.

## <a name="scale-your-build-automation-on-demand"></a>요청 시 빌드 자동화 규모 조정

기존 Jenkins 배포에 빌드 에이전트를 추가하여, 빌드 수, 작업 및 파이프라인의 복잡성이 증가함에 따라 Jenkins 빌드 용량을 조정합니다. [Azure VM 에이전트 플러그 인](https://plugins.jenkins.io/azure-vm-agents)을 사용하여 Azure 가상 머신에서 이러한 빌드 에이전트를 실행할 수 있습니다. 자세한 내용은 [자습서](/azure/jenkins/jenkins-azure-vm-agents)를 참조하세요.

[Azure 서비스 주체](/azure/azure-resource-manager/resource-group-overview)로 구성되었으면 다음을 위해 Jenkins 작업 및 파이프라인에서 이 자격 증명을 사용할 수 있습니다.

- [Azure Storage 플러그 인](https://plugins.jenkins.io/windows-azure-storage)을 사용하여 [Azure Storage](/azure/storage/common/storage-introduction)에 빌드 아티팩트를 안전하게 저장하고 보관합니다. 자세히 알아보려면 [Jenkins 스토리지 방법](azure-storage-blobs-as-build-artifact-repository.md)을 검토하세요.
- [Azure CLI](deploy-to-azure-app-service-using-azure-cli.md)로 Azure 리소스를 관리하고 구성합니다.

## <a name="deploy-your-code-into-azure-services"></a>Azure 서비스에 코드 배포

Jenkins CI/CD 파이프라인의 일부로 Azure에 애플리케이션을 배포하려면 Jenkins 플러그 인을 사용합니다. [Azure App Service](/azure/app-service/) 및 [Azure Container Service](/azure/container-service/kubernetes/)에 배포하면 기본 인프라를 관리하지 않고도 애플리케이션에 업데이트를 준비, 테스트 및 릴리스할 수 있습니다.

 플러그 인은 다음과 같은 서비스 및 환경에 배포하는 데 사용할 수 있습니다.

- [Linux에서의 Azure App Service](/azure/app-service/containers/app-service-linux-intro). 시작하려면 [자습서](deploy-to-azure-app-service-using-azure-cli.md)를 참조하세요.