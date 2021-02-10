---
title: VSCode를 사용하여 GitHub 리포지토리 복제
description: Visual Studio Code를 사용하여 GitHub에서 로컬 컴퓨터로 퍼블릭 리포지토리를 복제합니다.
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: devx-track-js
ms.openlocfilehash: f3bf194f7520779b92d4cfb5966eedb0f599edea
ms.sourcegitcommit: 3f8aa923e4626b31cc533584fe3b66940d384351
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99224807"
---
# <a name="clone-and-use-a-github-repository-in-visual-studio-code"></a>Visual Studio Code에서 GitHub 리포지토리 복제 및 사용

Visual Studio Code를 사용하여 GitHub에서 로컬 컴퓨터로 퍼블릭 리포지토리를 복제하는 단계를 알아봅니다.

리포지토리를 통해 Visual Studio Code에서 작업하려면 별도의 도구를 사용합니다.

|아이콘|정보|[액세스 위치](https://code.visualstudio.com/docs/getstarted/userinterface)|
|--|--|--|
|| [Git CLI](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette)|명령 팔레트 - F1|
|:::image type="content" source="../../media/how-to-clone-github-repo/git-commit-icon-activity-bar.png" alt-text="소스 제어 아이콘.":::|소스 제어 확장|작업 표시줄|
|:::image type="content" source="../../media/how-to-clone-github-repo/github-icon-activity-bar.png" alt-text="GitHub Prs 및 문제 아이콘":::|GitHub 확장|작업 표시줄|

다음 절차에서는 [Visual Studio Code 사용자 인터페이스](https://code.visualstudio.com/docs/getstarted/userinterface)의 명명된 부분을 사용합니다. 

## <a name="use-command-palette-to-clone-repository"></a>명령 팔레트를 사용하여 리포지토리 복제

시작하려면 다음 단계를 사용하여 샘플 프로젝트를 다운로드합니다.

1. **F1** 키를 눌러 명령 팔레트를 표시합니다.

1. 명령 팔레트 프롬프트에 `gitcl`을 입력하고 **Git: Clone**(Git: 복제) 명령을 선택하고 **Enter** 키를 누릅니다.

    ![Visual Studio Code 명령 팔레트 프롬프트의 gitcl 명령](../../media/how-to-clone-github-repo/visual-studio-code-git-clone.png)

1. **리포지토리 URL** 에 대한 프롬프트가 표시되면 GitHub 리포지토리 URL을 입력한 다음, **Enter** 를 누릅니다.

1. 프로젝트를 복제할 로컬 디렉터리를 선택하거나 만듭니다.

    ![Visual Studio Code 탐색기](../../media/how-to-clone-github-repo/visual-studio-code-explorer.png)

## <a name="create-a-branch-for-changes-with-git-cl"></a>Git CL을 사용하여 변경 내용에 대한 분기 만들기

명령 팔레트에서 Git을 사용하여 새 분기를 만듭니다.

1. **F1** 키를 눌러 명령 팔레트를 표시합니다.
1. `git branch`를 검색하고 `Git: Create Branch`를 선택합니다.

    :::image type="content" source="../../media/how-to-clone-github-repo/git-cli-branch-list.png" alt-text="`git 분기`를 검색하고 `Git: 분기 만들기`":::를 선택합니다.

1. 새 분기 이름을 입력합니다. 분기 이름이 상태 표시줄에 표시됩니다. 

    :::image type="content" source="../../media/how-to-clone-github-repo/git-branch-status-bar-visual-studio-code.png" alt-text="분기 이름이 상태 표시줄에 표시됩니다.":::

## <a name="create-a-branch-from-status-bar"></a>상태 표시줄에서 분기 만들기

1. 상태 표시줄에서 분기 이름을 선택합니다. 

    상태 표시줄은 일반적으로 Visual Studio Code의 아래쪽에 있습니다. 

1. 명령 팔레트에서 **++새 분기 만들기** 를 선택합니다.
1. 새 분기 이름을 입력합니다. 

1. 새 분기 이름을 입력합니다. 분기 이름이 상태 표시줄에 표시됩니다. 

    :::image type="content" source="../../media/how-to-clone-github-repo/git-branch-status-bar-visual-studio-code.png" alt-text="분기 이름이 상태 표시줄에 표시됩니다.":::

## <a name="commit-changes-with-git"></a>Git으로 변경 내용 커밋 

분기에서 변경한 후 변경 내용을 커밋합니다.

1. 활동 표시줄로 전환하고 Git 아이콘을 선택합니다.

1. **메시지** 상자에 커밋 메시지를 입력하고 **Ctrl**+**Enter** 를 누르거나 소스 제어 막대에서 확인 표시를 선택합니다.

    ![Git에 yarn.lock 파일 추가](../../media/how-to-clone-github-repo/visual-studio-code-add-yarn-lock.png)

## <a name="push-a-local-branch-to-remote-from-status-bar"></a>상태 표시줄에서 원격으로 로컬 분기 푸시

1. 분기 이름 오른쪽에 있는 푸시 아이콘을 선택합니다. 
1. 팝업 상자에서 원격 이름을 선택합니다. 하나의 원격만 있는 경우 원격 이름을 선택하라는 메시지가 표시되지 않습니다. 

## <a name="push-a-local-branch-to-remote-from-the-source-control-extension"></a>소스 제어 확장에서 원격으로 로컬 분기 푸시
1. 활동 표시줄에서 소스 제어 아이콘을 선택합니다. 
1. 줄임표(...)를 선택하고 **끌어오기, 밀어넣기** 를 선택한 다음, **밀어넣기 대상...** 을 선택합니다. 
1. 팝업 상자에서 원격 이름을 선택합니다. 하나의 원격만 있는 경우 원격 이름을 선택하라는 메시지가 표시되지 않습니다. 

## <a name="next-steps"></a>다음 단계

* [웹앱을 배포](../deploy-web-app.md)하는 방법
