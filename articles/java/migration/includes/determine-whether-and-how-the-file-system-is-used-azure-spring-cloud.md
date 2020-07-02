---
author: yevster
ms.author: yebronsh
ms.date: 5/26/2020
ms.openlocfilehash: 30d39530700806dc910c085c36a7834efa9c1414
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507610"
---
#### <a name="determine-whether-and-how-the-file-system-is-used"></a>파일 시스템의 사용 여부 및 방법 확인

서비스가 로컬 파일 시스템에서 쓰거나 읽는 인스턴스를 찾습니다. 단기/임시 파일을 쓰고 읽는 위치와 수명이 긴 파일을 쓰고 읽는 위치를 확인합니다.

> [!NOTE]
> Azure Spring Cloud는 `/tmp`에 탑재된 Azure Spring Cloud 인스턴스당 5GB의 임시 스토리지를 제공합니다. 임시 파일이 이 제한을 초과하거나 다른 위치에 쓰이는 경우 코드를 변경해야 합니다.

<!-- The following two "static content" sections should be identical to the contents of includes\static-content.md except that here we use H5 headings. -->

##### <a name="read-only-static-content"></a>읽기 전용 정적 콘텐츠

애플리케이션에서 현재 정적 콘텐츠를 제공하는 경우 이를 대체할 위치가 필요합니다. 정적 콘텐츠를 Azure Blob Storage로 이동하고 전역적으로 빠른 다운로드를 위해 Azure CDN을 추가하는 것이 좋습니다. 자세한 내용은 [Azure Storage에서 정적 웹 사이트 호스팅](/azure/storage/blobs/storage-blob-static-website) 및 [빠른 시작: Azure CDN과 Azure Storage 계정 통합](/azure/cdn/cdn-create-a-storage-account-with-cdn)을 참조하세요.

##### <a name="dynamically-published-static-content"></a>동적으로 게시된 정적 콘텐츠

애플리케이션이 애플리케이션에서 업로드/생성되었지만 생성 후 변경할 수 없는 정적 콘텐츠를 허용하는 경우, 위에서 설명한 대로 Azure Blob Storage 및 Azure CDN를 사용하여 업로드 및 CDN 새로 고침을 처리할 수 있습니다. [Azure Functions를 사용하여 정적 콘텐츠 업로드 및 CDN 사전 로드](https://github.com/Azure-Samples/functions-java-push-static-contents-to-cdn)에서 사용할 샘플 구현을 제공했습니다.
