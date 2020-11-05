---
title: Visual Studio Code에서 Azure App Service에 Deno 앱 배포
description: 'Deno 자습서 3부: 웹 사이트 배포'
ms.topic: tutorial
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: ceb55fb184582a4cb355c25f42c798713335b45c
ms.sourcegitcommit: e1175aa94709b14b283645986a34a385999fb3f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233411"
---
# <a name="deploy-deno-apps-to-azure"></a>Azure에 Deno 앱 배포

[이전 단계: 앱 만들기](tutorial-visual-studio-code-azure-app-service-deno-02.md)

이 단계에서는 Azure CLI를 사용하여 Azure에 Deno 앱을 배포합니다.

## <a name="deploy-the-app-to-azure"></a>Azure에 앱 배포

1. 다음 명령을 사용하여 `deno-quickstart`라는 리소스 그룹을 만듭니다.

    ```bash
    az groups create -n deno-quickstart -l eastus
    ```

    리소스 그룹의 이름을 변경하기로 결정하는 경우 다음 단계에서 모든 `-g` 플래그를 업데이트해야 합니다.

1. 다음 명령을 사용하여 웹 사이트를 보관할 `deno-plan`이라는 AppService 계획을 만듭니다.

    ```bash
    az appservice plan create -g deno-quickstart -n deno-plan --is-linux
    ```

1. 다음으로 웹앱 자체를 만듭니다. 이 명령은 새 AppService를 만들고, 이전에 만든 계획에 바인딩합니다. `<your-app-name>` 태그를 웹앱에 지정하려는 이름으로 변경합니다. 이 이름은 고유해야 합니다.

    ```bash
    az webapp create -n <your-app-name> -g deno-quickstart -p deno-plan -i anthonychu/azure-webapps-deno:1.0.2
    ```

    이 AppService는 모든 Deno 코드를 실행하는 기본 기능을 제공하는 Docker 이미지를 실행합니다. 이 프로세스가 완료될 때까지 몇 초 정도 걸릴 수 있습니다.

1. 만든 후에는 몇 가지 변수를 구성해야 합니다. 다음 명령을 실행하여 구성할 수 있습니다.

    ```bash
    az webapp config container set -n <your-app-name> -g deno-quickstart -i anthonychu/azure-webapps-deno:1.0.2 -r 'https://index.docker.io' -u '' -p  '' -t true && \
    az webapp config set -n <your-app-name> -g deno-quickstart --startup-file '' && \
    az webapp config appsettings set -n <your-app-name> -g deno-quickstart --settings WEBSITE_RUN_FROM_PACKAGE=1 WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
    ```

이제 AppService가 구성되었으며, 이전 단계에서 앱을 받기 위해 대기 중입니다. 그러나 앱을 실행하려면 앱을 `.zip` 패키지에 패키징해야 합니다. 다음 단계를 사용하여 수행할 수 있습니다.

1. `deno-demo` 폴더로 이동합니다.

    ```bash
    cd deno-demo
    ```

1. `zip` 명령을 실행합니다.

    ```bash
    zip demo demo.ts
    ```

    이 명령의 결과로 `demo.ts` 파일과 동일한 폴더에 `demo.zip`이라는 파일이 생성됩니다.

1. 앱을 패키징한 후에는 다음과 같이 파일을 AppService에 업로드하여 실행할 수 있습니다.

    ```bash
    az webapp deployment source config-zip -n <your-app-name> -g deno-quickstart --src ./demo.zip && \
    az webapp config set -n <your-app-name> -g deno-quickstart --startup-file 'deno run --allow-net demo.ts'
    ```

1. `https://<your-app-name>.azurewebsites.net`으로 이동하여 애플리케이션을 테스트합니다.

> [!div class="nextstepaction"]
> [내 사이트가 Azure에 있습니다.](tutorial-visual-studio-code-azure-app-service-deno-04.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=deploy-app)

## <a name="next-steps"></a>다음 단계

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [완료했습니다.](node-howto-deploy-web-app.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=clean-up-resources)
