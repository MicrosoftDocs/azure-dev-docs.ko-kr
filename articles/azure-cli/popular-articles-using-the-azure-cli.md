---
title: Azure CLI를 사용하기 위한 서비스 간 링크
description: 인기 있는 자습서 링크, 빠른 시작, 샘플, 개념 및 방법 가이드, Azure CLI, 가상 머신, AKS(Azure Kubernetes Service), Batch, Azure CLI(Core), Azure Resource Manager, Key Vault, Azure Stack Hub, Functions, 데이터베이스, Event Hubs, App Configuration, 앱 구성, 독일, 보안, 거버넌스, Insights, IoT, 사물 인터넷, DevOps, Virtual Network, Compute, 네트워킹, 개발자 도구, 데이터베이스, 분석, 관리 및 거버넌스, 하이브리드, 스토리지, 보안, AI, AI + Machine Learning, Linux, Windows, Ubuntu, 자동화, 애플리케이션, 웹앱, 스크립트
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 03/01/2020
ms.topic: conceptual
ms.prod: azure
ms.technology: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 648f147c8bc3f954f8b41c2fbec4500c3115e66a
ms.sourcegitcommit: 815cf2acff71e849735f7afce54723f03ffa5df3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88501278"
---
# <a name="popular-articles-using-the-azure-cli"></a>Azure CLI를 사용하는 인기 문서

Azure CLI는 여러 Azure 서비스에 사용되어 여러 문서 리포지토리에 문서를 분산합니다.  이 페이지에서는 인기 문서를 선택할 수 있는 링크를 제공합니다.  

## <a name="compute"></a>컴퓨팅

