---
title: Azure CLI에서 Azure App Service에 Deno 앱 배포
description: 이 자습서에서는 Azure CLI를 사용하여 Deno 애플리케이션을 Linux 또는 Windows의 Azure App Service에 배포합니다.
ms.topic: tutorial
ms.date: 10/13/2020
ms.custom: scenarios:getting-started, languages:JavaScript, devx-track-javascript
ms.openlocfilehash: ba2e0a42b6d2dedd2192629562a8415a0d6d7167
ms.sourcegitcommit: 0cda024089784b92c1db3a4506c1dccd6bfe6339
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96772629"
---
# <a name="deploy-deno-apps-to-azure-app-service-from-the-azure-cli"></a>Azure CLI에서 Azure App Service에 Deno 앱 배포

Azure CLI를 사용하여 Deno 애플리케이션을 Linux 또는 Windows의 Azure App Service에 배포합니다. Azure App Service용 Deno 런타임은 실험적 Docker 이미지와 함께 제공됩니다. 

![데모 서버 실행](../media/deploy-azure/deno-hello-world.png)

## <a name="1-prepare-your-environment"></a>1. 환경 준비

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-deno&mktingSource=vscode-tutorial-appservice-deno)
- [Visual Studio Code](https://code.visualstudio.com/) 설치
- [Deno](https://deno.land/#installation) 설치
- Bash 환경을 통해 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)을 사용합니다.

   [![Embed 시작](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell 시작")](https://shell.azure.com)   
- 원하는 경우 Azure CLI를 [설치](/cli/azure/install-azure-cli)하여 CLI 참조 명령을 실행합니다.
   - local install을 사용하는 경우 [az login](/cli/azure/reference-index#az-login) 명령을 사용하여 Azure CLI에 로그인합니다.  인증 프로세스를 완료하려면 터미널에 표시되는 단계를 수행합니다.  추가 로그인 옵션은 [Azure CLI를 사용하여 로그인](/cli/azure/authenticate-azure-cli)을 참조하세요.
  - 메시지가 표시되면 처음 사용할 때 Azure CLI 확장을 설치합니다.  확장에 대한 자세한 내용은 [Azure CLI에서 확장 사용](/cli/azure/azure-cli-extensions-overview)을 참조하세요.
  - [az version](/cli/azure/reference-index?#az_version)을 실행하여 설치된 버전과 종속 라이브러리를 찾습니다. 최신 버전으로 업그레이드하려면 [az upgrade](/cli/azure/reference-index?#az_upgrade)를 실행합니다.

## <a name="2-sign-in-to-azure-cli"></a>2. Azure CLI에 로그인

로컬 명령줄에서 Azure CLI를 사용하는 경우 CLI 명령을 사용하려면 먼저 [az login](/cli/azure/reference-index#az-login)을 사용하여 로그인해야 합니다.

[!INCLUDE [interactive-login](../../azure-cli/includes/interactive-login.md)]

3. 로그인하면 Azure 계정에 연결된 구독 목록이 나타납니다. `isDefault: true`가 있는 구독 정보는 로그인한 후 현재 활성화된 구독입니다. 

4. 다른 구독을 선택하려면 전환할 구독 ID와 함께 [az account set](/cli/azure/account#az-account-set) 명령을 사용합니다. 구독 선택에 대한 자세한 내용은 [여러 Azure 구독 사용](/cli/azure/manage-azure-subscriptions-azure-cli)을 참조하세요.

## <a name="3-create-local-deno-api-app"></a>3. 로컬 Deno API 앱 만들기

Deno의 기본 제공 웹 서버를 사용하여 Deno 앱을 만듭니다. 그런 다음, 애플리케이션을 로컬로 실행합니다.

1. 터미널 또는 명령 프롬프트에서 앱 폴더를 만들 위치로 이동하고 `deno-demo`라는 새 폴더를 만듭니다.

1. `demo.ts`라는 새 파일을 만듭니다.
1. Deno는 URL에서 코드를 직접 실행하는 것을 허용합니다. 모든 요청에 "Hello World"로 응답하는 HTTP 서버를 작성합니다. 다음 코드를 사용합니다.

    ```typescript
    import { serve } from "https://deno.land/std@0.54.0/http/server.ts"
    const handler = serve({ port: 80 })

    console.log("Serving at 80")

    for await (const req of handler) {
     req.respond({ body: "Hello World!\n" })
    }
    ```

1. 다음 스크립트를 실행하여 앱을 실행합니다.

    ```bash
    deno run --allow-net ./demo.ts
    ```

1. 브라우저에서 `http://localhost:80`을 열어 앱을 테스트합니다. 사이트가 다음과 같이 표시됩니다.

    ![데모 서버 실행](../media/deploy-azure/deno-hello-world.png)

    `deno run --allow-net https://gist.githubusercontent.com/khaosdoctor/cd2bbb28e682feb8d20a7aba47fc1e17/raw/92de998fd11f2a24ae40bbcb84f5262cfe9389b2/deno-demo.ts`를 입력하여 이 코드를 실행할 수도 있습니다.

1. 터미널에서 **Ctrl**+**C** 를 눌러 서버를 중지합니다.

## <a name="4-deploy-deno-app-to-azure"></a>4. Azure에 Deno 앱 배포

Azure CLI를 사용하여 Azure에 Deno 앱을 배포합니다.

1. 다음 명령을 사용하여 `deno-quickstart`라는 리소스 그룹을 만듭니다.

    ```azurecli
    az group create --name deno-quickstart --location eastus
    ```

    리소스 그룹의 이름을 변경하기로 결정하는 경우 다음 단계에서 모든 `-g` 플래그를 업데이트해야 합니다.

1. 다음 명령을 사용하여 웹 사이트를 보관할 `deno-plan`이라는 AppService 계획을 만듭니다.

    ```azurecli
    az appservice plan create --resource-group deno-quickstart --name deno-plan --is-linux
    ```

1. 다음으로 웹앱 자체를 만듭니다. 이 명령은 새 AppService를 만들고, 이전에 만든 계획에 바인딩합니다. `<your-app-name>` 태그를 웹앱에 지정하려는 이름으로 변경합니다. 이 이름은 고유해야 합니다.

    ```azurecli
    az webapp create -n <your-app-name> --resource-group deno-quickstart -p deno-plan -i anthonychu/azure-webapps-deno:1.0.2
    ```

    이 AppService는 Deno 코드를 실행하는 기본 기능을 제공하는 `anthonychu/azure-webapps-deno:1.0.2` Docker 이미지를 실행합니다. 이 프로세스가 완료될 때까지 몇 초 정도 걸릴 수 있습니다.

## <a name="5-configure-the-azure-app-service-webapp"></a>5. Azure 앱 서비스 웹앱 구성

1. 실험적 Deno 이미지 이름에 대한 Docker 컨테이너 이미지를 가져올 위치를 웹앱에 알려줍니다.

    ```azurecli
    az webapp config container set --name <your-app-name> --resource-group deno-quickstart -i anthonychu/azure-webapps-deno:1.0.2 -r 'https://index.docker.io' -u '' -p  '' -t true
    ```

1. 웹앱에 시작 파일이 없도록 설정합니다.

    ```azurecli
    az webapp config set --name <your-app-name> --resource-group deno-quickstart --startup-file ''

1. Set the webapp to have runtime environment variables:

    ```azurecli
    az webapp config appsettings set --name <your-app-name> --resource-group deno-quickstart --settings WEBSITE_RUN_FROM_PACKAGE=1 WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
    ```

## <a name="6-configure-deno-app-deployment-to-web-app"></a>6. 웹앱에 Deno 앱을 배포하도록 구성 

Azure 웹앱을 구성하여 사용 준비가 완료되었지만 아직 Deno 패키지가 없습니다. 앱을 `.zip` 패키지에 패키징하고 파일이 로컬 컴퓨터에 있다고 웹앱에 알려준 다음, zip 파일에 있는 시작 파일을 설정합니다. 

1. `deno-demo` 폴더로 이동합니다.

    ```bash
    cd deno-demo
    ```

1. `zip` 명령을 실행합니다.

    ```bash
    zip demo demo.ts
    ```

    이 명령의 결과로 `demo.ts` 파일과 동일한 폴더에 `demo.zip`이라는 파일이 생성됩니다.

1. 패키지를 배포의 코드 원본으로 구성합니다.

    ```azurecli
    az webapp deployment source config-zip --name <your-app-name> --resource-group deno-quickstart --src ./demo.zip
    ```

1. 패키지 내에서 실행할 파일을 구성합니다.

    ```azurecli
    az webapp config set --name <your-app-name> --resource-group deno-quickstart --startup-file 'deno run --allow-net demo.ts'
    ```

1. `https://<your-app-name>.azurewebsites.net`으로 이동하여 애플리케이션을 테스트합니다. 

## <a name="7-clean-up-resources"></a>7. 리소스 정리

다음 명령을 사용하여 리소스 그룹을 삭제합니다. 그러면 웹앱 리소스도 삭제됩니다.

```azurecli
az group delete deno-quickstart
```

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.
* Visual Studio Code 확장을 사용하여 [앱 서비스에 배포](../tutorial-vscode-azure-app-service-node-01.md)
* [Virtual Machine에 배포](./nodejs-virtual-machine-vm/introduction.md)
* [사용자 지정 처리기](/azure/azure-functions/functions-custom-handlers)로 [Deno 함수 배포](https://github.com/anthonychu/azure-functions-deno-worker)