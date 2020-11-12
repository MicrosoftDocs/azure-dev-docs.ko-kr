---
title: azure-sign-in.md 포함 파일
description: azure-sign-in.md 포함 파일
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: 6f9e5e8f30c5108996f14bef9dc4c90a668c60c1
ms.sourcegitcommit: 5f64710b2b0822e789c7f15acba5a3a257c033f9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93405296"
---
1. *python-docs-hello-world* 폴더에 있는지 확인합니다. 

1. 가상 환경을 만들고 종속성을 설치합니다.

    [!include [virtual environment setup](../app-service-quickstart-python-venv.md)]

    "[Errno 2] 해당하는 파일이나 디렉터리가 없습니다. 'requirements.txt'."라는 메시지가 표시되는 경우 *python-docs-hello-world* 폴더에 있는지 확인합니다.

1. 개발 서버를 실행합니다.

    ```terminal  
    flask run
    ```
    
    기본적으로 서버는 샘플에서 사용되는 것처럼 앱의 진입 모듈이 *app.py* 에 있다고 간주합니다. (다른 모듈 이름을 사용하는 경우 `FLASK_APP` 환경 변수를 해당 이름으로 설정합니다.)

1. 웹 브라우저를 열고 `http://localhost:5000/`의 샘플 앱으로 이동합니다. 앱에 **Hello World!** 메시지가 표시됩니다.

    ![샘플 Python 앱을 로컬로 실행](../../media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. 터미널 창에서 **Ctrl**+**C** 를 눌러 개발 서버를 종료합니다.
