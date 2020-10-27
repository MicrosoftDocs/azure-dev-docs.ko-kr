---
title: tutorial-azure-web-app-mongodb-04.md 포함 파일
description: tutorial-azure-web-app-mongodb-04.md 포함 파일
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: 73e629427359a625ea3ec4796c2ec7000a21536a
ms.sourcegitcommit: 8a2a7df568c69fff2080ffab248409040efda1ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183941"
---
자습서의 이 섹션에서는 Visual Studio Code 확장 및 Docker 컨테이너를 사용하여 로컬 MongoDB 데이터베이스를 애플리케이션에 추가합니다.

## <a name="configure-visual-studio-code-to-run-containers"></a>컨테이너를 실행하도록 Visual Studio Code 구성

이 섹션에서는 두 개의 컨테이너, 즉 Node.js 프로젝트용 컨테이너와 MongoDB 컨테이너용 컨테이너를 실행하도록 개발 환경을 구성합니다. 이 섹션에서는 Visual Studio Code 개발 컨테이너를 사용하므로 컨테이너 구성은 **.devcontainer** 폴더에 저장됩니다. 팀의 다른 멤버가 로컬 MongoDB에 액세스할 수 있도록 이를 원본 제어에 커밋할 수 있습니다.  

1. Visual Studio Code에서 명령 팔레트(CTRL+Shift+P)를 사용하여 **원격 컨테이너: 개발 컨테이너 구성 파일 추가** 를 선택합니다. 

1. 목록에서 **Node.js 및 Mongo DB** 를 선택합니다.

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-configure-development-container.png" alt-text="Visual Studio Code의 명령 팔레트에 대한 부분 스크린샷"::: 

1. **\.devcontainer\devcontainer.json** 파일에서 **forwardPorts** 속성을 찾아서 주석 처리를 제거하고, `8080`을 배열에 추가합니다. 또한 셸을 사용하여 MongoDB 컨테이너에 액세스할 수 있는 액세스 권한이 필요한 경우 `27017` 포트를 배열에 추가합니다.  

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-dev-container-configuration-forward-ports.png" alt-text="Visual Studio Code의 명령 팔레트에 대한 부분 스크린샷"::: 

## <a name="run-web-app-locally-with-database"></a>데이터베이스를 사용하여 로컬로 웹앱 실행

이 섹션에서는 두 컨테이너를 모두 사용하여 개발 환경을 실행하고 웹 사이트를 봅니다. 

1. Visual Studio Code의 왼쪽 아래 모서리에서 녹색 **원격 컨테이너** 아이콘을 선택합니다. 그러면 명령 팔레트가 열립니다. 

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-remote-container-icon.png" alt-text="Visual Studio Code의 명령 팔레트에 대한 부분 스크린샷"::: 

1. 명령 팔레트에서 **원격 컨테이너: 컨테이너에서 다시 열기** 를 선택합니다. 컨테이너를 사용하여 프로젝트를 처음 열면 Node.js 및 MongoDB 이미지를 끌어와서 시작합니다. 몇 분이 걸릴 수 있습니다. 

    컨테이너가 실행되면 Visual Studio Code 터미널에 Node.js 컨테이너의 터미널이 표시됩니다. 

    필요에 따라 `ls` 명령을 사용하여 파일을 볼 수 있습니다. 파일에서 로컬 컴퓨터와 공유된 볼륨을 사용하고 있는지 확인합니다. Node.js 컨테이너 내에서 코드 파일에 대한 변경 내용은 로컬 파일에 저장됩니다.

1. 다음 명령을 사용하여 터미널에서 프로젝트를 시작합니다.

    ```console
    npm start
    ```

1. 로컬 웹앱 URL을 사용하여 브라우저를 엽니다.

    ```
    http://localhost:8080/
    ```

1. 필드에 데이터를 입력하고, 양식을 제출합니다. 데이터는 서버 쪽 React 렌더링을 사용하여 표시됩니다. 

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/nodejs-app-connected-mongodb-form.png" alt-text="Visual Studio Code의 명령 팔레트에 대한 부분 스크린샷":::

1. 앱 검색이 완료되면 명령 팔레트에서 **원격 컨테이너: 로컬로 다시 열기...** 를 선택하여 컨테이너를 중지합니다. 그러면 컨테이너가 중지되지만 로컬 컴퓨터에는 그대로 남아 있습니다. 

## <a name="want-to-know-more"></a>자세히 알고 싶으세요? 

데이터를 보려면 Visual Studio Code 확장( **[VS Code용 MongoDB](https://marketplace.visualstudio.com/items?itemName=mongodb.mongodb-vscode)** )을 사용하여 MongoDB 컨테이너에 연결합니다.

**mongo** 셸을 사용하려면 새 Visual Studio Code 창을 열고 **원격 컨테이너: 실행 중인 컨테이너에 연결...** 을 사용하여 Visual Studio Code 터미널에서 MongoDB 컨테이너에 연결한 다음, `-db`로 끝나는 컨테이너를 선택합니다. 창이 컨테이너에 연결되면 Visual Studio Code 터미널을 엽니다. 다음 명령을 사용하여 Mongo 셸에 즉시 액세스할 수 있습니다.

```console
mongo
```

컨테이너를 정리하려면 **[Docker](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker)** Visual Studio Code 확장을 사용합니다.