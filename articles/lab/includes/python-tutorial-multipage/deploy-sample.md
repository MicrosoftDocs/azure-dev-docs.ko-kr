---
title: azure-sign-in.md 포함 파일
description: azure-sign-in.md 포함 파일
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: c8cf3e2f478265a81742093315e2a4041b2f0ed9
ms.sourcegitcommit: 5f64710b2b0822e789c7f15acba5a3a257c033f9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93405206"
---
`az webapp up` 명령을 사용하여 로컬 폴더( *python-docs-hello-world* )에 코드를 배포합니다.

```azurecli
az webapp up --sku F1 --name <app-name>
```

- `az` 명령이 인식되지 않는 경우 [초기 환경 설정](../../quickstart-python-flask-multipage.yml?tutorial-step=1)에서 설명한 대로 Azure CLI가 설치되어 있는지 확인합니다.
- `webapp` 명령이 인식되지 않는 경우 Azure CLI 버전이 2.0.80 이상이기 때문입니다. 그렇지 않은 경우 [최신 버전을 설치합니다](/cli/azure/install-azure-cli).
- `<app_name>`을 모든 Azure에서 고유한 이름으로 바꿉니다( *유효한 문자는 `a-z`, `0-9` 및 `-`* ). 좋은 패턴은 회사 이름과 앱 식별자의 조합을 사용하는 것입니다.
- `--sku F1` 인수는 무료 가격 책정 계층에 웹앱을 만듭니다. 이 인수를 생략하여 더 빠른 프리미엄 계층을 사용합니다. 이 경우 시간당 비용이 발생합니다.
- 선택적으로 인수 `--location <location-name>`을 포함할 수 있습니다. 여기서 `<location_name>`은 사용 가능한 Azure 지역입니다. Azure 계정에 허용되는 지역 목록은 [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) 명령을 실행하여 검색할 수 있습니다.
- "앱의 런타임 스택을 자동으로 검색할 수 없습니다."라는 오류가 표시되면 *requirements.txt* 파일이 포함된 *python-docs-hello-world* 폴더(Flask) 또는 *python-docs-hello-django* 폴더(Django)에서 명령을 실행하고 있는지 확인합니다. ([az webapp up으로 자동 검색 문제 해결](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md)(GitHub)을 참조하세요.)

이 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 실행되는 동안 리소스 그룹, App Service 계획 및 호스팅 앱을 만들고, 로깅을 구성한 다음, ZIP 배포 수행에 대한 메시지를 제공합니다. 그런 다음, "http://&lt;app-name&gt;.azurewebsites.net에서 앱을 시작할 수 있습니다."라는 메시지를 제공합니다. 이 메시지는 Azure에서 앱의 URL입니다.

![az webapp up 명령의 예제 출력](../../media/quickstart-python/az-webapp-up-output.png)

[!include [az webapp up command note](../app-service-web-az-webapp-up-note.md)]