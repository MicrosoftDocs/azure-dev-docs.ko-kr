---
title: Azure에 Node.js 컨테이너 배포
description: Docker 컨테이너를 사용하여 Azure에 Node.js 앱 배포
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 6bf8acb66a708433966bdfe90cc358a56d2d3b42
ms.sourcegitcommit: ae2fa266a36958c04625bb0ab212e6f2db98e026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96857811"
---
# <a name="deploy-nodejs-container-to-azure"></a>Azure에 Node.js 컨테이너 배포 

컨테이너를 사용하여 앱을 빌드하는 방법은 확장성을 확보하기 위한 일반적인 패턴입니다. Azure는 컨테이너를 배포하는 다양한 방법을 제공합니다.

## <a name="host-your-container-app-on-azure"></a>Azure에서 컨테이너 앱 호스트

다음 호스팅 옵션을 사용하여 컨테이너화된 애플리케이션을 배포할 수 있습니다.

| 서비스 | 권장 용도 |
|--|--|
|[App service](/azure/app-service/quickstart-custom-container?pivots=container-linux)|Azure 앱 서비스에 사용자 지정 컨테이너를 배포하고 실행합니다.|
|[Container Instances](/azure/container-instances/)|단일 컨테이너를 신속하게 설정합니다.|
|[Container Registry](/azure/container-registry/)|사용자 지정 또는 프라이빗 컨테이너 이미지를 빌드, 저장 및 관리합니다.|
|[Kubernetes Service](/azure/aks/)|여러 컨테이너를 오케스트레이션합니다.|
|[VM(Virtual Machines)](/azure/virtual-machines)|Windows 또는 Linux VM을 완벽하게 제어합니다. Azure Marketplace에서 [보증된 Linux 배포판을 찾거나](/azure/virtual-machines/linux/endorsed-distros?toc=/azure/virtual-machines/linux/toc.json) Linux VM 이미지를 [찾는 방법을 알아보세요](/azure/virtual-machines/linux/cli-ps-findimage).|

## <a name="build-containerize-and-deploy-app-to-azure"></a>앱을 빌드하고, 컨테이너화하고, Azure에 배포

시작하려면 이 [자습서](develop-nodejs-on-azure.md)를 따라 다음 방법을 알아보세요.

* 샘플 코드 다운로드
* Node.js 앱 실행
* Visual Studio Code에서 앱 디버그
* Node.js MEAN 앱 컨테이너화
* Azure CLI 명령을 사용하여 앱 배포
* CosmosDB 리소스에 MongoDB 서버 만들기
* 프라이빗 컨테이너 레지스트리에 컨테이너 이미지 추가
* 웹앱에 사용자 지정 도메인 이름 추가
* 웹앱을 더 큰 규모로 스케일링
* 모든 리소스에 대한 리소스 그룹 만들기 및 삭제

## <a name="next-steps"></a>다음 단계

Microsoft Learn 모듈:

- [Azure Container Instances에서 Docker 컨테이너 실행](/learn/modules/run-docker-with-azure-container-instances/)

- [Azure Container Registry를 사용하여 컨테이너 이미지 빌드 및 저장](/learn/modules/build-and-store-container-images/)
