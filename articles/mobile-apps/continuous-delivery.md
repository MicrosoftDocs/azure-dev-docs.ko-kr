---
title: Visual Studio App Center 및 Azure 서비스를 사용하여 모바일 애플리케이션의 배포 및 릴리스 자동화
description: 모바일 애플리케이션에 대한 지속적인 업데이트 파이프라인을 설정하는 데 도움이 되는 App Center와 같은 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 96ca72ffb239b7baa48c06f09afe08a2653e3e0b
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632375"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>지속적인 배달 서비스를 통해 모바일 애플리케이션의 배포 및 릴리스 자동화

개발자는 코드를 작성하고 코드 리포지토리에 체크 인하지만, 리포지토리에 체크 인된 커밋이 항상 일관되지 않을 수 있습니다. 여러 개발자가 동일한 프로젝트에서 작업하는 경우 통합 문제가 발생할 수 있습니다. 팀에서 제대로 작동하지 않고, 버그가 쌓이며, 프로젝트 개발이 지연되는 상황이 발생할 수 있습니다. 개발자는 전체 소프트웨어 코드를 빌드하고 오류를 확인하기 위해 테스트될 때까지 기다려야 하므로 프로세스가 느리고 반복성이 떨어집니다.

지속적인 업데이트를 사용하면 모바일 애플리케이션의 배포 및 릴리스를 자동화할 수 있습니다. 애플리케이션을 테스터 그룹이나 회사 직원(베타 테스트의 경우) 또는 앱 스토어(프로덕션의 경우)에 배포하는지 여부는 중요하지 않습니다. 지속적인 업데이트를 사용하면 배포의 위험이 줄어들고 빠른 반복이 추천됩니다. 또한 고객에게 새 변경 내용을 지속적인 방식으로 릴리스할 수 있습니다.

## <a name="distribute-application-binaries-to-beta-testers"></a>베타 테스터에게 애플리케이션 이진 파일 배포

모바일 애플리케이션 베타 테스트는 애플리케이션 개발 프로세스에서 중요한 단계 중 하나입니다. 애플리케이션에서 버그와 문제를 초기에 찾는 데 도움이 됩니다. 프로덕션에서 사용할 수 있도록 준비하는 경우 피드백은 애플리케이션 품질을 향상시킵니다.

다음 서비스를 사용하여 모바일 앱에서 지속적인 업데이트 파이프라인을 사용하도록 설정합니다.

### <a name="visual-studio-app-center-distribute"></a>Visual Studio App Center 배포

[App Center 배포](/appcenter/distribution/)는 개발자가 빌드를 디바이스에 빠르게 릴리스할 수 있는 도구입니다. 완벽한 설치 포털 환경에서 App Center 배포는 베타 앱 테스터 배포를 위한 강력한 솔루션입니다. 또한 퍼블릭 앱 스토어를 통한 배포의 편리한 대안입니다. 개발자는 App Center 빌드 및 퍼블릭 애플리케이션 스토어 통합을 사용하여 배포 워크플로를 더욱 자동화할 수 있습니다.

#### <a name="visual-studio-app-center-distribute-features"></a>Visual Studio App Center 배포 기능

- 베타 테스터 및 사용자에게 앱을 배포하고, 모든 테스터가 최신 버전의 애플리케이션에 있는지 확인합니다.
- 테스터가 다운로드 흐름을 다시 진행하지 않고 테스터에게 새 릴리스에 대해 알립니다.
- 여러 버전의 애플리케이션에 대한 배포 그룹을 관리합니다.
- 다음 스토어에 배포합니다. 
  - [Apple](/appcenter/distribution/stores/apple)
  - [Google Play](/appcenter/distribution/stores/googleplay)
  - [Intune](/appcenter/distribution/stores/intune)
- iOS, Android, macOS, tvOS, Xamarin, React Native, Unity 및 Cordova에 대한 플랫폼 지원을 얻습니다.
- iOS 디바이스를 프로비저닝 프로필에 자동으로 등록합니다.

#### <a name="visual-studio-app-center-distribute-references"></a>Visual Studio App Center 배포 참조

