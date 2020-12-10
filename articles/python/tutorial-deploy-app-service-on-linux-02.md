---
title: '2단계: Visual Studio Code에서 Linux의 Azure App Service에 배포할 앱 준비'
description: 자습서 2단계, 애플리케이션 설정
ms.topic: conceptual
ms.date: 11/20/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 7197f8afc28bd62e7247c3955c888199ee69c509
ms.sourcegitcommit: 09b4a2dbe13601fdf16fcc4082a5075b46ad3459
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96559197"
---
# <a name="2-prepare-your-app-for-deployment-to-azure-app-service"></a>2: Azure App Service에 배포할 수 있도록 앱 준비

[이전 단계: 환경 구성](tutorial-deploy-app-service-on-linux-01.md)

이 문서에서는 이 자습서에서 Azure App Service에 배포할 앱을 준비합니다. 기존 앱을 사용하거나 새로 앱을 만들거나 다운로드할 수 있습니다.

## <a name="if-you-already-have-an-app"></a>앱이 이미 있는 경우

작업하려는 앱이 이미 있는 경우 Flask 또는 Django와 같은 프레임워크를 포함하여 종속성을 나열하는 *requirements.txt* 파일이 프로젝트 루트에 있는지 확인합니다. 선택한 프레임워크를 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [앱이 이미 있는 경우 - 3단계 진행 >>>](tutorial-deploy-app-service-on-linux-03.md)

## <a name="if-you-dont-already-have-an-app"></a>앱이 아직 없는 경우

앱이 아직 없는 경우 아래 옵션 중 *하나* 를 사용합니다. 앱이 로컬에서 실행되는지 확인해야 합니다.

이 자습서의 나머지 부분에서는 [옵션 3](#option-3-create-a-minimal-flask-app)에 표시된 코드를 사용합니다.

### <a name="option-1-use-the-vs-code-flask-tutorial-sample"></a>옵션 1: VS Code Flask 자습서 샘플 사용

[Flask 자습서](https://code.visualstudio.com/docs/python/tutorial-flask)의 결과인 [https://github.com/Microsoft/python-sample-vscode-flask-tutorial](https://github.com/Microsoft/python-sample-vscode-flask-tutorial)을 다운로드하거나 복제합니다. 앱 코드는 정확히 *hello_app* 폴더에 있습니다. 샘플의 *readme.md* 파일에서 앱 로컬 실행 지침을 살펴보세요.

### <a name="option-2-use-the-vs-code-django-tutorial-sample"></a>옵션 2: VS Code Django 자습서 샘플 사용

[Django 자습서](https://code.visualstudio.com/docs/python/tutorial-django)의 결과인 [https://github.com/Microsoft/python-sample-vscode-django-tutorial](https://github.com/Microsoft/python-sample-vscode-django-tutorial)을 다운로드하거나 복제합니다.

원칙적으로는 클라우드에 배포된 Django 앱 역시 PostgreSQL for Azure 같은 클라우드 기반 데이터베이스를 사용합니다. 자세한 내용은 [자습서: Azure Portal을 사용하여 PostgreSQL을 사용하는 Django 웹앱 배포](tutorial-python-postgresql-app-portal.md)를 참조하세요.

Django 앱이 이 샘플과 같은 로컬 SQLite 데이터베이스를 사용하는 경우 *db.sqlite3* 파일의 미리 초기화되고 미리 채워진 복사본을 리포지토리에 포함하는 것이 가장 쉬운 방법입니다. 그렇지 않으면 앱이 배포된 컨테이너에서 Django의 `migrate` 명령을 실행하도록 빌드 후 명령을 구성해야 합니다. 자세한 내용은 [App Service 구성 - 빌드 자동화 사용자 지정](/azure/app-service/configure-language-python#customize-build-automation)을 참조하세요.

### <a name="option-3-create-a-minimal-flask-app"></a>옵션 3: 최소 Flask 앱 만들기

이 섹션에서는 이 연습에서 사용되는 최소 Flask 앱에 대해 설명합니다.

1. 새 폴더를 만들고, VS Code에서 열고, 아래 콘텐츠가 포함된 *hello.py* 라는 파일을 추가합니다. 앱 개체는 의도적으로 `myapp`이라고 이름이 지정되어 나중에 설명하는 것처럼 App Service의 시작 명령에 이름이 사용되는 방법을 보여 줍니다.

    ```python
    from flask import Flask
    myapp = Flask(__name__)

    @myapp.route("/")
    def hello():
        return "Hello Flask, on Azure App Service for Linux"
    ```

1. 다음과 같은 콘텐츠가 포함된 *requirements.txt* 라는 파일을 만듭니다.

    ```text
    Flask
    ```

1. 메뉴 명령 **터미널** > **새 터미널** 을 사용하여 터미널을 엽니다.

1. 터미널에서 `.venv`라는 가상 환경을 만들고 활성화합니다. 

    # <a name="macoslinux"></a>[macOS/Linux](#tab/linux)

    ```bash
    sudo apt-get install python3-venv    # If needed
    python3 -m venv .venv
    source .venv/bin/activate
    ```

    # <a name="windows"></a>[Windows](#tab/windows)

    ```cmd
    py -3 -m venv .venv
    .venv\scripts\activate
    ```

    ---

1. VS Code에서 새로 만든 환경을 활성화하라는 메시지가 표시되면 **예** 로 대답합니다.

1. 앱의 종속성을 설치합니다.

    ```cmd
    pip install -r requirements.txt
    ```

1. 앱 개체를 찾을 위치를 Flask에 알려주는 FLASK_APP 환경 변수를 설정합니다.

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set FLASK_APP=hello:myapp
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```ps
    $env:FLASK_APP = "hello:myapp"
    ```

   # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    export FLASK_APP=hello:myapp
    ```

    ---

1. 앱 실행:

    ```cmd
    flask run
    ```

1. URL `http://127.0.0.1:5000/`을 사용하여 브라우저에서 앱을 엽니다. "Hello Flask, on Azure App Service for Linux"라는 메시지가 표시됩니다.

1. 터미널에서 **Ctrl**+**C** 를 눌러 Flask 서버를 중지합니다.

> [!div class="nextstepaction"]
> [앱이 준비되었습니다. - 3단계 진행 >>>](tutorial-deploy-app-service-on-linux-03.md)

[문제가 있나요? 알려주세요.](https://aka.ms/FlaskVSCQuickstartHelp)
