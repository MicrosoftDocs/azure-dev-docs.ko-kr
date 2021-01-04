---
title: Computer Vision을 사용하는 React 코드
description: 이 샘플은 React 앱에 Computer Vision을 추가하는 데 필요한 모든 코드를 포함하고 있습니다. 자습서의 이 섹션에서는 단계와 코드를 _검토_ 합니다.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 44999c92ef2a938327a379d0d4993b000466fb3f
ms.sourcegitcommit: 525c4b41d85aae9c3026a070b07e00c2241ea716
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97394067"
---
# <a name="5-review-how-to-add-computer-vision-to-the-react-app"></a>5. React 앱에 Computer Vision을 추가하는 방법 검토

이 샘플은 React 앱에 Computer Vision을 추가하는 데 필요한 모든 TypeScript 코드를 포함하고 있습니다. 자습서의 이 섹션에서는 단계와 코드를 _검토_ 합니다. 이 자습서에서는 이러한 단계를 수행할 필요가 없습니다. 

## <a name="add-computer-vision-to-local-react-app"></a>로컬 React 앱에 Computer Vision 추가

npm을 사용하여 package.json 파일에 Computer Vision을 추가합니다. 

```bash
npm install @azure/cognitiveservices-computervision 
```

## <a name="add-computer-vision-code-as-separate-module"></a>Computer Vision 코드를 별도의 모듈로 추가

Computer Vision 코드는 `./src/VisualAI.js`라는 별도의 파일에 포함되어 있습니다. 모듈의 main 함수가 강조 표시됩니다. 

:::code language="javascript" source="~/../js-e2e-client-cognitive-services/src/VisualAI.js" highlight="55-75" :::

## <a name="add-catalog-of-images-as-separate-module"></a>이미지 카탈로그를 별도의 모듈로 추가

사용자가 이미지 URL을 입력하지 않으면 앱은 카탈로그에서 임의의 이미지를 선택합니다. 임의 선택 함수가 강조 표시됩니다. 

:::code language="javascript" source="~/../js-e2e-client-cognitive-services/src/DefaultImages.js" highlight="33-35" :::

## <a name="add-custom-computer-vision-module-to-react-app"></a>React 앱에 사용자 지정 Computer Vision 모듈 추가

React `app.js`에 메서드를 추가합니다. 이미지 분석 및 결과가 강조 표시됩니다.

:::code language="javascript" source="~/../js-e2e-client-cognitive-services/src/App.js" highlight="20-25, 29-42" :::

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [리소스 정리](clean-up-resources.md) 