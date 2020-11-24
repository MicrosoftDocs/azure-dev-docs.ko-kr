---
title: Azure CLI Linux 가상 머신
description: GitHub 리포지토리의 Express.js 기반 앱 복제를 사용하여 Azure Linux 가상 머신을 만듭니다.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 835a452e87fed88ac83085c882a5a40461bd2dd0
ms.sourcegitcommit: 6514a061ba5b8003ce29d67c81a9f0795c3e3e09
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94623179"
---
# <a name="1-create-linux-virtual-machine-with-expressjs-app-using-azure-cli"></a>1. Azure CLI를 사용하여 Express.js 앱으로 Linux 가상 머신 만들기

이 자습서에서는 Express.js 앱에 대한 Linux VM(가상 머신)을 만듭니다. 이 VM은 cloud-init 구성 파일을 통해 구성되며 NGINX 및 Express.js 앱용 GitHub 리포지토리를 포함합니다. VM이 실행되면 SSH를 통해 VM에 연결하고, 추적 로깅을 포함하도록 웹앱을 변경하고, 웹 브라우저에서 공용 Express.js 서버 앱을 볼 수 있습니다.

이 자습서에는 다음 작업이 포함되어 있습니다.

* Azure CLI를 사용하여 Azure에 로그인
* Azure CLI를 사용하여 Azure Linux VM 리소스 만들기
    * 공용 포트 80 열기
    * GitHub 리포지토리에서 데모 Express.js 웹앱 설치
    * 웹앱 종속성 설치
    * 웹앱 시작
* Azure CLI를 사용하여 Azure 모니터링 리소스 만들기
    * SSH를 통해 VM에 연결
    * npm을 사용하여 Azure SDK 클라이언트 라이브러리 설치
    * Application Insights 클라이언트 라이브러리 코드를 추가하여 사용자 지정 추적 만들기
* 브라우저에서 웹앱 보기
    * Application Insights 로그에 사용자 지정 추적을 생성하도록 `/trace` 경로 요청
    * Azure CLI를 사용하여 로그에 수집된 추적 수 보기
    * Azure Portal에서 추적 목록 보기
* Azure CLI를 사용하여 리소스 제거

[!INCLUDE [Create or use existing Azure Subscription ](../../includes/environment-subscription-h2.md)]

## <a name="prerequisites"></a>전제 조건

* Azure CLI 명령을 실행할 수 있도록 [Azure CLI](/cli/azure/install-azure-cli)를 설치합니다.
* 다음과 같이 SSH를 통해 VM에 연결합니다. bash 셸 같은 최신 터미널(SSH 포함)을 사용합니다.

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 리소스 그룹에 대한 리소스 그룹 만들기](create-azure-monitoring-application-insights-web-resource.md) 