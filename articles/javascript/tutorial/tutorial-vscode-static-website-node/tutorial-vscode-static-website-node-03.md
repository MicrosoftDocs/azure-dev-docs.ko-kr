---
title: Visual Studio Code에서 정적 Node.js 웹 사이트에 대한 Azure Storage 계정 만들기
description: '정적 웹앱 자습서 3부: Azure Storage 계정 만들기'
ms.topic: tutorial
ms.date: 09/24/2019
ms.custom: devx-track-js
ms.openlocfilehash: 17f62bfa8f596f540a2b732daf8ea24063063723
ms.sourcegitcommit: f723980ade4cbc13548a5d8ac3f3fa681b8a2dbd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609368"
---
# <a name="create-an-azure-storage-account"></a>Azure Storage 계정 만들기

[이전 단계: 앱 만들기](tutorial-vscode-static-website-node-02.md)

이 단계에서는 기본 제공 웹 서버를 사용하여 간단한 파일 저장소(또는 CDN) 역할을 하는 Azure Storage 계정을 만듭니다. 웹 서버가 기본 제공되기 때문에, 정적 사이트를 빠르게 호스트하려면 Azure Storage를 선택하는 것이 좋습니다.

1. 이전 단계에서 만든 `my-static-app` 폴더에서 Visual Studio Code를 시작하면 해당 폴더가 자동으로 열립니다.

    ```bash
    code .
    ```

1. VS Code에서 Azure 로고를 선택하여 **Azure** 탐색기를 엽니다. **Azure Storage** 에서 Azure 구독을 마우스 오른쪽 단추로 클릭하고 **스토리지 계정 만들기** 를 선택합니다.

    ![VS Code에서 스토리지 계정 만들기](../../media/static-website/create-storage-account.png)

1. "Enter the name of the new storage account"(새 스토리지 계정의 이름 입력) 프롬프트에서 스토리지 계정에 대해 전역적으로 고유한 이름을 입력하고 Enter를 누릅니다. 앱 이름으로 유효한 문자는 'a-z'와 '0-9'입니다.

    > [!NOTE]
    > 이렇게 하면 동일한 이름의 스토리지 계정과 리소스 그룹이 만들어집니다. 스토리지 계정이 미국 서부에 자동으로 배치됩니다. 리소스 그룹 및 위치를 지정하려면 상황에 맞는 메뉴에서 "스토리지 계정 만들기(고급)" 옵션을 선택합니다.

1. 스토리지 계정이 생성되는 동안 VS Code의 **출력** 패널에 진행률이 표시됩니다.

    ![VS Code 출력 창 ](../../media/static-website/output-storage.png)

1. 스토리지 계정이 만들어지면 정적 웹 사이트 호스팅을 스토리지 계정에 사용하도록 설정되었다는 메시지가 표시됩니다.

    ![스토리지 계정 만들기](../../media/static-website/static-website-enabled-notification.png)

    > [!IMPORTANT]
    > React, Angular 및 Vue와 같은 최신 SPA(단일 페이지 앱)에서 클라이언트의 경로 오류를 처리하므로 오류 문서에서 *index.html* 을 사용합니다. 클래식 정적 웹 사이트의 경우 사용자 지정 404 오류 페이지를 사용합니다.

> [!div class="nextstepaction"]
> [스토리지 컨테이너를 만들었습니다.](tutorial-vscode-static-website-node-04.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-storage)
