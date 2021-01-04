---
title: VSCode를 사용하여 Express.js/MongoDB 앱 배포 - App Service/CosmosDB
description: 이 자습서에서는 MongoDB 네이티브 API를 사용하여 MongoDB 데이터베이스와 함께 Node.js 앱을 사용합니다. Node.js 애플리케이션을 Linux의 Azure App Service에 배포한 다음, 호스트된 앱이 작동하는지 확인합니다.
ms.topic: tutorial
ms.date: 12/03/2020
ms.custom: scenarios:getting-started, languages:JavaScript, devx-track-javascript
ms.openlocfilehash: 1ddbe6a463f877e86a0c46da1ffb17c6a3094c0a
ms.sourcegitcommit: c8330128d5d6a71859933a890ecdf047cb950996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97522407"
---
# <a name="deploy-expressjs-mongodb-app-to-app-service-from-visual-studio-code"></a>Visual Studio Code에서 App Service에 Express.js MongoDB 앱 배포

MongoDB에 연결하는 Express.js 애플리케이션을 Linux의 Azure App Service와 CosmosDB에 배포합니다. 

프로그래밍 작업은 완료되었습니다. 이 자습서에서는 Azure 확장을 사용하여 Visual Studio Code 내에서 로컬 및 원격 Azure 환경을 성공적으로 사용하는 데 중점을 둡니다.

## <a name="top-tasks"></a>상위 작업

이 자습서에는 JavaScript 개발자를 위한 몇 가지 **상위 Azure 작업** 이 포함되어 있습니다.

* MongoDB 데이터베이스를 호스트하는 CosmosDB 리소스 만들기
* Express.js 앱을 호스트하는 앱 서비스 리소스 만들기
* 앱 서비스에 Express.js 앱 배포

## <a name="sample-application"></a>애플리케이션 예제

[샘플 Express.js 앱](https://github.com/Azure-Samples/js-e2e-express-mongo)은 다음 요소로 구성됩니다.

* 8080 포트에서 호스팅되는 **Express.js 서버**
* 간단한 **React.js 서버 쪽 보기** 엔진
* **MongoDB 네이티브 API** 함수(데이터 삽입, 삭제 및 찾기)

:::image type="content" source="../media/tutorial-end-to-end-app-cosmos/nodejs-app-connected-mongodb-form.png" alt-text="MongoDB 데이터베이스에 연결된 간단한 Node.js 앱":::

## <a name="create-or-use-existing-azure-subscription"></a>기존 Azure 구독 만들기 또는 사용 

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/)

## <a name="install-software"></a>소프트웨어 설치

