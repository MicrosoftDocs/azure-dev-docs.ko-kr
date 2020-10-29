---
title: tutorial-azure-web-app-mongodb-03.md 포함 파일
description: tutorial-azure-web-app-mongodb-03.md 포함 파일
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: 9d6fb0beaa19503541196c72ecbdae52a642648b
ms.sourcegitcommit: 1ddcb0f24d2ae3d1f813ec0f4369865a1c6ef322
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755449"
---
자습서의 이 섹션에서는 샘플 애플리케이션을 Azure에 배포합니다. 그런 다음, 브라우저에서 원격으로 실행되는 앱을 볼 수 있습니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [azure-sign-in](../azure-sign-in.md)]

## <a name="create-web-app-resource"></a>웹앱 리소스 만들기

Visual Studio Code 확장을 사용하여 앱 서비스 리소스를 만들고 웹앱을 리소스에 배포합니다.

1. Azure 탐색기로 이동합니다. 마우스 오른쪽 단추로 구독을 클릭한 다음, `Create new web app...`을 선택합니다.

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/create-web-app-with-extension.png" alt-text="Azure 앱 서비스 확장을 사용하여 웹앱을 만드는 Visual Studio Code의 부분 스크린샷입니다.":::

1. 다음 표를 사용하여 표시되는 메시지에 따라 값을 사용하는 방법을 이해합니다.

    |속성|값|
    |--|--|
    |전역적으로 고유한 새 웹앱의 이름을 입력합니다.| 앱 서비스 리소스에 대한 값(예: `web-app-with-mongodb-YOUR-NAME`)을 입력합니다. `<YOUR-NAME>`을 사용자의 이름 또는 고유 ID로 바꿉니다. 이 고유한 이름은 브라우저에서 리소스에 액세스하기 위한 URL의 일부로도 사용됩니다.|
    |Linux 앱에 대한 런타임을 선택합니다.|`Node 12 LTS`를 선택합니다.|

1. 앱 만들기 프로세스가 완료되면 `Deploy` 또는 `View output` 중 하나를 선택하여 Visual Studio Code의 오른쪽 아래 모서리에 상태 메시지가 표시됩니다. `Deploy`를 선택합니다.

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-app-extension-create-web-app-deploy-web-app.png" alt-text="Azure 앱 서비스 확장을 사용하여 웹앱을 만드는 Visual Studio Code의 부분 스크린샷입니다.":::

    상태 메시지가 더 이상 표시되지 않으면 Azure 탐색기를 선택하여 배포하고, 마우스 오른쪽 단추로 리소스 이름을 클릭한 다음, **웹앱에 배포...** 를 선택할 수 있습니다.

1. 배포 프로세스 중에 알림을 통해 **출력 창** 을 표시하도록 선택할 수 있습니다.  이렇게 하면 배포의 롤링 상태가 표시됩니다. 

1. 배포가 완료되면 알림이 표시됩니다. **로그 스트리밍** 을 선택하여 롤링 로그를 확인합니다. 

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-app-service-deployed.png" alt-text="Azure 앱 서비스 확장을 사용하여 웹앱을 만드는 Visual Studio Code의 부분 스크린샷입니다.":::

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-app-service-stream-logs.png" alt-text="Azure 앱 서비스 확장을 사용하여 웹앱을 만드는 Visual Studio Code의 부분 스크린샷입니다.":::    

1. 브라우저에서 웹 사이트를 열고, `YOUR-RESOURCE_NAME` 텍스트를 사용자 고유의 리소스 이름(예: `https://YOUR-RESOURCE_NAME.azurewebsites.net`)으로 바꿉니다.
    
    이제 웹 사이트는 로컬 및 원격으로 실행할 수 있지만 아직 데이터베이스에 연결되지 않습니다. 

## <a name="want-to-know-more"></a>자세히 알고 싶으세요?

초기 웹 서비스는 8080 포트에서 실행되도록 구성되며 공개적으로 사용할 수 있습니다. 이러한 유형의 웹 사이트 설정을 구성할 수 있습니다.
* [앱 설정](/azure/app-service/configure-common)
* [인증](/azure/app-service/configure-authentication-provider-microsoft)
* [네트워크를 통한 액세스 제한](/azure/app-service/app-service-ip-restrictions)

이 앱 서비스 확장을 사용하여 웹 사이트를 Azure 클라우드에 배포하는 경우 [해당 배포를 구성](https://github.com/microsoft/vscode-azureappservice/wiki/Configuring-Zip-Deployment#additional-zip-deploy-configuration-settings)하는 방법에 대해 자세히 알아보는 것이 좋습니다
