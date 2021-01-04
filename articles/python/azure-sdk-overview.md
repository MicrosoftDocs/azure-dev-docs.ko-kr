---
title: Python용 Azure 라이브러리(SDK) 사용
description: 개발자가 Azure 리소스를 프로비저닝, 사용 및 관리하는 경우 생산성을 높이는 데 도움이 되는 Python용 Azure 라이브러리의 특징과 기능에 대해 간략히 설명합니다.
ms.date: 09/19/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: d610099b3b877f0916079ca2000a5268f3f08c2a
ms.sourcegitcommit: c8330128d5d6a71859933a890ecdf047cb950996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97521999"
---
# <a name="use-the-azure-libraries-sdk-for-python"></a>Python용 Azure 라이브러리(SDK) 사용

Python용 오픈 소스 Azure 라이브러리는 Python 애플리케이션 코드에서 Azure 리소스를 프로비저닝하고, 관리하고, 사용하는 작업을 간소화합니다.

## <a name="the-details-you-really-want-to-know"></a>꼭 알아야 하는 정보

- Azure 라이브러리는 로컬로 또는 클라우드에서 실행하는 Python 코드 *에서* Azure 서비스와 통신하는 방법입니다. (특정 서비스의 범위 내에서 Python 코드를 실행할 수 있는지 여부는 해당 서비스 자체에서 현재 Python을 지원하는지에 따라 달라집니다.)

- 라이브러리는 Python 2.7 및 Python 3.5.3 이상을 지원하며 PyPy 5.4 이상에서도 테스트되었습니다.

- Python용 Azure SDK는 특정 Azure 서비스와 관련된 180개가 넘는 개별 Python 라이브러리로만 구성되어 있습니다. "SDK"에는 다른 도구가 없습니다.

- 코드를 로컬로 실행할 때 Azure를 사용하여 인증하려면 [로컬 개발 환경 구성](configure-local-development-environment.md)에 설명된 대로 환경 변수를 사용합니다. 

- [Python SDK 패키지 인덱스](azure-sdk-library-package-index.md)의 라이브러리 이름을 사용하여 `pip install <library_name>`에 필요한 라이브러리를 설치합니다. 자세한 내용은 [Azure 라이브러리 설치](azure-sdk-install.md)를 참조하세요.

