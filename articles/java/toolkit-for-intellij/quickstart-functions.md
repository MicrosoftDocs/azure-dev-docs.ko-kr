---
title: Azure Functions에서 IntelliJ IDEA를 사용하여 첫 번째 서버리스 함수 만들기
description: Azure Toolkit for IntelliJ를 사용하여 간단한 HTTP 트리거 함수를 Azure에 만들고 게시합니다.
ms.topic: quickstart
ms.date: 03/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: d796d6974d9591ae9df79d37c48f47d7e1cb5a72
ms.sourcegitcommit: 5541f993c01ce356e1b0eaa8f95aea9051c3c21e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93278468"
---
# <a name="quickstart-create-an-azure-functions-project-using-intellij-idea"></a>빠른 시작: IntelliJ IDEA를 사용하여 Azure Functions 프로젝트 만들기

이 문서에서는 IntelliJ IDEA를 사용하여 HTTP 요청에 응답하는 함수를 만듭니다. 코드를 로컬로 테스트한 후 서버리스 Azure Functions 환경에 배포합니다. 이 빠른 시작을 완료하면 Azure 계정에서 USD 센트 이하의 작은 비용이 발생합니다.

## <a name="configure-your-environment"></a>환경 구성

시작하기 전에 다음 요구 사항을 갖추었는지 확인합니다.

