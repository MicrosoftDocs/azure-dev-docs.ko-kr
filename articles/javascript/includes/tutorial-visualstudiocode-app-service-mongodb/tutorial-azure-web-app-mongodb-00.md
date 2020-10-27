---
title: tutorial-azure-web-app-mongodb-00.md 포함 파일
description: tutorial-azure-web-app-mongodb-00.md 포함 파일
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: c0d904935992b54b63f45f76a022947375414c55
ms.sourcegitcommit: 8a2a7df568c69fff2080ffab248409040efda1ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183914"
---
이 자습서에서는 MongoDB 네이티브 API를 사용하여 MongoDB 데이터베이스와 함께 Node.js 앱을 사용합니다. Node.js 애플리케이션을 Linux의 Azure App Service에 배포한 다음, 클라우드 기반 앱이 작동하는지 확인합니다. 

프로그래밍 작업은 완료되었습니다. 이 자습서에서는 Azure 확장을 사용하여 Visual Studio Code 내에서 로컬 및 원격 Azure 환경을 성공적으로 사용하는 데 중점을 둡니다.

## <a name="top-tasks"></a>상위 작업

이 자습서에는 JavaScript 개발자를 위한 몇 가지 **상위 Azure 작업** 이 포함되어 있습니다.

* 로컬 MongoDB 데이터베이스 사용
* 컨테이너를 통해 앱 사용
* 클라우드에 앱 배포
* 클라우드 호스팅 앱 설정 구성 
* 원격 데이터베이스에 로컬 앱 연결

## <a name="sample-application"></a>샘플 애플리케이션

GitHub에서 사용할 수 있는 [Node.js 앱 샘플](https://github.com/Azure-Samples/js-e2e-express-mongo)을 구성하는 요소는 다음과 같습니다.

* 8080 포트에서 호스팅되는 **Express.js 서버**
* 간단한 **React.js 서버 쪽 보기** 엔진
* **MongoDB 네이티브 API** 함수(데이터 삽입, 삭제 및 찾기)

:::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/nodejs-app-connected-mongodb-form.png" alt-text="MongoDB 데이터베이스에 연결된 간단한 Node.js 앱":::

## <a name="the-mongodb-connection"></a>MongoDB 연결

데이터베이스를 연결할 수 없는 경우 앱에서 `No database found.` 메시지를 표시합니다. 이 경우 앱의 초기 상태가 됩니다.

데이터베이스 연결이 수행되면 앱은 제출 단추가 있는 양식의 텍스트 필드 2개로 구성되며, 이 양식 아래에 Mongo 컬렉션의 콘텐츠가 표시됩니다.

## <a name="limited-time-to-work-on-the-tutorial"></a>자습서의 작업 시간이 제한되어 있나요?

이 자습서에서는 Azure에서 MongoDB 데이터베이스를 호스팅하는 방법인 Cosmos DB용 Azure 리소스를 만듭니다. 이 리소스 만들기 프로세스에는 최대 20분이 걸릴 수 있습니다. 시간이 제한된 경우 [지금 이 프로세스를 시작](../../tutorial-visualstudiocode-app-service-mongodb/tutorial-azure-web-app-mongodb-feedback.yml?tutorial-step=5)할 수 있으며, 필요한 경우 리소스를 사용할 수 있습니다. 

## <a name="want-to-know-more"></a>자세히 알고 싶으세요? 

자습서의 각 단계에는 **자세히 알고 싶으세요?** 섹션이 포함되어 있습니다. 이는 자세히 살펴볼 수 있도록 허용하는 _선택적 정보_ 입니다. 자습서를 진행하면서 참조하거나 나중에 자습서로 돌아갈 수 있습니다. 