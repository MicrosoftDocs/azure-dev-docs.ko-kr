---
title: GitHub 및 Azure DevOps를 사용하여 클라우드에서 모바일 애플리케이션 소스 코드 호스팅
description: Microsoft 서비스를 사용하여 클라우드에서 모바일 애플리케이션 코드를 호스팅하는 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 0ab44652d4a635d5ff04928b76883a76fe3a62fa
ms.sourcegitcommit: e97cb81a245ce7dcabeac3260abc3db7c30edd79
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91493114"
---
# <a name="cloud-hosted-source-code-management-services"></a>클라우드에서 호스팅되는 소스 코드 관리 서비스

동일한 코드베이스를 사용하는 여러 팀 멤버로 구성된 개발 팀이 있는 경우 코드를 호스팅하는 데 적합한 장소를 찾아야 합니다. 데이터를 클라우드에 저장하고 중앙 리포지토리를 사용하면 모든 사용자가 코드 파일을 업로드, 편집 및 관리할 수 있습니다. 또한 프로젝트에서 다른 개발자와 상호 작용할 수 있습니다. 인터넷이 연결되어 있는 한 어디에 있든 코드에 쉽게 액세스할 수 있습니다.

클라우드 호스팅은 온-프레미스 옵션보다 훨씬 더 쉽습니다. 하드웨어 구성이 덜 필요하며, 조직에서 구현 프로세스를 더 민첩한 방식으로 완료할 수 있습니다.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>클라우드에서 소스 코드를 호스팅하는 경우의 이점

- **중앙 클라우드 스토리지:** 데이터를 어디서나 보고 관리합니다.
- **더 나은 협업 및 클리너 코드:** 뛰어난 소프트웨어를 지속적으로 업데이트할 수 있도록 팀 내에서 코드를 추적하고 프로젝트를 관리합니다.
- **더 쉽게 기여:** 프로젝트에 쉽게 기여할 수 있습니다.
- **더 빠른 릴리스 주기:** 팀에서 더 빠르게 작업하고 프로젝트에 쉽게 기여할 수 있습니다.
- **비용 절감:** 사용자 고유의 하드웨어, 서버, VPN 등을 유지 관리할 필요가 없습니다.

다음 서비스를 사용하여 클라우드에서 애플리케이션 데이터를 호스팅합니다.

## <a name="github"></a>GitHub

