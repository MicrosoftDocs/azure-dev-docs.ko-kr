---
title: Static 웹앱 리소스 만들기
description: 해당 서비스의 Visual Studio Code 확장을 사용하여 Static 웹앱 리소스를 만듭니다.
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: dac91157531b3d26dddebe411d2c79883f77d4b9
ms.sourcegitcommit: 1c508f5ba73a12e4baeacc88ad9a8359301acb50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97687468"
---
# <a name="5-create-azure-static-web-app-resource"></a>5. Azure Static 웹앱 리소스 만들기

자습서의 이 섹션에서는 해당 서비스의 Visual Studio Code 확장을 사용하여 Static 웹앱 리소스를 만들고, 빌드할 원격 리포지토리에 로컬 코드를 푸시하고, 앱을 Azure에 배포합니다.

## <a name="create-a-new-branch-dedicated-to-deployment"></a>배포 전용 새 분기 만들기

Azure Static 웹앱은 GitHub 리포지토리의 특정 분기에서 빌드를 수신합니다. 현재 이 자습서에서는 `main` 분기를 사용했습니다. Visual Studio Code의 새 터미널에서는 앱을 빌드하고 배포하는 용도로만 사용되는 `live` 분기를 만듭니다.

```bash
git checkout -b live
```

## <a name="push-the-live-branch-to-github"></a>GitHub로 live 분기 푸시

Visual Studio Code 터미널에서 로컬 분기 `live`를 원격 리포지토리로 푸시합니다.

```bash
git push origin live
```

## <a name="create-a-static-web-app-resource"></a>Static 웹앱 리소스 만들기

1. **Azure** 아이콘을 선택하고 **Static Web Apps** 서비스를 마우스 오른쪽 단추로 클릭한 다음, **Static 웹앱 만들기...** 를 선택합니다. 

    :::image type="content" source="../../media/static-web-app/visualstudiocode-storage-extension-create-static-web-resource.png" alt-text="Visual Studio 확장을 사용하는 Visual Studio Code 스크린샷":::

1. 후속 필드에 다음 정보를 입력합니다. 후속 필드는 한 번에 하나씩 표시됩니다. 

    |필드 이름| 값|
    |--|--|
    |정적 웹앱의 이름.|`Demo-ComputerVisionAnalyzer`|
    |리포지토리의 분기 선택|`live`| 
    |애플리케이션 코드의 위치를 선택합니다.|`/`|
    |Azure Functions 코드의 위치를 선택합니다.|**지금은 건너뛰기** 를 선택합니다.|
    |앱의 위치를 기준으로 빌드 출력의 경로를 입력합니다.|`build`|
    |새 리소스의 위치 선택|가까운 Azure 위치를 선택합니다.|

## <a name="update-the-github-action-with-secret-environment-variables"></a>비밀 환경 변수를 사용하여 GitHub 작업 업데이트

Computer Vision 키와 엔드포인트가 리포지토리의 비밀 컬렉션에만 있고 GitHub 작업에는 아직 없습니다. 이 단계에서는 키와 엔드포인트를 작업에 추가합니다.

1. Azure 리소스를 만들 때 변경한 내용을 가져와서 GitHub 작업 파일을 가져옵니다.

    ```bash
    git pull origin live
    ```

1. Visual Studio Code 편집기에서 `./.github/workflows/`에 있는 GitHub 작업 파일을 편집하여 비밀을 추가합니다. 

    :::code language="yml" source="~/../js-e2e-client-cognitive-services/.github/workflows/sample-github-workflow.yml" highlight="34-36" :::

    
1. 로컬 `live` 분기에 변경 내용을 추가하고 커밋합니다.

    ```bash
    git add . && git commit -m "add secrets to action"
    ```

1. Azure Static 웹앱에 대한 새로운 build-and-deploy 작업부터 시작하여 변경 내용을 원격 리포지토리에 푸시합니다.

    ```bash
    git push origin live
    ```

## <a name="view-the-github-action-build-process"></a>GitHub 작업 빌드 프로세스 보기

1. 웹 브라우저에서 이 자습서에 대한 GitHub 리포지토리를 열고 **작업** 을 선택합니다. 

1. 목록에서 최상위 빌드를 선택한 다음, 왼쪽 메뉴에서 **작업 빌드 및 배포** 를 선택하여 빌드 프로세스를 살펴봅니다. **빌드 및 배포** 가 성공적으로 완료될 때까지 기다립니다.

    :::image type="content" source="../../media/static-web-app/browser-screenshot-github-action-build-react-computer-vision-app.png" alt-text=" 목록에서 최상위 빌드를 선택한 다음, 왼쪽 메뉴에서 `작업 빌드 및 배포`를 선택하여 빌드 프로세스를 살펴봅니다. 빌드가 성공적으로 완료될 때까지 기다립니다.":::

## <a name="view-azure-static-web-site-in-browser"></a>브라우저에서 Azure 정적 웹 사이트 보기

1. Visual Studio Code의 맨 오른쪽 메뉴에서 **Azure** 아이콘을 선택하고 Static 웹앱을 선택한 다음, **사이트 찾아보기** 를 마우스 오른쪽 단추로 클릭하고 **열기** 를 선택하여 공용 정적 웹 사이트를 봅니다. 

:::image type="content" source="../../media/static-web-app/visualstudiocode-browse-static-web-app.png" alt-text="`사이트 찾아보기`를 선택한 다음, `열기`를 선택하여 공용 정적 웹 사이트를 봅니다.":::

다음 위치에서도 사이트의 URL을 찾을 수 있습니다.
* 리소스에 대한 Azure Portal의 **개요** 페이지
* GitHub 작업의 build-and-deploy 출력에서 스크립트의 끝에 사이트 URL이 있습니다. 

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [React 코드 및 Cognitive Services Computer Vision 분석 검토](add-computer-vision-react-app.md)