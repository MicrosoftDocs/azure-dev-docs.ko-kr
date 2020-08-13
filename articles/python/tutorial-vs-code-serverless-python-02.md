---
title: '2단계: VS Code를 사용하여 Azure Functions에 대한 Python 함수 만들기'
description: 2단계 자습서로, VS Code에 대한 Azure Functions 확장 사용을 설명합니다.
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 13c0083434d024064462bb583a6b24d9e80c38dd
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87983515"
---
# <a name="2-create-a-python-function-for-azure-functions"></a>2: Azure Functions에 대한 Python 함수 만들기

[이전 단계: 필수 구성 요소](tutorial-vs-code-serverless-python-01.md)

이 문서에서는 Visual Studio Code를 사용하여 Azure Functions에 대한 Python 함수를 만듭니다. Azure Functions에 대한 코드는 코드 생성 전에 먼저 만드는 Functions _프로젝트_ 내에서 관리됩니다.

1. **Azure: Functions** 탐색기(왼쪽의 Azure 아이콘을 사용하여 열림)에서 **새 프로젝트 만들기** 명령 아이콘을 선택하거나 명령 팔레트(F1)를 열고 **Azure Functions: 새 프로젝트 만들기**를 선택합니다.

    ![Azure Functions 탐색기에서 새 프로젝트 만들기](media/tutorial-vs-code-serverless-python/create-a-new-project-in-azure-functions-explorer.png)

1. 아래 프롬프트에서:

    | prompt | 값 | Description |
    | --- | --- | --- |
    | 프로젝트에 대한 폴더 지정 | 현재 열려 있는 폴더 | 프로젝트를 만들 폴더입니다. 하위 폴더에서 프로젝트를 만들 수 있습니다. |
    | 함수 앱 프로젝트에 대한 언어를 선택합니다. | **Python** | 코드에 사용되는 템플릿을 결정하는 함수에 사용할 언어입니다. |
    | Python 인터프리터 선택 | (제공된 기본 경로를 사용합니다.) | 가상 환경에 사용할 Python 인터프리터입니다. |
    | 프로젝트의 첫 번째 함수에 대한 템플릿 선택 | **HTTP 트리거** | HTTP 트리거를 사용하는 함수는 함수 엔드포인트에 대한 HTTP 요청이 있을 때마다 실행됩니다. (Azure Functions에 대한 다양한 트리거가 있습니다. 자세히 알아보려면 [Functions로 할 수 있는 작업은 무엇인가요?](/azure/azure-functions/functions-overview#what-can-i-do-with-functions)를 참조하세요.) |
    | 함수 이름 제공 | HttpExample | 이름은 구성 데이터와 함께 함수 코드를 포함하는 하위 폴더에 사용되며 HTTP 엔드포인트의 이름도 정의합니다. 기본 "HTTPTrigger1"을 허용하는 대신 "HttpExample"을 사용하여 트리거에서 함수 자체를 구분합니다. |
    | 권한 부여 수준 | **익명** | 익명 권한 부여는 누구나 공개적으로 함수에 액세스할 수 있습니다. |

1. 잠시 후 새 프로젝트가 생성되었음을 나타내는 메시지가 표시됩니다. **탐색기**에는 함수에 대해 생성된 하위 폴더가 있으며 Visual Studio Code에서 기본 함수 코드를 포함하는 *\_\_init\_\_.py* 파일이 열립니다.

    ![Azure Functions의 새 Python 프로젝트 생성 결과](media/tutorial-vs-code-serverless-python/display-results-of-new-python-project-in-azure-functions.png)

    Visual Studio Code에서 *\_\_init\_\_.py*를 열 때 Python 인터프리터가 선택되지 않았다는 메시지가 표시되면 명령 팔레트(**F1**)를 열고 **Python: Interpreter 선택** 명령을 선택한 다음, 로컬 `.env` 폴더(프로젝트의 일부로 생성됨)에서 가상 환경을 선택합니다.

    ![Python 프로젝트를 사용하여 만든 가상 환경 선택](media/tutorial-vs-code-serverless-python/select-virtual-environment-created-with-the-python-project.png)

> [!TIP]
> 동일한 프로젝트에서 다른 함수를 만들려는 경우 **함수 만들기** 명령을 사용합니다. 이는 **Azure: Functions** 탐색기에 있습니다. 또는 명령 팔레트(**F1**)를 열고 **Azure Functions: 함수 만들기** 명령을 검색하여 선택합니다. 두 명령 모두 엔드포인트의 이름인 함수 이름을 묻는 메시지를 표시한 다음, 기본 파일을 사용하여 하위 폴더를 만듭니다.
>
> ![Azure Functions 탐색기에서 새 함수를 사용하여 함수 만들기](media/tutorial-vs-code-serverless-python/create-new-functions-in-azure-functions-explorer.png)

> [!div class="nextstepaction"]
> [함수를 만들었습니다. - 3단계 진행 >>>](tutorial-vs-code-serverless-python-03.md)

[문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=02-create-function)
