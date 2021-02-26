---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/08/2021
ms.openlocfilehash: 693e78bef335871dbee846666f889ad1cf2982b1
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100005031"
---
## <a name="create-azure-database-with-visual-studio-code-extension"></a>Visual Studio Code 확장을 사용하여 Azure 데이터베이스 만들기

다음 유형의 리소스에 대해 이 절차를 사용합니다.

* Azure Cosmos DB for MongoDB API
* SQL
* Azure Table
* Gremlin
* [PostgreSQL](#create-a-postgresql-database) 

### <a name="create-a-postgresql-database"></a>PostgreSQL 데이터베이스 만들기

1. Visual Studio Code용 [Azure 데이터베이스](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb) 확장 설치
1. Visual Studio Code의 [작업 표시줄](https://code.visualstudio.com/docs/getstarted/userinterface)에서 **Azure** 를 선택하고 [사이드바](https://code.visualstudio.com/docs/getstarted/userinterface)에서 **데이터베이스** 를 선택합니다.
1. 구독 이름을 마우스 오른쪽 단추로 클릭한 다음, **서버 만들기** 를 선택합니다.
1. 목록에서 **PostgreSQL** 을 선택합니다. 

    :::image type="content" source="../media/howto-visual-studio-code/create-azure-database-server.png" alt-text="목록에서 `PostgreSQL`을 선택합니다.":::

1. PostgreSQL 서버의 이름을 입력합니다. 이 이름은 연결 문자열의 일부로 사용됩니다. 
1. 관리자 사용자 이름을 입력합니다. 
1. 관리자 암호를 입력한 다음, 다음 화면에서 확인을 위해 두 번 입력합니다. 
1. 현재 IP 주소를 선택하여 방화벽 규칙으로 추가합니다. 
1. 리소스 그룹 이름을 선택하거나 새로 만듭니다. 
1. 서버의 Azure 지역을 선택합니다. 
1. 알림 창에 상태가 표시됩니다. 

    :::image type="content" source="../media/howto-visual-studio-code/create-azure-database-server-status.png" alt-text="알림 창에 상태가 표시됩니다.":::