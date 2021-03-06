---
title: Visual Studio Code를 사용하여 Node.js 개발
description: Visual Studio를 사용하여 JavaScript Node.js 프로젝트를 개발하고 디버그하는 단계를 알아봅니다.
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: devx-track-js
ms.openlocfilehash: 4a0aa42a51d76a8d4aaa9ae703e0ff3aa8b15fc0
ms.sourcegitcommit: 3f8aa923e4626b31cc533584fe3b66940d384351
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99230163"
---
# <a name="how-to-develop-and-debug-nodejs-with-visual-studio-code"></a>Visual Studio Code를 사용하여 Node.js를 개발하고 디버그하는 방법

Visual Studio를 사용하여 JavaScript Node.js 프로젝트를 개발하고 디버그하는 단계를 알아봅니다. 

## <a name="prepare-your-environment"></a>환경 준비

1. [Visual Studio Code](https://code.visualstudio.com/)를 설치합니다. 
1. [git](https://git-scm.com/)을 설치합니다. Visual Studio Code는 git과 통합되어 [사이드바](https://code.visualstudio.com/docs/getstarted/userinterface)에서 *원본 제어* 관리 기능을 제공합니다.

1. mongoDB 데이터베이스 연결 문자열을 가져옵니다.

    mongoDB 데이터베이스를 사용할 수 없는 경우 다음을 수행할 수 있습니다.
    * 컨테이너 중 하나가 mongoDB 데이터베이스인 다중 컨테이너 구성에서 이 로컬 프로젝트를 실행하도록 선택합니다. [Docker](https://www.docker.com/) 및 [원격 컨테이너](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) 확장을 설치하여 로컬 mongoDB 데이터베이스를 실행하는 컨테이너 중 하나로 다중 컨테이너 구성을 가져옵니다. 
    * mongoDB 데이터베이스에 대한 [Azure CosmosDB](/azure/cosmos-db/) 리소스를 만들도록 선택합니다. [이 자습서](../../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md#create-a-cosmosdb-database-resource-for-mongodb)를 통해 자세히 알아봅니다.

## <a name="clone-sample-project-to-local-computer"></a>로컬 컴퓨터에 샘플 프로젝트 복제

시작하려면 다음 단계를 사용하여 샘플 프로젝트를 다운로드합니다.

1. Visual Studio Code를 엽니다.

1. **F1** 키를 눌러 명령 팔레트를 표시합니다.

1. 명령 팔레트 프롬프트에 `gitcl`을 입력하고 **Git: Clone**(Git: 복제) 명령을 선택하고 **Enter** 키를 누릅니다.

    ![Visual Studio Code 명령 팔레트 프롬프트의 gitcl 명령](../../media/node-howto-e2e/visual-studio-code-git-clone.png)

1. **리포지토리 URL** 에 대한 프롬프트가 표시되면 `https://github.com/scotch-io/node-todo`를 입력하고 **Enter** 키를 누릅니다.

1. 프로젝트를 복제할 로컬 디렉터리를 선택하거나 만듭니다.

    ![Visual Studio Code 탐색기](../../media/node-howto-e2e/visual-studio-code-explorer.png)

## <a name="use-the-integrated-bash-terminal-to-install-dependencies"></a>통합 bash 터미널을 사용하여 종속성 설치

이 Node.js 프로젝트를 통해 먼저 모든 프로젝트의 종속성이 npm에서 설치되었는지 확인해야 합니다.  

1. **Ctrl**+ **`** 키를 눌러서 Visual Studio Code 통합 터미널을 표시합니다. 

1. `yarn`을 입력하고 **Enter** 키를 누릅니다.  

     ![Visual Studio Code 내에서 yarn 명령 실행](../../media/node-howto-e2e/visual-studio-code-install-yarn.png)

## <a name="navigate-the-project-files-and-code"></a>프로젝트 파일 및 코드 탐색

코드베이스 내에서 방향을 직접 설정하기 위해 Visual Studio Code에서 제공하는 몇 가지 탐색 기능의 예제를 살펴보겠습니다.

1. **Ctrl**+**P** 를 누릅니다.

1. `.js`를 입력하여 프로젝트의 모든 JavaScript/JSON 파일을 각 파일의 부모 디렉터리와 함께 표시합니다. 

1. 앱에 대한 시작 스크립트인 *server.js* 를 선택합니다.

1. 마우스로 **database**(6번 줄에서 가져옴) 변수 위를 가리켜서 해당 형식을 표시합니다. 파일 내의 변수/모듈/형식을 빠르게 검사하는 이 기능은 프로젝트를 개발하는 동안 유용합니다. 

    ![Visual Studio Code에서 호버 도움말을 사용하여 유형 검색](../../media/node-howto-e2e/visual-studio-code-hover-help.png)

1. **database** 와 같은 변수 범위 내에서 마우스를 클릭하면 동일한 파일 내에서 해당 변수에 대한 모든 참조를 확인할 수 있습니다. 프로젝트 내에서 변수에 대한 모든 참조를 보려면 마우스 오른쪽 단추로 해당 변수를 클릭하고 컨텍스트 메뉴에서 **모든 참조 찾기** 를 선택합니다.

    ![Visual Studio Code를 사용하여 모든 참조 찾기](../../media/node-howto-e2e/visual-studio-code-find-all-references.png)

1. 마우스로 변수 위를 가리켜서 형식을 검색하는 것 외에도 다른 파일에 있는 변수의 정의를 검사할 수 있습니다. 예를 들어, 마우스 오른쪽 단추로 **database.localUrl**(12번 줄)을 클릭하고 컨텍스트 메뉴에서 **정의 피킹** 을 선택합니다.

    ![Visual Studio Code에서 변수 정의 살펴보기](../../media/node-howto-e2e/visual-studio-code-peek-definition.png)

## <a name="use-visual-studio-code-autocompletion-with-mongodb"></a>mongoDB에서 Visual Studio Code 자동 완성 사용

MongoDB 연결 문자열은 `database.localUrl` 속성의 선언에 하드 코딩됩니다. 이 섹션에서는 환경 변수에서 연결 문자열을 검색하도록 코드를 수정하고 Visual Studio Code의 자동 완성 기능에 대해 알아봅니다.  

1. *server.js* 파일을 엽니다.

1. 다음 코드를

    ```javascript
    mongoose.connect(database.localUrl);
    ```

    바꿉니다.

    ```javascript
    mongoose.connect(process.env.MONGODB_URL || database.localUrl);
    ```

코드를 복사하여 붙여넣는 대신 수동으로 입력하는 경우 `process` 뒤에 마침표를 입력하면 Visual Studio Code에 Node.js 프로세스 글로벌 API의 사용 가능한 멤버가 표시됩니다.

![프로세스 env를 사용하는 VS Code 환경 변수](../../media/node-howto-e2e/visual-studio-code-process-env.png)

Visual Studio Code가 백그라운드에서(JavaScript의 경우에도) TypeScript를 사용하여 입력할 때 완성 목록을 알려주는 데 사용할 수 있는 형식 정보를 제공하기 때문에 자동 완성이 작동합니다. Visual Studio Code에서는 이 작업이 Node.js 프로젝트임을 감지할 수 있으며, 이에 따라 [NPM에서 Node.js](https://www.npmjs.com/package/@types/node)에 대한 TypeScript 입력 항목 파일을 자동으로 다운로드했습니다. 입력 항목 파일을 사용하면 `fs` 및 `http`와 같은 모든 기본 제공 모듈뿐만 아니라 `Buffer` 및 `setTimeout`과 같은 다른 Node.js 글로벌에 대한 자동 완성도 얻을 수 있습니다.

기본 제공 Node.js API 외에도 입력 항목의 자동 취득은 2,000개 이상의 타사 모듈(예: React, Underscore 및 Express)에서도 작동합니다. 예를 들어 Mongoose에서 구성된 MongoDB 데이터베이스 인스턴스에 연결할 수 없는 경우 Mongoose에서 정지된 샘플 앱을 해제하려면 13번 줄에 다음 코드 줄을 삽입합니다.

```javascript
mongoose.connection.on("error", () => { console.log("DB connection error"); });
```

이전 코드와 마찬가지로 사용자가 작업하지 않아도 자동 완성을 가져오는지 확인할 수 있습니다.

![API 멤버를 자동으로 표시하는 자동 완성](../../media/node-howto-e2e/visual-studio-code-autocomplete-mongoose.png)

모든 TypeScript 형식 정의의 커뮤니티 기반 소스인 [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) 프로젝트를 탐색하여 이 자동 완성 기능을 지원하는 모듈을 확인할 수 있습니다.

## <a name="running-the-local-nodejs-app"></a>로컬 Node.js 앱 실행

코드에 대해 약간 알아보았으면 앱을 실행할 차례입니다. Visual Studio Code에서 앱을 실행하려면 **F5** 키를 누릅니다. **F5**(디버그 모드) 키를 통해 코드를 실행하면 Visual Studio Code에서 앱을 실행하고, 앱의 stdout(표준 출력)을 보여 주는 **디버그 콘솔** 창을 표시합니다.

![디버그 콘솔을 통한 앱의 stdout 모니터링](../../media/node-howto-e2e/visual-studio-code-debug-console.png)

또한 새로 실행 중인 앱에 **디버그 콘솔** 이 연결되어 있으므로 JavaScript 식을 입력할 수 있습니다. 이 식은 앱에서 평가되며 자동 완성 기능도 포함하고 있습니다. 이 동작을 보려면 콘솔에서 `process.env`를 입력합니다.

![디버그 콘솔에 입력 항목 코드 추가](../../media/node-howto-e2e/visual-studio-code-debug-console-autocomplete.png)

현재 열려 있는 파일이 JavaScript 파일(*server.js*)이므로 **F5** 키를 눌러 앱을 실행할 수 있었습니다. 결과적으로 Visual Studio Code에서는 프로젝트가 Node.js 앱이라고 가정합니다. Visual Studio Code의 모든 JavaScript 파일을 닫은 다음, **F5** 키를 누르면 Visual Studio Code에서 사용자를 다음 환경으로 쿼리합니다.

![런타임 환경 지정](../../media/node-howto-e2e/visual-studio-code-select-environment.png)

브라우저를 열고 `http://localhost:8080`으로 이동하여 실행 중인 앱을 확인합니다. 텍스트 상자에 메시지를 입력하고 몇 가지 할 일을 추가/제거하여 앱이 작동하는 방식을 파악합니다.

![앱을 사용하여 할 일 추가 또는 제거](../../media/node-howto-e2e/add-remove-todos-app.png)

## <a name="debugging-the-local-nodejs-app"></a>로컬 Node.js 앱 디버깅

통합 콘솔을 통해 앱을 실행하고 상호 작용할 수 있을 뿐만 아니라 코드 내에서 중단점을 직접 설정할 수 있습니다. 예를 들어 **Ctrl**+**P** 를 눌러서 파일 선택기를 표시합니다. 파일 선택기가 표시되면 `route`를 입력하고 *route.js* 파일을 선택합니다.

앱에서 할 일 항목을 추가하려고 할 때 호출되는 Express 경로를 나타내는 중단점을 28번 줄에 설정합니다. 중단점을 설정하려면 다음 그림과 같이 편집기 내에서 줄 번호의 왼쪽 영역을 클릭하기만 하면 됩니다.

![Visual Studio Code 내 중단점 설정](../../media/node-howto-e2e/visual-studio-code-set-breakpoint.png)

> [!NOTE]
> Visual Studio Code는 표준 중단점 외에도 앱에서 실행을 일시 중단해야 하는 시점을 사용자 지정할 수 있는 조건부 중단점을 지원합니다. 조건부 중단점을 설정하려면 마우스 오른쪽 단추로 실행을 일시 중지하려는 줄의 왼쪽 영역을 클릭하고, **조건부 중단점 추가** 를 선택한 다음, JavaScript 식(예: `foo = "bar"`) 또는 실행을 일시 중지할 조건을 정의하는 실행 횟수를 지정합니다.

중단점이 설정되면 실행 중인 앱으로 돌아가서 할 일 항목을 추가합니다. 할 일 항목을 추가하는 즉시 중단점을 설정한 28번 줄에서 앱의 실행이 일시 중지됩니다.

![중단점에서 실행이 일시 중지된 Visual Studio Code](../../media/node-howto-e2e/visual-studio-code-pause-breakpoint-execution.png)

애플리케이션이 일시 중지되면 마우스로 코드 식 위를 가리켜서 현재 값을 볼 수 있고, 지역/조사 식 및 호출 스택을 검사하고, 디버그 도구 모음을 사용하여 코드 실행을 단계별로 수행할 수 있습니다. **F5** 키를 눌러 앱 실행을 다시 시작합니다.

## <a name="local-full-stack-debugging-in-visual-studio-code"></a>Visual Studio Code의 로컬 전체 스택 디버깅

이 항목의 앞부분에서 설명했듯이 할 일 앱은 MEAN 앱입니다. 즉 프런트 엔드와 백 엔드 코드가 모두 JavaScript를 사용하여 작성되었습니다. 따라서 현재 백 엔드(Node/Express) 코드를 디버그하고 있지만 어느 시점에서 프런트 엔드(Angular) 코드를 디버그해야 할 수도 있습니다. 이러한 목적을 위해 Visual Studio Code는 통합된 Chrome 디버깅을 포함하여 광범위한 확장 에코시스템을 갖추고 있습니다.

**확장** 탭으로 전환하고 검색 상자에 `chrome`을 입력합니다.

![Visual Studio Code용 Chrome 디버깅 확장](../../media/node-howto-e2e/visual-studio-code-chrome-extension.png)

**Chrome용 디버거** 를 선택하고 **설치** 를 선택합니다. Chrome 디버깅 확장을 설치한 후 **다시 로드** 를 선택하여 확장을 활성화하기 위해 Visual Studio Code를 닫고 다시 엽니다.

![Chrome 디버깅 확장을 설치한 후 Visual Studio Code 다시 로드](../../media/node-howto-e2e/visual-studio-code-reload-extension.png)

Visual Stdio 코드 관련 구성 없이 Node.js 코드를 실행하고 디버그할 수 있었지만, 프런트 엔드 웹앱을 디버그하려면 앱을 실행하는 방법을 Visual Studio Code에 지시하는 *launch.json* 파일을 생성해야 합니다.

## <a name="create-a-full-stack-launchjson-file-for-visual-studio-code"></a>Visual Studio Code용 전체 스택 launch.json 파일 만들기

*launch.json* 파일을 생성하려면 **디버그** 탭으로 전환하고 톱니 바퀴 아이콘(위쪽에 빨간색 점이 없어야 함)을 선택한 다음, **node.js** 환경을 선택합니다.

![launch.json 파일을 구성하는 Visual Studio Code 옵션](../../media/node-howto-e2e/visual-studio-code-debug-gear.png)

일단 만들어지면 *launch.json* 파일은 다음과 비슷하며, 디버그하기 위해 앱을 시작 및/또는 연결하는 방법을 Visual Studio Code에 알려줍니다.

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Launch Program",
            "program": "${workspaceRoot}/server.js"
        },
        {
            "type": "node",
            "request": "attach",
            "name": "Attach to Port",
            "address": "localhost",
            "port": 5858
        }
    ]
}
```

Visual Studio Code는 앱의 시작 스크립트가 *server.js* 임을 감지할 수 있었습니다.

*launch.json* 파일을 연 상태에서 **구성 추가**(오른쪽 아래)를 선택하고 **Chrome: Launch with userDataDir**(Chrome: userDataDir로 시작)을 선택합니다.

![Visual Studio Code에 Chrome 구성 추가](../../media/node-howto-e2e/visual-studio-code-add-chrome-config.png)

Chrome에 새 실행 구성을 추가하면 프런트 엔드 JavaScript 코드를 디버그할 수 있습니다. 

마우스로 지정된 설정 위를 가리키면 설정에서 수행하는 작업에 대한 설명서를 볼 수 있습니다. 또한 Visual Studio Code는 앱의 URL을 자동으로 감지합니다. **webRoot** 속성을 `${workspaceRoot}/public`으로 업데이트하여 Chrome 디버거에서 앱의 프런트 엔드 자산을 찾을 위치를 인식할 수 있게 합니다.

```json
{
   "type": "chrome",
   "request": "launch",
   "name": "Launch Chrome",
   "url": "http://localhost:8080",
   "webRoot": "${workspaceRoot}/public",
   "userDataDir": "${workspaceRoot}/.vscode/chrome"
}
```

프런트 엔드와 백 엔드 모두를 동시에 시작하고 디버그하려면 동시에 실행할 구성 세트를 Visual Studio Code에 알려주는 *복합* 실행 구성을 만들어야 합니다.

다음 코드 조각을 *launch.json* 파일 내의 최상위 수준 속성(기존 **configurations** 속성의 형제)으로 추가합니다.

```json
"compounds": [
   {
      "name": "Full-Stack",
      "configurations": ["Launch Program", "Launch Chrome"]
   }
]
```

**compounds.configurations** 배열에 지정된 문자열 값은 **configurations** 목록에 있는 개별 항목의 **name** 을 참조합니다. 이러한 이름을 수정한 경우 배열을 적절히 변경해야 합니다. 예를 들어, 디버그 탭으로 전환하고, 선택한 구성을 **Full-Stack**(복합 구성의 이름)으로 변경한 다음, **F5** 키를 눌러 해당 구성을 실행합니다.

![Visual Studio Code에서 구성 실행](../../media/node-howto-e2e/visual-studio-code-full-stack-configuration.png)

구성을 실행하면 Node.js 앱(디버그 콘솔 출력에서 볼 수 있는 것과 동일함)과 Chrome(`http://localhost:8080`에서 Node.js 앱으로 이동하도록 구성됨)이 시작됩니다.

**Ctrl**+**P** 를 누르고 앱의 프런트 엔드에 대한 주 Angular 컨트롤러인 *todos.js* 를 입력하거나 선택합니다.

11번 줄에 중단점을 설정합니다. 이 중단점은 만들려는 새 할 일 항목에 대한 진입점입니다.

실행 중인 앱으로 돌아가서 새 할 일 항목을 추가하고, 이제 Angular 코드 내에서 일시 중단된 실행이 Visual Studio Code에 있는지 확인합니다.

![Visual Studio Code에서 프런트 엔드 코드 디버깅](../../media/node-howto-e2e/visual-studio-code-chrome-pause.png)

Node.js 디버깅과 마찬가지로 마우스로 식 위를 가리키고, 지역/조사 식을 보고, 콘솔에서 식을 평가하는 등의 작업을 수행할 수 있습니다. 

유의해야 할 두 가지 사항이 있습니다.

1. **호출 스택** 창에는 서로 다른 두 가지 스택, **Node** 및 **Chrome** 이 표시되며, 현재 일시 중지된 스택을 나타내고 있습니다.

1. 프런트 엔드 코드와 백 엔드 코드 사이에서 단계별로 실행할 수 있습니다. **F5** 키를 누르면 실행이 시작되고 Express 경로에서 이전에 설정한 중단점에 도달합니다.

이 설정을 사용하면 이제 Visual Studio Code 내에서 프런트 엔드, 백 엔드 또는 전체 스택 JavaScript 코드를 효율적으로 직접 디버그할 수 있습니다.

또한 복합 디버거 개념은 두 가지 대상 프로세스만으로 제한되지 않고 JavaScript만으로도 제한되지 않습니다. 따라서 마이크로 서비스 앱(잠재적으로 다국어 사용)에서 작동하는 경우 언어/프레임워크에 적절한 확장을 설치하면 똑같은 워크플로를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 배포](../deploy-containers.md)
