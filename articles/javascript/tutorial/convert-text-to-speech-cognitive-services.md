---
title: Express.js 앱은 Cognitive Services 음성을 사용하여 텍스트를 음성으로 변환합니다.
description: 클라이언트와 서버에 표시되는 텍스트를 음성으로 변환하려면 Cognitive Services 음성을 사용합니다.
ms.topic: tutorial
ms.date: 01/20/2021
ms.custom: languages:JavaScript, devx-track-javascript
ms.openlocfilehash: 028871f55a7be2e38191efef37a65ad41ab9c3dc
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102117887"
---
# <a name="expressjs-app-converts-text-to-speech-with-cognitive-services-speech"></a>Express.js 앱은 Cognitive Services 음성을 사용하여 텍스트를 음성으로 변환합니다.

이 자습서에서는 Cognitive Services Speech Service를 통해 기존 Express.js 앱에 Cognitive Services 음성을 추가하여 텍스트를 음성으로 변환하는 기능을 추가합니다. 텍스트를 음성으로 변환하면 오디오를 수동으로 생성하는 데 비용을 들이지 않고 오디오를 제공할 수 있습니다. 

이 자습서에서는 Azure Cognitive Services 음성을 통해 텍스트를 음성으로 변환하는 세 가지 방법을 보여 줍니다.

* 클라이언트 JavaScript가 직접 오디오를 가져오는 방법 
* 서버 JavaScript가 파일(*.MP3)에서 오디오를 가져오는 방법
* 서버 JavaScript가 메모리 내 arrayBuffer에서 오디오를 가져오는 방법

## <a name="application-architecture"></a>애플리케이션 아키텍처

이 자습서에서는 최소 Express.js 앱을 사용하며 다음 조합을 사용하여 기능을 추가합니다.

* 텍스트 음성 변환을 제공하고, MP3 스트림을 반환하기 위한 서버 API의 새 경로
* 정보를 입력할 수 있는 HTML 양식의 새 경로
* Speech Service에 대한 클라이언트 측 호출을 제공하는 새 HTML 양식(JavaScript 사용)

이 애플리케이션은 음성을 텍스트로 변환하기 위한 세 가지 호출을 제공합니다.

* 첫 번째 서버 호출은 서버에 파일을 만든 다음, 클라이언트에 반환합니다. 일반적으로 긴 텍스트 또는 두 번 이상 제공될 텍스트에 이 호출을 사용합니다. 
* 두 번째 서버 호출은 짧은 용어 텍스트를 위한 것으로, 클라이언트에 반환되기 전에 메모리에 도움이 됩니다. 
* 클라이언트 호출은 SDK를 사용하여 Speech Service에 대한 직접 호출을 보여 줍니다. 서버가 없는 클라이언트 전용 애플리케이션이 있는 경우 이 호출을 수행하도록 선택할 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소


