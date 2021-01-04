---
title: JavaScript 개발자용 Azure란?
description: JavaScript, TypeScript 및 Node.js 개발자용 Azure의 개념입니다.
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: devx-track-js
ms.openlocfilehash: 155add5f2c1119f028cfae966438bda4ead3e758
ms.sourcegitcommit: f723980ade4cbc13548a5d8ac3f3fa681b8a2dbd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97601004"
---
# <a name="what-is-azure-for-javascript-developers"></a>JavaScript 개발자용 Azure란?

Azure는 다양한 호스팅 옵션 및 클라우드 기반 서비스를 제공하는 클라우드 플랫폼입니다. 클라우드 개발이 처음이라면 Azure에 대해 자세히 알아보세요.

* [Azure 아키텍처 센터](/azure/architecture/) 
* [Azure 용어](/azure/cloud-adoption-framework/ready/considerations/fundamental-concepts)
* [Azure 애플리케이션을 위한 10가지 설계 원칙](/azure/architecture/guide/design-principles/)
* [클라우드 디자인 패턴](/azure/architecture/patterns/)

## <a name="javascript-typescript-and-other-languages"></a>JavaScript, TypeScript 및 기타 언어

또한 JavaScript용 Azure 런타임 지원은 TypeScript 또는 JavaScript로 변환 컴파일되는 다른 모든 버전을 지원합니다. 

## <a name="run-javascript-with-hosted-runtime"></a>호스팅 런타임으로 JavaScript 실행 

* Azure [App Service](/azure/app-service/)는 Node.js 런타임 엔진을 사용합니다. 지원되는 Node.js 버전을 모두 표시하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

    ```azurecli-interactive
    az webapp list-runtimes | grep node
    ```

