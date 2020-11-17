---
title: '빠른 시작: Python 앱 만들기'
description: Azure App Service를 시작하고 첫 번째 Python 앱을 App Service의 Linux 컨테이너에 배포합니다.
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python, devx-track-azurecli
robots: noindex
ms.openlocfilehash: f58c53d4723bdcea1e49a7cd75dff95bd79c2517
ms.sourcegitcommit: 12f80b1e0fe08db707c198271d0c399c3aba343a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515204"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>빠른 시작: Azure App Service on Linux에서 Python 앱 만들기

이 빠른 시작에서는 확장성이 뛰어난 자체 패치 웹 호스팅 서비스인 [App Service on Linux](/azure/app-service/overview#app-service-on-linux)에 Python 앱을 배포합니다. Mac, Linux 또는 Windows 컴퓨터에서 로컬 [Azure CLI(명령줄 인터페이스)](/cli/azure/install-azure-cli)를 사용하여 Flask 또는 Django 프레임워크로 샘플을 배포합니다. 구성하는 웹앱은 무료 App Service 계층을 사용하기 때문에 이 문서를 진행하는 동안 비용이 발생하지 않습니다.

> [!TIP]
> 대신 Visual Studio Code를 사용하려면 **[Visual Studio Code App Service 빠른 시작](/azure/developer/python/tutorial-deploy-app-service-on-linux-01)** 을 따르세요.

## <a name="1-set-up-your-initial-environment"></a>1. 초기 환경 설정

<a name="setup"></a>

1. 활성 구독이 포함된 Azure 계정이 있어야 합니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 이상</a>을 설치합니다.
1. <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 이상을 설치합니다. 이를 통해 셸에서 명령을 실행하여 Azure 리소스를 프로비저닝하고 구성할 수 있습니다.

터미널 창을 열고 Python 버전이 3.6 이상인지 확인합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Azure CLI 버전이 2.0.80 이상인지 확인합니다.

```azurecli
az --version
```

그런 다음, CLI를 통해 Azure에 로그인합니다.

```azurecli
az login
```

이 명령은 로그인 정보를 수집하는 브라우저를 엽니다. 명령이 완료되면 구독에 대한 정보가 포함된 JSON 출력이 표시됩니다.

로그인하면 Azure CLI에서 Azure 명령을 실행하여 구독의 리소스를 사용할 수 있습니다.

[문제가 있나요? 알려주세요.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="2-clone-the-sample"></a>2. 샘플 복제

다음 명령을 사용하여 샘플 리포지토리를 복제하고 샘플 폴더로 이동합니다. (git를 아직 설치하지 않은 경우 [git를 설치](https://git-scm.com/downloads)합니다.)

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

그런 다음, 해당 폴더로 이동합니다.

```terminal
cd python-docs-hello-world
```

이 샘플에는 Azure App Service가 앱을 시작할 때 인식하는 프레임워크 관련 코드가 포함되어 있습니다. 자세한 내용은 [컨테이너 시작 프로세스](/azure/app-service/configure-language-python#container-startup-process)를 참조하세요.

[문제가 있나요? 알려주세요.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="3-run-the-sample"></a>3. 샘플 실행

1. *python-docs-hello-world* 폴더에 있는지 확인합니다. 

1. 가상 환경을 만들고 종속성을 설치합니다.

    [!include [virtual environment setup](includes/app-service-quickstart-python-venv.md)]

    "[Errno 2] 해당하는 파일이나 디렉터리가 없습니다. 'requirements.txt'."라는 메시지가 표시되는 경우 *python-docs-hello-world* 폴더에 있는지 확인합니다.

1. 개발 서버를 실행합니다.

    ```terminal  
    flask run
    ```
    
    기본적으로 서버는 샘플에서 사용되는 것처럼 앱의 진입 모듈이 *app.py* 에 있다고 간주합니다. (다른 모듈 이름을 사용하는 경우 `FLASK_APP` 환경 변수를 해당 이름으로 설정합니다.)

1. 웹 브라우저를 열고 `http://localhost:5000/`의 샘플 앱으로 이동합니다. 앱에 **Hello World!** 메시지가 표시됩니다.

    ![샘플 Python 앱을 로컬로 실행](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. 터미널 창에서 **Ctrl**+**C** 를 눌러 개발 서버를 종료합니다.


[문제가 있나요? 알려주세요.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="4-deploy-the-sample"></a>4. 샘플 배포

`az webapp up` 명령을 사용하여 로컬 폴더(*python-docs-hello-world*)에 코드를 배포합니다.

```azurecli
az webapp up --sku F1 --name <app-name>
```

- `az` 명령이 인식되지 않는 경우 [초기 환경 설정](#setup)에서 설명한 대로 Azure CLI가 설치되어 있는지 확인합니다.
- `webapp` 명령이 인식되지 않는 경우 Azure CLI 버전이 2.0.80 이상이기 때문입니다. 그렇지 않은 경우 [최신 버전을 설치합니다](/cli/azure/install-azure-cli).
- `<app_name>`을 모든 Azure에서 고유한 이름으로 바꿉니다(*유효한 문자는 `a-z`, `0-9` 및 `-`* ). 좋은 패턴은 회사 이름과 앱 식별자의 조합을 사용하는 것입니다.
- `--sku F1` 인수는 무료 가격 책정 계층에 웹앱을 만듭니다. 이 인수를 생략하여 더 빠른 프리미엄 계층을 사용합니다. 이 경우 시간당 비용이 발생합니다.
- 선택적으로 인수 `--location <location-name>`을 포함할 수 있습니다. 여기서 `<location_name>`은 사용 가능한 Azure 지역입니다. Azure 계정에 허용되는 지역 목록은 [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) 명령을 실행하여 검색할 수 있습니다.
- "앱의 런타임 스택을 자동으로 검색할 수 없습니다."라는 오류가 표시되면 *requirements.txt* 파일이 포함된 *python-docs-hello-world* 폴더(Flask) 또는 *python-docs-hello-django* 폴더(Django)에서 명령을 실행하고 있는지 확인합니다. ([az webapp up으로 자동 검색 문제 해결](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md)(GitHub)을 참조하세요.)

이 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 실행되는 동안 리소스 그룹, App Service 계획 및 호스팅 앱을 만들고, 로깅을 구성한 다음, ZIP 배포 수행에 대한 메시지를 제공합니다. 그런 다음, "http://&lt;app-name&gt;.azurewebsites.net에서 앱을 시작할 수 있습니다."라는 메시지를 제공합니다. 이 메시지는 Azure에서 앱의 URL입니다.

![az webapp up 명령의 예제 출력](./media/quickstart-python/az-webapp-up-output.png)

[문제가 있나요? 알려주세요.](https://aka.ms/FlaskCLIQuickstartHelp)

[!include [az webapp up command note](includes/app-service-web-az-webapp-up-note.md)]

## <a name="5-browse-to-the-app"></a>5. 앱으로 이동

웹 브라우저에서 배포된 애플리케이션(URL: `http://<app-name>.azurewebsites.net`)으로 이동합니다. 처음에 앱을 시작하는 데 몇 분이 걸립니다.

Python 샘플 코드가 기본 제공 이미지를 사용하여 App Service에서 Linux 컨테이너를 실행 중입니다.

![Azure에서 샘플 Python 앱 실행](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**축하합니다.** Python 앱을 App Service에 배포했습니다.

[문제가 있나요? 알려주세요.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="6-redeploy-updates"></a>6. 업데이트 재배포

이 섹션에서는 작은 코드를 변경한 다음, 코드를 Azure에 다시 배포합니다. 코드 변경 사항은 다음 섹션에서 작업하는 로깅 출력을 생성하기 위해 `print` 문을 포함합니다.

편집기에서 *app.py* 를 열고 다음 코드와 일치하도록 `hello` 함수를 업데이트합니다. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello, Azure!"
```

    
변경 내용을 저장한 다음, `az webapp up` 명령을 사용하여 앱을 다시 배포합니다.

```azurecli
az webapp up
```

이 명령은 앱 이름, 리소스 그룹 및 App Service 계획을 포함하여 *.azure/config* 파일에서 로컬로 캐시된 값을 사용합니다.

배포가 완료되면 `http://<app-name>.azurewebsites.net`으로 열린 브라우저 창으로 다시 전환합니다. 수정된 메시지를 표시하는 페이지를 새로 고칩니다.

![Azure에서 업데이트된 샘플 Python 앱 실행](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

[문제가 있나요? 알려주세요.](https://aka.ms/FlaskCLIQuickstartHelp)

> [!TIP]
> Visual Studio Code는 Python 웹앱을 App Service에 배포하는 프로세스를 간소화하는 Python 및 Azure App Service에 대한 강력한 확장 기능을 제공합니다. 자세한 내용은 [Visual Studio Code에서 Azure App Service에 Python 앱 배포](/azure/python/tutorial-deploy-app-service-on-linux-01)를 참조하세요.

## <a name="7-stream-logs"></a>7. 로그 스트리밍

앱 내부에서 생성되는 콘솔 로그와 앱이 실행되는 컨테이너에 액세스할 수 있습니다. 로그에는 `print` 문을 사용하여 생성된 모든 출력이 포함됩니다.

로그를 스트리밍하려면 [az webapp log tail](/cli/azure/webapp/log?view=azure-cli-latest&preserve-view=true#az_webapp_log_tail) 명령을 실행합니다.

```azurecli
az webapp log tail
```

`--logs` 매개 변수를 포함한 다음, `az webapp up` 명령을 포함하여 배포 시 로그 스트림을 자동으로 열 수 있습니다.

브라우저에서 앱을 새로 고쳐서 콘솔 로그를 생성합니다. 여기에는 앱에 대한 HTTP 요청을 설명하는 메시지를 포함합니다. 출력이 즉시 표시되지 않으면 30초 후에 다시 시도합니다.

`https://<app-name>.scm.azurewebsites.net/api/logs/docker`의 브라우저에서 로그 파일을 검사할 수도 있습니다.

언제든지 로그 스트리밍을 중지하려면 터미널에서 **Ctrl**+**C** 를 누릅니다.

[문제가 있나요? 알려주세요.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="8-manage-the-azure-app"></a>8. Azure 앱 관리

만든 앱을 관리하려면 <a href="https://portal.azure.com" target="_blank">Azure Portal</a>로 이동합니다. **App Services** 를 검색하여 선택합니다.

![Azure Portal의 App Services로 이동](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Azure 앱의 이름을 선택합니다.

![Azure Portal의 App Services에서 Python 앱으로 이동](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

앱을 선택하면 해당 **개요** 페이지가 열리며 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다.

![Azure Portal의 개요 페이지에서 Python 앱 관리](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

App Service 메뉴는 앱 구성을 위한 다양한 페이지를 제공합니다.

[문제가 있나요? 알려주세요.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="9-clean-up-resources"></a>9. 리소스 정리

이전 단계에서 Azure 리소스를 리소스 그룹에 만들었습니다. 리소스 그룹의 이름은 사용자의 위치에 따라 "appsvc_rg_Linux_CentralUS" 등입니다. 무료 F1 계층 이외의 App Service SKU를 사용하면 이러한 리소스에 지속적인 비용이 발생합니다([App Service 가격 계층](https://azure.microsoft.com/pricing/details/app-service/linux/) 참조).

나중에 이러한 리소스가 필요하지 않으면 다음 명령을 실행하여 리소스 그룹을 삭제합니다.

```azurecli
az group delete --no-wait
```

이 명령은 *.azure/config* 파일에 캐시된 리소스 그룹 이름을 사용합니다.

`--no-wait` 인수를 사용하면 작업이 완료되기 전에 명령을 반환할 수 있습니다.

[문제가 있나요? 알려주세요.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Python 앱 구성](/azure/app-service/configure-language-python)

> [!div class="nextstepaction"]
> [Python 웹앱에 사용자 로그인 추가](/azure/active-directory/develop/quickstart-v2-python-webapp)

> [!div class="nextstepaction"]
> [자습서: 사용자 지정 컨테이너에서 Python 앱 실행](/azure/app-service/tutorial-custom-container)
