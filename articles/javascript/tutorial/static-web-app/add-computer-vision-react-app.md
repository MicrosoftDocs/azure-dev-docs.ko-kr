---
title: Computer Vision을 사용하는 React 코드
description: 자습서의 이 섹션에서는 단계와 코드를 _검토_ 합니다. 이 자습서에서는 이러한 단계를 수행할 필요가 없습니다.
ms.topic: tutorial
ms.date: 12/17/2020
ms.custom: devx-track-js
ms.openlocfilehash: 84140472c4bb57e208cc0e2c0665e72680664a2f
ms.sourcegitcommit: 1c508f5ba73a12e4baeacc88ad9a8359301acb50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97687490"
---
# <a name="6-review-how-to-add-computer-vision-to-the-react-app"></a>6. React 앱에 Computer Vision을 추가하는 방법 검토

이 샘플은 React 앱에 Computer Vision을 추가하는 데 필요한 모든 TypeScript 코드를 포함하고 있습니다. 자습서의 이 섹션에서는 단계와 코드를 _검토_ 합니다. 이 자습서에서는 이러한 단계를 수행할 필요가 없습니다. 

* [예제 코드](https://github.com/Azure-Samples/js-e2e-client-cognitive-services)
* Azure 서비스
    * [정적 웹앱](https://docs.microsoft.com/azure/static-web-apps)
    * [Cognitive Services의 Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="add-computer-vision-to-local-react-app"></a>로컬 React 앱에 Computer Vision 추가

npm을 사용하여 package.json 파일에 Computer Vision을 추가합니다. 

```bash
npm install @azure/cognitiveservices-computervision 
```

## <a name="add-computer-vision-code-as-separate-module"></a>Computer Vision 코드를 별도의 모듈로 추가

Computer Vision 코드는 `./src/azure-cognitiveservices-computervision.js`라는 별도의 파일에 포함되어 있습니다. 모듈의 main 함수가 강조 표시됩니다. 

:::code language="javascript" source="~/../js-e2e-client-cognitive-services/src/azure-cognitiveservices-computervision.js" highlight="55-75" :::

## <a name="add-catalog-of-images-as-separate-module"></a>이미지 카탈로그를 별도의 모듈로 추가

사용자가 이미지 URL을 입력하지 않으면 앱은 카탈로그에서 임의의 이미지를 선택합니다. 임의 선택 함수가 강조 표시됩니다. 

:::code language="javascript" source="~/../js-e2e-client-cognitive-services/src/DefaultImages.js" highlight="33-35" :::

## <a name="add-custom-computer-vision-module-to-react-app"></a>React 앱에 사용자 지정 Computer Vision 모듈 추가

React `app.js`에 메서드를 추가합니다. 이미지 분석 및 결과가 강조 표시됩니다.

:::code language="javascript" source="~/../js-e2e-client-cognitive-services/src/App.js" highlight="20-25, 29-42" :::

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [리소스 정리](clean-up-resources.md) 