- [Visual Studio App Center 가입](https://appcenter.ms/signup)
- [App Center 배포 시작](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)는 기본 설정 Git 공급자에서 작동하는 완전한 기능을 갖춘 CI(연속 통합) 및 CD(지속적인 업데이트) 서비스입니다. Azure Pipelines는 Azure 서비스와 같은 대부분의 주요 클라우드 서비스에 배포할 수 있습니다. GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud 또는 Azure Repos에서 코드를 시작할 수 있습니다. 그런 다음, 코드의 빌드, 테스트 및 배포를 Microsoft Azure, Google Cloud Platform 또는 AWS(Amazon Web Services)에 배포하는 프로세스를 자동화할 수 있습니다.

#### <a name="azure-pipelines-features"></a>Azure Pipelines 기능

- **CI 서버를 설정하기 위한 간소화된 작업 기반 환경:** 네이티브(Android, iOS 및 Windows) 및 플랫폼 간(Xamarin, Cordova 및 Resact Native) 모바일 애플리케이션 모두에 대해 CI 서버를 설정합니다.
- **모든 언어, 플랫폼 및 클라우드:** Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android 및 iOS 앱을 빌드, 테스트 및 배포합니다. Linux, macOS 및 Windows에서 병렬로 실행합니다. 클라우드 공급자(예: Azure, AWS 및 Google Cloud Platform)에 배포합니다. 베타 채널 및 앱 스토어를 통해 모바일 애플리케이션을 배포합니다.
- **기본 컨테이너 지원:** 새 컨테이너를 쉽게 만들고, 모든 레지스트리에 푸시합니다. 컨테이너를 독립적인 호스트 또는 Kubernetes에 배포합니다.
- **고급 워크플로 및 기능:** 빌드 체인과 다단계 빌드를 쉽게 만듭니다. YAML, 테스트 통합, 릴리스 게이트, 보고 등을 지원합니다.
- **확장 가능:** 커뮤니티에서 구축한 다양한 빌드, 테스트 및 배포 작업을 사용합니다. 여기에는 Slack에서 SonarCloud까지 수백 개의 확장이 포함됩니다. Jenkins와 같은 다른 CI 시스템에서도 배포할 수 있습니다. 웹후크 및 REST API는 통합하는 데 도움이 될 수 있습니다.
- **클라우드에서 호스팅되는 무료 빌드:** 이러한 빌드는 퍼블릭 및 프라이빗 리포지토리에서 사용할 수 있습니다.
- **다른 클라우드 공급업체에 배포 지원:** 공급업체에는 AWS 및 Google 클라우드 플랫폼이 포함됩니다.

#### <a name="azure-pipelines-references"></a>Azure Pipelines 참조

- [Azure Pipelines 가이드 시작](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Azure DevOps 시작](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>애플리케이션을 App Store에 직접 배포

애플리케이션을 프로덕션 환경에서 사용할 수 있도록 준비하고 공개적으로 사용하려면 해당 애플리케이션을 고객이 다운로드할 수 있는 앱 스토어에 제출해야 합니다. 애플리케이션을 앱 스토어에 직접 배포하는 여러 가지 방법이 있습니다. 

### <a name="visual-studio-app-center-distribute-stores"></a>Visual Studio App Center 배포 스토어

[App Center 배포](/appcenter/distribution/stores/)를 사용하면 모바일 애플리케이션을 앱 스토어에 직접 게시할 수 있습니다. 사용자가 애플리케이션을 다운로드할 준비가 되면 Visual Studio App Center 포털에서 직접 애플리케이션 이진 파일을 게시할 수 있습니다. 

다음에 직접 배포할 수 있습니다.

- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play 스토어](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)

### <a name="apple-app-store"></a>Apple App Store

Apple에서 개발하고 유지 관리하는 앱 스토어에서 사용자는 iOS, MacOS, WatchOS 및 tvOS 디바이스용으로 개발된 애플리케이션을 찾아보고 다운로드할 수 있습니다. 개발자는 공개적으로 사용할 수 있도록 iOS 앱을 Apple App Store에 제출해야 합니다.

### <a name="google-play"></a>Google Play

Google Play는 Android OS용 공식 앱스토어이며, 여기서는 사용자가 Android 디바이스용으로 개발되어 Google을 통해 게시된 애플리케이션을 찾아보고 다운로드할 수 있습니다.

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management)은 엔터프라이즈 이동성 관리 공간에 있는 클라우드 기반 서비스이며, 회사 데이터를 보호하면서 직원의 생산성을 높일 수 있도록 지원합니다. Intune을 사용하여 수행할 수 있는 작업은 다음과 같습니다.

- 직원이 회사 데이터에 액세스하는 데 사용하는 모바일 디바이스 및 PC를 관리합니다.
- 직원이 사용하는 모바일 애플리케이션을 관리합니다.
- 직원이 액세스하고 공유하는 방법을 제어하여 회사 정보를 보호합니다.
- 디바이스 및 애플리케이션에서 회사 보안 요구 사항을 준수하는지 확인합니다.

## <a name="deploy-updates-directly-to-users-devices"></a>업데이트를 사용자의 디바이스에 직접 배포

### <a name="codepush"></a>CodePush

App Center에서 [CodePush](/appcenter/distribution/codepush/)를 사용하면 Apache Cordova 및 Resact Native 개발자가 모바일 애플리케이션 업데이트를 사용자의 디바이스에 직접 배포할 수 있습니다. 이는 개발자가 특정 업데이트(예: JavaScript, HTML, CSS 및 이미지 변경)를 게시할 수 있는 중앙 리포지토리로 작동합니다. 그런 다음, 애플리케이션에서 제공된 클라이언트 SDK를 사용하여 리포지토리에서 업데이트를 쿼리할 수 있습니다. 이러한 방식으로 버그를 해결하거나 작은 기능을 추가하면서 더 결정적이고 직접적인 사용자 참여 모델을 제공할 수 있습니다. 이진 파일을 다시 빌드하거나 퍼블릭 앱 스토어를 통해 재배포할 필요가 없습니다.

#### <a name="codepush-key-features"></a>CodePush 주요 기능

- Cordova 및 React Native 개발자는 스토어에 릴리스하지 않고도 모바일 애플리케이션 업데이트를 사용자 디바이스에 직접 배포할 수 있습니다.
- 이진 파일을 다시 빌드하거나 개별 스토어를 통해 재배포할 필요가 없는 작은 기능을 추가 또는 제거하거나 버그를 수정하는 데 유용합니다.

#### <a name="codepush-references"></a>CodePush 참조

- [Visual Studio App Center 가입](https://appcenter.ms/signup)
- [App Center에서 CodePush 시작](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)
