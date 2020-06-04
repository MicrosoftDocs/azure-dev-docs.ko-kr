---
title: GitHub Actions를 사용하여 Azure에 배포
description: 워크플로를 리포지토리 내에 만들어 Azure에 빌드, 테스트, 패키지, 릴리스 및 배포합니다.
author: N-Usha
ms.author: ushan
ms.topic: conceptual
ms.service: azure
ms.date: 05/05/2020
ms.openlocfilehash: a7bcf09da47e9af41f404bfdd2454b25f94eb7b4
ms.sourcegitcommit: a9b9157bb3a802ecfe3699854788d010a3f08d7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84202852"
---
# <a name="deploy-to-azure-using-github-actions"></a>GitHub Actions를 사용하여 Azure에 배포

[GitHub Actions](https://help.github.com/articles/about-github-actions)를 통해 개발자는 자동화된 소프트웨어 개발 수명 주기 워크플로를 빌드할 수 있습니다.  

Azure용 GitHub Actions를 사용하면 Azure에 빌드, 테스트, 패키지, 릴리스 및 **배포**하기 위해 리포지토리로 설정할 수 있는 워크플로를 만들 수 있습니다. [Azure와 다른 모든 통합에 대해 자세히 알아봅니다](https://aka.ms/GitHubonAzure).

지금 [체험 Azure 계정](https://azure.com/free/open-source)으로 시작하세요!

> [!NOTE]   
> 이 문서에서 제공하는 링크는 GitHub 문서 또는 GitHub 리포지토리에 연결됩니다. 

## <a name="key-concepts"></a>주요 개념

GitHub Actions를 사용하면 GitHub 리포지토리에서 사용자 지정 SDLC(소프트웨어 개발 수명 주기) 워크플로를 직접 만들 수 있습니다. GitHub Actions 및 핵심 개념에 대한 개요는 다음 문서를 검토하세요. 

- [GitHub Actions 정보](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions)
- [핵심 개념](https://help.github.com/actions/getting-started-with-github-actions/core-concepts-for-github-actions)
- [GitHub Actions를 사용한 패키징 정보](https://help.github.com/en/actions/publishing-packages-with-github-actions/about-packaging-with-github-actions)

## <a name="get-started"></a>시작하기 

GitHub Actions에는 미리 구성된 템플릿 및 Marketplace 작업이 포함됩니다. 

- [미리 구성된 템플릿 사용](https://help.github.com/actions/getting-started-with-github-actions/starting-with-preconfigured-workflow-templates)  
- [GitHub Marketplace의 작업 사용](https://help.github.com/en/actions/getting-started-with-github-actions/using-actions-from-github-marketplace)  
- [GitHub Marketplace 작업, Azure에 배포](https://github.com/marketplace?type=actions&query=Azure)  
  
Azure용 GitHub Actions의 경우 다음 페이지를 참조하세요. 
   
- [Azure 작업](https://github.com/marketplace?query=Azure&type=actions)  
- [Azure에 배포할 스타터 작업 워크플로](https://github.com/Azure/actions-workflow-samples)


## <a name="connect-to-azure"></a>Azure에 연결

Azure에 연결하고 Az CLI 또는 Az PowerShell을 기반으로 스크립트를 실행하는 샘플 워크플로의 경우, 다음 GitHub 작업을 사용합니다.  

- [Azure 로그인](https://github.com/Azure/login)  
- [Azure CLI](https://github.com/Azure/CLI)
- [Azure PowerShell](https://github.com/Azure/powershell)


## <a name="sample-apps-with-cicd-workflow-samples"></a>CI/CD 워크플로 샘플이 포함된 샘플 앱 

다음 샘플에서는 모든 언어, 모든 에코시스템의 웹앱을 Azure에 빌드하고 배포하기 위한 엔드투엔드 워크플로를 제공합니다. 

- [ASP.NET 지원을 사용하여 웹앱 배포](https://github.com/Azure-Samples/dotnet-sample)  
- [ASP.NET Core 앱 배포](https://github.com/Azure-Samples/dotnet_core_sample)  
- [Node.js 웹앱 배포](https://github.com/Azure-Samples/node_express_app)  
- [Java 웹앱 배포](https://github.com/Azure-Samples/java-spring-petclinic)  
- [Java 스프링 앱 배포](https://github.com/Azure-Samples/Java-application-petstore-ee7)  
- [Python 웹앱 배포](https://github.com/Azure-Samples/pythonSample_thecatsaidno)  
- [Docker를 사용하여 컨테이너화된 웹앱 배포](https://github.com/Azure-Samples/Node_express_container)


## <a name="deploy-a-web-app"></a>웹앱 배포

Azure Web App 및 Azure Web App for Containers에 배포:

- [Azure/webapps-deploy action](https://github.com/Azure/webapps-deploy)

정적 웹앱 배포:
- [Azure/static-web-apps-deploy](https://docs.microsoft.com/azure/static-web-apps/getting-started?tabs=angular)


다음 작업을 사용하여 앱 설정 및 연결 문자열을 구성합니다.

- [Azure/appservice-settings](https://github.com/Azure/appservice-settings) 
- [Azure App Service 설정](https://github.com/Azure/appservice-settings)  

## <a name="deploy-a-serverless-app"></a>서버리스 앱 배포

- [Azure Functions](https://github.com/Azure/functions-action)  
- [Azure Functions for Containers](https://github.com/Azure/webapps-container-deploy)  
 
## <a name="build-and-deploy-containerized-apps"></a>컨테이너화된 앱 빌드 및 배포

- [Docker 로그인](https://github.com/Azure/docker-login)  
- [Azure Container Instances에 배포](https://github.com/Azure/aci-deploy)

## <a name="deploy-to-kubernetes"></a>Kubernetes에 배포

- [Kubectl 도구 설치 관리자](https://github.com/Azure/setup-kubectl)  
- [Kubernetes 컨텍스트 설정](https://github.com/Azure/k8s-set-context)  
- [AKS 컨텍스트 설정](https://github.com/Azure/aks-set-context)  
- [Kubernetes 비밀 만들기](https://github.com/Azure/k8s-create-secret)  
- [Kubernetes 배포](https://github.com/Azure/k8s-deploy)  
- [Helm 설치](https://github.com/Azure/setup-helm)  
- [Kubernetes 베이킹](https://github.com/Azure/k8s-bake)  

## <a name="train-and-deploy-a-machine-learning-model"></a>기계 학습 모델 학습 및 배포 

- [로그인](https://github.com/Azure/aml-workspace) 
- [학습](https://github.com/Azure/aml-run)
- [모델 배포](https://github.com/Azure/aml-deploy)

## <a name="deploy-to-databases"></a>데이터베이스에 배포

- [Azure SQL 데이터베이스](https://github.com/Azure/sql-action)  
- [Azure MySQL 작업](https://github.com/Azure/mysql-action)  

## <a name="trigger-a-run-in-azure-pipelines"></a>Azure Pipelines에서 실행 트리거

- [Azure Pipelines](https://github.com/Azure/pipelines)  
 
## <a name="utility-actions"></a>유틸리티 작업

- [변수 대체](https://github.com/Microsoft/variable-substitution) 


## <a name="additional-resources"></a>추가 리소스

다음 GitHub 리소스는 GitHub를 통해 앱을 Azure에 배포하는 데 사용할 수 있습니다.  

- [Azure용 GitHub Actions Marketplace](https://github.com/marketplace?query=Azure&type=actions)
- [학습 랩, Azure를 사용하여 지속적인 업데이트](https://lab.github.com/githubtraining/github-actions:-continuous-delivery-with-azure)
- [Azure에 배포할 스타터 작업 워크플로](https://github.com/Azure/actions-workflow-samples)
