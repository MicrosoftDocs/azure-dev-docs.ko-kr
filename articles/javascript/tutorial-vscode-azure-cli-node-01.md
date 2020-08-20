---
title: Azure CLI를 사용하여 Azure App Service에 Node.js 앱 배포
description: 자습서 1부, 소개 및 필수 구성 요소
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: 51fb29a80ba69dd59ca18dd94eaaeb6f09e53074
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88218074"
---
# <a name="deploy-to-azure-app-service-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure App Service에 배포

이 자습서에서는 모든 운영 체제에서 실행되는 [Azure CLI(명령 줄 인터페이스)](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)를 사용하여 Azure App Service에 Node.js 애플리케이션을 배포합니다. CLI를 사용하면 Azure 리소스를 만들고 Git 리포지토리와 Azure 간에 배포 파이프라인을 설정하고 앱의 `console.log` 출력을 볼 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Azure 구독](#azure-subscription).
- [Node.js 및 npm 6.x 이상](https://nodejs.org/en/download)(Node.js 패키지 관리자)
- [Git](https://git-scm.com/downloads)(뒤에 나오는 `git --version` 명령에 버전 번호를 표시해야 함)
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)

대안으로, Azure CLI 스크립트를 작성할 때 구문 색 지정, IntelliSense(완료) 및 코드 조각을 제공하는 [Visual Studio Code용 Azure CLI 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)을 대신 사용할 수도 있습니다.

두 번째 대안은 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)이며, [Azure Account 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)을 사용하여 Visual Studio Code 내에서 사용할 수 있습니다.

### <a name="azure-subscription"></a>Azure 구독

Azure 구독이 없는 경우 지금 200달러의 Azure 크레딧으로 체험 계정에 [지금 가입](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-node-git&mktingSource=vscode-tutorial-node-git)하여 서비스 조합을 사용해 볼 수 있습니다.

### <a name="sign-in-to-the-azure-cli"></a>Azure CLI에 로그인

Azure CLI가 설치되면 터미널 또는 명령 프롬프트에서 다음 명령을 실행합니다.

```azurecli
az login
```

이 명령은 Azure에 로그인하라는 메시지가 표시되는 브라우저 창을 엽니다. 로그인하면 터미널 창에 구독 세부 정보가 포함된 JSON 출력이 표시됩니다.

## <a name="check-npm-version"></a>npm 버전 확인

Node.js가 이미 설치되어 있으면 `node -v` 명령을 실행하여 버전이 10.x 이상인지 확인합니다. 이전 버전이 있는 경우 최신 LTS("Long Term Support") 릴리스로 [업그레이드](https://nodejs.org/en/download/)하세요.

> [!div class="nextstepaction"]
> [필수 구성 요소를 설치했습니다.](tutorial-vscode-azure-cli-node-02.md) [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=getting-started)
