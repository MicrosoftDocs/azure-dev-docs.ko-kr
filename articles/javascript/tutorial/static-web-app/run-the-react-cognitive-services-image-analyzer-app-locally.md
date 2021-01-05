---
title: 샘플 React 앱 다운로드
description: 전체 샘플 앱은 GitHub 리포지토리에 제공됩니다. 리포지토리를 포크하고, 종속성을 설치하고, 로컬로 실행합니다.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 5050aca4c34f20f8b4c7cb7a8460815516abd7c7
ms.sourcegitcommit: 1c508f5ba73a12e4baeacc88ad9a8359301acb50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97687460"
---
# <a name="3-download-and-run-the-react-cognitive-services-image-analyzer-app"></a>3. React Cognitive Services Image Analyzer 앱을 다운로드하여 실행

전체 샘플 앱은 GitHub 리포지토리에 제공됩니다. 리포지토리를 포크하고, 종속성을 설치하고, 로컬로 실행합니다.

## <a name="fork-the-sample-repo"></a>샘플 리포지토리 포크

사용자 고유의 GitHub 리포지토리에서 변경 내용을 푸시하려면 리포지토리를 단순히 로컬 컴퓨터에 복제하는 대신 리포지토리를 포크합니다. 

1. 별도의 브라우저 창 또는 탭을 열고 <a href="https://github.com/login" target="_blank">GitHub</a>에 로그인합니다. 
1. 웹 브라우저에서 <a href="https://github.com/Azure-Samples/js-e2e-client-cognitive-services" target="_blank">GitHub 샘플 리포지토리</a>로 이동합니다. 

    ```http
    https://github.com/Azure-Samples/js-e2e-client-cognitive-services
    ```

1. 페이지의 오른쪽 위 섹션에서 **포크** 를 선택합니다. 
1. **코드** 를 선택한 다음, 포크의 위치 URL을 복사합니다. 

    :::image type="content" source="../../media/static-web-app/browser-screenshot-clone-github-sample-repository-fork.png" alt-text="GitHub 웹 사이트의 부분 스크린샷. **코드**를 선택한 다음, 포크의 위치를 복사합니다.":::    

## <a name="create-local-development-environment"></a>로컬 개발 환경 만들기

1. 터미널 또는 bash 창에서 포크를 로컬 컴퓨터에 복제합니다. `YOUR-ACCOUNT-NAME`을 GitHub 계정 이름으로 바꿉니다.

    ```bash
    git clone https://github.com/YOUR-ACCOUNT-NAME/js-e2e-client-cognitive-services
    ```

1. 새 디렉터리로 변경하고 종속성을 설치합니다. 

    ```bash
    cd js-e2e-client-cognitive-services && npm install
    ```

## <a name="run-sample"></a>샘플 실행

1. 예제를 실행합니다. 

    ```bash
    npm start
    ```

    :::image type="content" source="../../media/static-web-app/browser-screenshot-react-cognitive-services-app-before-authentication.png" alt-text="키와 엔드포인트를 설정하기 전의 이미지 분석을 위한 React Cognitive Service Computer Vision 샘플을 보여주는 브라우저의 부분 스크린샷":::    
    
1. 앱을 중지합니다. 터미널 창을 닫거나 터미널에서 `control+c`를 사용합니다. 
    
## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [코드로 Computer Vision 리소스를 만들고 사용](create-computer-vision-resource-use-in-code.md) 