* Azure [Functions에서 지원하는 Node.js 버전](/azure/azure-functions/functions-reference-node?tabs=v2#node-version)은 사용하는 Functions 버전을 기준으로 합니다. 

* 사용자 지정 런타임 - 사용자 지정 런타임은 다음과 같은 방법으로 지원됩니다.

    * [가상 머신](/azure/virtual-machines/)
    * 컨테이너 - [단일](/azure/container-instances/), [웹앱](/azure/app-service/), [Kubernetes](/azure/aks/)
    * (서버리스) Functions - [사용자 지정 처리기](/azure/azure-functions/functions-custom-handlers) 사용

## <a name="run-javascript-with-azure-sdks"></a>Azure SDK를 사용하는 JavaScript 실행

모든 Azure SDK는 다른 도구 없이 JavaScript를 사용하여 실행됩니다. 대부분의 최신 SDK는 TypeScript로 작성되고 형식 검사를 위해 `*.d.ts` 파일을 제공하지만 Azure SDK 또는 Azure 클라우드 서비스 사용 시 TypeScript가 요구되는 것은 아닙니다. 

JavaScript 코드는 호스팅 위치(로컬, 하이브리드, 클라우드)에 관계없이 Azure 서비스를 사용할 수 있습니다. JavaScript를 사용하여 프로그래매틱 방식으로 Azure 서비스를 사용할 때 권장되는 방법은 Azure SDK입니다. 이러한 SDK에서 지원하는 최소 Node.js 런타임 버전은 8+입니다. 

## <a name="azure-concepts-and-terminology"></a>Azure 개념 및 용어

Azure를 시작하려면 다음을 수행해야 합니다.
* [무료 구독](https://azure.microsoft.com/en-us/free/) 만들기
* 다음을 포함하는 리소스 만들기
    * [Azure Portal](https://portal.azure.com/)에 로그인
    * Azure 리소스에 대한 논리적 컬렉션을 보관하는 리소스 그룹 만들기
    * 리소스 그룹에 리소스 만들기
* 다음과 같이 리소스에 따라 리소스 사용
    * Azure Portal 사용
    * Azure CLI 사용
    * PowerShell 사용
    * Azure SDK와 함께 사용

[Azure 개념 및 용어 소개](/azure/cloud-adoption-framework/ready/considerations/fundamental-concepts)에 대해 자세히 알아보세요. 

Azure는 리소스를 찾는 데 도움이 되는 [명명 규칙 및 리소스 태그 지정](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)에 대한 지침을 제공합니다. 

## <a name="select-development-ide-and-set-up-environment"></a>개발 IDE 선택 및 환경 설정

모든 통합 개발 환경을 사용하여 JavaScript 애플리케이션을 개발합니다. JavaScript 개발에는 **Visual Studio Code** 를 사용하는 것이 좋습니다. 

[JavaScript 개발자를 위한 권장 도구](../node-azure-tools.md)에 대해 자세히 알아보세요. 

## <a name="select-azure-account-subscription-resource-and-tag"></a>Azure 계정, 구독, 리소스 및 태그 선택

* **Azure 계정** 은 이메일 주소를 통해 구독에 연결됩니다. 하나 이상의 Azure 계정을 가질 수 있습니다.

* **구독** 은 리소스의 조직 및 청구 단위입니다. 하나 이상의 구독을 가질 수 있습니다. [초기 프로덕션 구독 만들기](/azure/cloud-adoption-framework/ready/azure-best-practices/initial-subscriptions)에 대해 자세히 알아보세요.

* **리소스 그룹** 은 구독 내에서 다음 하위 조직 수준입니다. 함께 사용하려는 각 프로젝트 또는 Azure 서비스 그룹에 대한 새 리소스 그룹을 만듭니다. 

* **리소스** 의 이름을 선택하는 경우 리소스의 URL 엔드포인트에 리소스 이름이 사용되는 시기를 알고 있어야 합니다. 또한 리소스가 생성된 후에 많은 리소스에 사용자 지정 도메인 이름을 할당할 수 있습니다. 

* 리소스를 보다 유연하게 그룹화하려면 속성 이름 및 값을 사용하여 리소스에 **태그를 지정합니다**. 태그의 일반적인 용도는 사용자의 이름 또는 이메일을 사용자가 만드는 모든 리소스의 프로젝트 소유자로 추가하는 것입니다. 구독 관리자는 소유하고 있는 리소스를 손쉽게 찾거나 사용량 또는 청구 문제가 발생할 경우 소유권을 확인할 수 있습니다. 

[체계적인 명명 규칙](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming)은 프로덕션 수준 Azure 사용의 중요한 부분입니다.

## <a name="create-azure-resources"></a>Azure 리소스 만들기

Azure를 처음 접하는 사용자가 리소스를 만드는 가장 일반적인 방법은 Azure Portal을 사용하는 것입니다. 생성 단계에서는 사용자가 답해야 하는 모든 옵션과 권장되는 선택 항목을 보여줍니다.

### <a name="pricing-tiers"></a>가격 책정 계층

가격 책정 계층은 리소스가 청구되는 방법입니다. [Azure 가격 계산기](https://azure.microsoft.com/en-us/pricing/calculator)를 사용하여 리소스에 대한 청구를 이해할 수 있습니다. 

### <a name="free-tier-resources"></a>무료 계층 리소스

무료(F0) 가격 책정 계층을 선택하는 경우 해당 계획과 함께 제공되는 제한 사항을 이해하는 것이 중요합니다.

무료 계층이 제공되는 경우:

* 해당 서비스의 무료 리소스 하나로 구독이 제한될 수 있습니다. 무료 리소스를 만들 수 없는 경우 무료 리소스가 이미 구독에 존재하는 것입니다.
* TPS(초당 트랜잭션 수) 또는 TPS(월별 트랜잭션 수)의 가격 책정 계층 할당량을 초과하는 경우 애플리케이션에 할당량 부족을 나타내는 메시지와 함께 HTTP 오류가 표시됩니다. 

### <a name="resource-creation-time"></a>리소스 생성 시간

대부분의 리소스는 만드는 데 몇 분 정도 걸리지만 몇 분 정도 걸리는 리소스도 있습니다. Azure Portal 알림 창에서 리소스를 사용할 수 있는 시기와 리소스에 대한 링크를 알려줍니다. 

## <a name="install-azure-sdk-for-resources"></a>리소스용 Azure SDK 설치

[**Azure SDK**](../azure-sdk-library-package-index.md)는 JavaScript 개발자가 리소스를 사용할 때 권장되는 프로그래매틱 방식입니다. 

Azure 리소스는 다음에서도 사용할 수 있습니다.
* [Azure CLI](/cli/azure/install-azure-cli) 및 [Azure Cloud Shell](https://shell.azure.com/)
* [Azure PowerShell](/powershell/azure/?view=azps-5.2.0&preserve-view=true)
* [REST API](/rest/api/azure/)

## <a name="deploy-web-apps-to-hosting-options"></a>호스팅 옵션에 웹앱 배포

호스팅 옵션을 사용하면 애플리케이션에 Azure를 신속하게 사용할 수 있습니다. 다음 호스팅 빠른 시작 및 자습서에서는 가장 일반적인 Azure 1일차 경험을 안내합니다.

* 클라이언트/정적 애플리케이션
    * [Vanilla JS](/azure/static-web-apps/getting-started?tabs=vanilla-javascript)
    * [React](/azure/static-web-apps/getting-started?tabs=react)
    * [Angular](/azure/static-web-apps/getting-started?tabs=angular)
    * [Vue](/azure/static-web-apps/getting-started?tabs=vue)
* 서버 애플리케이션 
    * [Visual Studio Code에서 App Service에 Express.js MongoDB 앱 배포](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md)
* 컨테이너 애플리케이션 
    * [Visual Studio Code를 사용하여 프라이빗 컨테이너 레지스트리에서 App Service에 Express .js 컨테이너화된 앱 배포](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-01.md?tabs=bash)
* 가상 머신 애플리케이션
    * [Azure CLI 및 GitHub 작업을 사용하여 Express.js 앱으로 Linux 가상 머신 생성 및 배포](../tutorial/nodejs-virtual-machine-vm/create-linux-virtual-machine-azure-cli.md)

[호스팅 옵션](../how-to/deploy-web-app.md)에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

* [Node.js 설치](install-nodejs-develop-azure-sdk-project.md)
* [Azure JavaScript 개발자를 위한 권장 도구 알아보기](../node-azure-tools.md)
