---
ms.topic: include
ms.date: 10/13/2020
ms.custom: devx-track-javascript
title: understand-the-code.md 포함 파일
description: understand-the-code.md 포함 파일
ms.openlocfilehash: fd9d2eb2c6c8dd3a39d737cdcdf609b9cf04cf7e
ms.sourcegitcommit: ced8331ba36b28e6e2eacd23a64b39ddc7ffe6ab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92344241"
---
자습서의 이 섹션에서는 샘플의 코드를 이해하여 웹앱에서 파일을 업로드하도록 합니다.

## <a name="sample-created-with-create-react-app"></a>create-react-app을 사용하여 만든 샘플

[샘플](https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob)은 TypeScript 템플릿을 통해 [create-react-app](https://create-react-app.dev/docs/adding-typescript/)을 사용하여 만든 기본적인 React 앱입니다.

```typescript
npx create-react-app my-app --template typescript
```

create-react-app 프레임워크가 유용한 경우는 다음과 같습니다.
* 브라우저에서 Azure 클라이언트 라이브러리를 사용하기 위한 요구 사항인 파일 묶음을 자동으로 제공
* 변환(transpilation) 및 빌드 스크립트 제공 

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

