---
title: Visual Studio App Center 및 Azure 서비스를 사용하여 앱의 수명 주기 자동화
description: 모바일 애플리케이션에 대한 연속 빌드 및 통합을 설정하는 데 도움이 되는 App Center와 같은 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 82ce8246b916549d2207a10d96de89ad26cde49c
ms.sourcegitcommit: 3d906f265b748fbc0a070fce252098675674c8d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98699891"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>연속 빌드 및 통합으로 앱 수명 주기 자동화

개발자는 코드를 작성하고 코드 리포지토리에 체크 인하지만, 리포지토리에 체크 인된 커밋이 항상 일관되지 않을 수 있습니다. 여러 개발자가 동일한 프로젝트에서 작업하는 경우 통합 문제가 발생할 수 있습니다. 팀에서 제대로 작동하지 않고, 버그가 쌓이며, 프로젝트 개발이 지연되는 상황이 발생할 수 있습니다. 개발자는 전체 소프트웨어 코드를 빌드하고 오류를 확인하기 위해 테스트될 때까지 기다려야 하므로 프로세스가 느리고 반복성이 떨어집니다. 

연속 빌드 및 통합을 사용하면 개발자가 변경 내용을 소스 코드 리포지토리에 적용하고 테스트 및 확인을 빌드 환경에 배치하여 빌드를 간소화하고 코드를 테스트할 수 있습니다. 코드에 대한 테스트는 항상 이 방식으로 실행합니다. 리포지토리에 대한 커밋이 있을 때마다 소스 코드에 대한 모든 변경 내용이 지속적으로 빌드됩니다. 모든 체크 인에서 CI(연속 통합) 서버는 개발자가 만든 모든 테스트의 유효성을 검사하고 실행합니다. 테스트가 통과되지 않으면 추가 변경을 위해 코드를 다시 보냅니다. 개발자는 만들어진 빌드를 이 방식으로 중단시키지 않습니다. 또한 컴퓨터에서 모든 테스트를 로컬로 실행할 필요가 없으므로 개발자의 생산성이 향상됩니다. 

## <a name="key-benefits"></a>주요 이점

- 파이프라인의 빌드, 테스트 및 배포를 자동화합니다.
- 더 빠른 릴리스 속도를 보장하기 위해 조기에 버그를 검색하고 문제를 해결합니다.
- 코드를 더 자주 커밋하고 애플리케이션을 빠르게 빌드합니다.
- 코드를 문제 없이 빠르게 변경할 수 있는 유연성을 제공합니다.
- 우수한 품질의 코드만 일관성을 유지하도록 출시 시간을 단축합니다.
- 작은 코드 조각이 한 번에 통합되므로 작은 코드를 더 효율적으로 수행합니다.
- 고객과 팀으로부터 피드백을 지속적으로 받을 수 있도록 팀의 투명성과 책임성을 높입니다.

다음 서비스를 사용하여 모바일 앱에서 연속 통합 파이프라인을 사용하도록 설정합니다.

## <a name="visual-studio-app-center"></a>Visual Studio App Center

[App Center 빌드](/appcenter/build/)를 사용하면 팀에서 보안 클라우드 인프라를 사용하여 작업하는 네이티브 및 플랫폼 간 애플리케이션을 빌드할 수 있습니다. Visual Studio App Center에서 리포지토리를 쉽게 연결하고, 커밋할 때마다 클라우드에서 앱 빌드를 시작할 수 있습니다. 빌드 서버를 로컬로 구성하고, 복잡하게 구성하며, 동료의 컴퓨터에는 빌드하지만 사용자의 컴퓨터에는 빌드하지 않는 코드를 구성하는 방법에 대해서는 걱정할 필요가 없습니다.

Visual Studio App Center 서비스의 추가 기능을 사용하면 워크플로를 추가로 자동화할 수 있습니다. App Center 배포를 사용하여 테스터 및 퍼블릭 앱 스토어에 빌드를 자동으로 릴리스할 수 있습니다. 또한 App Center 테스트를 사용하여 클라우드에서 수천 개의 실제 디바이스 및 OS 구성에 대해 자동화된 UI 테스트를 실행할 수 있습니다.

