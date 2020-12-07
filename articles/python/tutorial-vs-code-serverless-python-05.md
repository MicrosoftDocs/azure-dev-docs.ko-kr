---
title: '5단계: VS Code를 사용하여 Python에서 서버리스 Azure Functions 배포'
description: 자습서 5단계로, Azure에 Python 서버리스 함수 코드를 배포하고 로컬 프로젝트와 Azure 간에 로그를 스트리밍하고 설정을 동기화하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 294ecb106ab55c87e0191f5a0df807da89a8acee
ms.sourcegitcommit: 709fa38a137b30184a7397e0bfa348822f3ea0a7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96442212"
---
# <a name="5-deploy-azure-functions-in-python"></a>5: Python에서 Azure Functions 배포

[이전 단계: 로컬에서 디버그](tutorial-vs-code-serverless-python-04.md)

함수를 Azure에 배포한다는 것은 다른 필수 Azure 리소스와 함께 Azure에 Functions 앱을 만드는 것을 의미합니다. 함수 앱을 통해 함수를 논리 단위로 그룹화하여 더욱 쉽게 관리, 배포 및 리소스 공유할 수 있습니다.

또한 함수 앱을 사용하려면 데이터 및 [호스팅 계획](/azure/azure-functions/functions-scale#hosting-plan-support)에 대한 Azure Storage 계정이 필요합니다. 이러한 리소스는 모두 단일 리소스 그룹 내에서 구성됩니다.

1. **Azure: Functions** 탐색기에서 **함수 앱에 배포** 명령을 선택하거나 명령 팔레트(**F1**)를 열고 **Azure Functions: 함수 앱에 배포** 명령을 선택합니다. 또한 함수 앱은 Python 프로젝트가 Azure에서 실행되는 위치입니다.

    ![Azure 함수 앱에 Python 함수 배포](media/tutorial-vs-code-serverless-python/deploy-a-python-fuction-to-azure-function-app.png)

1. 프롬프트(**Azure에서 함수 앱 선택**)가 표시되면 **Azure에서 새 함수 앱 만들기** 를 선택하고 Azure에서 고유한 이름을 입력합니다(일반적으로 개인 또는 회사 이름을 다른 고유 식별자와 함께 사용하며, 문자, 숫자 및 하이픈을 사용할 수 있음).

    이전에 함수 앱을 만든 경우 이 옵션 목록에 해당 이름이 표시됩니다.

1. 다음 두 개의 프롬프트에서 Python 버전과 Azure 위치를 선택합니다.

1. 확장은 다음과 같은 작업을 수행합니다. 이 작업은 Visual Studio Code 팝업 메시지와 **출력** 창에서 확인할 수 있습니다(프로세스에 몇 분이 소요됨).

    - 선택한 위치에서 지정한 이름(하이픈 제거)을 사용하여 리소스 그룹을 만듭니다.
    - 해당 리소스 그룹에서 스토리지 계정, 호스팅 계획 및 함수 앱을 만듭니다. Azure Functions는 기본값으로 [사용량 요금제](/azure/azure-functions/functions-scale#consumption-plan)를 사용합니다. 전용 계획에서 함수를 실행하려면 [고급 만들기 옵션을 사용하여 게시를 사용하도록 설정](/azure/azure-functions/functions-develop-vs-code)해야 합니다.
    - 함수 앱에 코드를 추가합니다.

    **Azure: Functions** 탐색기에도 진행률이 표시됩니다.

    ![Azure: Functions 탐색기의 배포 진행률 표시기](media/tutorial-vs-code-serverless-python/deployment-progress-indicator-in-azure-function-explorer.png)

1. 배포가 완료되면 다음 세 가지 추가 작업에 대한 단추가 포함된 메시지가 Azure Functions 확장에 표시됩니다.

    ![추가 작업을 사용하여 성공적인 배포를 나타내는 메시지](media/tutorial-vs-code-serverless-python/azure-functions-deployment-success-with-additional-actions.png)

    **로그 스트리밍** 및 **설정 업로드** 의 경우 다음 섹션을 참조하세요.

1. **출력 보기** 를 선택하여 **출력** 창으로 전환합니다. 출력은 Azure의 퍼블릭 엔드포인트를 표시합니다(특정 엔드포인트의 URL은 함수 앱에 지정한 이름과 일치함).

    <pre>
    1:38:04 PM vscode-azure-functions: HTTP Trigger Urls:
      HttpExample: https://vscode-azure-functions.azurewebsites.net/api/HttpExample
    </pre>

    이 엔드포인트를 사용하여 요청 본문에서 JSON 데이터를 포함하는 URL 매개 변수 및/또는 요청을 사용하여 로컬에서 수행한 동일한 테스트를 실행합니다. 공용 엔드포인트의 결과는 [4부](tutorial-vs-code-serverless-python-04.md)에서 이전에 테스트한 로컬 엔드포인트의 결과와 일치해야 합니다.

## <a name="examine-logs-live-metrics"></a>로그 검사(라이브 메트릭)

배포 메시지 팝업의 **스트림 로그** 단추를 누르면 함수에 대한 Azure Portal의 **라이브 메트릭** 섹션으로 브라우저를 엽니다. 함수 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **로그 스트리밍 시작** 을 선택하여 **Azure Functions** 탐색기에서 메트릭에 연결할 수도 있습니다.

## <a name="sync-local-settings-to-azure"></a>Azure에 로컬 설정 동기화

배포 메시지 팝업의 **설정 업로드** 단추는 *local.settings.json* 파일에 대한 변경 내용을 Azure에 적용합니다. 또한 Functions 프로젝트 노드를 확장하고 **애플리케이션 설정** 을 마우스 오른쪽 단추로 클릭하고 **로컬 설정 업로드** 를 선택하여 **Azure Functions** 탐색기에서 명령을 호출할 수 있습니다. 명령 팔레트를 사용하여 **Azure Functions: 로컬 설정 업로드** 명령을 선택합니다.

설정을 업로드하면 기존 설정이 업데이트되고 *local.settings.json* 에 정의된 새 설정이 추가됩니다. 업로드해도 로컬 파일에 나열되지 않은 Azure의 설정은 제거되지 않습니다. 이러한 설정을 제거하려면 **Azure Functions** 탐색기에서 **애플리케이션 설정** 노드를 확장하고 설정을 마우스 오른쪽 단추로 클릭한 다음, **설정 삭제** 를 선택합니다. Azure Portal에서 직접 설정을 편집할 수도 있습니다.

포털을 통해 또는 **Azure Explorer** 를 통해 변경된 내용을 *local.settings.json* 파일에 적용하려면 **애플리케이션 설정** 노드를 마우스 오른쪽 단추로 클릭하고 **원격 설정 다운로드** 명령을 선택합니다. 명령 팔레트를 사용하여 **Azure Functions: 원격 설정 다운로드** 명령을 선택할 수 있습니다.

> [!div class="nextstepaction"]
> [함수를 배포했습니다. - 6단계 진행 >>>](tutorial-vs-code-serverless-python-06.md)

[문제가 있나요? 알려주세요.](https://aka.ms/python-functions-qs-ms-survey)
