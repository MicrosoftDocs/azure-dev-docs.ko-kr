---
title: Visual Studio Code에서 Azure Storage에 정적 Node.js 앱 파일 배포
description: 자습서 4부, Azure Storage에 파일 배포
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: buhollan
ms.custom: devx-track-javascript
ms.openlocfilehash: 3ab1a95caea62bdbd36f6f82fee6cd18301a35ba
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88218337"
---
# <a name="deploy-the-website-to-azure-storage"></a>Azure Storage에 웹 사이트 배포

[이전 단계: 스토리지 계정 만들기](tutorial-vscode-static-website-node-03.md)

이 단계에서는 Visual Studio Code를 사용하여 이전 단계에서 만든 정적 웹 사이트 파일을 Azure Storage에 배포한 다음, 해당 파일을 호스트하여 제공합니다.

# <a name="angular"></a>[Angular](#tab/angular)

1. Visual Studio Code에서 **Azure Storage** 탐색기로 이동하여 구독을 펼치고 이전 단계에서 만든 Azure Storage 계정에 대한 노드를 펼친 다음, **Blob 컨테이너** 노드를 펼칩니다. `$web` 컨테이너가 앱 코드를 배포하는 위치입니다.

   ![Azure Storage 탐색기의 Azure Storage 노드](media/static-website/storage-nodes.png)

1. **파일** 탐색기를 선택하고 _dist/my-static-app_ 폴더를 마우스 오른쪽 단추로 클릭한 다음, **Deploy to Static Website**(정적 웹 사이트에 배포)를 선택합니다.

    ![정적 웹 사이트에 배포 명령](media/static-website/deploy-build-angular.png)

1. 메시지가 표시되면 이전에 만든 스토리지 계정을 선택합니다.

1. 배포가 완료되면 **Browse to website**(웹 사이트 찾아보기) 단추와 함께 메시지가 나타납니다. 이 단추를 선택하면 배포된 앱 코드의 기본 엔드포인트가 열립니다.

    ![배포 완료 메시지](media/static-website/deployment-complete.png)

    ![Azure에서 실행되는 정적 웹 사이트](media/static-website/azure-app-angular.png)

# <a name="react"></a>[React](#tab/react)

1. Visual Studio Code에서 **Azure Storage** 탐색기로 이동하여 구독을 펼치고 이전 단계에서 만든 Azure Storage 계정에 대한 노드를 펼친 다음, **Blob 컨테이너** 노드를 펼칩니다. `$web` 컨테이너가 앱 코드를 배포하는 위치입니다.

   ![Azure Storage 탐색기의 Azure Storage 노드](media/static-website/storage-nodes.png)

1. **파일** 탐색기를 선택하고 _build_ 폴더를 마우스 오른쪽 단추로 클릭한 다음, **Deploy to Static Website**(정적 웹 사이트에 배포)를 선택합니다.

    ![정적 웹 사이트에 배포 명령](media/static-website/deploy-build-react.png)

1. 메시지가 표시되면 이전에 만든 스토리지 계정을 선택합니다.

1. 배포가 완료되면 **Browse to website**(웹 사이트 찾아보기) 단추와 함께 메시지가 나타납니다. 이 단추를 선택하면 배포된 앱 코드의 기본 엔드포인트가 열립니다.

    ![배포 완료 메시지](media/static-website/deployment-complete.png)

    ![Azure에서 실행되는 정적 웹 사이트](media/static-website/azure-app-react.png)

# <a name="vue"></a>[Vue](#tab/vue)

1. Visual Studio Code에서 **Azure Storage** 탐색기로 이동하여 구독을 펼치고 이전 단계에서 만든 Azure Storage 계정에 대한 노드를 펼친 다음, **Blob 컨테이너** 노드를 펼칩니다. `$web` 컨테이너가 앱 코드를 배포하는 위치입니다.

   ![Azure Storage 탐색기의 Azure Storage 노드](media/static-website/storage-nodes.png)

1. **파일** 탐색기를 선택하고 _dist_ 폴더를 마우스 오른쪽 단추로 클릭한 다음, **Deploy to Static Website**(정적 웹 사이트에 배포)를 선택합니다.

    ![정적 웹 사이트에 배포 명령](media/static-website/deploy-build-vue.png)

1. 메시지가 표시되면 이전에 만든 스토리지 계정을 선택합니다.

1. 배포가 완료되면 **Browse to website**(웹 사이트 찾아보기) 단추와 함께 메시지가 나타납니다. 이 단추를 선택하면 배포된 앱 코드의 기본 엔드포인트가 열립니다.

    ![배포 완료 메시지](media/static-website/deployment-complete.png)

    ![Azure에서 실행되는 정적 웹 사이트](media/static-website/azure-app-vue.png)

# <a name="svelte"></a>[Svelte](#tab/svelte)

1. Visual Studio Code에서 **Azure Storage** 탐색기로 이동하여 구독을 펼치고 이전 단계에서 만든 Azure Storage 계정에 대한 노드를 펼친 다음, **Blob 컨테이너** 노드를 펼칩니다. `$web` 컨테이너가 앱 코드를 배포하는 위치입니다.

   ![Azure Storage 탐색기의 Azure Storage 노드](media/static-website/storage-nodes.png)

1. **파일** 탐색기를 선택하고 _public_ 폴더를 마우스 오른쪽 단추로 클릭한 다음, **Deploy to Static Website**(정적 웹 사이트에 배포)를 선택합니다.

    ![정적 웹 사이트에 배포 명령](media/static-website/deploy-build-svelte.png)

1. 메시지가 표시되면 이전에 만든 스토리지 계정을 선택합니다.

1. 배포가 완료되면 **Browse to website**(웹 사이트 찾아보기) 단추와 함께 메시지가 나타납니다. 이 단추를 선택하면 배포된 앱 코드의 기본 엔드포인트가 열립니다.

    ![배포 완료 메시지](media/static-website/deployment-complete-svelte.png)

    ![Azure에서 실행되는 정적 웹 사이트](media/static-website/azure-app-svelte.png)

---

> [!div class="nextstepaction"]
> [내 사이트가 Azure에 있습니다.](tutorial-vscode-static-website-node-05.md)[문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-storage)
