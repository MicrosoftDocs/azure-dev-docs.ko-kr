---
title: tutorial-azure-web-app-mongodb-05.md 포함 파일
description: tutorial-azure-web-app-mongodb-05.md 포함 파일
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: 9b8a718ec921050237f911ee4b5c3a47b6bbb45e
ms.sourcegitcommit: 8a2a7df568c69fff2080ffab248409040efda1ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183938"
---
자습서의 이 섹션에서는 클라우드 기반 데이터베이스를 만들고, 원격 앱을 연결하여 해당 클라우드 데이터베이스를 사용합니다. 

## <a name="create-a-cosmos-database"></a>Cosmos 데이터베이스 만들기

클라우드 기반 MongoDB 데이터베이스를 호스팅하는 Cosmos 리소스를 만듭니다. 

1. Visual Studio Code의 맨 왼쪽 메뉴에서 **Azure** 아이콘을 선택한 다음, **데이터베이스** 섹션을 선택합니다. 

    **데이터베이스** 섹션이 표시되지 않으면 위쪽 Azure **...** 메뉴에서 섹션을 선택했는지 확인합니다. 

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-azure-extension-select-database-section.png" alt-text="Visual Studio Code의 원격 컨테이너 아이콘에 대한 부분 스크린샷"::: 

1. Azure 탐색기의 **데이터베이스** 섹션에서 마우스 오른쪽 단추를 클릭하여 구독을 선택한 다음, **서버 만들기** 를 선택합니다.
1. **새 Azure 데이터베이스 서버 만들기** 명령 팔레트에서 **Azure Cosmos DB for MongoDB API** 를 선택합니다. 
1. 다음 표를 사용하여 표시되는 메시지에 따라 값을 사용하는 방법을 이해합니다. 데이터베이스를 만드는 데 최대 15분이 걸릴 수 있습니다.

    |속성|값|
    |--|--|
    |새 리소스에 대해 전역적으로 고유한 **계정 이름** 을 입력합니다.| 리소스에 대한 값(예: `cosmos-mongodb-YOUR-NAME`)을 입력합니다. `YOUR-NAME`을 사용자의 이름 또는 고유 ID로 바꿉니다. 이 고유한 이름은 브라우저에서 리소스에 액세스하기 위한 URL의 일부로도 사용됩니다.|
    |리소스 그룹을 선택하거나 만듭니다.|리소스 그룹을 만들어야 하는 경우 `westus-cosmostutorial-joesmith`와 같이 소유자, 용도 및 지역을 식별하는 명명 규칙을 사용합니다.|
    |위치|리소스의 위치입니다. 이 자습서에서는 지역적으로 가까운 위치를 선택합니다.|

    > [!CAUTION]
    > 리소스를 만드는 데 최대 15분이 걸릴 수 있습니다.     

1. 탐색기에서 새로 만든 Cosmos 리소스를 확인합니다. 아직 데이터베이스가 없습니다. 
1. Azure 데이터베이스 탐색기에서 마우스 오른쪽 단추로 리소스 이름을 클릭하고 **연결 문자열 복사** 를 선택하여 연결 문자열을 복사합니다. 이 정보는 자습서의 뒷부분에서 필요합니다.

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-databases-extenion-copy-connection-string.png" alt-text="Visual Studio Code의 원격 컨테이너 아이콘에 대한 부분 스크린샷":::

## <a name="optional-use-new-cloud-database-in-local-environment"></a>선택 사항: 로컬 환경에서 새 클라우드 데이터베이스 사용

새 클라우드 데이터베이스를 사용하려면 새 연결 문자열을 사용하도록 로컬 애플리케이션을 변경해야 합니다. 

1. Visual Studio Code에서 `.env` 파일을 열고, **DATABASE_URL** 값을 새 연결 문자열에 추가합니다. 
1. 웹앱을 처음 실행할 때 데이터베이스를 만들 수 있도록 `&retrywrites=false`를 연결 문자열 끝에 추가합니다. 
1. 개발 컨테이너를 사용하지 않고 웹앱을 로컬로 실행하여 해당 앱이 클라우드 데이터베이스에 연결되도록 합니다. 

## <a name="use-new-cloud-database-in-remote-web-app"></a>원격 웹앱에서 새 클라우드 데이터베이스 사용

데이터베이스에 대한 연결은 `DATABASE_URL`이라는 환경 변수로 설정됩니다. 해당 환경 변수를 사용하도록 원격 웹앱을 구성하려면 원격 웹앱에서 해당 변수를 만들어야 합니다. 

1. Visual Studio Code의 Azure 앱 서비스 탐색기에서 웹앱 서비스 노드를 선택하여 펼칩니다.
1.  마우스 오른쪽 단추로 **애플리케이션 설정** 노드를 클릭하고, Azure Cosmos DB for MongoDB에 대한 연결 문자열과 함께 `DATABASE_URL` 속성을 추가합니다. 웹앱을 처음 실행할 때 데이터베이스를 만들 수 있도록 `&retrywrites=false`를 연결 문자열 끝에 추가합니다. 

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-remote-web-app-application-settings.png" alt-text="Visual Studio Code의 원격 컨테이너 아이콘에 대한 부분 스크린샷"::: 

1. 브라우저에서 원격 웹 사이트를 열고, 양식을 사용하여 데이터를 추가하고 삭제합니다. 

## <a name="want-to-know-more"></a>자세히 알고 싶으세요? 

### <a name="mongodb-connection-strings"></a>MongoDB 연결 문자열
코드를 처음 실행할 때 데이터베이스를 만드는 경우 재시도가 필요할 수 있으므로 `&retrywrites=false` 설정이 연결 문자열에 있어야 합니다. 이 문제를 자세히 조사하려면 GitHub에서 이 [공개 문제 #1296](https://github.com/microsoft/vscode-cosmosdb/issues/1296)부터 시작하세요. 