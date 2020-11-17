---
title: Azure용 GitHub Actions란?
description: 리포지토리 내에 워크플로를 만들어 프로젝트를 빌드, 테스트, 패키징, 릴리스 및 Azure에 배포합니다.
author: N-Usha
ms.author: ushan
ms.topic: conceptual
ms.service: azure
ms.date: 10/30/2020
ms.custom: github-actions-azure
ms.openlocfilehash: bbb87890f4db4b744ae4b2794c86e86a18c0e352
ms.sourcegitcommit: 12f80b1e0fe08db707c198271d0c399c3aba343a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515124"
---
# <a name="what-is-github-actions-for-azure"></a>Azure용 GitHub Actions란?

[GitHub Actions](https://help.github.com/articles/about-github-actions)는 GitHub 내에서 소프트웨어 개발 워크플로를 자동화하는 데 도움이 됩니다. 코드를 저장하고 끌어오기 요청 및 이슈에 대해 협업하는 위치와 동일한 위치에 워크플로를 배포할 수 있습니다.

GitHub Actions에서 [워크플로](https://help.github.com/articles/about-github-actions#workflow)는 GitHub 리포지토리에서 설정하는 자동화된 프로세스입니다. GitHub에서 워크플로를 사용하여 프로젝트를 빌드, 테스트, 패키징, 릴리스 또는 배포할 수 있습니다.

각 워크플로는 특정 이벤트(예: 끌어오기 요청)가 발생한 후에 실행되는 개별 [작업](https://docs.github.com/en/free-pro-team@latest/actions/learn-github-actions/introduction-to-github-actions)으로 구성됩니다.  개별 작업은 소프트웨어 개발 작업을 자동화하는 패키징된 스크립트입니다.

Azure용 GitHub Actions를 사용하면 프로젝트를 빌드, 테스트, 패키징, 릴리스 및 Azure에 배포하도록 리포지토리에서 설정 가능한 워크플로를 만들 수 있습니다. Azure용 GitHub Actions는 Azure App Service, Azure Functions 및 Azure Key Vault를 비롯한 Azure 서비스를 지원합니다.

GitHub Actions에는 Azure Resource Manager 템플릿, Azure CLI 및 Azure Policy를 비롯한 유틸리티 지원도 포함되어 있습니다.

## <a name="why-should-i-use-github-actions-for-azure"></a>Azure용 GitHub Actions를 사용해야 하는 이유

Microsoft에서 개발한 Azure용 GitHub Actions는 Azure와 함께 사용하도록 설계되었습니다. [GitHub Marketplace](https://github.com/marketplace?query=Azure&type=actions)에서 모든 Azure용 GitHub Actions를 볼 수 있습니다. 워크플로에 작업을 통합하는 방법에 대한 자세한 내용은 [작업 찾기 및 사용자 지정](https://docs.github.com/en/free-pro-team@latest/actions/learn-github-actions/finding-and-customizing-actions)을 참조하세요.

## <a name="what-is-the-difference-between-github-actions-and-azure-pipelines"></a>GitHub Actions와 Azure Pipelines의 차이점

Azure Pipelines 및 GitHub Actions 둘 다 소프트웨어 개발 워크플로를 자동화하는 데 도움이 됩니다. 두 서비스의 차이점 및 Azure Pipelines에서 GitHub Actions로 마이그레이션하는 방법에 대해 [자세히 알아보세요](https://docs.github.com/en/free-pro-team@latest/actions/learn-github-actions/migrating-from-azure-pipelines-to-github-actions).

## <a name="what-do-i-need-to-use-github-actions-for-azure"></a>Azure용 GitHub Actions를 사용하려면 무엇이 필요한가요?

Azure 및 GitHub 계정이 필요합니다.

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* GitHub 계정. 없는 경우 [평가판](https://github.com/join)에 등록하세요.  

## <a name="how-do-i-connect-github-actions-and-azure"></a>GitHub Actions와 Azure를 연결하는 방법

작업에 따라 서비스 주체 또는 게시 프로필을 사용하여 GitHub에서 Azure에 연결합니다. [Azure 로그인](https://github.com/marketplace/actions/azure-login) 작업을 사용할 때마다 서비스 주체를 사용하게 됩니다. [Azure App Service 작업](https://github.com/marketplace/actions/azure-webapp)은 게시 프로필 또는 서비스 주체 사용을 지원합니다. 서비스 주체에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 주체 개체](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)를 참조하세요.  

Azure 로그인 작업을 [Azure CLI](https://github.com/marketplace/actions/azure-cli-action) 및 Azure [Azure PowerShell](https://github.com/marketplace/actions/azure-powershell-action) 작업과 함께 사용할 수 있습니다. Azure 로그인 작업은 [웹앱에 배포](https://github.com/marketplace/actions/azure-webapp) 및 [키 자격 증명 모음 비밀에 액세스](https://github.com/marketplace/actions/azure-key-vault-get-secrets)를 포함하여 Azure에 대한 대부분의 다른 GitHub 작업에서도 작동합니다.

## <a name="what-is-included-in-a-github-actions-workflow"></a>GitHub Actions 워크플로에 포함되는 내용

워크플로는 하나 이상의 작업으로 구성됩니다. 작업 내에는 개별 작업으로 구성된 단계가 있습니다. GitHub Actions 개념에 대한 자세한 내용은 [GitHub Actions 소개](https://docs.github.com/en/free-pro-team@latest/actions/learn-github-actions/introduction-to-github-actions)를 참조하세요.  

## <a name="where-can-i-see-complete-workflow-examples"></a>전체 워크플로 예제를 볼 수 있는 위치

[Azure 스타터 작업 워크플로 리포지토리](https://github.com/Azure/actions-workflow-samples)에는 모든 언어, 모든 에코시스템의 웹앱을 빌드하고 Azure에 배포할 수 있는 엔드투엔드 워크플로가 포함되어 있습니다.

## <a name="where-can-i-see-all-the-available-actions"></a>사용 가능한 모든 작업을 볼 수 있는 위치

사용 가능한 모든 Azure용 GitHub Actions를 확인하려면 [Azure용 GitHub Actions Marketplace](https://github.com/marketplace?query=Azure&type=actions)를 방문하세요.

* [정적 웹앱에 배포](/azure/static-web-apps/getting-started?tabs=angular)
* [Azure App Service 설정](https://github.com/Azure/appservice-settings)  
* [Azure Functions에 배포](https://github.com/Azure/functions-action)  
* [Azure Functions for Containers에 배포](https://github.com/Azure/webapps-container-deploy)  
* [Docker 로그인](https://github.com/Azure/docker-login)  
* [Azure Container Instances에 배포](https://github.com/Azure/aci-deploy)
* [컨테이너 스캔 작업](https://github.com/Azure/container-scan)
* [Kubectl 도구 설치 관리자](https://github.com/Azure/setup-kubectl)  
* [Kubernetes 컨텍스트 설정](https://github.com/Azure/k8s-set-context)  
* [AKS 컨텍스트 설정](https://github.com/Azure/aks-set-context)  
* [Kubernetes 비밀 만들기](https://github.com/Azure/k8s-create-secret)  
* [Kubernetes 배포](https://github.com/Azure/k8s-deploy)  
* [Helm 설치](https://github.com/Azure/setup-helm)  
* [Kubernetes 베이킹](https://github.com/Azure/k8s-bake)  
* [Azure 가상 머신 이미지 빌드](https://github.com/Azure/build-vm-image)
* [기계 학습 로그인](https://github.com/Azure/aml-workspace)
* [기계 학습 - 학습](https://github.com/Azure/aml-run)
* [기계 학습 - 배포 모델](https://github.com/Azure/aml-deploy)
* [Azure SQL 데이터베이스에 배포](https://github.com/Azure/sql-action)  
* [Azure MySQL 작업에 배포](https://github.com/Azure/mysql-action)  
* [Azure Policy 규정 준수 검사](https://github.com/Azure/policy-compliance-scan)
* [Azure Policy 관리](https://github.com/Azure/manage-azure-policy)
* [Azure Pipelines 실행 트리거](https://github.com/Azure/pipelines)  
* [변수 대체](https://github.com/Microsoft/variable-substitution)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [학습 경로, GitHub Actions를 사용하여 워크플로 자동화](https://docs.microsoft.com/learn/modules/github-actions-automate-tasks/)

> [!div class="nextstepaction"]
> [학습 랩, Azure를 사용하여 지속적인 업데이트](https://lab.github.com/githubtraining/github-actions:-continuous-delivery-with-azure)
