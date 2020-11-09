---
title: Azure에 JavaScript 앱 배포
description: 호스팅 옵션 및 배포 시나리오에는 Azure를 위한 여러 가지 서비스와 도구가 포함됩니다. 앱을 게시하고 Azure에 제공합니다.
ms.topic: how-to
ms.date: 10/28/2020
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js, contperfq2
ms.openlocfilehash: 93c9fa67e7fd928640478650d23207d48dd07531
ms.sourcegitcommit: e1175aa94709b14b283645986a34a385999fb3f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93192515"
---
# <a name="deploy-and-host-your-nodejs-apps-on-azure"></a>Azure에 Node.js 앱 배포 및 호스트

호스팅 옵션 및 배포 시나리오에는 Azure를 위한 여러 가지 서비스와 도구가 포함됩니다. Azure는 로컬 또는 클라우드 리포지토리에서 Azure로 앱을 이동하는 데 도움이 되는 여러 도구와 호스팅 옵션을 제공합니다. 

## <a name="choose-a-hosting-provider-from-azure"></a>Azure에서 호스팅 공급자 선택

Azure에 클라이언트, 서버 또는 백그라운드 작업 앱을 호스트하는 방법은 다양한 솔루션에서 사용할 수 있습니다. 다음 표를 참조하여 원하는 서비스를 선택하세요. 대부분의 사용 사례에 권장하는 솔루션은 [Azure App Service](/azure/app-service/overview.md)입니다. 

| 서비스 | 권장 용도 |
|--|--|
|[App Service](/azure/app-service/overview.md) - **권장**|코드 또는 컨테이너에서 앱을 호스트합니다. 이렇게 하면 기본 환경을 관리할 필요 없이 웹 서버를 관리할 수 있습니다.|
|[Static Web Apps](/azure/static-web-apps/)|정적 클라이언트 앱(예: Angular, Vue, React)을 호스트합니다. 필요에 따라 서버리스 함수 엔드포인트를 추가하여 전체 스택 앱을 호스트합니다. 이 간단한 서비스는 수많은 웹 서버를 추상화하므로, 개발자는 클라이언트 애플리케이션에 중요한 기능에 집중할 수 있습니다. |
|[함수](/azure/azure-functions/)|서버리스 애플리케이션 엔드포인트를 호스트합니다.|
|[스토리지](/azure/storage/blobs/storage-blob-static-website-how-to.md?tabs=azure-portal)|Azure Storage도 정적 웹앱을 호스트할 수 있습니다. 강력한 Storage와 클라이언트 애플리케이션을 긴밀하게 통합해야 할 때 유용합니다.|
|[Container Instances](/azure/container-instances/)|단일 컨테이너를 신속하게 설정합니다.|
|[Kubernetes Service](/azure/aks/)|여러 컨테이너를 오케스트레이션합니다.|
|[VM(Virtual Machines)](/azure/virtual-machines)|Windows 또는 Linux VM을 완벽하게 제어합니다. Azure Marketplace에서 [보증된 Linux 배포판을 찾거나](/azure/virtual-machines/linux/endorsed-distros?toc=/azure/virtual-machines/linux/toc.json) Linux VM 이미지를 [찾는 방법을 알아보세요](/azure/virtual-machines/linux/cli-ps-findimage.md).|
|[CDN(Content Delivery Network)](/azure/cdn/)|가장 가까운 POP(Point of Presence) 서버를 사용하여 Azure Blob 스토리지, 웹 애플리케이션 또는 공개적으로 액세스 가능한 웹 서버에서 로드된 정적 개체를 캐시할 수 있습니다. Azure CDN은 다양한 네트워크를 활용하고 최적화를 라우팅하여 캐싱할 수 없는 동적 콘텐츠를 가속화할 수 있습니다.|

다양한 호스팅 옵션에 대한 전체 개요는 [Azure 컴퓨팅 서비스에 대한 의사 결정 트리](/azure/architecture/guide/technology-choices/compute-decision-tree)와 Microsoft Learn의 [핵심 Cloud Services - Azure 컴퓨팅 옵션](/learn/modules/intro-to-azure-compute.md) 모듈을 참조하세요.

## <a name="choose-your-deployment-process-for-azure"></a>Azure에 사용할 배포 프로세스 선택

애플리케이션을 호스트할 서비스를 선택한 후에는 배포 프로세스 및 도구를 선택합니다. 클라이언트 및 서버 앱을 Azure 서비스에 배포하는 것은 HTTP 엔드포인트를 통해 제공할 파일 또는 파일 세트를 Azure로 이동하는 것을 의미합니다. 

다음 표에는 파일을 Azure 클라우드로 이동하는 일반적인 방법이 나열되어 있습니다.

| 메서드 | 세부 정보 |
|--|--|
|[GitHub Actions](/azure/app-service/deploy-github-actions.md?tabs=applevel)|자동화된 연속 배포 또는 트리거된 연속 배포에 사용합니다.|
|[Visual Studio Code 확장](https://marketplace.visualstudio.com/search?term=azure&target=VSCode&category=All%20categories&sortBy=Relevance)|수동, 테스트 또는 셀덤 배포에 사용됩니다. 서비스의 확장이 로컬에 설치되어 있어야 합니다.|
|[Azure CLI](../tutorial-vscode-azure-cli-node-04.md)|수동 또는 셀덤 배포에 사용됩니다. 서비스의 확장이 로컬에 설치되어 있어야 합니다.|

서비스에 따라 다른 배포 방법이 있을 수도 있습니다. 예를 들어 Azure 앱 서비스는 다음과 같은 다양한 배포 방법을 지원합니다.
* [Zip 파일에서](/azure/app-service/deploy-zip.md)
* [FTP 사용](/azure/app-service/deploy-ftp.md)
* [Dropbox 또는 OneDrive](/app-service/deploy-content-sync.md)
* [로컬 Git](/azure/app-service/deploy-local-git.md)

## <a name="verify-your-deployment-with-your-http-endpoint"></a>HTTP 엔드포인트를 사용하여 배포 확인

배포를 확인하려면 HTTP 엔드포인트에 액세스합니다. HTTP 엔드포인트는 **개요** 페이지의 모든 서비스에서 볼 수 있습니다. 

### <a name="view-http-endpoint-in-azure-portal"></a>Azure Portal에서 HTTP 엔드포인트 보기

Azure Portal의 서비스 [개요] 페이지에서 HTTP 엔드포인트를 볼 수 있습니다. 

:::image type="content" source="../media/howto-deploy/azure-portal-hosting-url.png" alt-text="Azure Portal의 서비스 [개요] 페이지에서 HTTP 엔드포인트 보기":::

## <a name="next-steps"></a>다음 단계

* [컨테이너를 사용하여 배포](deploy-containers.md)