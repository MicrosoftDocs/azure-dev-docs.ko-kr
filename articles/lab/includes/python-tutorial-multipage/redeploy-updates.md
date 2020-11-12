---
title: azure-sign-in.md 포함 파일
description: azure-sign-in.md 포함 파일
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: e500a760430d92fa6640d964320a3ef3f161b7cc
ms.sourcegitcommit: 5f64710b2b0822e789c7f15acba5a3a257c033f9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93405241"
---
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

![Azure에서 업데이트된 샘플 Python 앱 실행](../../media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code는 Python 웹앱을 App Service에 배포하는 프로세스를 간소화하는 Python 및 Azure App Service에 대한 강력한 확장 기능을 제공합니다. 자세한 내용은 [Visual Studio Code에서 Azure App Service에 Python 앱 배포](/azure/python/tutorial-deploy-app-service-on-linux-01)를 참조하세요.