+ 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
+ Java 8용 [Azure 지원 JDK(Java Development Kit)](../fundamentals/java-jdk-long-term-support.md)
+ [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition 또는 Community Edition 설치
+ [Maven 3.5.0+](https://maven.apache.org/download.cgi)
+ 최신 [Function Core Tools](https://github.com/Azure/azure-functions-core-tools)

## <a name="installation-and-sign-in"></a>설치 및 로그인

1. IntelliJ IDEA의 설정/기본 설정 대화 상자(Ctrl+Alt+S)에서 **플러그인** 을 선택합니다. 그런 다음, **Marketplace** 에서 **Azure Toolkit for IntelliJ** 를 찾고 **설치** 를 클릭합니다. 설치가 완료된 후 **다시 시작** 을 클릭하여 플러그인을 활성화합니다. 

   ![Marketplace의 Azure Toolkit for IntelliJ][marketplace]

2. Azure 계정에 로그인하려면 사이드바 **Azure Explorer** 를 연 다음, 위쪽 막대에서(또는 IDEA 메뉴 **도구/Azure/Azure 로그인** 에서) **Azure 로그인** 아이콘을 클릭합니다.

   ![IntelliJ Azure 로그인 명령][I01]

3. **Azure 로그인** 창에서 **디바이스 로그인** 을 선택하고 **로그인** ( [다른 로그인 옵션](sign-in-instructions.md))을 클릭합니다.

   ![디바이스 로그인을 선택한 Azure 로그인 창][I02]

4. **Azure 디바이스 로그인** 대화 상자에서 **복사 및 열기** 를 클릭합니다.

   ![Azure 로그인 대화 상자 창][I03]

5. 브라우저에서, 마지막 단계에서 **복사 및 열기** 를 클릭할 때 복사한 디바이스 코드를 붙여넣은 후 **다음** 을 클릭합니다.

   ![디바이스 로그인 브라우저][I04]

6. **구독 선택** 대화 상자에서 사용하려는 구독을 선택한 다음, **확인** 을 클릭합니다.

   ![구독 선택 대화 상자][I05]

## <a name="create-your-local-project"></a>로컬 프로젝트 만들기

이 섹션에서는 Azure Toolkit for IntelliJ를 사용하여 로컬 Azure Functions 프로젝트를 만듭니다. 이 문서의 뒷부분에서 함수 코드를 Azure에 게시합니다. 

1. IntelliJ 시작 대화 상자를 열고 *새 프로젝트 만들기* 를 선택하여 새 프로젝트 마법사를 열고 *Azure Functions* 를 선택합니다.

    ![Functions 프로젝트 만들기](media/quickstart-functions/create-functions-project.png)

1. *Http 트리거* 를 선택한 후 *다음* 을 클릭하고 마법사에 따라 다음 페이지의 모든 구성을 진행합니다. 프로젝트 위치를 확인한 다음, *마침* 을 클릭합니다. 그러면 Intellj IDEA에 새 프로젝트가 열립니다.

    ![Functions 프로젝트 만들기 완료](media/quickstart-functions/create-functions-project-finish.png)

## <a name="run-the-function-app-locally"></a>함수 앱을 로컬로 실행

1. `src/main/java/org/example/functions/HttpTriggerFunction.java`로 이동하여 생성된 코드를 확인합니다. 줄 *17* 옆에 녹색 *실행* 단추가 있는지 확인하고 클릭한 후 *'azure-function-exam...' 실행* 을 선택하면 몇 가지 로그로 함수 앱이 로컬에서 실행 중임을 확인할 수 있습니다.

    ![로컬 실행 함수 프로젝트](media/quickstart-functions/local-run-functions-project.png)

    ![로컬 실행 함수 출력](media/quickstart-functions/local-run-functions-output.png)

1. 브라우저에서 출력된 엔드포인트(예: `http://localhost:7071/api/HttpTrigger-Java?name=Azure`)에 액세스하여 함수를 사용해 볼 수 있습니다.

    ![로컬 실행 함수 테스트 결과](media/quickstart-functions/local-run-functions-test.png)

1. 로그는 IDEA에서도 출력됩니다. 이제 *중지* 단추를 클릭하여 함수를 중지합니다.

    ![로컬 실행 함수 테스트 로그](media/quickstart-functions/local-run-functions-log.png)

## <a name="debug-the-function-app-locally"></a>함수 앱을 로컬에서 디버그

1. 이제 함수 앱을 로컬에서 디버그해 보겠습니다. 도구 모음에서 *디버그* 단추를 클릭합니다. 도구 모음이 보이지 않는다면 *보기 -> 모음 -> 도구 모음* 을 클릭하여 도구 모음을 사용하도록 설정합니다.

    ![로컬 디버그 함수 단추](media/quickstart-functions/local-debug-functions-button.png)

1. `src/main/java/org/example/functions/HttpTriggerFunction.java` 파일의 줄 *20* 을 클릭하여 중단점을 추가하고 엔드포인트 `http://localhost:7071/api/HttpTrigger-Java?name=Azure`에 액세스하면 중단점에 도달한 것을 확인할 수 있습니다. *한 단계 실행* , *조사식* , *평가* 같은 더 많은 디버그 기능을 사용해 볼 수 있습니다. 중지 단추를 클릭하여 디버그 세션을 중지합니다.

    ![로컬 디버그 함수 중단](media/quickstart-functions/local-debug-functions-break.png)

## <a name="deploy-your-function-app-to-azure"></a>Azure에 함수 앱 배포

1. IntelliJ 프로젝트 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 *Azure -> Azure Functions에 배포* 를 선택합니다.

    ![Azure에 함수 배포](media/quickstart-functions/deploy-functions-to-azure.png)

1. 함수 앱이 아직 없는 경우 *사용 가능한 함수 없음을 클릭하여 새로 만듭니다*.

    ![Azure 만들기 앱에 함수 배포](media/quickstart-functions/deploy-functions-create-app.png)

1. 함수 앱 이름을 입력하고 적절한 구독/플랫폼/리소스 그룹/App Service 계획을 선택합니다. 여기에서 리소스 그룹/App Service 계획을 만들 수도 있습니다. 그런 다음, 앱 설정을 그대로 유지하고 *확인* 을 클릭한 다음, 새 함수가 생성될 때까지 몇 분 정도 기다립니다. *새 함수 앱 만들기...* 진행률 표시줄이 사라지면 완료된 것입니다.

    ![Azure 만들기 앱에 함수 배포 마법사](media/quickstart-functions/deploy-functions-create-app-wizard.png)

1. 배포하려는 함수 앱을 선택합니다(방금 만든 새 함수 앱이 자동으로 선택됨). *실행* 을 클릭하여 함수를 배포합니다.

    ![Azure에 함수 배포 실행](media/quickstart-functions/deploy-functions-run.png)

    ![Azure에 함수 배포 로그](media/quickstart-functions/deploy-functions-log.png)

## <a name="manage-azure-functions-from-idea"></a>IDEA에서 Azure Functions 관리

1. IDEA에서 *Azure Explorer* 를 사용하여 함수를 관리할 수 있습니다. *함수 앱* 을 클릭하면 모든 함수가 여기에 표시됩니다.

    ![탐색기에서 함수 보기](media/quickstart-functions/explorer-view-functions.png)

1. 함수 중 하나를 클릭하여 선택하고 마우스 오른쪽 단추를 클릭한 다음, *속성 표시* 를 선택하여 세부 정보 페이지를 엽니다. 

    ![함수 속성 표시](media/quickstart-functions/explorer-functions-show-properties.png)

1. *HttpTrigger-Java* 함수를 마우스 오른쪽 단추로 클릭하고 *트리거 함수* 를 선택하면 브라우저에 트리거 URL이 열린 것을 볼 수 있습니다.

    ![Azure에 함수 배포 실행](media/quickstart-functions/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>프로젝트에 함수 추가

1. *org.example.functions* 패키지를 마우스 오른쪽 단추로 클릭하고 *새로 만들기-> Azure 함수 클래스* 를 선택합니다. 

    ![프로젝트 항목에 함수 추가](media/quickstart-functions/add-functions-entry.png)

1. 클래스 이름 *HttpTest* 를 입력하고 함수 클래스 만들기 마법사에서 *HttpTrigger* 를 선택한 후 *확인* 을 클릭하면 원하는 대로 새 함수를 만들 수 있습니다.

    ![프로젝트 선택 트리거에 함수 추가](media/quickstart-functions/add-functions-trigger.png)
    
    ![프로젝트 출력에 함수 추가](media/quickstart-functions/add-functions-output.png)

## <a name="cleaning-up-functions"></a>Functions 정리

1. Azure 탐색기에서 Azure Functions 삭제
      
      ![프로젝트 선택 트리거에 함수 추가](media/quickstart-functions/delete-function.png)
      

## <a name="next-steps"></a>다음 단계

HTTP 트리거 함수를 사용하여 Java 함수 프로젝트를 만들고, 로컬 머신에서 실행하고, Azure에 배포했습니다. 이제 아래 방법으로 함수를 확장하겠습니다.

> [!div class="nextstepaction"]
> [Azure Storage 큐 출력 바인딩 추가](/azure/azure-functions/functions-add-output-binding-storage-queue-java)


[marketplace]:./media/create-hello-world-web-app/marketplace.png
[I01]: media/sign-in-instructions/I01.png
[I02]: media/sign-in-instructions/I02.png
[I03]: media/sign-in-instructions/I03.png
[I04]: media/sign-in-instructions/I04.png
[I05]: media/sign-in-instructions/I05.png