### <a name="visual-studio-app-center-features"></a>Visual Studio App Center 기능

- 연속 통합을 몇 분 안에 설정하고, 애플리케이션을 더 자주, 더 빠르게 빌드합니다.
- GitHub, BitBucket, Azure DevOps 및 GitLab과 통합합니다.
- 클라우드에서 호스팅되는 관리형 컴퓨터에서 빠르고 안전한 빌드를 만듭니다.
- 빌드를 사용하도록 설정하여 테스트를 시작하고, 앱이 실제 iOS 및 Android 디바이스에서 빌드되는지 여부를 확인합니다.
- iOS, Android, macOS, Windows, Xamarin 및 Resact Native에 대한 네이티브 및 플랫폼 간 지원을 제공합니다.
- 복제 후, 빌드 전 및 빌드 후 스크립트를 추가하여 빌드를 사용자 지정합니다.

### <a name="visual-studio-app-center-references"></a>Visual Studio App Center 참조

- [Visual Studio App Center 가입](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center 빌드 시작](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines

 Azure DevOps의 서비스인 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)는 기본 설정 Git 공급자에서 작동하는 완전한 기능을 갖춘 연속 통합 및 CD(지속적인 업데이트) 서비스입니다. 이는 Azure를 포함한 대부분의 주요 클라우드 서비스에 배포할 수 있습니다. GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud 또는 Azure Repos에서 코드를 시작할 수 있습니다. 그런 다음, 코드의 빌드, 테스트 및 배포를 Microsoft Azure, Google Cloud Platform 또는 AWS(Amazon Web Services)에 배포하는 프로세스를 자동화할 수 있습니다.

### <a name="azure-pipelines-features"></a>Azure Pipelines 기능

- **CI 서버를 설정하기 위한 간소화된 작업 기반 환경:** Microsoft 및 타사(Node.js, Java) 기반 서버 기술 외에도 네이티브(Android, iOS 및 Windows) 및 플랫폼 간(Xamarin, Cordova 및 Resact Native) 모바일 애플리케이션 모두에 대해 CI 서버를 설정합니다.
- **모든 언어, 플랫폼 및 클라우드:** Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android 및 iOS 애플리케이션을 빌드, 테스트 및 배포합니다. Linux, macOS 및 Windows에서 병렬로 실행합니다. 클라우드 공급자(예: Azure, AWS 및 Google Cloud Platform)에 배포합니다. 베타 채널 및 앱 스토어를 통해 모바일 애플리케이션을 배포합니다.
- **기본 컨테이너 지원:** 새 컨테이너를 쉽게 만들고, 모든 레지스트리에 푸시합니다. 컨테이너를 독립적인 호스트 또는 Kubernetes에 배포합니다.
- **고급 워크플로:** 빌드 체인과 다단계 빌드를 쉽게 만듭니다. YAML, 테스트 통합, 릴리스 게이트, 보고 등을 지원합니다.
- **확장 가능:** 커뮤니티에서 구축한 다양한 빌드, 테스트 및 배포 작업을 사용합니다. 여기에는 Slack에서 SonarCloud까지 수백 개의 확장이 포함됩니다. Jenkins와 같은 다른 CI 시스템에서도 배포할 수 있습니다. 웹후크 및 REST API는 통합하는 데 도움이 될 수 있습니다.
- **클라우드에서 호스팅되는 무료 빌드:** 이러한 빌드는 퍼블릭 및 프라이빗 리포지토리에서 사용할 수 있습니다.
- **다른 클라우드 공급업체에 배포 지원:** 공급업체에는 AWS 및 Google 클라우드 플랫폼이 포함됩니다.

### <a name="azure-pipelines-references"></a>Azure Pipelines 참조

- [Azure Pipelines 가이드 시작](/azure/devops/pipelines/get-started/pipelines-get-started)
- [Azure DevOps 시작](https://app.vsaex.visualstudio.com/signup/)
- [빠른 시작](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2)

애플리케이션 빌드에 적합한 서비스를 선택하는 데 도움이 되도록 [App Center 빌드 및 Azure Pipelines](/appcenter/build/choose-between-services)를 비교하는 문서를 참조하세요.
