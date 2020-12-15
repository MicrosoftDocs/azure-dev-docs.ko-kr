---
title: VSCode를 사용하여 Blob Storage에 이미지 업로드 - App Service/CosmosDB
description: React 앱을 사용하여 파일을 Azure Storage Blob에 업로드합니다. 이 자습서에서는 Visual Studio Code 확장을 통해 로컬 및 원격 환경을 사용하는 데 중점을 둡니다.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: scenarios:getting-started, languages:JavaScript, devx-track-javascript, azure-sdk-storage-blob-typescript-version-12.2.1
ms.openlocfilehash: 514c4cf3c5d54c30451759958b1cef5e465c5c8e
ms.sourcegitcommit: 0cda024089784b92c1db3a4506c1dccd6bfe6339
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96772610"
---
# <a name="upload-an-image-to-an-azure-storage-blob"></a>Azure Storage Blob에 이미지 업로드

클라이언트 쪽 React 앱을 사용하여 Azure Storage npm 패키지를 통해 이미지 파일을 Azure Storage Blob에 업로드합니다. 

프로그래밍 작업은 완료되었습니다. 이 자습서에서는 Azure 확장을 사용하여 Visual Studio Code 내에서 로컬 및 원격 Azure 환경을 성공적으로 사용하는 데 중점을 둡니다.

* [소스 코드](https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob)

## <a name="application-architecture-and-functionality"></a>애플리케이션 아키텍처 및 기능

이 자습서에는 JavaScript 개발자를 위한 몇 가지 **상위 Azure 작업** 이 포함되어 있습니다.

* Visual Studio Code를 사용하여 로컬로 React 앱 실행
* Storage 리소스 만들기 및 파일 업로드 구성
    * CORS 구성
    * SAS(공유 액세스 서명) 토큰 만들기
* SAS 토큰을 사용하여 서비스를 인증하도록 Azure SDK 클라이언트 라이브러리에 대한 코드 구성

[GitHub에서 사용할 수 있는](https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob) Resact 앱 샘플을 구성하는 요소는 다음과 같습니다.

* 3000 포트에서 호스팅되는 **React 앱**
* Storage Blob에 업로드할 Azure SDK 클라이언트 라이브러리 스크립트

:::image type="content" source="../media/tutorial-browser-file-upload/browser-react-app-azure-storage-resource-image-uploaded-displayed.png" alt-text="Azure Storage Blob에 연결된 간단한 React 앱":::

## <a name="1-set-up-development-environment"></a>1. 개발 환경 설정

