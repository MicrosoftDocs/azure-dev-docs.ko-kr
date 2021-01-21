---
title: Computer Vision 앱 아키텍처
description: 자습서의 이 섹션에서는 클라이언트 앱 및 배포 프로세스를 설명합니다.
ms.topic: tutorial
ms.date: 12/17/2020
ms.custom: devx-track-js
ms.openlocfilehash: b8949b355ec30775dfacf5ea77db53edb595578c
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561649"
---
# <a name="2-application-architecture-for-static-web-app-with-computer-vision"></a>2. Computer Vision이 있는 정적 웹앱을 위한 애플리케이션 아키텍처

클라이언트 애플리케이션 및 배포 프로세스에 대해 알아봅니다.

## <a name="what-is-an-azure-static-web-app"></a>Azure Static 웹앱이란?

정적 웹앱을 빌드할 때 원하는 기능과 제어 수준에 따라 Azure에서 여러 가지 옵션을 선택할 수 있습니다. 이 자습서는 여러분이 호스팅 환경이 아닌 프런트 엔드 코드에 집중할 수 있도록 여러 옵션이 자동으로 선택되는 가장 쉬운 서비스를 중심으로 진행됩니다.

## <a name="client-application-architecture"></a>클라이언트 애플리케이션 아키텍처

React(create-react-app) 앱은 다음과 같은 기능을 제공합니다. 
* Cognitive Services [**Computer Vision**](/azure/cognitive-services/computer-vision/)의 Azure 키 및 엔드포인트를 찾을 수 없는 경우 메시지 표시
* Cognitive Services Computer Vision을 사용하여 이미지 분석
    * 공용 이미지 URL을 입력하거나 컬렉션의 이미지 분석
    * 분석이 완료되면
        * 이미지 표시
        * Computer Vision JSON 결과 표시 

:::image type="content" source="../../media/static-web-app/browser-screenshot-react-computervision-app-image-analysis-result.png" alt-text="React Cognitive Service Computer Vision 샘플 결과를 보여주는 브라우저의 부분 스크린샷.":::

## <a name="deploy-to-azure-with-github-action"></a>GitHub 작업으로 Azure에 배포

특정 분기에 대한 푸시가 발생하면 다음과 같은 GitHub 작업이 시작됩니다.
* Computer Vision 키 및 엔드포인트의 GitHub 비밀을 빌드에 삽입
* React(create-react-app) 클라이언트 빌드
* 결과 파일을 Azure [**Static 웹앱**](/azure/static-web-apps) 리소스로 이동

> [!div class="nextstepaction"]
> [React Cognitive Services Image Analyzer 앱을 다운로드하여 로컬로 실행](run-the-react-cognitive-services-image-analyzer-app-locally.md)