- **관리** 및 **클라이언트** 라이브러리("관리 평면" 및 "데이터 평면" 라이브러리라고도 함)가 별도로 있습니다. 각 세트는 서로 다른 용도로 사용되며 서로 다른 종류의 코드에 사용됩니다. 자세한 내용은 이 문서의 뒷부분에서 다음 섹션을 참조하십시오.
  - [관리 라이브러리를 사용하여 Azure 리소스 프로비저닝 및 관리](#provision-and-manage-azure-resources-with-management-libraries)
  - [클라이언트 라이브러리를 사용하여 Azure 리소스에 연결 및 사용](#connect-to-and-use-azure-resources-with-client-libraries)

- 라이브러리에 대한 설명서는 [Python용 Azure 라이브러리 참조](/python/api/overview/azure/)(Azure 서비스로 구성되어 있음) 또는 [Python API 브라우저](/python/api/)(패키지 이름으로 구성되어 있음)에서 찾을 수 있습니다. 현재, 관심 있는 클래스와 메서드로 이동하려면 여러 레이어를 클릭해야 하는 경우가 종종 있습니다. 환경이 열악한 점에 대해 미리 사과드립니다. 개선하기 위해 노력하고 있습니다!

- 라이브러리를 직접 사용해 보려면 먼저 [로컬 개발 환경을 설정](configure-local-development-environment.md)하는 것이 좋습니다. 그런 다음, 다음의 독립 실행형 예제를 순서에 관계없이 사용해 볼 수 있습니다. [예: 리소스 그룹 프로비저닝](azure-sdk-example-resource-group.md), [예: Azure Storage 프로비저닝 및 사용](azure-sdk-example-storage.md), [예: 웹앱 프로비저닝 및 코드 배포](azure-sdk-example-web-app.md), [예: MySQL 데이터베이스 프로비저닝 및 사용](azure-sdk-example-database.md), [예: 가상 머신 프로비저닝](azure-sdk-example-virtual-machines.md).

- 데모 비디오를 보려면 가상 PyCon 2020에서 <a href="https://www.youtube.com/watch?v=M1pVxItg2Mg&feature=youtu.be&ocid=AID3006292" target="_blank">Azure SDK를 사용하여 Azure 리소스와 상호 작용</a>(youtube.com)을 참조하세요.

### <a name="non-essential-but-still-interesting-details"></a>필수는 아니지만 흥미로운 정보

- Azure CLI는 라이브러리 관리 라이브러리를 사용하여 Python으로 작성했기 때문에 Azure CLI 명령으로 수행할 수 있는 모든 작업은 Python 스크립트로도 수행할 수 있습니다. 즉, CLI 명령은 여러 작업을 함께 수행하고, 비동기 작업을 자동으로 처리하고, 연결 문자열과 같은 출력 형식을 지정하는 등의 유용한 기능을 제공합니다. 따라서 자동화된 프로비저닝 및 관리 스크립트에 CLI(또는 상응하는 Azure PowerShell)를 사용하는 경우, 프로세스에 대해 훨씬 정확한 제어 수준이 필요한 경우가 아니면, 상응하는 Python 코드를 작성하는 것보다 훨씬 더 편리할 수 있습니다.

- Python용 Azure 라이브러리는 기본 Azure REST API를 기반으로 빌드되기 때문에, 익숙한 Python 패러다임을 통해 해당 API를 사용할 수 있습니다. 하지만 필요할 때는 언제든지 Python 코드에서 직접 REST API를 사용할 수 있습니다.

- [https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python)에서 Azure 라이브러리용 소스 코드를 찾을 수 있습니다. 오픈 소스 프로젝트이기 때문에 여러분의 기여를 환영합니다!

- 테스트를 거치지 않은 IronPython 및 Jython과 같은 인터프리터에서 라이브러리를 사용할 수 있지만 격리된 문제와 비호환성 문제가 발생할 수 있습니다.

- 라이브러리 API 참조 설명서의 원본 리포지토리는 [https://github.com/MicrosoftDocs/azure-docs-sdk-python/](https://github.com/MicrosoftDocs/azure-docs-sdk-python/)에 있습니다.

- 현재 인증 프로토콜, 로깅, 추적, 전송 프로토콜, 버퍼링된 응답 및 재시도와 같은 일반적인 클라우드 패턴을 공유하도록 Python 라이브러리용 Azure 라이브러리를 업데이트하고 있습니다.

  - 이 공유 기능은 [azure-core](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/core/azure-core) 라이브러리에 포함되어 있습니다.

  - 현재 코어 라이브러리와 작동하는 라이브러리는 [Python용 Azure SDK 최신 릴리스](azure-sdk-library-package-index.md#libraries-using-azurecore)에 나열되어 있습니다. 이러한 라이브러리는 주로 클라이언트 라이브러리이며 "트랙 2"라고 하는 경우도 있습니다.

  - 관리 라이브러리와 아직 업데이트되지 않은 다른 라이브러리를 "트랙 1"이라고도 합니다.

- 라이브러리에 적용되는 지침에 대한 자세한 내용은 [Python 지침: 소개](https://azure.github.io/azure-sdk/python_introduction.html)를 참조하세요.

## <a name="provision-and-manage-azure-resources-with-management-libraries"></a>관리 라이브러리를 사용하여 Azure 리소스 프로비저닝 및 관리

SDK의 관리(또는 "관리 평면") 라이브러리는 이름이 모두 `azure-mgmt-`로 시작되며 Python 스크립트에서 Azure 리소스를 생성, 프로비저닝 및 관리하는 데 유용합니다. 모든 Azure 서비스에는 해당하는 관리 라이브러리가 있습니다.

관리 라이브러리를 사용하면 구성 및 배포 스크립트를 작성하여 [Azure Portal](https://portal.azure.com)이나 [Azure CLI](/cli/azure/install-azure-cli)를 통해 수행할 수 있는 동일한 작업을 수행할 수 있습니다. (앞서 언급했듯이 Azure CLI는 Python으로 작성되었으며 관리 라이브러리를 사용하여 다양한 명령을 구현합니다.)

다음 예제에서는 기본 관리 라이브러리 중 일부를 사용하는 방법을 보여줍니다.

- [리소스 그룹 프로비저닝](azure-sdk-example-resource-group.md)
- [구독의 리소스 그룹 나열](azure-sdk-example-list-resource-groups.md)
- [Azure Storage 프로비저닝](azure-sdk-example-storage.md)
- [웹앱 프로비저닝 및 코드 배포](azure-sdk-example-web-app.md)
- [데이터베이스 프로비저닝 및 쿼리](azure-sdk-example-database.md)
- [가상 머신 프로비전](azure-sdk-example-virtual-machines.md)

각 관리 라이브러리를 사용하는 방법에 대한 자세한 내용은 [SDK GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk)에서 라이브러리의 프로젝트 폴더에 있는 *README.md* 또는 *README.rst* 파일을 참조하세요. 추가 코드 조각은 [참조 설명서](/python/api) 및 [Azure 샘플](/samples/browse/?languages=python&term=Getting%20started%20-%20Managing)에서 찾을 수도 있습니다.

### <a name="migrating-from-older-management-libraries"></a>이전 관리 라이브러리에서 마이그레이션

이전 버전의 관리 라이브러리에서 코드를 마이그레이션하는 경우 다음 세부 정보를 참조하세요.

- `ServicePrincipalCredentials` 클래스를 사용하는 경우 [인증 방법 - 토큰 자격 증명 사용](azure-sdk-authenticate.md#authenticate-with-token-credentials)을 참조하세요.
- [라이브러리 사용 패턴 - 비동기 작업](azure-sdk-library-usage-patterns.md#asynchronous-operations)에 설명된 대로 비동기 API의 이름이 변경되었습니다. 간단히 말해서, 최신 라이브러리의 비동기 API 이름은 `begin_`으로 시작합니다. 대부분의 경우 API 서명은 동일하게 유지됩니다.

## <a name="connect-to-and-use-azure-resources-with-client-libraries"></a>클라이언트 라이브러리를 사용하여 Azure 리소스에 연결 및 사용

SDK의 클라이언트(또는 "데이터 평면") 라이브러리는 이미 프로비저닝된 서비스와 상호 작용하는 Python 애플리케이션 코드를 작성하는 데 유용합니다. 클라이언트 라이브러리는 클라이언트 API를 지원하는 서비스를 위해서만 존재합니다.

문서 [예제: Azure Storage 사용](azure-sdk-example-storage-use.md)에서는 클라이언트 라이브러리 사용에 대한 기본적인 설명을 제공합니다.

그 외에도 이러한 라이브러리의 사용 예제를 제공하는 여러 Azure 서비스가 있습니다. 추가 링크는 다음 인덱스 페이지를 참조하세요.

- [앱 호스팅](quickstarts-app-hosting.md)
- [Cognitive Services](quickstarts-cognitive-services.md)
- [데이터 솔루션](quickstarts-data-solutions.md)
- [ID 및 보안](quickstarts-identity-security.md)
- [기계 학습](quickstarts-machine-learning.md)
- [메시지 및 IoT](quickstarts-messaging-iot.md)
- [기타 서비스](quickstarts-other-services.md)

각 클라이언트 라이브러리를 사용하는 방법에 대한 자세한 내용은 [SDK GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk)에서 라이브러리의 프로젝트 폴더에 있는 *README.md* 또는 *README.rst* 파일을 참조하세요. 추가 코드 조각은 [참조 설명서](/python/api) 및 [Azure 샘플](/samples/browse/?languages=python&products=azure)에서 찾을 수도 있습니다.

## <a name="get-help-and-connect-with-the-sdk-team"></a>SDK 팀과 교류하고 도움 받기

- [Python용 Azure 라이브러리 설명서](https://aka.ms/python-docs)를 참조하세요.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sdk-python)의 커뮤니티에 질문을 게시합니다.
- [GitHub](https://github.com/Azure/azure-sdk-for-python/issues)에서 SDK에 대한 문제를 엽니다.
- Twitter에서 [@AzureSDK](https://twitter.com/AzureSdk/)를 언급합니다.

## <a name="next-step"></a>다음 단계

Python용 Azure 라이브러리를 쉽게 사용할 수 있도록 로컬 개발 환경을 한 번만 설정하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [로컬 개발 환경 설정 >>>](configure-local-development-environment.md)
