---
title: '2단계: VS Code를 사용하여 Azure Functions에 대한 서버리스 Python 함수 만들기'
description: 자습서 2단계, VS Code에 대한 Azure Functions 확장을 사용하여 서버리스 Python 함수를 추가합니다.
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, seo-python-october2019, contperfq2
ms.openlocfilehash: ac9bf8f90399a5ad2421689104fd5a6d9772c462
ms.sourcegitcommit: 0cda024089784b92c1db3a4506c1dccd6bfe6339
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96759310"
---
# <a name="2-create-a-python-function-for-azure-functions"></a>2: Azure Functions에 대한 Python 함수 만들기

[이전 단계: 환경 구성](tutorial-vs-code-serverless-python-01.md)

이 문서에서는 Visual Studio Code를 사용하여 서버리스 Azure Functions에 대한 Python 함수를 만듭니다. Azure Functions에 대한 코드는 코드 생성 전에 먼저 만드는 Functions _프로젝트_ 내에서 관리됩니다.

1. **Azure: Functions** 탐색기(왼쪽의 Azure 아이콘을 사용하여 열림)에서 **새 프로젝트 만들기** 명령 아이콘을 선택하거나 명령 팔레트(F1)를 열고 **Azure Functions: 새 프로젝트 만들기** 를 선택합니다.

    ![Azure Functions 탐색기에서 새 프로젝트 만들기](media/tutorial-vs-code-serverless-python/create-a-new-project-in-azure-functions-explorer.png)

1. 아래 프롬프트에서:

    | prompt | 값 | 설명 |
    | --- | --- | --- |
    | 함수 프로젝트를 포함할 폴더 선택 | 현재 열려 있는 폴더 | 프로젝트를 만들 폴더입니다. 하위 폴더에서 프로젝트를 만들 수 있습니다. |
    | 언어 선택 | **Python** | 코드에 사용되는 템플릿을 결정하는 함수에 사용할 언어입니다. |
    | 가상 환경을 만들 Python 인터프리터 선택 | 제공된 기본 경로를 사용하거나 적절한 인터프리터에 대한 경로를 수동으로 입력합니다(제공된 기본 경로가 없을 경우). | 가상 환경에 사용할 Python 인터프리터입니다. |
    | 프로젝트의 첫 번째 함수에 대한 템플릿 선택 | **HTTP 트리거** | HTTP 트리거를 사용하는 함수는 함수 엔드포인트에 대한 HTTP 요청이 있을 때마다 실행됩니다. (Azure Functions에 대한 다양한 트리거가 있습니다. 자세히 알아보려면 [Functions로 할 수 있는 작업은 무엇인가요?](/azure/azure-functions/functions-overview#what-can-i-do-with-functions)를 참조하세요.) |
    | 함수 이름 제공 | HttpExample | 이름은 구성 데이터와 함께 함수 코드를 포함하는 하위 폴더에 사용되며 HTTP 엔드포인트의 이름도 정의합니다. 기본 "HTTPTrigger1"을 허용하는 대신 "HttpExample"을 사용하여 트리거에서 함수 자체를 구분합니다. |
    | 권한 부여 수준 | **익명** | 익명 권한 부여는 누구나 공개적으로 함수에 액세스할 수 있습니다. |

1. 이 프로세스는 탐색기에 표시되는 **Local Project** 항목을 만듭니다. 그 아래에 **함수** 라는 컬렉션이 있으며, 지정한 이름의 함수가 포함됩니다(여기서는 **HttpExample**).

    ![Azure Functions의 새 Python 프로젝트의 탐색기 보기](media/tutorial-vs-code-serverless-python/explorer-view-new-python-project-in-azure-functions.png)

    Visual Studio Code에서 *\_\_init\_\_.py* 를 열 때 Python 인터프리터가 선택되지 않았다는 메시지가 표시되면 명령 팔레트(**F1**)를 열고 **Python: Interpreter 선택** 명령을 선택한 다음, 로컬 `.venv` 폴더(프로젝트의 일부로 생성됨)에서 가상 환경을 선택합니다.

    ![Python 프로젝트를 사용하여 만든 가상 환경 선택](media/tutorial-vs-code-serverless-python/select-virtual-environment-created-with-the-python-project.png)

1. VS Code 편집기는 기본 함수 코드를 포함하는 *\_\_init\_\_.py* 파일에서도 열립니다. 편집기 상단에서 **HttpExample> __init.py__** 컨트롤을 확인합니다.

    ![VS Code의 함수에 대한 파일 선택기 컨트롤](media/tutorial-vs-code-serverless-python/file-selector-in-azure-functions-editor-01.png)

    **__init.py__** 를 선택하는 경우, VS Code는 함수를 구성하는 모든 파일을 표시합니다. *\_\_init\_\_.py* 에는 코드, *function.json* 에는 구성, *sample.dat* 에는 샘플 데이터가 포함되어 있습니다. 파일을 선택하여 전환할 수 있습니다.

    ![함수 코드 파일에 대한 파일 선택기 컨트롤](media/tutorial-vs-code-serverless-python/file-selector-in-azure-functions-editor-02.png)

    마찬가지로 **HttpExample** 을 선택하면 VS Code가 모든 *프로젝트* 수준 파일 및 폴더를 표시합니다.

    ![VS Code의 함수에 대한 프로젝트 선택기 컨트롤](media/tutorial-vs-code-serverless-python/file-selector-in-azure-functions-editor-03.png)

> [!TIP]
> 동일한 프로젝트에서 다른 함수를 만들려는 경우 **함수 만들기** 명령을 사용합니다. 이는 **Azure: Functions** 탐색기에 있습니다. 또는 명령 팔레트(**F1**)를 열고 **Azure Functions: 함수 만들기** 명령을 검색하여 선택합니다. 두 명령 모두 함수 이름(엔드포인트의 이름)을 입력하라는 메시지를 표시한 다음, 자체 *\_\_init\_\_.py*, *function.json* 및 *sample.dat* 파일과 함께 프로젝트에 다른 함수 하위 폴더를 만듭니다.
>
> ![Azure Functions 탐색기에서 새 함수를 사용하여 함수 만들기](media/tutorial-vs-code-serverless-python/create-new-functions-in-azure-functions-explorer.png)

> [!div class="nextstepaction"]
> [함수를 만들었습니다. - 3단계 진행 >>>](tutorial-vs-code-serverless-python-03.md)

[문제가 있나요? 알려주세요.](https://aka.ms/python-functions-qs-ms-survey)
