---
title: VS Code 및 Azure를 사용하여 컨테이너화된 Node.js 앱 배포
description: Azure에 Node.js 앱을 만들고, 컨테이너화하고, 배포하는 방법을 보여주는 완벽한 엔드투엔드 자습서
ms.topic: how-to
ms.date: 06/25/2017
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js, devx-track-azurecli
ms.openlocfilehash: e549ef0cf5baaf003788a55bf2549e9c3f2fbe5c
ms.sourcegitcommit: 0eb25e1fdafcd64118843748dc061f60e7e48332
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98626056"
---
# <a name="develop-and-deploy-a-containerized-nodejs-app-with-visual-studio-code-and-azure"></a>Visual Studio Code 및 Azure를 사용하여 컨테이너화된 Node.js 앱 개발 및 배포

이 자습서에서는 기존 Node.js 앱을 가져와서 "컨테이너화"한(Docker 사용) 다음 Visual Studio Code를 사용하여 Azure에 앱을 배포하는 방법을 보여 줍니다.

이 자습서에서는 [Scotch.io](https://scotch.io/tutorials/creating-a-single-page-todo-app-with-node-and-angular)를 통해 만들고 게시하는 간단한 할 일 앱을 사용합니다. 단일 페이지 MEAN 앱이므로 MongoDB를 데이터베이스로 사용하고, REST API/웹 서버의 경우 Node/Express를 사용하고, 프런트 엔드 UI의 경우 Angular.js 1.x를 사용합니다. 

## <a name="prerequisites"></a>사전 요구 사항

데모와 함께 수행하려면 다음 소프트웨어가 설치되어 있어야 합니다.

- [Visual Studio Code](https://code.visualstudio.com/)
- [Docker](https://www.docker.com/products/docker)
- [Docker Hub 계정](https://hub.docker.com/)
- [Azure CLI 2.0](/cli/azure/install-az-cli2)
- [Azure 계정](https://azure.microsoft.com/free/)
- [Yarn](https://yarnpkg.com/en/docs/install)
- [Chrome](https://www.google.com/chrome/browser/desktop/) - 데모 앱의 프런트 엔드 디버깅에 사용됩니다.
- MongoDB - 데모 앱에서 MongoDB를 사용하므로 표준 `27017` 포트에서 수신 대기 중인 MongoDB 인스턴스가 로컬로 실행되고 있어야 합니다. MongoDB를 로컬에서 실행하는 가장 간단한 방법은 Docker를 설치한 후에 `docker pull mongo` 명령에 이어 `docker run -it -p 27017:27017 mongo` 명령을 실행하는 것입니다.

## <a name="project-setup"></a>프로젝트 설정

시작하려면 다음 단계를 사용하여 샘플 프로젝트를 다운로드합니다.

1. Visual Studio Code를 엽니다.

1. **F1** 키를 눌러 명령 팔레트를 표시합니다.

1. 명령 팔레트 프롬프트에 `gitcl`을 입력하고 **Git: Clone**(Git: 복제) 명령을 선택하고 **Enter** 키를 누릅니다.

    ![Visual Studio Code 명령 팔레트 프롬프트의 gitcl 명령](../media/node-howto-e2e/visual-studio-code-git-clone.png)

1. **리포지토리 URL** 에 대한 프롬프트가 표시되면 `https://github.com/scotch-io/node-todo`를 입력하고 **Enter** 키를 누릅니다.

1. 프로젝트를 복제할 로컬 디렉터리를 선택하거나 만듭니다.

    ![Visual Studio Code 탐색기](../media/node-howto-e2e/visual-studio-code-explorer.png)

## <a name="integrated-terminal"></a>통합 터미널

이 Node.js 프로젝트를 통해 먼저 모든 프로젝트의 종속성이 npm에서 설치되었는지 확인해야 합니다.  

1. **Ctrl**+ **`** 키를 눌러서 Visual Studio Code 통합 터미널을 표시합니다. 

1. `yarn`을 입력하고 **Enter** 키를 누릅니다.  

     ![Visual Studio Code 내에서 yarn 명령 실행](../media/node-howto-e2e/visual-studio-code-install-yarn.png)

## <a name="integrated-git-version-control"></a>통합된 Git 버전 제어

Yarn을 통해 앱의 종속성을 설치한 후 나중에 조금도 놀라지 않고 CI(지속적인 통합) 빌드, 프로덕션 배포 또는 다른 개발자 머신 중 하나에서 똑같은 종속성을 다시 가져오는 예측 가능한 방법을 제공하는 *yarn.lock* 파일이 만들어집니다.

다음 단계에서는 *yarn.lock* 파일을 소스 제어에 체크 인하는 방법을 보여줍니다.

1. Visual Studio Code 내에서 통합된 Git 탭(Git 로고가 있는 탭)으로 전환합니다.

1. **메시지** 상자에서 커밋 메시지를 입력하고 **Ctrl**+**Enter** 를 누릅니다.

    ![Git에 yarn.lock 파일 추가](../media/node-howto-e2e/visual-studio-code-add-yarn-lock.png)

## <a name="project-and-code-navigation"></a>프로젝트 및 코드 탐색

코드베이스 내에서 방향을 직접 설정하기 위해 Visual Studio Code에서 제공하는 몇 가지 탐색 기능의 예제를 살펴보겠습니다.

1. **Ctrl**+**P** 를 누릅니다.

1. `.js`를 입력하여 프로젝트의 모든 JavaScript/JSON 파일을 각 파일의 부모 디렉터리와 함께 표시합니다. 

1. 앱에 대한 시작 스크립트인 *server.js* 를 선택합니다.

1. 마우스로 **database**(6번 줄에서 가져옴) 변수 위를 가리켜서 해당 형식을 표시합니다. 파일 내의 변수/모듈/형식을 빠르게 검사하는 이 기능은 프로젝트를 개발하는 동안 유용합니다. 

    ![Visual Studio Code에서 호버 도움말을 사용하여 유형 검색](../media/node-howto-e2e/visual-studio-code-hover-help.png)

1. **database** 와 같은 변수 범위 내에서 마우스를 클릭하면 동일한 파일 내에서 해당 변수에 대한 모든 참조를 확인할 수 있습니다. 프로젝트 내에서 변수에 대한 모든 참조를 보려면 마우스 오른쪽 단추로 해당 변수를 클릭하고 컨텍스트 메뉴에서 **모든 참조 찾기** 를 선택합니다.

    ![Visual Studio Code를 사용하여 모든 참조 찾기](../media/node-howto-e2e/visual-studio-code-find-all-references.png)

1. 마우스로 변수 위를 가리켜서 형식을 검색하는 것 외에도 다른 파일에 있는 변수의 정의를 검사할 수 있습니다. 예를 들어, 마우스 오른쪽 단추로 **database.localUrl**(12번 줄)을 클릭하고 컨텍스트 메뉴에서 **정의 피킹** 을 선택합니다.

    ![Visual Studio Code에서 변수 정의 살펴보기](../media/node-howto-e2e/visual-studio-code-peek-definition.png)

## <a name="modifying-the-code-and-using-autocompletion"></a>코드 수정 및 자동 완성 사용

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

복사 및 붙여넣기 대신 수동으로 코드를 입력할 때 `process` 뒤에 마침표를 입력하면 Visual Studio Code에 Node.js 프로세스 글로벌 API의 사용 가능한 멤버가 표시됩니다.

![프로세스 env를 사용하는 VS Code 환경 변수](../media/node-howto-e2e/visual-studio-code-process-env.png)

Visual Studio Code가 백그라운드에서(JavaScript의 경우에도) TypeScript를 사용하여 입력할 때 완성 목록을 알려주는 데 사용할 수 있는 형식 정보를 제공하기 때문에 자동 완성이 작동합니다. Visual Studio Code에서는 이 작업이 Node.js 프로젝트임을 감지할 수 있으며, 이에 따라 [NPM에서 Node.js](https://www.npmjs.com/package/@types/node)에 대한 TypeScript 입력 항목 파일을 자동으로 다운로드했습니다. 입력 항목 파일을 사용하면 `fs` 및 `http`와 같은 모든 기본 제공 모듈뿐만 아니라 `Buffer` 및 `setTimeout`과 같은 다른 Node.js 글로벌에 대한 자동 완성도 얻을 수 있습니다.

기본 제공 Node.js API 외에도 입력 항목의 자동 취득은 2,000개 이상의 타사 모듈(예: React, Underscore 및 Express)에서도 작동합니다. 예를 들어 Mongoose에서 구성된 MongoDB 데이터베이스 인스턴스에 연결할 수 없는 경우 Mongoose에서 정지된 샘플 앱을 해제하려면 13번 줄에 다음 코드 줄을 삽입합니다.

```javascript
mongoose.connection.on("error", () => { console.log("DB connection error"); });
```

이전 코드와 마찬가지로 사용자가 작업하지 않아도 자동 완성을 가져오는지 확인할 수 있습니다.

![API 멤버를 자동으로 표시하는 자동 완성](../media/node-howto-e2e/visual-studio-code-autocomplete-mongoose.png)

모든 TypeScript 형식 정의의 커뮤니티 기반 소스인 [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) 프로젝트를 탐색하여 이 자동 완성 기능을 지원하는 모듈을 확인할 수 있습니다.

## <a name="running-the-app"></a>앱 실행

코드에 대해 약간 알아보았으면 앱을 실행할 차례입니다. Visual Studio Code에서 앱을 실행하려면 **F5** 키를 누릅니다. **F5**(디버그 모드) 키를 통해 코드를 실행하면 Visual Studio Code에서 앱을 실행하고, 앱의 stdout(표준 출력)을 보여 주는 **디버그 콘솔** 창을 표시합니다.

![디버그 콘솔을 통한 앱의 stdout 모니터링](../media/node-howto-e2e/visual-studio-code-debug-console.png)

또한 새로 실행 중인 앱에 **디버그 콘솔** 이 연결되어 있으므로 JavaScript 식을 입력할 수 있습니다. 이 식은 앱에서 평가되며 자동 완성 기능도 포함하고 있습니다. 이 동작을 보려면 콘솔에서 `process.env`를 입력합니다.

![디버그 콘솔에 입력 항목 코드 추가](../media/node-howto-e2e/visual-studio-code-debug-console-autocomplete.png)

현재 열려 있는 파일이 JavaScript 파일(*server.js*)이므로 **F5** 키를 눌러 앱을 실행할 수 있었습니다. 결과적으로 Visual Studio Code에서는 프로젝트가 Node.js 앱이라고 가정합니다. Visual Studio Code의 모든 JavaScript 파일을 닫은 다음, **F5** 키를 누르면 Visual Studio Code에서 사용자를 다음 환경으로 쿼리합니다.

![런타임 환경 지정](../media/node-howto-e2e/visual-studio-code-select-environment.png)

브라우저를 열고 `http://localhost:8080`으로 이동하여 실행 중인 앱을 확인합니다. 텍스트 상자에 메시지를 입력하고 몇 가지 할 일을 추가/제거하여 앱이 작동하는 방식에 대해 파악합니다.

![앱을 사용하여 할 일 추가 또는 제거](../media/node-howto-e2e/add-remove-todos-app.png)

## <a name="debugging"></a>디버깅

Visual Studio Code는 통합된 콘솔을 통해 앱을 실행하고 상호 작용할 수 있을 뿐만 아니라 코드 내에서 중단점을 직접 설정할 수 있는 기능도 제공합니다. 예를 들어 **Ctrl**+**P** 를 눌러서 파일 선택기를 표시합니다. 파일 선택기가 표시되면 `route`를 입력하고 *route.js* 파일을 선택합니다.

앱에서 할 일 항목을 추가하려고 할 때 호출되는 Express 경로를 나타내는 중단점을 28번 줄에 설정합니다. 중단점을 설정하려면 다음 그림과 같이 편집기 내에서 줄 번호의 왼쪽 영역을 클릭하기만 하면 됩니다.

![Visual Studio Code 내 중단점 설정](../media/node-howto-e2e/visual-studio-code-set-breakpoint.png)

> [!NOTE]
> Visual Studio Code는 표준 중단점 외에도 앱에서 실행을 일시 중단해야 하는 시점을 사용자 지정할 수 있는 조건부 중단점을 지원합니다. 조건부 중단점을 설정하려면 마우스 오른쪽 단추로 실행을 일시 중지하려는 줄의 왼쪽 영역을 클릭하고, **조건부 중단점 추가** 를 선택한 다음, JavaScript 식(예: `foo = "bar"`) 또는 실행을 일시 중지할 조건을 정의하는 실행 횟수를 지정합니다.

중단점이 설정되면 실행 중인 앱으로 돌아가서 할 일 항목을 추가합니다. 할 일 항목을 추가하는 즉시 중단점을 설정한 28번 줄에서 앱의 실행이 일시 중지됩니다.

![중단점에서 실행이 일시 중지된 Visual Studio Code](../media/node-howto-e2e/visual-studio-code-pause-breakpoint-execution.png)

애플리케이션이 일시 중지되면 마우스로 코드 식 위를 가리켜서 현재 값을 볼 수 있고, 지역/조사 식 및 호출 스택을 검사하고, 디버그 도구 모음을 사용하여 코드 실행을 단계별로 수행할 수 있습니다. **F5** 키를 눌러 앱 실행을 다시 시작합니다.

## <a name="full-stack-debugging"></a>전체 스택 디버깅

이 항목의 앞부분에서 설명했듯이 할 일 앱은 MEAN 앱입니다. 즉 프런트 엔드와 백 엔드 코드가 모두 JavaScript를 사용하여 작성되었습니다. 따라서 현재 백 엔드(Node/Express) 코드를 디버그하고 있지만 어느 시점에서 프런트 엔드(Angular) 코드를 디버그해야 할 수도 있습니다. 이러한 목적을 위해 Visual Studio Code는 통합된 Chrome 디버깅을 포함하여 광범위한 확장 에코시스템을 갖추고 있습니다.

**확장** 탭으로 전환하고 검색 상자에 `chrome`을 입력합니다.

![Visual Studio Code용 Chrome 디버깅 확장](../media/node-howto-e2e/visual-studio-code-chrome-extension.png)

**Chrome용 디버거** 를 선택하고 **설치** 를 선택합니다. Chrome 디버깅 확장을 설치한 후 **다시 로드** 를 선택하여 확장을 활성화하기 위해 Visual Studio Code를 닫고 다시 엽니다.

![Chrome 디버깅 확장을 설치한 후 Visual Studio Code 다시 로드](../media/node-howto-e2e/visual-studio-code-reload-extension.png)

Visual Stdio 코드 관련 구성 없이 Node.js 코드를 실행하고 디버그할 수 있었지만, 프런트 엔드 웹앱을 디버그하려면 앱을 실행하는 방법을 Visual Studio Code에 지시하는 *launch.json* 파일을 생성해야 합니다.

*launch.json* 파일을 생성하려면 **디버그** 탭으로 전환하고, 톱니 바퀴 아이콘(위쪽에 빨간색 점이 없어야 함)을 클릭한 다음, **node.js** 환경을 선택합니다.

![launch.json 파일을 구성하는 Visual Studio Code 옵션](../media/node-howto-e2e/visual-studio-code-debug-gear.png)

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

Visual Studio Code에서 앱의 시작 스크립트가 *server.js* 임을 감지할 수 있었습니다.

*launch.json* 파일을 연 상태에서 **구성 추가**(오른쪽 아래)를 선택하고 **Chrome: Launch with userDataDir**(Chrome: userDataDir로 시작)을 선택합니다.

![Visual Studio Code에 Chrome 구성 추가](../media/node-howto-e2e/visual-studio-code-add-chrome-config.png)

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

프런트 엔드와 백 엔드 모두를 동시에 시작/디버그하려면 동시에 실행할 구성 집합을 Visual Studio Code에 알려주는 *복합* 실행 구성을 만들어야 야 합니다.

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

![Visual Studio Code에서 구성 실행](../media/node-howto-e2e/visual-studio-code-full-stack-configuration.png)

구성을 실행하면 Node.js 앱(디버그 콘솔 출력에서 볼 수 있는 것과 동일함)과 Chrome(`http://localhost:8080`에서 Node.js 앱으로 이동하도록 구성됨)이 시작됩니다.

**Ctrl**+**P** 를 누르고 앱의 프런트 엔드에 대한 주 Angular 컨트롤러인 *todos.js* 를 입력하거나 선택합니다.

11번 줄에 중단점을 설정합니다. 이 중단점은 만들려는 새 할 일 항목에 대한 진입점입니다.

실행 중인 앱으로 돌아가서 새 할 일 항목을 추가하고, 이제 Angular 코드 내에서 일시 중단된 실행이 Visual Studio Code에 있는지 확인합니다.

![Visual Studio Code에서 프런트 엔드 코드 디버깅](../media/node-howto-e2e/visual-studio-code-chrome-pause.png)

Node.js 디버깅과 마찬가지로 마우스로 식 위를 가리키고, 지역/조사 식을 보고, 콘솔에서 식을 평가하는 등의 작업을 수행할 수 있습니다. 

유의해야 할 두 가지 사항이 있습니다.

1. **호출 스택** 창에는 서로 다른 두 가지 스택, **Node** 및 **Chrome** 이 표시되며, 현재 일시 중지된 스택을 나타내고 있습니다.

1. 프런트 엔드 코드와 백 엔드 코드 사이에서 단계별로 실행할 수 있습니다. **F5** 키를 누르면 실행이 시작되고 Express 경로에서 이전에 설정한 중단점에 도달합니다.

이 설정을 사용하면 이제 Visual Studio Code 내에서 프런트 엔드, 백 엔드 또는 전체 스택 JavaScript 코드를 효율적으로 직접 디버그할 수 있습니다.

또한 복합 디버거 개념은 두 가지 대상 프로세스만으로 제한되지 않고 JavaScript만으로도 제한되지 않습니다. 따라서 마이크로 서비스 앱(잠재적으로 다국어 사용)에서 작동하는 경우 언어/프레임워크에 적절한 확장을 설치하면 똑같은 워크플로를 사용할 수 있습니다.

## <a name="dockerizing-the-app"></a>앱 Docker화

이 섹션에서는 Visual Studio Coded에서 [Docker](https://www.docker.com/)를 사용하여 개발할 때 제공하는 환경에 중점을 둡니다. Node.js 개발자는 Docker를 사용하여 개발, 즉 CI(연속 통합) 및 프로덕션 환경 모두에 이식 가능한 앱 배포를 제공합니다. Docker는 급격한 학습 곡선과 같이 빠르게 습득할 수 있으므로 앱에서 Docker를 사용하여 일부를 간소화하는 데 도움이 되는 확장이 Visual Studio Code에서 제공됩니다.

**확장** 탭으로 다시 전환하고, `docker`를 검색한 다음, **Docker** 확장을 선택합니다.

Docker 확장을 설치하고 Visual Studio Code를 다시 로드합니다.

![Visual Studio Code용 Docker 확장 설치](../media/node-howto-e2e/visual-studio-code-docker-extension.png)

Visual Studio Code의 Docker 확장에는 기존 프로젝트를 위한 *Dockerfile* 및 *docker-compose.yml* 파일을 생성하는 명령이 포함되어 있습니다.

사용 가능한 Docker 명령을 보려면 명령 팔레트를 표시(**F1**)하고 `docker`를 입력합니다.

![Visual Studio Code용 Docker 확장에서 지원되는 명령 ](../media/node-howto-e2e/visual-studio-code-available-docker-codes.png)

**Docker: Add docker files to workspace**(Docker: 작업 영역에 Docker 파일 추가)를 선택하고, **Node.js** 를 앱 플랫폼으로 선택한 다음, 앱에서 `8080` 포트를 공개하도록 지정합니다.

Docker 명령은 즉시 사용할 수 있는 완전한 *Dockerfile* 및 Docker 작성 파일을 생성합니다.

![Visual Studio Code에서 생성된 Dockerfile](../media/node-howto-e2e/visual-studio-code-complete-dockerfile.png)

Docker 확장은 *Dockerfile* 및 *docker-compose.yml* 파일에 대한 자동 완성 기능도 제공합니다. 예를 들어, *Dockerfile* 을 열고 다음과 같은 2번 줄을

```docker
FROM node:latest
```

아래와 같이 변경합니다.

```docker
FROM mhart
```

`mhart`의 `t` 뒤에 커서를 배치한 다음, **Ctrl**+**Space** 를 눌러 `mhart`에서 DockerHub에 게시한 모든 이미지 리포지토리를 봅니다.

![DockerHub에서 이미지 리포지토리 보기](../media/node-howto-e2e/visual-studio-code-dockerhub-image-repositories.png)

이 앱에 필요한 모든 항목을 제공하는 `mhart/alpine-node`를 선택합니다. 

앱 빌드 및 배포가 가능한 빨리 수행되기를 원하므로 일반적으로 이미지가 작을수록 더 효율적이며, 이에 따라 배포와 크기 조정이 더 빠릅니다.

이제 *Dockerfile* 을 생성했으므로 실제 Docker 이미지를 빌드해야 합니다. 다시금 말하지만 Visual Studio Code에 설치한 Docker 확장 명령을 사용할 수 있습니다. **F1** 키를 누르고, 명령 팔레트에서 `dockerb`를 입력한 다음, **Docker: Build Image**(Docker: 이미지 빌드) 명령을 선택합니다. 방금 생성하여 수정한 *Dockerfile* 을 선택합니다. DockerHub 사용자 이름(예: `lostintangent/node`)이 포함된 태그를 지정합니다. **Enter** 키를 눌러 빌드되는 Docker 이미지의 출력을 표시하는 통합 터미널 창을 시작합니다.

![Docker 이미지 빌드 출력](../media/node-howto-e2e/docker-build-image-output.png)

이 명령은 `docker build`를 실행하는 프로세스를 자동화했습니다. 이는 사용하도록 선택할 수 있거나 Docker CLI를 직접 사용할 수 있는 생산성 강화 도구의 또 다른 예입니다.

이 시점에서 이 이미지를 쉽게 배포할 수 있게 하려면 해당 이미지를 DockerHub로 푸시하면 됩니다. 이미지를 푸시하려면 CLI에서 `docker login`을 실행하고 자신의 계정 자격 증명을 입력하여 DockerHub를 통해 이미 인증되었는지 확인합니다. 그런 다음 Visual Studio Code에서 명령 팔레트를 표시하고, `dockerpush`를 입력한 다음, `Docker: Push` 명령을 선택할 수 있습니다. 방금 빌드한 이미지 태그(예: `lostintangent/node`)를 선택하고 **Enter** 키를 누릅니다. 이 명령은 `docker push`의 호출을 자동화하고 통합 터미널에 출력을 표시합니다.

## <a name="deploying-the-app"></a>앱 배포

이제 Docker화된 앱을 만들어서 DockerHub에 푸시했으므로 전체 환경에서 볼 수 있도록 클라우드에 배포해야 합니다. 배포를 위해 Azure의 PaaS 제품인 Azure App Service를 사용할 수 있습니다. App Service에는 Node.js 개발자와 관련된 두 가지 기능이 있습니다.

- Linux 기반 VM 지원 - 네이티브 Node 모듈을 사용하여 빌드된 앱 또는 Windows를 지원하지 않거나 다르게 작동할 수 있는 다른 도구에 대한 비호환성 문제가 줄어듭니다.
- Docker 기반 배포 지원 - Docker 이미지의 이름을 지정하고, App Service에서 이미지를 자동으로 가져오고, 배포하고, 크기 조정할 수 있습니다.

시작하려면 Visual Studio 터미널을 엽니다. 새 Azure CLI 2.0을 사용하여 Azure 계정을 관리하고 할 일 앱을 실행하는 데 필요한 인프라를 프로비저닝합니다. 필수 구성 요소에서 설명한 대로 CLI에서 `az login` 명령을 사용하여 계정에 로그인한 후 다음 단계를 수행하여 App Service 인스턴스를 프로비저닝하고 할 일 앱 컨테이너를 배포합니다.

1. Azure 리소스를 구성하는 데 도움이 되는 *네임스페이스* 또는 *디렉터리* 로 간주할 수 있는 리소스 그룹을 만듭니다. `-n` 옵션은 그룹의 이름을 지정하는 데 사용되며, 원하는 대로 지정할 수 있습니다.

    ```azurecli
    az group create -n nina-demo -l westus
    ```

    `-l` 옵션은 리소스 그룹의 위치를 나타냅니다. 미리 보기로 있는 동안 Linux에 대한 App Service 지원은 일부 지역에서만 제공됩니다. 따라서 미국 서부에 있지 않고 다른 지역에서 사용할 수 있는지 확인하려면 CLI에서 `az appservice list-locations --linux-workers-enabled`를 실행하여 데이터 센터 옵션을 확인하세요.

1. 각 Azure CLI 호출에서 리소스 그룹을 명시적으로 지정하지 않고도 CLI를 계속 사용할 수 있도록 새로 만든 리소스 그룹을 기본 리소스 그룹으로 설정합니다.

   ```azurecli
   az configure -d group=nina-demo
   ```

1. 앱이 배포된 기본 가상 머신의 생성 및 크기 조정을 관리하는 App Service *계획* 을 만듭니다. 다시 한 번 `n` 옵션에 대해 원하는 값을 지정합니다.

    ```azurecli
    az appservice plan create -n nina-demo-plan --is-linux
    ```

    `--is-linux option`은 Linux 기반 가상 머신이 필요하다는 것을 나타냅니다. 이 옵션이 없으면 CLI에서 기본적으로 Windows 기반 가상 머신을 프로비전하도록 설정합니다.

1. 방금 생성된 계획 및 리소스 그룹 내에서 실행될 실제 할 일 앱을 나타내는 App Service 웹앱을 만듭니다. 웹앱은 프로세스 또는 컨테이너와 동의어로, 계획은 실행 중인 가상 머신/컨테이너 호스트로 간주할 수 있습니다. 또한 웹앱을 만드는 작업의 일부로 DockerHub에 게시한 Docker 이미지를 사용하도록 해당 웹앱을 구성해야 합니다.

    ```azurecli
    az webapp create -n nina-demo-app -p nina-demo-plan -i lostintangent/node
    ```

    > [!NOTE]
    > 사용자 지정 컨테이너를 사용하는 대신 Git를 배포하려면 [Azure에 Node.js 웹앱 만들기](/azure/app-service-web/app-service-web-get-started-nodejs) 문서를 참조하세요.

1. 웹앱을 기본 웹 인스턴스로 설정합니다.

    ```azurecli
    az configure -d web=nina-demo-app
    ```

1. 앱을 실행하여 `*.azurewebsites.net` URL에서 사용할 수 있는 배포된 컨테이너를 봅니다.

    ```azurecli
    az webapp browse
    ```

    App Service에서 DockerHub의 Docker 이미지를 끌어온 다음 시작해야 하므로 앱을 처음 로드하는 데 몇 분 정도 걸릴 수 있습니다.

    ![브라우저에서 실행 중인 할 일 앱](../media/node-howto-e2e/deployed-container-app.png)

이 시점에서 할 일 앱을 배포하고 실행했습니다. 그러나 회전하는 아이콘은 앱이 데이터베이스에 연결할 수 없음을 나타냅니다. 이 문제는 개발 중에 MongoDB의 로컬 인스턴스를 사용했기 때문이며, Azure 데이터 센터 내에서는 연결할 수 없습니다. 환경 변수를 통해 연결 문자열을 수락하도록 앱을 수정했으므로 MongoDB 서버를 시작하고 이 환경 변수를 참조하도록 App Service 인스턴스를 다시 구성하기만 하면 됩니다. 이 작업은 다음 섹션에 설명되어 있습니다.

## <a name="provisioning-a-mongodb-server"></a>MongoDB 서버 프로비전

직접 MongoDB 서버 또는 복제본 세트를 구성하고 해당 인프라를 관리할 수 있지만 Azure에서는 [Cosmos DB](https://azure.microsoft.com/services/documentdb/)라는 솔루션을 제공합니다. Cosmos DB는 완벽하게 관리되고 지리적으로 복제 가능한 고성능 NoSQL 데이터베이스로서 MongoDB 호환성 계층을 제공합니다. 따라서, 연결 문자열만 변경하여 Cosmos DB(또는 [Studio 3T](https://studio3t.com/)와 같은 MongoDB 클라이언트/도구)에서 기존 MEAN 앱을 가리킬 수 있습니다. 다음 단계는 이 기능을 보여줍니다.

1. Visual Studio Code 터미널에서 다음 명령을 실행하여 Cosmos DB 서비스의 MongoDB 호환 인스턴스를 만듭니다. **<NAME** 자리 표시자를 전역적으로 고유한 값으로 바꿉니다(Cosmos DB에서는 이 이름을 사용하여 데이터베이스의 서버 URL을 생성함).

   ```azurecli
   COSMOSDB_NAME=<NAME>
   az cosmosdb create -n $COSMOSDB_NAME --kind MongoDB
   ```

1. 이 인스턴스에 대한 MongoDB 연결 문자열을 검색합니다.

   ```bash
   MONGODB_URL=$(az cosmosdb list-connection-strings -n $COSMOSDB_NAME -otsv --query "connectionStrings[0].connectionString")
   ```

1. 로컬로 실행 중인 MongoDB 서버(존재하지 않음)에 연결하는 대신 새로 프로비전된 Cosmos DB 인스턴스에 연결하도록 웹앱의 **MONGODB_URL** 환경 변수를 업데이트합니다.

    ```azurecli
    az webapp config appsettings set --settings MONGODB_URL=$MONGODB_URL
    ```

1. 브라우저로 돌아가서 새로 고칩니다. 할 일 항목을 추가하거나 제거하여 아무 것도 변경하지 않고도 앱이 작동함을 증명합니다! 환경 변수를 만든 Cosmos DB 인스턴스로 설정합니다.이 인스턴스는 MongoDB 데이터베이스를 완벽하게 에뮬레이션합니다.

    ![데이터베이스에 연결된 이후의 데모 앱](../media/node-howto-e2e/finish-demo-walkthrough.png)

필요에 따라 Cosmos DB 인스턴스로 다시 전환하고 MongoDB 인스턴스에 필요한 예약된 처리량을 강화(또는 축소)할 수 있으며, 수동으로 인프라를 관리할 필요 없이 추가된 트래픽의 이점을 얻을 수 있습니다.

또한 Cosmos DB는 자동으로 모든 단일 문서와 속성을 인덱싱합니다. 이렇게 하면 느린 쿼리를 프로파일링하거나 수동으로 인덱스를 미세 조정할 필요가 없습니다. 필요에 따라 프로비전 및 크기 조정하고, Cosmos DB에서 나머지 작업을 처리하게 합니다.

## <a name="hosting-a-private-docker-registry"></a>프라이빗 Docker 레지스트리 호스팅

DockerHub는 보안/거버넌스 또는 성능 이점과 같이 컨테이너 이미지를 배포하는 데 유용한 환경을 제공하지만 고유의 프라이빗 Docker 레지스트리를 호스팅하려는 시나리오가 있을 수 있습니다. 이를 위해 Azure에서는 지원하는 스토리지가 웹앱(더 빨리 끌어올 수 있음)과 동일한 데이터 센터에 있는 Docker 레지스트리를 만들 수 있는 [ACR(Azure Container Registry)](https://azure.microsoft.com/services/container-registry/)을 제공합니다. 또한 ACR은 이미지를 푸시하거나 끌어올 수 있는 사람과 같이 콘텐츠 및 액세스 제어에 대한 완벽한 제어 권한도 사용자에게 제공합니다.

다음 명령을 실행하여 사용자 지정 레지스트리 프로비전을 수행할 수 있습니다. ACR에서 레지스트리의 로그인 서버 URL을 생성하는 데 지정된 값을 사용하므로 **<NAME** 자리 표시자를 전역적으로 고유한 값으로 바꿉니다.

```azurecli
ACR_NAME=<NAME>
az acr create -n $ACR_NAME -l westus --admin-enabled
```

> [!NOTE]
> 이 항목의 예제에서는 **관리자 계정** 을 사용하여 작업을 단순하게 유지하지만 프로덕션 레지스트리에는 권장되지 않습니다.

`az acr create` 명령은 Docker CLI(예: `ninademo.azurecr.io`)를 사용하여 로그인하는 데 사용하는 로그인 서버 URL(`LOGIN SERVER` 열을 통해)을 표시합니다. 또한 이 명령은 인증에 사용할 수 있는 관리자 자격 증명을 생성합니다. 이러한 자격 증명을 검색하려면 다음 명령을 실행하고 표시된 사용자 이름과 암호를 기록해 둡니다.

```azurecli
az acr credential show -n $ACR_NAME
```

이전 단계의 자격 증명과 개별 로그인 서버를 사용하면 표준 Docker CLI 워크플로를 통해 레지스트리에 로그인할 수 있습니다.

```console
docker login <LOGIN_SERVER> -u <USERNAME> -p <PASSWORD>
```

이제 `lostintangent/node`를 컨테이너 이미지에 지정한 이름으로 바꾸는 다음 명령을 사용하여 프라이빗 레지스트리와 연결되어 있음을 나타내도록 Docker 컨테이너에 태그를 지정할 수 있습니다.

```console
docker tag lostintangent/node <LOGIN_SERVER>/lostintangent/node
```

마지막으로 태그가 지정된 이미지를 프라이빗 Docker 레지스트리로 푸시합니다.

```console
docker push <LOGIN_SERVER>/lostintangent/node
```

이제 컨테이너가 사용자 고유의 프라이빗 레지스트리에 저장되고, Docker CLI는 DockerHub를 사용할 때와 동일한 방식으로 계속 작업할 수 있게 되었습니다. App Service 웹앱에서 프라이빗 레지스트리에서 가져오도록 지시하려면 다음 명령만 실행하면 됩니다.

```azurecli
az appservice web config container set \
    -r <LOGIN_SERVER> \
    -c <LOGIN_SERVER>/lostintangent/node \
    -u <USERNAME> \
    -p <PASSWORD>
```

`-r` 옵션의 시작 부분에 `https://` 접두사를 추가해야 합니다. 그러나 이 접두사를 컨테이너 이미지 이름에는 추가하지 마세요.

브라우저에서 앱을 새로 고치면 모든 것이 동일하게 표시되고 작동합니다. 그러나 이제는 프라이빗 Docker 레지스트리를 통해 앱을 실행됩니다. 앱을 업데이트한 후 위에서 수행한 대로 태그를 지정하고, 변경 내용을 적용한 다음, App Service 컨테이너 구성에서 태그를 업데이트합니다.

## <a name="configuring-a-custom-domain-name"></a>사용자 지정 도메인 이름 구성

`*.azurewebsites.net` URL은 테스트에 적합하지만, 어느 시점에서 사용자 지정 도메인 이름을 웹앱에 추가할 수 있습니다. 등록자의 도메인 이름이 있으면 웹앱의 외부 IP(실제로 부하 분산 장치)를 가리키는 `A` 레코드만 추가하면 됩니다. 다음 명령을 실행하여 이 IP를 검색할 수 있습니다.

```azurecli
az webapp config hostname get-external-ip
```

`A` 레코드를 추가하는 것 외에도, 지금까지 사용 중인 `*.azurewebsites.net` 도메인을 가리키는 `TXT` 레코드를 도메인에 추가해야 합니다. `A`과 `TXT` 레코드의 조합을 사용하면 Azure에서 사용자가 해당 도메인을 소유하고 있음을 확인할 수 있습니다.

일단 레코드가 만들어지고 DNS 변경 내용이 전파되면 들어오는 트래픽을 올바르게 예상하는지 파악할 수 있도록 Azure에 사용자 지정 도메인을 등록합니다.

```azurecli
az webapp config hostname add --hostname <DOMAIN>
```

> [!NOTE]
> 이 명령이 작동하려면 먼저 DNS 변경 내용이 전파되어야 합니다.

브라우저를 열고 사용자 지정 도메인으로 이동하여 이제 Azure에서 배포된 앱으로 확인하는지 알아봅니다.

## <a name="scaling-up-and-out"></a>강화 및 확장

어느 시점에서는 증가된 트래픽 및 운영 요구 사항을 처리하기에는 할당된 리소스(CPU 및 RAM)가 충분하지 않을 만큼 웹앱이 충분히 인기가 있을 수 있습니다. 이전에 만든 App Service 계획(**B1**)에는 하나의 CPU 코어와 1.75GB RAM이 포함되어 있어서 쉽게 오버로드될 수 있습니다. **B2** 계획은 이러한 CPU와 RAM의 두 배가 됩니다. 따라서 둘 중 어느 하나가 부족하게 되면 앱에서 다음 명령을 실행하여 기본 가상 머신을 강화할 수 있습니다.

```azurecli
az appservice plan update -n nina-demo-plan --sku B2
```

> [!NOTE]
> Azure App Service 계획에 대한 가격 책정 세부 정보 및 사양은 [App Service 가격](https://azure.microsoft.com/pricing/details/app-service/) 문서를 참조하세요.

잠시 후 요청된 하드웨어로 웹앱이 마이그레이션되어 관련 리소스를 이용할 수 있습니다. 강화 외에도 더 낮은 가격으로 더 적은 리소스를 제공하는 `--sku` 옵션을 지정하여 위와 동일한 명령을 실행하여 규모를 축소할 수 있습니다.

가상 머신 사양을 강화하는 것 외에도, 상태 비저장 웹앱인 경우 기본 가상 머신 인스턴스를 더 많이 추가하여 *확장* 하는 옵션이 있습니다. 이전에 만든 App Service 계획에는 단일 가상 머신(*작업자*)만 있으므로 들어오는 모든 트래픽은 최종적으로 해당 인스턴스 하나의 사용 가능한 리소스 제한을 따릅니다. 두 번째 가상 머신 인스턴스를 추가하려는 경우 이전에 실행한 동일한 명령을 실행해도 되지만 SKU를 강화하는 대신 작업자 가상 머신의 수를 확장합니다.

```azurecli
az appservice plan update -n nina-demo-plan --number-of-workers 2
```

웹앱을 이와 같이 확장하면 들어오는 트래픽이 모든 인스턴스 간에 투명하게 부하 분산되어 코드를 변경하거나 필요한 인프라에 대해 걱정하지 않고 용량을 즉시 늘릴 수 있습니다.

상태 비저장 웹앱은 단일 가상 머신이나 앱 인스턴스가 작동하는 데 필요한 상태를 포함하지 않으므로 완전히 결정적인 크기 조정(강화, 축소 또는 확장) 기능을 제공하는 것이 모범 사례로 간주됩니다.

> [!NOTE]
> 이 항목에서는 App Service 계획의 일부로 단일 웹앱을 실행하는 방법을 보여주지만, 하나의 계획을 프로비저닝하고 이에 대한 비용을 지불할 수 있도록 여러 웹앱을 만들어 동일한 계획에 배포할 수 있습니다.

## <a name="clean-up"></a>정리

사용하지 않는 Azure 리소스에 대해 요금이 청구되지 않도록 하려면 Visual Studio Code 터미널에서 다음 명령을 실행하여 이 자습서에서 프로비전된 모든 리소스를 삭제합니다.

```azurecli
az group delete
```

정리 프로세스를 완료하는 데 몇 분 정도 걸릴 수 있습니다. 완료되면 `az group delete` 명령은 Azure 계정을 자습서를 시작하기 전과 동일한 상태로 유지합니다. Azure 리소스를 단일 단위로 구성, 배포 및 삭제하는 기능은 리소스 그룹의 주요 이점 중 하나입니다. 따라서 동일한 수명을 갖춘 것으로 예상되는 리소스로 그룹화하는 것이 좋습니다.
