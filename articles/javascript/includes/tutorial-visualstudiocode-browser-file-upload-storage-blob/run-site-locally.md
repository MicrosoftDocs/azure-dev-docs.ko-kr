---
ms.topic: include
ms.date: 10/13/2020
ms.custom: devx-track-javascript
title: run-site-locally.md 포함 파일
description: run-site-locally.md 포함 파일
ms.openlocfilehash: 129546c7f6a845c335405c6fc615f907848138f6
ms.sourcegitcommit: ced8331ba36b28e6e2eacd23a64b39ddc7ffe6ab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92344209"
---
자습서의 이 섹션에서는 샘플 애플리케이션을 로컬 컴퓨터에 다운로드하고 Visual Studio Code 터미널에서 실행합니다. 그런 다음, 브라우저에서 로컬로 실행되는 앱을 확인합니다.

## <a name="clone-and-run-the-initial-react-app"></a>초기 React 앱 복제 및 실행

전체 React 앱이 제공됩니다. 이 절차에서는 앱을 복제하고, 종속성을 설치하고, 앱을 실행합니다. 초기 앱이 코드에서 구성된 경우 Azure Storage에 연결하려고 시도하거나 사용할 수 없는 경우 `Storage is not configured`라는 메시지를 표시합니다. 

1. Visual Studio Code를 엽니다.
1. Git 아이콘, **리포지토리 복제** 를 차례로 선택하여 GitHub 리포지토리를 복제합니다. 이 프로세스를 수행하려면 GitHub에 로그인해야 합니다. `https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob`의 리포지토리 URL을 사용한 다음, 샘플을 복제할 로컬 컴퓨터의 폴더를 선택합니다. 메시지가 표시되면 복제된 리포지토리를 엽니다. 

    :::image type="content" source="../../media/tutorial-browser-file-upload/vscode-git-clone-repository.png" alt-text="Git 아이콘, '리포지토리 복제'를 차례로 선택하여 GitHub 리포지토리를 복제합니다.":::

1. Visual Studio Code에서 터미널 창을 열고, 다음 명령을 실행하여 샘플의 종속성을 설치합니다.

    ```javascript
    npm install
    ```

1. 동일한 터미널 창에서 명령을 실행하여 웹앱을 실행합니다.

    ```javascript
    npm start
    ```

1. 웹 브라우저를 열고, 다음 URL을 사용하여 로컬 컴퓨터에서 웹앱을 확인합니다.

    ```url
    http://localhost:3000/
    ```

    Storage가 구성되지 않았다는 텍스트가 포함된 간단한 웹앱이 브라우저에 표시되면 자습서의 이 섹션을 성공적으로 수행한 것입니다.

    :::image type="content" source="../../media/tutorial-browser-file-upload/browser-react-app-no-azure-storage-resource-configured.png" alt-text="Git 아이콘, '리포지토리 복제'를 차례로 선택하여 GitHub 리포지토리를 복제합니다.":::

1. Visual Studio Code 터미널에서 Ctrl+C를 사용하여 코드를 중지합니다.