- 활성 구독이 포함된 Azure 사용자 계정 [체험 계정 만들기](https://azure.microsoft.com/free/)
- [Node.js 및 npm](https://nodejs.org/en/download) - 로컬 컴퓨터에 설치된 Node.js 패키지 관리자입니다.
- [Visual Studio Code](https://code.visualstudio.com/)(로컬 컴퓨터에 설치됨) 
- Visual Studio Code 확장:
    - [Azure Storage](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) - Storage 리소스를 보는 데 사용됩니다.

## <a name="2-clone-and-run-the-initial-react-app"></a>2. 초기 React 앱 복제 및 실행

앱을 복제하고, 종속성을 설치하고, 앱을 실행합니다. 초기 앱이 코드에서 구성된 경우 Azure Storage에 연결하려고 시도하거나 사용할 수 없는 경우 `Storage is not configured`라는 메시지를 표시합니다. 

1. Visual Studio Code를 엽니다.
1. Git 아이콘, **리포지토리 복제** 를 차례로 선택하여 GitHub 리포지토리를 복제합니다. 이 프로세스를 수행하려면 GitHub에 로그인해야 합니다. `https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob`의 리포지토리 URL을 사용한 다음, 샘플을 복제할 로컬 컴퓨터의 폴더를 선택합니다. 메시지가 표시되면 복제된 리포지토리를 엽니다. 

    :::image type="content" source="../media/tutorial-browser-file-upload/vscode-git-clone-repository.png" alt-text="Git 아이콘, '리포지토리 복제'를 차례로 선택하여 GitHub 리포지토리를 복제합니다.":::

1. Visual Studio Code에서 터미널 창을 열고, 다음 명령을 실행하여 샘플의 종속성을 설치합니다.

    ```javascript
    npm install
    ```

1. 동일한 터미널 창에서 명령을 실행하여 웹앱을 실행합니다.

    ```javascript
    npm start
    ```

1. 웹 브라우저를 열고, 다음 URL을 사용하여 로컬 컴퓨터에서 웹앱을 확인합니다.

    ```url
    http://localhost:3000/
    ```

    Storage가 구성되지 않았다는 텍스트가 포함된 간단한 웹앱이 브라우저에 표시되면 자습서의 이 섹션을 성공적으로 수행한 것입니다.

    :::image type="content" source="../media/tutorial-browser-file-upload/browser-react-app-no-azure-storage-resource-configured.png" alt-text="MongoDB 데이터베이스에 연결된 간단한 Node.js 앱":::

1. Visual Studio Code 터미널에서 Ctrl+C를 사용하여 코드를 중지합니다.

## <a name="3-create-storage-resource-with-visual-studio-extension"></a>3. Visual Studio 확장을 사용하여 Storage 리소스 만들기

1. Azure Storage 확장으로 이동합니다. 마우스 오른쪽 단추로 구독을 클릭한 다음, `Create Storage Account...`를 선택합니다.

    :::image type="content" source="../media/tutorial-browser-file-upload/visualstudiocode-storage-extension-create-resource.png" alt-text="Azure Storage 확장으로 이동합니다. 마우스 오른쪽 단추로 구독을 클릭한 다음, '스토리지 계정 만들기...'를 선택합니다.":::

1. 다음 표를 사용하여 표시되는 메시지에 따라 값을 사용하는 방법을 이해합니다.

    |속성|값|
    |--|--|
    |전역적으로 고유한 새 웹앱의 이름을 입력합니다.| Storage 리소스 이름에 대한 값(예: `fileuploadyourname`)을 입력합니다. `yourname`을 소문자 이름 또는 고유 ID로 바꿉니다. 이 고유한 이름은 브라우저에서 리소스에 액세스하기 위한 URL의 일부로도 사용됩니다. 최대 24자의 문자와 숫자만 사용합니다. 나중에 사용하기 위해 이 **계정 이름** 이 필요합니다.|

1. 앱 만들기 프로세스가 완료되면 새 리소스에 대한 정보가 포함된 알림이 표시됩니다. 

    :::image type="content" source="../media/tutorial-browser-file-upload/visualstudiocode-storage-extension-create-resource-complete.png" alt-text="앱 만들기 프로세스가 완료되면 새 리소스에 대한 정보가 포함된 알림이 표시됩니다.":::

## <a name="4-set-storage-account-name-in-code-file"></a>4. 코드 파일에서 스토리지 계정 이름 설정

스토리지 키 이름을 빈 문자열에 추가하여 `storageAccountName` 값에 대한 `src/uploadToBlob.ts`의 리소스 이름을 설정합니다. 나머지 코드는 그대로 둡니다. 

```typescript
const storageAccountName = process.env.storageresourcename || ""; 
```

## <a name="5-generate-your-shared-access-signature-sas-token"></a>5. SAS(공유 액세스 서명) 토큰 생성 

CORS를 구성하기 전에 SAS 토큰을 생성합니다. 

1. Storage용 Visual Studio Code 확장에서 마우스 오른쪽 단추로 리소스를 클릭하고, **포털에서 열기** 를 선택합니다. 그러면 정확한 Storage 리소스에 대한 Azure Portal이 열립니다.
1. **설정** 섹션에서 **공유 액세스 서명** 을 선택합니다. 
1. 다음 설정을 사용하여 SAS 토큰을 구성합니다. 

    | 속성|값|
    |--|--|
    |허용되는 서비스|Blob|
    |허용되는 리소스 유형|서비스, 컨테이너, 개체|
    |허용되는 권한|읽기, 쓰기, 삭제, 나열, 추가, 만들기|
    |버전 삭제 사용|선택됨|
    |시작 및 만료 날짜/시간|시작 날짜/시간을 적용하고, 종료 날짜 시간을 24시간 이후로 설정합니다. SAS 토큰은 24시간 동안만 유효합니다.|
    |HTTPS만 사용|선택됨|
    |기본 설정 라우팅 계층|Basic|
    |서명 키|key1 선택됨|

    :::image type="content" source="../media/tutorial-browser-file-upload/azure-portal-storage-blob-generate-sas-token.png" alt-text="SAS 토큰을 이미지와 같이 구성합니다. 설정은 이미지 아래에 설명되어 있습니다.":::

1. **SAS 및 연결 문자열 생성** 을 선택합니다. SAS 토큰을 즉시 복사합니다. 이 토큰을 나열할 수 없으므로 복사하지 않은 경우 새 SAS 토큰을 생성해야 합니다. 

## <a name="set-sas-token-in-code-file"></a>코드 파일에서 SAS 토큰 설정

SAS 토큰 값은 부분 쿼리 문자열이며, 클라우드 기반 리소스를 쿼리할 때 URL에 사용됩니다.

토큰 형식은 토큰을 만드는 데 사용한 도구에 따라 달라집니다. 
* **Azure 포털**: 포털에서 SAS 토큰을 만드는 경우 토큰에는 `?`가 문자열의 첫 번째 문자로 포함됩니다.
* **Azure CLI**: Azure CLI를 사용하여 SAS 토큰을 만드는 경우 반환되는 값에는 `?`가 문자열의 첫 번째 문자로 포함되지 않습니다. 

1. 토큰의 첫 번째 문자인 경우 `?`를 제거합니다. 코드 파일에서 `?`를 제공하므로 토큰에 필요하지 않습니다.

1. SAS 토큰을 빈 문자열에 추가하여 SAS 토큰을 sasToken 값에 대한 `src/uploadToBlob.ts`로 설정합니다. 나머지 코드는 그대로 둡니다. 

```typescript
// remove `?` if it is first character of token
const sasToken = process.env.storagesastoken || "";
```

## <a name="6-configure-cors-for-azure-storage-resource"></a>6. Azure Storage 리소스에 대한 CORS 구성

클라이언트 쪽 React 코드에서 스토리지 계정에 액세스할 수 있도록 리소스에 대한 CORS를 구성합니다. 

1. 여전히 Azure Portal의 [설정] 섹션에서 **CORS** 를 선택합니다. 
1. CORS를 이미지와 같이 구성합니다. 설정은 이미지 아래에 설명되어 있습니다. 

    | 속성|값|
    |--|--|
    |허용된 원본|`*`|
    |허용된 메서드|패치를 제외한 모든 메서드|
    |허용된 헤더|`*`|
    |노출된 헤더|`*`|
    |최대 기간|86400|

    :::image type="content" source="../media/tutorial-browser-file-upload/azure-portal-storage-blob-cors.png" alt-text="CORS를 이미지와 같이 구성합니다. 설정은 이미지 아래에 설명되어 있습니다.":::

1. 설정 위쪽에서 **저장** 을 선택하여 리소스에 저장합니다. 이러한 CORS 설정을 사용하기 위해 코드를 변경할 필요가 없습니다. 

## <a name="7-run-project-locally-to-verify-connection-to-storage-account"></a>7. 프로젝트를 로컬로 실행하여 Storage 계정에 대한 연결 확인

SAS 토큰 및 스토리지 계정 이름이 `src/uploadToBlob.ts` 파일에 설정되어 있으므로 애플리케이션을 실행할 준비가 되었습니다.

1. Visual Studio Code 터미널에서 다음 명령을 입력합니다.

    ```javascript
    npm start
    ```

1. 터미널에서 `http://localhost:3000`과 같은 URL을 표시하면 앱이 준비된 것입니다. 브라우저를 열고 해당 URL을 입력합니다. Azure Storage Blob에 연결된 웹 사이트가 파일 선택 단추 및 파일 업로드 단추와 함께 표시됩니다. 

    :::image type="content" source="../media/tutorial-browser-file-upload/browser-react-app-azure-storage-resource-configured-upload-button-displayed.png" alt-text="Azure Storage Blob에 연결된 React 웹 사이트가 파일 선택 단추 및 파일 업로드 단추와 함께 표시됩니다.":::

1. `images` 폴더에서 업로드할 이미지를 선택합니다. `spring-flowers.jpg`는 이 테스트에 적합한 시각적 개체입니다. **업로드!** 단추를 선택합니다. 클릭합니다. 

    React 프런트 엔드 클라이언트 코드에서 `src/uploadToBlob.ts`를 호출하여 Azure에 인증한 다음, Storage 컨테이너를 만들고(아직 없는 경우) 파일을 이 컨테이너에 업로드합니다. 

## <a name="troubleshoot-local-connection-to-storage-account"></a>Storage 계정에 대한 로컬 연결 문제 해결

오류가 발생했거나 파일이 컨테이너에 업로드되지 않으면 다음 사항을 확인합니다.

* SAS 토큰을 다시 만들고, 컨테이너 수준이 아닌 Storage 리소스 수준에서 토큰이 생성되었는지 확인합니다. 새 토큰을 올바른 위치의 코드에 복사합니다.
* 코드에 복사한 토큰 문자열의 시작 부분에 `?`(물음표)가 포함되지 않도록 확인합니다.
* Storage 리소스의 CORS 설정을 확인합니다.

## <a name="upload-button-functionality"></a>업로드 단추 기능

`src/app` 파일은 create-react-app을 사용하여 해당 앱을 만드는 과정의 일부로 제공됩니다. 파일 선택 단추, 업로드 단추 및 해당 기능을 제공하는 지원 코드를 제공하도록 파일이 수정되었습니다. 

Azure Blob 스토리지 코드에 연결하는 코드가 강조 표시됩니다. `uploadFileToBlob`에 대한 호출에서 컨테이너의 모든 Blob(파일)을 단순 목록으로 반환합니다. 이 목록은 `DisplayImagesFromContainer` 함수를 통해 표시됩니다.

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/App.tsx" highlight="3,28":::

## <a name="upload-file-to-azure-storage-blob-with-azure-sdk-client-library"></a>Azure SDK 클라이언트 라이브러리를 사용하여 Azure Storage Blob에 파일 업로드

파일을 Azure Storage에 업로드하는 코드는 프레임워크와 관련이 없습니다. 편의상 이해를 돕기 위해 자습서용으로 작성된 코드가 선택되었습니다. 이러한 선택 사항이 설명되어 있으며, 의도적인 사용, 보안 및 효율성에 대해 사용자 고유의 프로젝트를 검토해야 합니다. 

이 샘플에서는 공개적으로 액세스할 수 있는 컨테이너 및 파일을 만들고 사용합니다. 사용자 고유의 프로젝트에서 파일을 보호하려면 전체 인증에서 리소스에 대한 전체 인증 요구에서 각 Blob 개체에 대한 구체적인 권한에 이르기까지 이를 제어할 수 있는 많은 계층이 있습니다. 

### <a name="dependencies-and-variables"></a>종속성 및 변수

`uploadToBlob.ts` 파일은 종속성을 로드하고, 환경 변수 또는 하드 코딩된 문자열을 통해 필요한 변수를 가져옵니다.

| 변수 | 설명 |
|--|--|
|`sasToken`|Azure Portal을 사용하여 만든 SAS 토큰 앞에는 `?`가 붙습니다. `sasToken` 변수에 설정하기 전에 이를 제거합니다.| 
|`container`|Blob 스토리지에 있는 컨테이너의 이름입니다. 파일 시스템의 폴더 또는 디렉터리와 동일한 것으로 간주할 수 있습니다.|
|`storageAccountName`|리소스 이름입니다.|

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/uploadToBlob.ts" highlight="2,5,16" id="snippet_package":::

### <a name="security-for-azure-credentials"></a>Azure 자격 증명에 대한 보안

사용자 고유의 프로젝트에서 SAS 토큰과 같은 비밀을 저장할 위치를 고려합니다. 애플리케이션에서 Azure 정보를 보호해야 하는 경우 클라이언트 대신 [Azure Function](/azure/azure-functions/)에서 이 스토리지 코드를 호스팅한 다음, react 앱에서 Azure Function을 호출하는 것이 좋습니다.  

### <a name="create-storage-client-and-manage-steps"></a>Storage 클라이언트 만들기 및 단계 관리

`uploadFileToBlob` 함수는 파일의 기본 함수입니다. Storage 서비스에 대한 클라이언트 개체를 만든 다음, 클라이언트를 컨테이너 개체에 만들고, 파일을 업로드하고, 컨테이너의 모든 Blob 목록을 가져옵니다. 

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/uploadToBlob.ts" highlight="5,6,7" id="snippet_uploadFileToBlob":::

### <a name="upload-file-to-blob"></a>Blob에 파일 업로드

`createBlobInContainer` 함수는 `uploadBrowserData` 클라이언트 라이브러리 메서드를 사용하여 파일을 컨테이너에 업로드합니다. 그림 표시와 같은 파일 형식에 따라 달라지는 브라우저 기능을 사용하려는 경우 콘텐츠 형식을 요청과 함께 보내야 합니다. 

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/uploadToBlob.ts" highlight="10" id="snippet_createBlobInContainer":::

### <a name="get-list-of-blobs"></a>Blob 목록 가져오기

`getBlobsInContainer` 함수는 컨테이너의 Blob에 대한 URL 목록을 가져옵니다. URL은 HTML에서 이미지 표시(`<img src={item} alt={item} height="200" />`)의 `src`로 사용하도록 구성됩니다. 

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/uploadToBlob.ts" highlight="10" id="snippet_getBlobsInContainer":::

## <a name="clean-up-resources"></a>리소스 정리

Visual Studio Code에서 Storage에 대한 Azure 탐색기를 사용하여 마우스 오른쪽 단추로 리소스를 클릭한 다음, **스토리지 계정 삭제...** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 앱을 계속 사용하려면 다음 옵션 중 하나를 사용하여 호스트할 수 있도록 Azure에 앱을 배포하는 방법을 알아보세요.

* [정적 웹앱으로 업로드](/azure/static-web-apps/getting-started?tabs=vanilla-javascript)
* [App Service용 Visual Studio 코드 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)을 사용하여 웹앱 리소스에 업로드
* [Azure VM에 앱 업로드](nodejs-virtual-machine-vm/introduction.md)