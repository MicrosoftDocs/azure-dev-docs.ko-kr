---
title: create-storage-resource.md 포함 파일
description: create-storage-resource.md 포함 파일
ms.date: 11/13/2020
ms.topic: include
ms.custom: devx-track-javascript, devx-track-azurecli
ms.openlocfilehash: 19a21dbf557c31f7eeae6afdb4722bfed35c86fe
ms.sourcegitcommit: dc74b60217abce66fe6cc93923e869e63ac86a8f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94885449"
---
자습서의 이 섹션에서는 Visual Studio 확장을 사용하여 Azure Storage 리소스를 만든 다음, Azure Portal에서 이 리소스를 구성합니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [azure-sign-in](azure-sign-in.md)]

## <a name="create-storage-resource"></a>Storage 리소스 만들기 

Visual Studio Code 확장을 사용하여 Storage 리소스를 만듭니다. 

1. Azure Storage 확장으로 이동합니다. 마우스 오른쪽 단추로 구독을 클릭한 다음, `Create Storage Account...`를 선택합니다.

    :::image type="content" source="../../media/tutorial-browser-file-upload/visualstudiocode-storage-extension-create-resource.png" alt-text="Azure Storage 확장으로 이동합니다. 마우스 오른쪽 단추로 구독을 클릭한 다음, '스토리지 계정 만들기...'를 선택합니다.":::

1. 다음 표를 사용하여 표시되는 메시지에 따라 값을 사용하는 방법을 이해합니다.

    |속성|값|
    |--|--|
    |전역적으로 고유한 새 웹앱의 이름을 입력합니다.| Storage 리소스 이름에 대한 값(예: `fileuploadyourname`)을 입력합니다. `yourname`을 소문자 이름 또는 고유 ID로 바꿉니다. 이 고유한 이름은 브라우저에서 리소스에 액세스하기 위한 URL의 일부로도 사용됩니다. 최대 24자의 문자와 숫자만 사용합니다. 나중에 사용하기 위해 이 **계정 이름** 이 필요합니다.|

1. 앱 만들기 프로세스가 완료되면 새 리소스에 대한 정보가 포함된 알림이 표시됩니다. 

    :::image type="content" source="../../media/tutorial-browser-file-upload/visualstudiocode-storage-extension-create-resource-complete.png" alt-text="앱 만들기 프로세스가 완료되면 새 리소스에 대한 정보가 포함된 알림이 표시됩니다.":::

## <a name="set-storage-account-name-in-code-file"></a>코드 파일에서 스토리지 계정 이름 설정

스토리지 키 이름을 빈 문자열에 추가하여 `storageAccountName` 값에 대한 `src/uploadToBlob.ts`의 리소스 이름을 설정합니다. 나머지 코드는 그대로 둡니다. 

```typescript
const storageAccountName = process.env.storageresourcename || ""; 
```

## <a name="generate-your-shared-access-signature-sas-token"></a>SAS(공유 액세스 서명) 토큰 생성 

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

    :::image type="content" source="../../media/tutorial-browser-file-upload/azure-portal-storage-blob-generate-sas-token.png" alt-text="SAS 토큰을 이미지와 같이 구성합니다. 설정은 이미지 아래에 설명되어 있습니다.":::

1.  **SAS 및 연결 문자열 생성** 을 선택합니다. SAS 토큰을 즉시 복사합니다. 이 토큰을 나열할 수 없으므로 복사하지 않은 경우 새 SAS 토큰을 생성해야 합니다. 

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

## <a name="configure-your-azure-storage-resource-for-cors-with-azure-cli"></a>Azure CLI를 사용하여 CORS에 대한 Azure Storage 리소스 구성

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

    :::image type="content" source="../../media/tutorial-browser-file-upload/azure-portal-storage-blob-cors.png" alt-text="CORS를 이미지와 같이 구성합니다. 설정은 이미지 아래에 설명되어 있습니다.":::

1. 설정 위쪽에서 **저장** 을 선택하여 리소스에 저장합니다. 이러한 CORS 설정을 사용하기 위해 코드를 변경할 필요가 없습니다. 

## <a name="run-project-locally-to-verify-connection-to-storage-account"></a>프로젝트를 로컬로 실행하여 Storage 계정에 대한 연결 확인

SAS 토큰 및 스토리지 계정 이름이 `src/uploadToBlob.ts` 파일에 설정되어 있으므로 애플리케이션을 실행할 준비가 되었습니다.

1. Visual Studio Code 터미널에서 다음 명령을 입력합니다.

    ```javascript
    npm start
    ```

1. 터미널에서 `http://localhost:3000`과 같은 URL을 표시하면 앱이 준비된 것입니다. 브라우저를 열고 해당 URL을 입력합니다. Azure Storage Blob에 연결된 웹 사이트가 파일 선택 단추 및 파일 업로드 단추와 함께 표시됩니다. 

    :::image type="content" source="../../media/tutorial-browser-file-upload/browser-react-app-azure-storage-resource-configured-upload-button-displayed.png" alt-text="Azure Storage Blob에 연결된 React 웹 사이트가 파일 선택 단추 및 파일 업로드 단추와 함께 표시됩니다.":::

1. `images` 폴더에서 업로드할 이미지를 선택합니다. `spring-flowers.jpg`는 이 테스트에 적합한 시각적 개체입니다. **업로드!** 단추를 선택합니다. 클릭합니다. 

    React 프런트 엔드 클라이언트 코드에서 `src/uploadToBlob.ts`를 호출하여 Azure에 인증한 다음, Storage 컨테이너를 만들고(아직 없는 경우) 파일을 이 컨테이너에 업로드합니다. 

## <a name="troubleshooting"></a>문제 해결

오류가 발생했거나 파일이 컨테이너에 업로드되지 않으면 다음 사항을 확인합니다.

* SAS 토큰을 다시 만들고, 컨테이너 수준이 아닌 Storage 리소스 수준에서 토큰이 생성되었는지 확인합니다. 새 토큰을 올바른 위치의 코드에 복사합니다.
* 코드에 복사한 토큰 문자열의 시작 부분에 `?`(물음표)가 포함되지 않도록 확인합니다.
* Storage 리소스의 CORS 설정을 확인합니다.

## <a name="want-to-know-more"></a>자세히 알고 싶으세요? 

Storage 계정을 구성하는 다른 방법은 다음과 같습니다.
* [PowerShell을 사용하는 SAS 토큰](/powershell/module/azure.storage/new-azurestorageblobsastoken)
* 포털을 사용하는 SAS 토큰
* [PowerShell을 사용하는 CORS](/powershell/module/azure.storage/set-azurestoragecorsrule)
* 포털을 사용하는 CORS

[공유 액세스 서명](/azure/storage/common/storage-sas-overview)에 대해 자세히 알아보세요.
