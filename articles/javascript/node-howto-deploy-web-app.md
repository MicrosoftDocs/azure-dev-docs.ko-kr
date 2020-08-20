---
title: Azure에 Node.js 웹앱 배포
description: 프로그레시브 웹앱(PWA)을 포함하여 웹앱에 대한 Azure App Service 및 기타 호스팅 옵션 시작
ms.topic: article
ms.date: 08/20/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-javascript
ms.openlocfilehash: 379960ccbeec1fc5fdd0030e7460a97acb149066
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88218642"
---
# <a name="deploy-nodejs-web-apps-to-azure-app-service"></a>Azure App Service에 Node.js 웹앱 배포

Azure에는 웹앱을 배포 및 호스팅하기 위한 몇 가지 옵션이 있습니다.

- 웹앱을 위한 최상의 호스팅 옵션은 PaaS(Platform-as-a-Service) 제품인 Azure App Service입니다. 시작하려면 다음 리소스 중 하나를 시도하세요.

  - [Azure에서 Node.js 웹앱 만들기](/azure/app-service/app-service-web-get-started-nodejs)
  - [Azure App Service 사용해보기 - 템플릿에서 Express 앱 만들기](https://code.visualstudio.com/tryappservice/?utm_source=msftdocs&utm_medium=microsoft&utm_campaign=tryappservice)
  - [Azure App Service를 사용하여 웹앱 호스팅 - 학습 모듈](/learn/modules/host-a-web-app-with-azure-app-service/index)
  - [Azure에서 Node.js 및 MongoDB 앱 작성](/azure/app-service/app-service-web-tutorial-nodejs-mongodb-app)
  - [App Service 샘플](/samples/browse/?languages=javascript%2Cnodejs&products=azure-app-service)

- Azure Container Registry 및 Azure Kubernetes Service를 사용하여 자체 컨테이너를 빌드하고 Azure에 배포할 수 있습니다. 자세한 내용은 [Azure에 Node.js 컨테이너 배포 방법](node-howto-deploy-containers.md)을 참조하세요.

- 서버리스 코드를 주로 사용하려면 [Azure에서 서버리스 Node.js 코드를 작성하는 방법](node-howto-write-serverless-code.md)을 참조하세요.

- JAMstack(정적) 사이트 만들기에 대한 자세한 내용은 [Azure를 사용하여 JAMstack(정적 사이트) 웹앱을 빌드하는 방법](node-howto-create-static-site-jamstack.md)을 참조하세요.

- 인프라를 제어하려면 가상 머신을 사용하면 됩니다. 시작하려면 Microsoft Learn에서 [Azure Virtual Machines를 사용하여 웹 사이트 배포](/learn/paths/deploy-a-website-with-azure-virtual-machines/) 경로를 따르세요.

다양한 호스팅 옵션에 대한 전체 개요는 [Azure 컴퓨팅 서비스에 대한 의사 결정 트리](/azure/architecture/guide/technology-choices/compute-decision-tree)와 Microsoft Learn의 [핵심 Cloud Services - Azure 컴퓨팅 옵션](/learn/modules/intro-to-azure-compute/) 모듈을 참조하세요.
