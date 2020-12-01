---
title: Computer Vision 리소스 만들기
description: Cognitive Services Computer Vision 리소스를 만들고 환경 변수로 설정합니다.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 4ac324171f47ab8795169c5dd453d1e6451e8906
ms.sourcegitcommit: 4dac39849ba2e48034ecc91ef578d11aab796e58
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94993507"
---
# <a name="3-create-computer-vision-resource-and-use-in-code"></a>3. 코드로 Computer Vision 리소스를 만들고 사용

이 단계에서는 Computer Vision 리소스를 만들고 환경 변수로 설정합니다. 

## <a name="create-azure-resources"></a>Azure 리소스 만들기

리소스 그룹을 만들면 리소스를 쉽게 찾고, 작업을 마친 후 쉽게 삭제할 수 있습니다.

이러한 단계를 마친 후에는 리소스의 **키 및 엔드포인트** 가 필요합니다.

1. 터미널 또는 bash 셸에서 [Azure 리소스 그룹을 만드는 Azure CLI 명령을 입력](/cli/azure/group?view=azure-cli-latest#az_group_create)하고, 이름을 `rg-demo`로 지정합니다.

    ```azurecli
    az group create \
        --location eastus \
        --name rg-demo 
    ```
1. 다음 명령을 실행하여 [Computer Vision 리소스 그룹을 만듭니다](/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create).


    ```azurecli
    az cognitiveservices account create \
        --name demo-ComputerVision \
        --resource-group rg-demo \
        --kind ComputerVision \
        --sku F0 \
        --location eastus \
        --yes
    ```

1. 결과에서 `properties.endpoint`를 찾아 복사합니다. 나중에 필요합니다.

    ```json
    ...
    "properties":{
        ...
        "endpoint": "https://eastus.api.cognitive.microsoft.com/",
        ...
    }
    ...
    ```

1. 다음 [명령](/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list)을 실행하여 키를 가져옵니다. 

    ```azurecli
    az cognitiveservices account keys list \
    --name demo-ComputerVision \
    --resource-group rg-demo
    ```

1. 나중에 필요하므로, 키 중 하나를 복사합니다.

    ```json
    {
      "key1": "8eb7f878bdce4e96b26c89b2b8d05319",
      "key2": "c2067cea18254bdda71c8ba6428c1e1a"
    }
    ```

## <a name="add-environment-variables-to-your-local-environment"></a>로컬 환경에 환경 변수 추가

리소스를 사용하려면 로컬 코드에서 키와 엔드포인트를 사용할 수 있어야 합니다. 이 코드 베이스는 다음과 같은 키와 엔드포인트를 환경 변수에 저장합니다.

* REACT_APP_COMPUTERVISIONKEY
* REACT_APP_COMPUTERVISIONENDPOINT 

1. 다음 명령을 실행하여 이러한 변수를 환경에 추가합니다.

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    export REACT_APP_COMPUTERVISIONKEY="REPLACE-WITH-YOUR-KEY"
    export REACT_APP_COMPUTERVISIONENDPOINT="REPLACE-WITH-YOUR-ENDPOINT"
    ```
    
    # <a name="cmd"></a>[cmd](#tab/cmd)
    
    ```cmd
    set REACT_APP_COMPUTERVISIONKEY="REPLACE-WITH-YOUR-KEY"
    set REACT_APP_COMPUTERVISIONENDPOINT="REPLACE-WITH-YOUR-ENDPOINT"
    ```
    ---

## <a name="add-environment-variables-to-your-remote-environment"></a>원격 환경에 환경 변수 추가

Azure Static 웹앱을 사용하는 경우 비밀과 같은 환경 변수를 GitHub 작업에서 Static 웹앱으로 전달해야 합니다. GitHub 작업은 해당 리포지토리의 GitHub 비밀을 통해 전달된 Computer Vision 키와 엔드포인트를 포함하여 앱을 빌드한 다음, 환경 변수를 사용하여 코드를 정적 웹앱에 푸시합니다.

1. 웹 브라우저의 GitHub 리포지토리에서 **설정**, **비밀**, **새 리포지토리 비밀** 을 차례로 선택합니다.

    :::image type="content" source="../../media/static-web-app/browser-screenshot-github-create-new-repository-secret.png" alt-text="키와 엔드포인트를 설정하기 전의 이미지 분석을 위한 React Cognitive Service Computer Vision 샘플을 보여주는 브라우저의 부분 스크린샷":::

1. 이전 섹션에서 사용한 것과 동일한 엔드포인트 이름과 값을 입력합니다. 그런 다음, 이전 섹션에서 사용한 것과 동일한 키 이름 및 값을 사용하여 또 다른 비밀을 만듭니다. 
    
    :::image type="content" source="../../media/static-web-app/browser-screenshot-github-add-secret.png" alt-text="동일한 엔드포인트 이름 및 값을 입력합니다. 그런 다음, 동일한 키 이름과 값을 사용하여 또 다른 비밀을 만듭니다.":::

## <a name="run-react-app-with-computervision-resource"></a>Computer Vision 리소스를 사용하여 React 앱 실행

이 React 앱은 앱을 다시 빌드하고 다시 실행하기 위한 변경 내용을 감시합니다. 강제로 다시 빌드하려면 앱을 변경하세요.

1. 첫 번째 빈 줄(4번째 줄) 바로 뒤의 `./src/VisualAi.js`에 **새 줄을 입력** 합니다. 이렇게 변경하면 로컬로 실행되는 웹 사이트가 다시 빌드됩니다.

    :::image type="content" source="../../media/static-web-app/browser-screenshot-react-computervision-app-start-up.png" alt-text="URL 또는 Enter 키 누르기 준비가 완료된 React Cognitive Service Computer Vision 샘플을 보여주는 브라우저의 부분 스크린샷.":::

1. 텍스트 필드를 비워 두고 **분석 단추를 선택하세요**. 

    :::image type="content" source="../../media/static-web-app/browser-screenshot-react-computervision-app-image-analysis-result.png" alt-text="React Cognitive Service Computer Vision 샘플 결과를 보여주는 브라우저의 부분 스크린샷.":::

    이미지는 `./src/DefaultImages.js`에 정의된 이미지 카탈로그에서 임의로 선택됩니다. 

1. **분석** 단추를 선택하여 다른 이미지와 결과를 계속 확인하세요. 

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Static 웹앱 만들기](create-static-web-app-visual-studio-code-extension.md)