- [Node.js 12(LTS) 및 npm](https://nodejs.org/en/download) - 로컬 컴퓨터에 설치된 Node.js 패키지 관리자입니다.
- [Visual Studio Code](https://code.visualstudio.com/)(로컬 컴퓨터에 설치됨) 
- Visual Studio Code 확장:
    - Visual Studio Code용 [Azure App Service 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)(Visual Studio Code 내에서 설치됨)
    - [Azure 데이터베이스](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)

## <a name="create-a-cosmosdb-database-resource-for-mongodb"></a>MongoDB용 CosmosDB 데이터베이스 리소스 만들기

만드는 데 몇 분 정도 걸리는 Cosmos 리소스부터 만듭니다. 

1. Visual Studio Code의 맨 왼쪽 메뉴에서 **Azure** 아이콘을 선택한 다음, **데이터베이스** 섹션을 선택합니다. 

    **데이터베이스** 섹션이 표시되지 않으면 위쪽 Azure **...** 메뉴에서 섹션을 선택했는지 확인합니다. 

    :::image type="content" source="../media/tutorial-end-to-end-app-cosmos/vscode-azure-extension-select-database-section.png" alt-text="Visual Studio Code의 원격 컨테이너 아이콘에 대한 부분 스크린샷"::: 

1. Azure 탐색기의 **데이터베이스** 섹션에서 마우스 오른쪽 단추를 클릭하여 구독을 선택한 다음, **서버 만들기** 를 선택합니다.
1. **새 Azure 데이터베이스 서버 만들기** 명령 팔레트에서 **Azure Cosmos DB for MongoDB API** 를 선택합니다. 
1. 다음 표를 사용하여 표시되는 메시지에 따라 값을 사용하는 방법을 이해합니다. 데이터베이스를 만드는 데 최대 15분이 걸릴 수 있습니다.

    |속성|값|
    |--|--|
    |새 리소스에 대해 전역적으로 고유한 **계정 이름** 을 입력합니다.| 리소스에 대한 값(예: `cosmos-mongodb-YOUR-NAME`)을 입력합니다. `YOUR-NAME`을 사용자의 이름 또는 고유 ID로 바꿉니다. 이 고유한 이름은 브라우저에서 리소스에 액세스하기 위한 URL의 일부로도 사용됩니다.|
    |리소스 그룹을 선택하거나 만듭니다.|리소스 그룹을 만들어야 하는 경우 `westus-cosmostutorial-joesmith`와 같이 소유자, 용도 및 지역을 식별하는 명명 규칙을 사용합니다.|
    |위치|리소스의 위치입니다. 이 자습서에서는 지역적으로 가까운 위치를 선택합니다.|

    리소스를 만드는 데 최대 15분이 걸릴 수 있습니다. 시간이 없다면 다음 섹션을 건너뛰어도 되지만, 나중에 돌아와서 다음 섹션을 꼭 완료하세요.

## <a name="get-cosmosdb-connection-string"></a>CosmosDB 연결 문자열 가져오기

Azure 데이터베이스 탐색기에서 마우스 오른쪽 단추로 리소스 이름을 클릭하고 **연결 문자열 복사** 를 선택하여 연결 문자열을 복사합니다. 자습서의 뒷부분에서 환경 변수 파일에 필요합니다.

:::image type="content" source="../media/tutorial-end-to-end-app-cosmos/vscode-databases-extenion-copy-connection-string.png" alt-text="로컬 MongoDB의 Express.js 웹앱 양식 및 데이터 결과":::

## <a name="download-and-run-the-sample-expressjs-app"></a>샘플 Express.js 앱 다운로드 및 실행

Express.js 웹앱이 제공됩니다. 이 앱을 다운로드하고, 종속성을 설치하고, 앱을 실행합니다. 

1. [압축된 GitHub 리포지토리를 로컬 컴퓨터에 다운로드](https://github.com/Azure-Samples/js-e2e-express-mongo.git)한 다음, 폴더를 펼칩니다. 
1. Visual Studio Code에서 폴더를 엽니다. 마우스 오른쪽 단추로 폴더를 클릭하여 **Code로 열기** 를 선택하거나 폴더 내에서 해당하는 CLI를 사용할 수 있습니다.

    ```bash
    code .
    ```

1. `.env` 환경 파일을 편집합니다. CosmosDB의 연결 문자열 속성을 `DATABASE_URL` 속성의 값으로 추가합니다. 

    ```bash
    ENVIRONMENT=development
    DATABASE_NAME=
    DATABASE_COLLECTION_NAME=
    DATABASE_URL=
    ```

1. Visual Studio Code에서 터미널 창을 열고, 다음 명령을 실행하여 샘플의 종속성을 설치하고 웹앱을 시작합니다.

    ```bash
    npm install && npm start
    ```

1. 로컬 컴퓨터에서 브라우저를 통해 웹앱을 봅니다.

    ```url
    http://localhost:8080/
    ```

    :::image type="content" source="../media/tutorial-end-to-end-app-cosmos/nodejs-app-connected-mongodb-form.png" alt-text="MongoDB 데이터베이스에 연결된 간단한 Node.js 앱":::

## <a name="create-web-app-resource-and-deploy-expressjs-app"></a>웹앱 리소스 만들기 및 Express.js 앱 배포

App Service용 Visual Studio Code 확장을 사용하여 앱 서비스 리소스를 만들고 Express.js 웹앱을 리소스에 배포합니다.

1. Azure 탐색기로 이동합니다. 마우스 오른쪽 단추로 구독을 클릭한 다음, `Create new web app...`을 선택합니다.

    :::image type="content" source="../media/tutorial-end-to-end-app-cosmos/create-web-app-with-extension.png" alt-text="Azure 앱 서비스 확장을 사용하여 웹앱을 만드는 Visual Studio Code의 부분 스크린샷입니다.":::

1. 다음 표를 사용하여 표시되는 메시지에 따라 값을 사용하는 방법을 이해합니다.

    |속성|값|
    |--|--|
    |전역적으로 고유한 새 웹앱의 이름을 입력합니다.| 앱 서비스 리소스에 대한 값(예: `web-app-with-mongodb-YOUR-NAME`)을 입력합니다. `<YOUR-NAME>`을 사용자의 이름 또는 고유 ID로 바꿉니다. 이 고유한 이름은 브라우저에서 리소스에 액세스하기 위한 URL의 일부로도 사용됩니다.|
    |Linux 앱에 대한 런타임을 선택합니다.|`Node 12 LTS`를 선택합니다.|

1. 앱 만들기 프로세스가 완료되면 `Deploy` 또는 `View output` 중 하나를 선택하여 Visual Studio Code의 오른쪽 아래 모서리에 상태 메시지가 표시됩니다. `Deploy`를 선택합니다.

    :::image type="content" source="../media/tutorial-end-to-end-app-cosmos/vscode-app-extension-create-web-app-deploy-web-app.png" alt-text="Azure 앱 서비스 확장을 사용하여 웹앱을 만든 직후 웹앱을 배포하는 Visual Studio Code의 부분 스크린샷입니다.":::

    상태 메시지가 더 이상 표시되지 않으면 Azure 탐색기를 선택하여 배포하고, 마우스 오른쪽 단추로 리소스 이름을 클릭한 다음, **웹앱에 배포...** 를 선택할 수 있습니다.

1. 배포 프로세스 중에 알림을 통해 **출력 창** 을 표시하도록 선택할 수 있습니다.  이렇게 하면 배포의 롤링 상태가 표시됩니다. 

1. 배포가 완료되면 알림이 표시됩니다. **로그 스트리밍** 을 선택하여 롤링 로그를 확인합니다. 

    :::image type="content" source="../media/tutorial-end-to-end-app-cosmos/vscode-app-service-deployed.png" alt-text="서비스가 배포되었습니다. '로그 스트리밍'입니다.":::

    :::image type="content" source="../media/tutorial-end-to-end-app-cosmos/vscode-app-service-stream-logs.png" alt-text="배포가 완료되면 '로그 스트리밍'을 선택할 수 있는 알림이 표시됩니다.":::    

1. 브라우저에서 웹 사이트를 열고, `YOUR-RESOURCE_NAME` 텍스트를 사용자 고유의 리소스 이름(예: `https://YOUR-RESOURCE_NAME.azurewebsites.net`)으로 바꿉니다.
1. 웹앱을 사용하여 항목을 추가하고 삭제합니다. 

## <a name="clean-up-resources"></a>리소스 정리 

이 자습서를 완료한 후에는 요금이 청구되지 않도록 만든 두 개의 리소스를 제거해야 합니다. 

1. Visual Studio Code에서 데이터베이스에 대한 Azure 탐색기를 사용하여 마우스 오른쪽 단추로 리소스를 클릭한 다음, **계정 삭제...** 를 선택합니다.
1. Visual Studio Code에서 App Service에 대한 Azure 탐색기를 사용하여 마우스 오른쪽 단추로 리소스를 클릭한 다음, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

App Service 및 CosmosDB에 대해 계속 학습합니다.
* [앱 설정 구성 방법 알아보기](../how-to/configure-web-app-settings.md)
* [Azure App Service용 Node.js 앱 구성](/azure/app-service/configure-language-nodejs?pivots=platform-linux)
* [SSH를 사용하여 연결](/azure/app-service/configure-linux-open-ssh-session)
* [CosmosDB로 데이터 마이그레이션](/azure/dms/tutorial-mongodb-cosmos-db?toc=/azure/cosmos-db/toc.json?toc=/azure/cosmos-db/toc.json)