- [Node.js 10.1+ 및 npm](https://nodejs.org/en/download) - 로컬 머신에 설치
- [Visual Studio Code](https://code.visualstudio.com/) - 로컬 컴퓨터에 설치 
- VS Code용 [Azure App Service 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)(VS Code 내에서 설치됨)
- [Git](https://git-scm.com/downloads) - GitHub에 푸시하는 데 사용됩니다. 푸시하면 GitHub 작업이 활성화됩니다.
- bash [![Embed launch](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png "Azure Cloud Shell 시작")](https://shell.azure.com)를 통해 [Azure Cloud Shell](/azure/cloud-shell/quickstart)을 사용합니다.   
- 원하는 경우 Azure CLI를 [설치](/cli/azure/install-azure-cli)하여 CLI 참조 명령을 실행합니다.
   - local install을 사용하는 경우 [az login](/cli/azure/reference-index#az-login) 명령을 사용하여 Azure CLI에 로그인합니다.  인증 프로세스를 완료하려면 터미널에 표시되는 단계를 수행합니다.  추가 로그인 옵션은 [Azure CLI를 사용하여 로그인](/cli/azure/authenticate-azure-cli)을 참조하세요.
  - 메시지가 표시되면 처음 사용할 때 Azure CLI 확장을 설치합니다.  확장에 대한 자세한 내용은 [Azure CLI에서 확장 사용](/cli/azure/azure-cli-extensions-overview)을 참조하세요.
  - [az version](/cli/azure/reference-index?#az_version)을 실행하여 설치된 버전과 종속 라이브러리를 찾습니다. 최신 버전으로 업그레이드하려면 [az upgrade](/cli/azure/reference-index?#az_upgrade)를 실행합니다.

## <a name="download-sample-expressjs-repo"></a>샘플 Express.js 리포지토리 다운로드 

1. Git을 사용하여 Express.js 샘플 리포지토리를 로컬 컴퓨터에 복제합니다. 

    ```bash
    git clone https://github.com/Azure-Samples/js-e2e-express-server
    ```

1. 샘플의 새 디렉터리로 변경합니다.

    ```bash
    cd js-e2e-express-server
    ```

1. Visual Studio Code에서 프로젝트를 엽니다.

    ```bash
    code .
    ```

1. Visual Studio Code에서 새 터미널을 열고 프로젝트 종속성을 설치합니다.

    ```bash
    npm install
    ```

## <a name="install-cognitive-services-speech-sdk-for-javascript"></a>JavaScript용 Cognitive Services Speech SDK 설치

Visual Studio Code 터미널에서 Azure Cognitive Services Speech SDK를 설치합니다.

```bash
npm install microsoft-cognitiveservices-speech-sdk
```

## <a name="create-a-speech-module-for-the-expressjs-app"></a>Express.js 앱용 음성 모듈 만들기

1. Speech SDK를 Express.js 애플리케이션에 통합하려면 `src` 폴더에 `azure-cognitiveservices-speech.js`라는 파일을 만듭니다.
1. 다음 코드를 추가하여 종속성을 끌어오고 텍스트를 음성으로 변환하는 함수를 만듭니다.

    :::code language="javascript" source="~/../js-e2e-express-server-cognitive-services/text-to-speech/src/azure-cognitiveservices-speech.js" highlight="3,21,32" :::

    * 매개 변수 - 파일이 SDK, 스트림, 버퍼 및 파일 시스템(fs)을 사용하는 데 필요한 종속성을 끌어옵니다. `textToSpeech` 함수는 4개의 인수를 사용합니다. 로컬 경로를 가진 파일 이름이 전송되면 텍스트가 오디오 파일로 변환됩니다. 파일 이름이 전송되지 않으면 메모리 내 오디오 스트림이 생성됩니다. 
    * Speech SDK 메서드 - Speech SDK 메서드 [synthesizer.speakTextAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer#speakTextAsync_string___e__SpeechSynthesisResult_____void___e__string_____void__AudioOutputStream___PushAudioOutputStreamCallback___PathLike_)는 수신한 구성에 따라 서로 다른 형식을 반환합니다. 
        이 메서드는 수행해야 할 작업에 따라 다른 결과를 반환합니다.
        * 파일 만들기 
        * 메모리 내 스트림을 버퍼 배열로 만들기
    * 오디오 형식 - 선택한 오디오 형식은 MP3이지만 다른 [오디오 구성 방법](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?preserve-view=true&view=azure-node-latest#methods)과 [다른 형식](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?preserve-view=true&view=azure-node-latest)이 있습니다. 

    로컬 메서드인 `textToSpeech`는 SDK 콜백 함수를 래핑하고 프라미스로 변환합니다. 

## <a name="create-a-new-route-for-the-expressjs-app"></a>Express.js 앱용 새 경로 만들기

1. `src/server.js` 파일을 엽니다. 
1. `azure-cognitiveservices-speech.js` 모듈을 파일의 맨 위에 종속성으로 추가합니다.

    :::code language="javascript" source="~/../js-e2e-express-server-cognitive-services/text-to-speech/src/server.js" range="3"  :::
    

1. 새 API 경로를 추가하여 자습서의 이전 섹션에서 만든 **textToSpeech** 메서드를 호출합니다. 

    :::code language="javascript" source="~/../js-e2e-express-server-cognitive-services/text-to-speech/src/server.js" range="30-51" highlight="45-50" :::

    이 메서드는 쿼리 문자열의 `textToSpeech` 메서드에 대한 필수 및 선택적 매개 변수를 사용합니다. 파일을 만들어야 하는 경우 고유한 파일 이름이 개발됩니다. `textToSpeech` 메서드는 비동기적으로 호출되며 결과를 응답(`res`) 개체로 파이프합니다. 

## <a name="update-the-client-web-page-with-a-form"></a>양식을 사용하여 클라이언트 웹 페이지 업데이트 

필수 매개 변수를 수집하는 양식을 사용하여 클라이언트 HTML 웹 페이지를 업데이트합니다. 선택적 매개 변수는 사용자가 선택하는 오디오 컨트롤에 따라 전달됩니다. 이 자습서에서는 클라이언트에서 Azure Speech Service를 호출하는 메커니즘을 제공하므로 해당 JavaScript도 제공됩니다. 

`/public/client.html` 파일을 열고 콘텐츠를 다음으로 바꿉니다.

:::code language="html" source="~/../js-e2e-express-server-cognitive-services/text-to-speech/public/client.html" highlight="75, 102 137" :::

파일의 강조 표시된 줄: 

* 줄 74: `cdn.jsdelivr.net` 사이트를 사용하여 NPM 패키지를 배달하기 위해 Azure Speech SDK를 클라이언트 라이브러리로 끌어옵니다. 
* 줄 102: `updateSrc` 메서드는 키, 지역 및 텍스트를 포함하는 쿼리 문자열로 오디오 컨트롤의 `src` URL을 업데이트합니다. 
* 줄 137: 사용자가 `Get directly from Azure` 단추를 선택하면 웹 페이지는 클라이언트 페이지에서 Azure에 직접 호출하고 결과를 처리합니다. 

## <a name="create-cognitive-services-speech-resource"></a>Cognitive Services 음성 리소스 만들기

Azure Cloud Shell에서 Azure CLI 명령을 사용하여 음성 리소스를 만듭니다.


1. [Azure Cloud Shell](https://shell.azure.com)에 로그인합니다. 이렇게 하려면 유효한 Azure 구독에 대한 권한이 있는 사용자 계정으로 브라우저에 인증해야 합니다. 
1. 음성 리소스에 대한 리소스 그룹을 만듭니다. 

    ```azurecli
    az group create \
        --location eastus \
        --name tutorial-resource-group-eastus
    ```

1. 리소스 그룹에 음성 리소스를 만듭니다.

    ```azurecli
    az cognitiveservices account create \
        --kind SpeechServices \
        --location eastus \
        --name tutorial-speech \
        --resource-group tutorial-resource-group-eastus \
        --sku F0
    ```

    무료 음성 리소스가 이미 생성된 경우에는 이 명령이 실패합니다. 

1. 명령을 사용하여 새 음성 리소스의 키 값을 가져옵니다. 

    ```azurecli
    az cognitiveservices account keys list \
        --name tutorial-speech \
        --resource-group tutorial-resource-group-eastus \
        --output table
    ```

1. 키 중 하나를 복사합니다. 

    웹 양식에서 키를 사용하여 Azure Speech Service에 인증합니다.

## <a name="run-the-expressjs-app-to-convert-text-to-speech"></a>Express.js 앱을 실행하여 텍스트를 음성으로 변환

1. 다음 bash 명령을 사용하여 앱을 시작합니다.

    ```bash
    npm start
    ```

1. 브라우저에서 웹앱을 엽니다.

    ```
    http://localhost:3000    
    ```

1. 강조 표시된 텍스트 상자에 음성 키를 붙여넣습니다. 

    :::image type="content" source="../media/speech-tutorial/expressjs-webapp-form-with-speech-key-field-highlighted.png" alt-text="음성 키 입력 필드가 강조 표시된 웹 양식의 브라우저 스크린샷":::

1. 필요에 따라 텍스트를 새로운 항목으로 변경합니다. 

1. 3개의 단추 중 하나를 선택하여 오디오 형식으로의 변환을 시작합니다.
    * Azure에서 직접 가져오기 - Azure에 대한 클라이언트 쪽 호출
    * 파일의 오디오에 대한 오디오 컨트롤
    * 버퍼의 오디오에 대한 오디오 컨트롤

    컨트롤을 선택한 후 오디오가 재생되기 전까지 약간의 지연이 발생할 수 있습니다. 

## <a name="create-new-azure-app-service-in-visual-studio-code"></a>Visual Studio Code에서 Azure App Service 만들기

1. 명령 팔레트(**Ctrl**+**Shift**+**P**)에서 "웹 만들기"를 입력하고, **Azure App Service: 새 웹앱 만들기...고급** 을 선택합니다. 고급 명령을 사용하면 Linux 기본값을 사용하는 대신 리소스 그룹, App Service 계획 및 운영 체제를 포함한 배포를 완전히 제어할 수 있습니다.

1. 프롬프트에 다음과 같이 응답합니다.

    - **구독** 계정을 선택합니다.
    - **전역적으로 고유한 이름 입력** 에 `my-text-to-speech-app`처럼 
        - 모든 Azure에서 고유한 이름을 입력합니다. 영숫자 문자('A-Z', 'a-z' 및 '0-9') 및 하이픈('-')만 사용합니다.
    - 리소스 그룹에 `tutorial-resource-group-eastus`를 선택합니다.
    - `Node 10.1` 이상의 **런타임 스택을 선택** 합니다. 
    - Linux 운영 체제를 선택합니다.
    - **새 App Service 계획 만들기** 를 선택하고, `my-text-to-speech-app-plan`과 같은 이름을 제공하고, **F1 체험** [가격 책정 계층](../core/what-is-azure-for-javascript-development.md#free-tier-resources)을 선택합니다.
    - **F1** 무료 가격 책정 계층을 선택합니다.
    - Application Insights 리소스에 대해 **지금 건너뛰기** 를 선택합니다.
    - `eastus` 위치를 선택합니다. 

1. 잠시 후에 Visual Studio Code에서 만들기가 완료되었음을 알려줍니다. **X** 단추를 사용하여 알림을 닫습니다.

## <a name="deploy-local-expressjs-app-to-remote-app-service-in-visual-studio-code"></a>Visual Studio Code에서 원격 App Service에 로컬 Express.js 앱 배포

1. 웹앱이 준비되면 로컬 컴퓨터에서 코드를 배포합니다. Azure 아이콘을 선택하여 **Azure App Service** 탐색기를 열고, 구독 노드를 펼치고, 마우스 오른쪽 단추로 방금 만든 웹앱 이름을 클릭하고, **웹앱에 배포** 를 선택합니다.

1. 프롬프트에서 Express.js 앱의 루트 폴더를 선택하고, **구독** 계정을 다시 선택한 다음, 이전에 만든 웹앱의 이름(`my-text-to-speech-app`)을 선택합니다.

1. Linux에 배포하는 경우 대상 서버에서 `npm install`을 실행하도록 구성을 업데이트하라는 메시지가 표시되면 **예** 를 선택합니다.

    ![대상 Linux 서버의 구성을 업데이트하도록 요구하는 메시지](../media/deploy-azure/server-build.png)

1. 배포가 완료되면 프롬프트에서 **웹 사이트 찾아보기** 를 선택하여 새로 배포된 웹앱을 확인합니다. 

1. (선택 사항): 코드 파일을 변경한 다음, **웹앱에 배포** 를 사용하여 Azure App Service 확장에서 웹앱을 업데이트합니다.

## <a name="stream-remote-service-logs-in-visual-studio-code"></a>Visual Studio Code에서 원격 서비스 로그 스트림

실행 중인 앱에서 `console.log`에 대한 호출을 통해 생성한 출력을 보거나 추적합니다. 이러한 출력은 Visual Studio Code의 **출력** 창에 나타납니다.

1. **Azure App Service** 탐색기에서 마우스 오른쪽 단추로 새 앱 노드를 클릭하고 **스트리밍 로그 시작** 을 선택합니다.

    <pre>
    Starting Live Log Stream ---
    </pre>

1. 브라우저에서 페이지를 몇 번 새로 고치면 로그 출력이 추가로 표시됩니다.


## <a name="clean-up-resources-by-removing-resource-group"></a>리소스 그룹을 제거하여 리소스 정리

이 자습서를 완료한 후에는 사용 요금이 청구되지 않도록 리소스 그룹과 그 안에 포함된 리소스를 제거해야 합니다. 

Azure Cloud Shell에서 [Azure CLI 명령](/cli/azure/group#az_group_delete)을 사용하여 리소스 그룹을 삭제합니다.

```azurecli
az group delete --name tutorial-resource-group-eastus  -y
```

이 명령은 몇 분 정도 걸릴 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [Visual Studio Code에서 App Service에 Express.js MongoDB 앱 배포](deploy-nodejs-mongodb-app-service-from-visual-studio-code.md)