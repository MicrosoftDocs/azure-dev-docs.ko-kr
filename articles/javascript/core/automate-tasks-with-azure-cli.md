---
title: Azure CLI를 사용한 웹앱 자동화 작업
description: Azure 작업 자동화는 호스팅 환경에 대한 지속적인 배포를 위한 일반적인 요구 사항입니다. Azure CLI는 작업을 관리하고 모든 위치에서 배포하는 JavaScript 개발자에게 권장되는 옵션입니다.
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: devx-track-js
ms.openlocfilehash: 7cfce90d8d0daf861dab9ba02e46ce489ae10742
ms.sourcegitcommit: 0d2ea78f18430c845a32e0d2311427ab81033465
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97754059"
---
# <a name="automate-tasks-with-azure-cli"></a>Azure CLI를 사용하여 작업 자동화

Azure 작업 자동화는 호스팅 환경에 대한 지속적인 배포를 위한 일반적인 요구 사항입니다. [Azure CLI](/cli/azure/)는 작업을 관리하고 모든 위치에서 배포하는 JavaScript 개발자에게 권장되는 옵션입니다.

JavaScript 개발자의 일반 작업 명령을 학습합니다. 

## <a name="automation-with-azure-cli"></a>Azure CLI를 사용한 자동화

Azure CLI를 자동화하려면 환경에 CLI를 설치해야 합니다. 일반적인 방법은 다음과 같습니다. 

* [Azure CLI를 로컬로 설치](/cli/azure/install-azure-cli)
* [Docker 컨테이너에서 명령 실행](/cli/azure/run-azure-cli-docker)

## <a name="using-the-example-commands"></a>예제 명령 사용 

1. 대괄호 안의 변수(`<...>`)를 원하는 값으로 바꿉니다. 
1. `<MY_GITHUB_DEFAULT_BRANCH_NAME>`에 대한 GitHub 리포지토리 값은 사용된 리포지토리에 따라 다릅니다. 현재 일반적인 값은 `main` 또는 `default`입니다. 이전 리포지토리는 `master`를 사용할 수 있습니다. 

## <a name="log-in-for-automated-tasks-with-azure-cli"></a>Azure CLI를 사용하여 자동화된 작업에 로그인

Azure CLI가 설치되면 Azure CLI 명령을 계속 실행하려면 로그인해야 합니다. 자동화의 경우 Azure CLI에 대한 인증을 수행할 수 있습니다.

**참조 설명서**: [az login](/cli/azure/reference-index?view=azure-cli-latest#az-login)

[관리 ID](/cli/azure/authenticate-azure-cli#sign-in-with-a-managed-identity)는 인증에 권장되는 선택 사항입니다.

```azurecli
az login --identity
```

[서비스 주체가 만들어진](../core/node-sdk-azure-authenticate-principal.md#create-a-service-principal-using-the-azure-cli-20) 후 [사용자의 서비스 주체를 사용하여 로그인](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)합니다. 

```dotnetcli
read -sp "Azure password: " AZ_PASS && echo && \ 
    az login --service-principal \
    -u <MY-SP-APP-URL> \
    -p $AZ_PASS \
    --tenant <MY-TENANT>
```


[사용자 자격 증명 사용: 사용자 이름 및 암호](/cli/azure/authenticate-azure-cli#sign-in-with-credentials-on-the-command-line)

```dotnetcli
az login -u <MY_AZURE_USERNAME> -p <MY_AZURE_PASSWORD>
```    

## <a name="create-resource-group-for-resources"></a>리소스에 대한 리소스 그룹 만들기

리소스 그룹은 Azure 리소스에 대한 논리적 컬렉션입니다. 논리적 그룹화는 프로젝트에 대한 특정 지역에 필요한 서비스를 기반으로 합니다. [명명 규칙](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming)에 대해 자세히 알아보세요.

**참조 설명서**: [az group create](/cli/azure/group?view=azure-cli-latest#az_group_create)

```azurecli
az group create \
    --name <MY-AZURE-RESOURCE_GROUP_NAME> \
    --location <AZURE_REGION_LOCATION>
```

## <a name="static-web-apps-with-azure-cli"></a>Azure CLI를 사용한 정적 웹앱

정적 웹앱은 다음에 대한 코드를 포함합니다.

* GitHub 리포지토리에 포함된 프런트 엔드 애플리케이션
* 필요에 따라 `/API` 디렉터리의 기존 Azure Functions API [자세한 정보](/azure/static-web-apps/add-api#create-the-api)

앱은 서버리스 API에 Azure 함수를 사용할 수 있지만 정적 웹앱에 대한 요구 사항은 아닙니다. 

**참조 설명서**: [az staticwebapp](/cli/azure/staticwebapp?view=azure-cli-latest)

### <a name="create-azure-static-web-app"></a>Azure Static 웹앱 만들기 

**참조 설명서**: [az staticwebapp create](/cli/azure/staticwebapp?view=azure-cli-latest#az_staticwebapp_create)

```azurecli
az staticwebapp create \
    --name <MY_AZURE_WEB_APP_NAME> \
    --resource-group <MY-AZURE-RESOURCE_GROUP_NAME> \
    --source https://github.com/<MY_GITHUB_ACCOUNT_NAME>/<MY_AZURE_WEB_APP_NAME> \
    --location <AZURE_REGION_LOCATION> \
    --branch <MY_GITHUB_DEFAULT_BRANCH_NAME> \
    --app-artifact-location "<MY_WEB_APP_BUILD_DIRECTORY_NAME>" \
    --token <MY_GITHUB_PERSONAL_ACCESS_TOKEN>
```

### <a name="deploy-azure-static-web-app"></a>Azure Static 웹앱 배포 

앱을 배포하려면 Git을 사용하여 이전 집합의 리소스 생성 중에 원격 및 분기 집합으로 푸시합니다. 

```bash
git push <REMOTE_NAME> <MY_GITHUB_DEFAULT_BRANCH_NAME>
```

이 명령의 예제는 다음과 같습니다.

```bash
git push origin main
```

### <a name="delete-static-web-app"></a>정적 웹앱 삭제 

**참조 설명서**: [az staticwebapp delete](/cli/azure/staticwebapp?view=azure-cli-latest#az_staticwebapp_delete)

```azurecli
az staticwebapp delete && \
    --name <MY_AZURE_WEB_APP_NAME> && \
    --resource-group <MY-AZURE-RESOURCE_GROUP_NAME>
```

## <a name="next-steps"></a>다음 단계

* [자습서: 정적 웹앱을 빌드하여 Azure에 배포](../tutorial/static-web-app/introduction.md)