[GitHub](https://github.com/)는 소스 코드 프로젝트를 다양한 프로그래밍 언어로 호스팅하는 오픈 소스 리포지토리 호스팅 서비스입니다. GitHub는 모든 반복에 대한 다양한 변경 내용을 추적합니다.

### <a name="github-key-features"></a>GitHub 주요 기능

- 코드 호스팅을 사용하여 모든 코드를 한 곳에 보관합니다. 프라이빗, 퍼블릭 및 오픈 소스 리포지토리에는 모두 코드를 호스팅, 버전 관리 및 릴리스하는 데 도움이 되는 도구가 포함되어 있습니다.
- 코드를 검토하고, 기본 제공 검토 도구를 사용하여 코드 검토를 팀 프로세스의 필수적인 부분으로 만듭니다.
  - 분기를 보호하고, 변경 내용을 제안하며, 검토를 요청합니다.
  - 차이점을 파악하고, 상황에 맞는 의견을 제시하며, 명확한 피드백을 얻습니다.
- GitHub의 프로젝트 관리 도구를 사용하여 초기에 조정하고, 조정된 상태를 유지하며, 더 많은 작업을 수행합니다.
  - 프로젝트의 큰 그림을 봅니다.
  - GitHub 내의 코드 바로 옆에 있는 작업 보드를 사용합니다.
  - 카드를 끌어서 팀 멤버에게 문제를 할당하거나 요청을 끌어옵니다.
  - 진행률 상황을 구성하고 추적할 마일스톤을 설정합니다.
  - 유용하지만 특정 문제 또는 끌어오기 요청에 속하지 않는 아이디어를 캡처하기 위해 메모를 작성합니다.
- [GitHub Marketplace](https://github.com/marketplace)에서 애플리케이션을 구입하여 더 효율적으로 통신하고 작업을 자동화하는 데 적합한 도구를 쉽게 검색하여 선택합니다.
- 다음을 사용하여 팀을 관리하고 확장합니다. 
  - 팀을 구성하고 권한에 액세스하는 데 도움이 되는 사용자 역할
  - 주제 및 팀 중심의 대화를 유지하기 위한 토론 스레드 도구
  - 계정을 사용하여 새 팀 멤버를 빠르게 설정하기 위한 커뮤니티 지침
- 이러한 작업을 수행하기 위해 인기 있는 프로젝트를 찾아보고 별표로 표시
- 네트워크를 통해 업계의 다른 사용자로부터 알아보기

### <a name="github-references"></a>GitHub 참조

- [GitHub](https://github.com/)
- [GitHub 가이드](https://guides.github.com/)
- [GitHub 커뮤니티 포럼](https://github.community/)
- [GitHub Marketplace](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps

[Azure DevOps](https://azure.microsoft.com/services/devops/)는 소스 제어 옵션으로 [Azure Repos](https://azure.microsoft.com/services/devops/repos/) 및 [TFVC(Team Foundation 버전 제어)](/azure/devops/repos/tfvc/index?view=azure-devops)를 지원합니다. 협업 코드 검토, 고급 파일 관리, 코드 검색 및 분기 정책이 있는 프라이빗 리포지토리를 추가 비용 없이 무제한으로 제공하여 고품질 코드를 보장합니다. Azure Repos는 네이티브 Azure Active Directory 지원 및 고급 정책이 필요한 소규모 프로젝트와 대규모 조직에 적합합니다.

### <a name="azure-devops-features"></a>Azure DevOps 기능

- 클라우드에서 호스팅되는 무제한 Git 소스 코드 리포지토리를 퍼블릭 및 프라이빗 리포지토리에 사용합니다.
  - Git 클라이언트에 대한 지원을 받습니다.
  - 웹후크 및 API 통합을 사용합니다.
- 리포지토리 끌어오기 요청에서 다음 빌드를 시작합니다.
  - 각 변경에 대해 스레드된 토론 및 연속 통합을 사용하여 더 나은 코드를 빌드하도록 협업합니다.
  - 완료된 끌어오기 요청마다 빌드, 테스트 및 배포를 자동으로 트리거하도록 CI/CD(연속 통합/지속적인 업데이트)를 설정합니다. Azure Pipelines 또는 도구를 사용할 수 있습니다.
  - 분기 정책을 사용하여 코드 품질을 보호합니다.
- 코드 검토가 포함된 Team Foundation 버전 제어를 사용하여 중앙 집중식 버전 제어를 유지합니다.
- Xcode, Eclipse, IntelliJ, Android Studio, Visual Studio, Visual Studio Code 등을 사용하여 코드에 연결합니다.
- 강력한 의미 체계 코드 검색을 사용합니다.
- 엔터프라이즈 지원 기능을 활용합니다. Azure DevOps는 기본적으로 Azure Active Directory와 통합되어 코드 리포지토리에 대한 액세스를 관리하는 프로세스를 간소화합니다.
- 최소 코드 검토 수, 성공적인 빌드 요구 사항 및 Git 병합 전략 적용과 같은 분기 정책을 사용하여 코드 품질을 보장합니다.
- 즐겨찾는 개발 환경을 연결하여 리포지토리에 액세스하고 작업을 관리합니다.

### <a name="azure-devops-references"></a>Azure DevOps 참조

- [Azure Repos 시작](https://azure.microsoft.com/services/devops/repos/) 
- [Azure Repos 설명서](/azure/devops/repos/?view=azure-devops)