|서비스|아티클 유형|아티클 제목|Description|
|-|-|-|-|
|Virtual Machines | 자습서: Linux | [Azure CLI를 사용하여 Linux 가상 머신 만들기](azure-cli-vm-tutorial.yml) | 가상 머신을 만듭니다.  출력 쿼리 및 환경 변수 설정에 대해 알아봅니다.
|Virtual Machines | 빠른 시작: Linux | [Azure CLI를 사용하여 Linux 가상 머신 만들기](/azure/virtual-machines/linux/quick-create-cli) | Linux 가상 머신을 만들고 배포합니다.  웹 트래픽에 대한 포트를 열고 웹 서버를 설치합니다.
|Virtual Machines | 방법 가이드: Linux |[가상 머신 또는 VHD의 Linus 이미지 만들기](/azure/virtual-machines/linux/capture-image) | 기존 가상 머신의 프로비전을 해제하고, 이미지를 만들고, 캡처한 이미지에서 새 가상 머신을 만듭니다.
|Virtual Machines | 방법 가이드: Linux | [Azure CLI를 사용하여 Azure에 VHD 업로드](/azure/virtual-machines/linux/disks-upload-vhd-to-managed-disk-cli) | 빈 관리 디스크를 만들고, 로컬 VHD 파일을 업로드하고, 관리 디스크를 복사합니다.
|Virtual Machines | 방법 가이드: Linux | [Azure CLI를 사용하여 공유 이미지 갤러리 만들기](/azure/virtual-machines/linux/shared-images) | 조직 내, 지역 내/다른 지역 또는 Azure Active Directory 테넌트 내의 다른 사람과 공유하는 사용자 지정 VM 이미지의 Shared Image Gallery를 만듭니다.
|Virtual Machines | 방법 가이드: Linux | [Azure CLI를 사용하여 스폿 VM 배포(미리 보기)](/azure/virtual-machines/linux/spot-cli) | 가격에 따라 제거되지 않는 Linux 스폿 가상 머신을 배포합니다.
|Virtual Machines | 빠른 시작: Windows | [Azure CLI를 사용하여 Windows 가상 머신 만들기](/azure/virtual-machines/windows/quick-create-cli) | Azure에서 Windows Server 2016을 실행하는 가상 머신을 배포합니다.
|Virtual Machines | 모듈 배우기 | [Azure CLI를 사용하여 가상 머신 관리](https://docs.microsoft.com/learn/modules/manage-virtual-machines-with-azure-cli/) | 가상 머신을 만들고, 시작하고, 중지하고, 가상 머신과 관련된 추가 관리 작업을 수행합니다.
|AKS(Azure Kubernetes Service)| 빠른 시작 | [Azure CLI를 사용하여 AKS(Azure Kubernetes Service) 클러스터 배포](/azure/aks/kubernetes-walkthrough) | AKS 클러스터를 배포하고 관리합니다.  애플리케이션을 실행하는 클러스터와 Pod의 상태를 모니터링하는 방법을 알아봅니다.
|Azure Batch|샘플 | [Azure CLI를 사용하여 Azure Batch로 작업 및 태스크 실행](/azure/batch/scripts/batch-cli-sample-run-job) | Batch 작업을 만들고 일련의 태스크를 작업에 추가합니다. 작업 및 해당 태스크를 모니터링합니다.
|Azure Batch|샘플 | [Azure CLI를 사용하여 Azure Batch에서 Windows 풀 만들기 및 관리](/azure/batch/scripts/batch-cli-sample-manage-windows-pool) | Cloud Services 구성을 사용하여 Windows 컴퓨팅 노드 풀을 만들고 관리합니다.
|Azure Container Instance|빠른 시작 | [Azure CLI를 사용하여 컨테이너 인스턴스 배포](/azure/container-instances/container-instances-quickstart) | 격리된 Docker 컨테이너를 배포하고 해당 애플리케이션을 FQDN(정규화된 도메인 이름)으로 사용할 수 있게 만듭니다. 단일 배포 명령을 실행한 다음, 컨테이너에서 실행 중인 애플리케이션을 찾습니다.
|Azure Function|빠른 시작 |  [Azure에서 Azure CLI를 사용하여 HTTP 요청에 응답하는 함수 만들기](/azure/azure-functions/functions-create-first-azure-function-azure-cli) | 명령줄 도구를 사용하여 HTTP 요청에 응답하는 함수를 만듭니다. 코드를 로컬로 테스트한 다음, 서버리스 Azure Functions 환경에 함수를 배포합니다.

## <a name="networking"></a>네트워킹

|서비스|아티클 유형|아티클 제목|Description|
|-|-|-|-|
|Virtual Network|빠른 시작 | [Azure CLI를 사용하여 가상 네트워크 만들기](/azure/virtual-network/quick-create-cli) | 가상 네트워크를 만들고, 가상 네트워크에 두 개의 가상 머신을 배포하고, 인터넷에서 가상 머신에 연결합니다.
|Virtual Network|방법 가이드 | [Azure CLI를 사용하여 Linux 가상 머신에서 가속 네트워킹을 사용하도록 설정](/azure/virtual-network/create-vm-accelerated-networking-cli) | Linux 가상 머신을 만들고, 가상 함수의 동적 바인딩 및 해지를 처리하고, 가속 네트워킹을 사용하도록 설정합니다.

## <a name="internet-of-things"></a>사물 인터넷

|서비스|아티클 유형|아티클 제목|Description|
|-|-|-|-|
|IoT Hub|자습서 | [Azure CLI를 사용하여 IoT Hub 메시지 라우팅 구성](/azure/iot-hub/tutorial-routing) | IoT Hub를 사용하여 사용자 지정 라우팅 쿼리를 설정하고 사용합니다.

## <a name="developer-tools"></a>개발자 도구

|서비스|아티클 유형|아티클 제목|Description|
|-|-|-|-|
|Azure App Configuration|샘플 |[Azure App Configuration용 Azure CLI 샘플](/azure/azure-app-configuration/cli-samples) | Azure App Configuration에 Azure CLI를 사용하는 bash 스크립트의 링크를 가져옵니다.
|Azure DevOps| 시작: DevOps 파이프라인 |[Azure CLI를 사용하여 첫 번째 Azure 파이프라인 만들기](/azure/devops/pipelines/create-first-pipeline-cli) | 복제된 GitHub 디렉터리에 새 파이프라인을 만들고, 파이프라인을 관리 및 실행합니다.
|Azure DevOps| 방법 가이드: DevOps 파이프라인 |[Azure CLI를 사용하는 Azure 파이프라인 배포 작업](/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops) | 빌드 또는 릴리스 파이프라인에서 Azure CLI가 포함된 셸 또는 일괄 처리 스크립트를 실행합니다.  명령은 Linux, macOS 또는 Windows 운영 체제를 실행하는 플랫폼 간 에이전트에서 실행됩니다.
|Azure DevOps| 자습서: Jenkins 파이프라인 |[Jenkins에서 Azure CLI를 사용하여 Azure App Service에 배포](/azure/jenkins/execute-cli-jenkins-pipeline) | Jenkins 가상 머신을 만들어서 구성하고, Azure에서 웹앱을 만들고, GitHub 리포지토리를 준비합니다.  Jenkins 파이프라인을 만들고 실행합니다.

## <a name="databases"></a>데이터베이스

|서비스|아티클 유형|아티클 제목|Description|
|-|-|-|-|
|SQL Database| 샘플 |[Azure CLI를 사용하여 Azure SQL Database 구성](/azure/sql-database/sql-database-cli-samples?tabs=single-database) | Azure SQL Database용 Azure CLI 샘플입니다.
|MySQL|빠른 시작 |[Azure CLI를 사용하여 Azure Database for MySQL 서버 만들기](/azure/mysql/quickstart-create-mysql-server-database-using-azure-cli) | Azure Database for MySQL 서버를 만듭니다.  방화벽 규칙 및 SSL 설정을 구성합니다.  연결 정보를 가져와서 사용합니다.
|Cosmos DB |방법 가이드 |[Azure CLI를 사용하여 Azure Cosmos 리소스 관리](/azure/cosmos-db/manage-with-cli) | 일반 명령을 사용하여 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너의 관리를 자동화합니다.
|Cosmos DB |샘플 |[Azure Cosmos DB SQL(Core) API용 Azure CLI 샘플](/azure/cosmos-db/cli-samples) | Azure Cosmos DB SQL(Core) API의 Azure CLI 샘플 스크립트에 대한 링크를 제공합니다.

## <a name="analytics"></a>분석

|서비스|아티클 유형|아티클 제목|Description|
|-|-|-|-|
Azure Event Hub |빠른 시작 |[Azure CLI를 사용하여 이벤트 허브 만들기](/azure/event-hubs/event-hubs-quickstart-cli) | Event Hubs 네임스페이스 및 이벤트 허브를 만듭니다.
HDInsight |방법 가이드 |[Azure CLI를 사용하여 HDInsight 클러스터 만들기](/azure/hdinsight/hdinsight-hadoop-create-linux-clusters-azure-cli) | HDInsight 3.6 클러스터를 만듭니다.
HDInsight |자습서 |[Azure CLI를 사용하여 Azure HDInsight 클러스터 관리](/azure/hdinsight/hdinsight-administer-use-command-line) | HDInsight 클러스터를 나열하고, 표시하고, 삭제하고, 크기를 조정합니다.

## <a name="management-and-governance"></a>관리 및 거버넌스

|서비스|아티클 유형|아티클 제목|Description|
|-|-|-|-|
Resource Manager 템플릿 |방법 가이드 |[Azure Resource Manager 템플릿과 Azure CLI를 사용하여 리소스 배포](/azure/azure-resource-manager/templates/deploy-cli) | 템플릿을 사용하여 Azure에 리소스를 배포합니다.
Resource Manager 그룹 |방법 가이드 |[Azure CLI를 사용하여 Azure Resource Manager 리소스 그룹 관리](/azure/azure-resource-manager/management/manage-resource-groups-cli) | Azure Resource Manager를 사용하여 Azure 리소스 그룹을 관리합니다.
Resource Graph |빠른 시작 |[Azure CLI를 사용하여 첫 번째 Resource Graph 쿼리 실행](/azure/governance/resource-graph/first-query-azurecli) | Azure CLI 설치에 Azure Resource Graph를 추가하고 첫 번째 Resource Graph 쿼리를 실행합니다.
정책 할당 |빠른 시작 |[Azure CLI를 사용하여 비준수 리소스를 식별하는 정책 할당 만들기](/azure/governance/policy/assign-policy-azurecli) | 관리 디스크를 사용하지 않는 가상 머신을 식별하는 정책 할당을 만듭니다.

## <a name="hybrid"></a>하이브리드

|서비스|아티클 유형|아티클 제목|Description|
|-|-|-|-|
Azure Stack Hub| 빠른 시작: Linux VM |[Azure Stack Hub에서 Azure CLI를 사용하여 Linux 서버 가상 머신 만들기](/azure-stack/user/azure-stack-quick-create-vm-linux-cli) | Ubuntu Server 16.04 LTS 가상 머신을 만들고, 원격 클라이언트를 사용하여 가상 머신에 연결하고, NGINX 웹 서버를 설치합니다.
Azure Stack Hub| 빠른 시작: Windows VM |[Azure Stack Hub에서 Azure CLI를 사용하여 Windows Server 가상 머신 만들기](/azure-stack/user/azure-stack-quick-create-vm-windows-cli) |Windows Server 2016 가상 머신을 만들고, 원격 클라이언트를 사용하여 가상 머신에 연결하고, IIS 웹 서버를 설치합니다.
Azure Stack Hub| 방법 가이드: ASDK 리소스 |[Azure CLI를 사용하여 리소스를 관리하고 Azure Stack Hub에 배포](/azure-stack/user/azure-stack-version-profiles-azurecli2) | Linux, Mac 및 Windows 클라이언트 플랫폼에서 ASDK(Azure Stack Development Kit) 리소스를 관리하도록 Azure CLI를 설정합니다.

## <a name="storage"></a>스토리지

|서비스|아티클 유형|아티클 제목|Description|
|-|-|-|-|
Blob Storage |빠른 시작 |  [Azure CLI를 사용하여 BLOB 만들기, 다운로드 및 나열](/azure/storage/blobs/storage-quickstart-blobs-cli) | Azure Blob Storage로 데이터를 업로드하고 다운로드합니다.
Blob Storage |방법 가이드 |[Azure CLI를 사용하여 BLOB 또는 큐 데이터에 대한 액세스 권한 부여](/azure/storage/common/authorize-data-operations-cli) | 데이터 작업에 권한을 부여하는 방법을 지정하고, 매개 변수에 대한 환경 변수를 설정합니다.
Blob Storage |방법 가이드 |[Azure Data Lake Storage Gen2에서 Azure CLI를 사용하여 디렉터리, 파일 및 ACL 관리(미리 보기)](/azure/storage/blobs/data-lake-storage-directory-file-acl-cli) | 계층 구조 네임스페이스를 사용하는 스토리지 계정에서 디렉터리, 파일 및 권한을 만들고 관리합니다.
File Storage |빠른 시작 |[Azure CLI를 사용하여 Azure 파일 공유 만들기 및 관리](/azure/storage/files/storage-how-to-use-files-cli) | Azure 파일 공유를 만들고 사용합니다.  공유 스냅샷을 만들고 관리합니다.

## <a name="security"></a>보안

|서비스|아티클 유형|아티클 제목|Description|
|-|-|-|-|
서비스 주체 |방법 가이드 |[Azure CLI를 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli) | Azure CLI를 사용하여 서비스 주체를 만들고, 정보를 가져오고, 다시 설정합니다.
RBAC |방법 가이드 |[Azure RBAC 및 Azure CLI를 사용하여 역할 할당 추가 또는 제거](/azure/role-based-access-control/role-assignments-cli) | Azure 역할 기반 액세스 제어에 역할을 할당합니다.
Key Vault |방법 가이드 |[Azure CLI를 사용하여 Key Vault 관리](/azure/key-vault/key-vault-manage-with-cli2) | Azure Key Vault를 만들고 관리합니다.  애플리케이션을 등록하고 권한을 부여하고, 고급 액세스 정책을 설정하고, 플랫폼 간 명령줄 인터페이스 명령에 대해 알아봅니다.
Key Vault |자습서 |[Key Vault 및 Azure CLI를 사용하여 스토리지 계정 키 관리](/azure/key-vault/key-vault-ovw-storage-keys) | 스토리지 계정 키를 관리하고, 공유 액세스 서명 토큰을 생성합니다.

## <a name="ai--machine-learning"></a>AI + Machine Learning

|서비스|아티클 유형|아티클 제목|Description|
|-|-|-|-|
Machine Learning |참조 |[Azure Machine Learning용 Azure CLI 확장 사용](/azure/machine-learning/reference-azure-machine-learning-cli) | 실험을 실행하여 기계 학습 모델을 만들고 고객이 사용하기 위한 기계 학습 모델을 등록합니다.  기계 학습 모델을 패키징하고, 배포하고, 수명 주기를 추적합니다.
Cognitive Services |방법 가이드 |[Azure CLI를 사용하여 Cognitive Services 리소스 만들기](/azure/cognitive-services/cognitive-services-apis-create-account-cli) | Azure Cognitive Services에 가입하고 단일 서비스 또는 다중 서비스 구독이 있는 계정을 만듭니다.  자동으로 생성된 키와 엔드포인트를 사용하여 애플리케이션을 인증합니다.
Azure Monitor |방법 가이드 |[Azure CLI를 사용하여 Log Analytics 작업 영역 만들기](/azure/azure-monitor/learn/quick-create-workspace-cli). | Log Analytics 작업 영역을 만들고 배포합니다.

## <a name="geographies"></a>지리적 위치

|서비스|아티클 유형|아티클 제목|Description|
|-|-|-|-|
Azure 독일 |시작 |[Azure CLI를 사용하여 Azure 독일에 연결](/azure/germany/germany-get-started-connect-with-cli) | Azure 독일을 사용하여 스크립트를 통해 대규모 구독을 관리하거나 현재 글로벌 Azure Portal에서 사용할 수 없는 기능에 액세스할 수 있습니다.
Azure Government|시작 |[Azure CLI를 사용하여 Azure Government에 연결](/azure/azure-government/documentation-government-get-started-connect-with-cli)|Azure Government에서 리소스에 액세스하여 관리를 시작합니다.

## <a name="see-also"></a>참고 항목

* [Azure CLI 시작](get-started-with-azure-cli.md)
* [Azure CLI의 전체 명령 참조 목록](/cli/azure/reference-index)
* [Azure CLI로 관리할 수 있는 서비스](azure-services-the-azure-cli-can-manage.md)
