---
title: '4단계: Azure App Service on Linux에서 Python 앱에 대한 사용자 지정 시작 파일 구성'
description: 자습서 4단계에서는 Django, Flask 및 기타 프레임워크에 대한 특정 지침을 포함하여 웹앱을 시작하는 방법을 App Service에 지시합니다.
ms.topic: conceptual
ms.date: 11/20/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 0c7fd378fa1166113812c4748c10676030941e13
ms.sourcegitcommit: 29930f1593563c5e968b86117945c3452bdefac1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95485652"
---
# <a name="4-configure-a-custom-startup-file-for-python-apps-on-azure-app-service"></a>4: Azure App Service에서 Python 앱에 대한 사용자 지정 시작 파일 구성

[이전 단계: App Service 만들기](tutorial-deploy-app-service-on-linux-03.md)

이 단계에서는 필요에 따라 Azure App Service에서 Python 앱의 커스텀 시작 파일을 구성합니다.

다음과 같은 경우 사용자 지정 시작 파일이 필요합니다.

- 기본값을 초과하는 추가 인수(`--bind=0.0.0.0 --timeout 600`)로 Gunicorn 웹 서버를 시작하려는 경우.

- 앱이 Flask 또는 Django 이외의 프레임워크를 사용하여 빌드되었거나 다른 웹 서버를 사용하려는 경우.

- 주 코드 파일의 이름이 *app.py* 또는 *application.py**가 아닌 **다른** 이름이거나 앱 개체 이름이 `app`이 아닌 **다른** 이름인 경우.

    즉, 프로젝트의 루트 폴더에 *app.py* 또는 *application.py* 파일이 *있으며* Flask 앱 개체 이름이 `app`인 경우 외에는 사용자 지정 시작 명령이 필요합니다.

    예를 들어 [2단계](tutorial-deploy-app-service-on-linux-02.md)의 코드 예제에 있는 주 파일의 이름이 *hello.py* 이고 앱 개체의 이름은 `myapp`이므로 이 문서에 설명된 것처럼 사용자 지정 시작 파일이 필요합니다.

