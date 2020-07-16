---
title: Eclipse에서 Javadoc 콘텐츠 표시
titleSuffix: Azure Libraries for Java
description: Eclipse에서 Azure 라이브러리의 Javadoc 콘텐츠를 표시하는 방법입니다.
documentationcenter: java
ms.assetid: 30f8b6a1-1d76-4d1c-861b-1db478c46e6b
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: cada923ab2c583d3c1b94c6397c09ea1ad760ac9
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86378237"
---
# <a name="displaying-javadoc-content-in-eclipse-for-the-azure-libraries-package-for-java"></a>Eclipse에서 Java용 Azure 라이브러리 패키지의 Javadoc 콘텐츠 표시

Javadoc 콘텐츠를 Java용 Azure 라이브러리 패키지에 연결하면 Java용 Azure 라이브러리 패키지의 Javadoc 콘텐츠를 Eclipse 환경 내에서 볼 수 있습니다. 다음 단계는 Eclipse 내에서 이 기능을 사용하는 방법을 보여 줍니다.

이 절차는 빌드 경로에 Java용 Azure 라이브러리를 이미 추가했다고 가정합니다.

## <a name="to-display-javadoc-content-in-eclipse-for-the-azure-libraries-for-java"></a>Eclipse에서 Java용 Azure 라이브러리의 Javadoc 콘텐츠를 표시하려면

1. Eclipse의 프로젝트 탐색기에 있는 프로젝트 **참조 라이브러리** 섹션에서 Java JAR용 Azure 라이브러리의 상황에 맞는 메뉴를 엽니다. 예를 들어 **microsoft windowsazure-api 0.1.0.jar** (버전 번호는 설치한 버전에 따라 다를 수 있음)입니다.

1. **속성**을 클릭합니다.

1. **속성** 대화 상자의 왼쪽 창에서 **Javadoc 위치**를 클릭합니다. **Javadoc Location** (Javadoc 위치) 대화 상자가 표시됩니다.

1. **Javadoc URL** 또는 **보관 중인 Javadoc**을 지정할 수 있습니다.

   * **Javadoc URL**을 지정하려면 `https://dl.windowsazure.com/javadoc` 또는 `https://dl.windowsazure.com/storage/javadoc`와 같은 URL을 사용합니다.

   * **Javadoc in archive**(보관 중인 Javadoc)를 사용하도록 선택하는 경우 외부 파일 또는 작업 영역 파일을 지정할 수 있습니다.

   필요에 따라 선택하여 찾거나 유효성을 검사합니다. 다음 예제에서는 Java용 Azure 라이브러리를 **c:\MyAzureJARs**라는 폴더에 로컬로 다운로드된 해당 Javadoc JAR와 연결합니다.

   ![Javadoc 콘텐츠를 표시합니다.][ic553487]

1. *선택적 단계*: **유효성 검사**를 클릭합니다. Javadoc JAR의 잠재적 문제가 여기에 표시될 수 있습니다.

1. **확인**을 클릭합니다.

라이브러리에 연결되면 Javadoc 콘텐츠가 Eclipse IDE 내에 표시됩니다. 예를 들어 `blob`이 코드에서 `CloudBlockBlob` 형식으로 정의되는 경우 다음은 코드에서 `blob.acquireLease`를 입력했을 때 표시되는 Javadoc 콘텐츠의 예입니다.

![Javadoc 콘텐츠를 표시합니다.][ic553488]

## <a name="next-steps"></a>다음 단계

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->

<!-- IMG List -->

[ic553487]: media/displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: media/displaying-javadoc-content-for-azure-libraries/ic553488.png
