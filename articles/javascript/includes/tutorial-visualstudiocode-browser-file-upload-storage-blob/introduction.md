---
title: tutorial-azure-web-app-mongodb-00.md 포함 파일
description: tutorial-azure-web-app-mongodb-00.md 포함 파일
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: b9ab409c845b7690474eb47117df4401ecaf59cd
ms.sourcegitcommit: ced8331ba36b28e6e2eacd23a64b39ddc7ffe6ab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92344233"
---
이 자습서에서는 React 앱을 사용하여 파일을 Azure Storage Blob에 업로드합니다. 

프로그래밍 작업은 완료되었습니다. 이 자습서에서는 Azure 확장을 사용하여 Visual Studio Code 내에서 로컬 및 원격 Azure 환경을 성공적으로 사용하는 데 중점을 둡니다.

## <a name="top-tasks"></a>상위 작업

이 자습서에는 JavaScript 개발자를 위한 몇 가지 **상위 Azure 작업** 이 포함되어 있습니다.

* Visual Studio Code를 사용하여 로컬로 React 앱 실행
* Storage 리소스 만들기 및 파일 업로드 구성
    * CORS 구성
    * SAS(공유 액세스 서명) 토큰 만들기
* SAS 토큰을 사용하여 서비스를 인증하도록 Azure SDK 클라이언트 라이브러리에 대한 코드 구성

## <a name="sample-application"></a>샘플 애플리케이션

[GitHub에서 사용할 수 있는](https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob) Resact 앱 샘플을 구성하는 요소는 다음과 같습니다.

* 3000 포트에서 호스팅되는 **React 앱**
* Storage Blob에 업로드할 Azure SDK 클라이언트 라이브러리 스크립트

:::image type="content" source="../../media/tutorial-browser-file-upload/browser-react-app-azure-storage-resource-image-uploaded-displayed.png" alt-text="Azure Storage Blob에 연결된 간단한 React 앱":::