자세한 내용은 [Python 앱 구성 - 컨테이너 시작 프로세스](/azure/app-service/configure-language-python#container-startup-process)를 참조하세요.

## <a name="create-a-startup-file"></a>시작 파일 만들기

사용자 지정 시작 파일이 필요한 경우 다음 단계를 수행합니다.

1. 프로젝트에서 *startup.txt*, *startup.sh* 라는 파일 또는 시작 명령이 포함된 다른 이름의 파일을 만듭니다. Django, Flask 및 기타 프레임워크에 대한 자세한 내용은 이 문서의 뒷부분에 나오는 섹션을 참조하세요.

    필요한 경우 시작 파일에는 여러 명령이 포함될 수 있습니다.

1. 앱의 나머지 부분을 사용하여 배포할 수 있도록 파일을 코드 리포지토리에 커밋합니다.

1. **Azure: App Service** 탐색기에서 App Service를 확장하고, **애플리케이션 설정** 을 마우스 오른쪽 단추로 클릭한 다음, **포털에서 열기** 를 선택합니다.

    ![App Service 탐색기에서 포털의 애플리케이션 설정 열기](media/deploy-azure/open-application-settings-in-portal-for-app-service.png)

1. Azure Portal에서 필요한 경우 로그인한 다음, **구성** 페이지에서 **일반 설정** 을 선택하고 **스택 설정** > **시작 명령** 에서 시작 파일의 이름(예: *startup.txt* 또는 *startup.sh*)을 입력한 다음, **저장** 을 선택합니다.

    ![Azure Portal에서 시작 명령 파일 이름 설정](media/deploy-azure/enter-startup-file-for-app-service-in-the-azure-portal.png)

    > [!NOTE]
    > 시작 명령 파일을 사용하는 대신 시작 명령 자체를 Azure Portal의 **시작 명령** 필드에 직접 배치할 수도 있습니다. 그러나 해당 구성의 이 부분이 변경 내용을 감사하고 다른 App Service 인스턴스에 다시 배포할 수 있는 리포지토리에 있기 때문에 명령 파일을 사용하는 것이 더 좋습니다.

1. 변경 내용을 저장하면 App Service가 다시 시작됩니다.

    그러나 앱 코드를 아직 배포하지 않았으므로 이 시점에서 사이트를 방문하면 “애플리케이션 오류”가 표시됩니다. 이 메시지는 Gunicorn 서버가 시작되었지만 앱을 찾지 못했으며, 따라서 HTTP 요청에 응답하지 않음을 나타냅니다. 다음 단계에서 앱 코드를 배포합니다.

## <a name="django-startup-commands"></a>Django 시작 명령

기본적으로 App Service는 *wsgi.py* 파일이 포함하는 폴더를 자동으로 찾고, 다음 명령을 사용하여 Gunicorn을 시작합니다.

```sh
# <module> is the folder that contains wsgi.py. If you need to use a subfolder,
# specify the parent of <module> using --chdir.
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

`--timeout 1200`을 사용하는 등 Gunicorn 인수를 변경하려면 그러한 수정 항목이 포함된 명령 파일을 만듭니다.

## <a name="flask-startup-commands"></a>Flask 시작 명령

기본적으로 App Service on Linux 컨테이너는 Flask 앱의 WSGI 호출 가능 파일 이름이 `app`이고, *application.py* 또는 *app.py* 라는 파일에 포함되어 앱의 루트 폴더에 있는 것으로 가정합니다.

다음 변형 중 하나를 사용하는 경우 사용자 지정 시작 명령은 *file:app_object* 형식으로 앱 개체의 위치를 식별해야 합니다.

- **다른 파일 이름 및/또는 앱 개체 이름**: 예를 들어 앱의 주 코드 파일이 *hello.py* 이고 앱 개체의 이름이 `myapp`인 경우 시작 명령은 다음과 같습니다.

    ```text
    gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
    ```

- **시작 파일이 하위 폴더에 있음**: 예를 들어 시작 파일이 *myapp/website.py* 이고 앱 개체가 `app`이면 Gunicorn의 `--chdir` 인수를 사용하여 폴더를 지정한 다음, 시작 파일 및 앱 개체의 이름을 일반적인 방법으로 지정합니다.

    ```text
    gunicorn --bind=0.0.0.0 --timeout 600 --chdir myapp website:app
    ```

- **시작 파일이 모듈 내에 있음**: [python-sample-vscode-flask-tutorial](https://github.com/Microsoft/python-sample-vscode-flask-tutorial) 코드에서 *webapp.py* 시작 파일은 그 자체가 *\_\_init\_\_.py* 파일이 포함된 모듈인 *hello_app* 폴더 내에 포함되어 있습니다. 앱 개체 이름이 `app`이며 *\_\_init\_\_.py* 에 정의되어 있고, *webapp.py* 가 상대 가져오기를 사용합니다.

    이 배열로 인해 Gunicorn이 `webapp:app`을 가리키면 “비패키지 내에서 상대 가져오기를 시도함”이라는 오류가 발생하며 앱이 시작되지 않습니다.

    이 경우 모듈에서 앱 개체를 가져오는 간단한 shim 파일을 만든 다음, Gunicorn이 shim을 사용하여 앱을 시작하게 합니다. 예를 들어 [python-sample-vscode-flask-tutorial](https://github.com/Microsoft/python-sample-vscode-flask-tutorial) 코드에는 다음 콘텐츠가 포함된 *startup.py* 가 들어있습니다.

    ```python
    # startup.py shim
    from hello_app.webapp import app
    ```

    따라서 시작 명령은 다음과 같습니다.

    ```text
    gunicorn --bind=0.0.0.0 --timeout 600 startup:app
    ```


## <a name="startup-commands-for-other-frameworks-and-web-servers"></a>다른 프레임워크 및 웹 서버에 대한 시작 명령

Python 앱을 실행하는 App Service 컨테이너에는 gunicorn 웹 서버와 함께 Django 및 Flask가 기본적으로 설치되어 있습니다.

Django 또는 Flask 이외의 프레임워크(예: Falcon, FastAPI 등)를 사용하거나 다른 웹 서버를 사용하려면 다음을 수행합니다.

- *requirements.txt* 파일에 프레임워크 및/또는 웹 서버를 포함합니다.

- 시작 명령에서 [Flask의 이전 섹션](#flask-startup-commands)에 설명된 대로 호출 가능한 WSGI를 식별합니다.

- gunicorn 이외의 웹 서버를 시작하려면 서버를 직접 호출하는 대신 `python -m` 명령을 사용합니다. 예를 들어 다음 명령은 호출 가능한 WSGI 이름이 `app`이고 *application.py* 에 있다고 가정하여 uvicorn 서버를 시작합니다.

    ```sh
    python -m uvicorn application:app --host 0.0.0.0
    ```

    *requirements.txt* 를 통해 설치된 웹 서버는 Python 글로벌 환경에 추가되지 않아 직접 호출할 수 없기 때문에 `python -m`을 사용합니다. `python -m` 명령은 현재 가상 환경 내에서 서버를 호출합니다.

> [!div class="nextstepaction"]
> [시작 파일을 구성했습니다. - 5단계 진행 >>>](tutorial-deploy-app-service-on-linux-05.md)

[문제가 있나요? 알려주세요.](https://aka.ms/FlaskVSCQuickstartHelp)
