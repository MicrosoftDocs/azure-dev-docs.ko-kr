---
title: 빠른 시작 - GitHub Actions 워크플로에서 Azure Key Vault 비밀 사용
description: GitHub Actions에서 Key Vault 비밀을 사용하여 소프트웨어 개발 워크플로를 자동화합니다.
author: juliakm
ms.custom: github-actions-azure
ms.author: jukullam
ms.date: 02/04/2021
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 385889e067b375c80744c8f798d62c1b05468aab
ms.sourcegitcommit: 7287dff6bf4b30c2033924702c941bf520403e07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99589725"
---
# <a name="use-key-vault-secrets-in-github-actions-workflows"></a>GitHub Actions 워크플로에서 Key Vault 비밀 사용

[GitHub Actions](https://help.github.com/en/articles/about-github-actions)에서 Key Vault 비밀을 사용하고, 암호 및 기타 비밀을 Azure 키 자격 증명 모음에 안전하게 저장합니다. [Key Vault](/azure/key-vault/general/overview)에 대해 자세히 알아보세요. 

Key Vault 및 GitHub Actions를 사용하면 중앙 집중식 비밀 관리 도구와 GitHub Actions의 모든 이점을 활용할 수 있습니다. GitHub Actions는 소프트웨어 개발 워크플로를 자동화하는 GitHub의 기능 모음입니다. 코드를 저장하고 끌어오기 요청 및 이슈에 대해 협업하는 위치와 동일한 위치에 워크플로를 배포할 수 있습니다. 


## <a name="prerequisites"></a>필수 구성 요소 
- GitHub 계정. 없는 경우 [평가판](https://github.com/join)에 등록하세요.  
- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- GitHub 리포지토리에 연결된 Azure 앱. 이 예에서는 [Azure App Service에 컨테이너 배포](/azure/developer/javascript/tutorial-vscode-docker-node-01)를 사용합니다. 
- Azure 키 자격 증명 모음.  Azure Key Vault는 Azure Portal, Azure CLI 또는 Azure PowerShell을 사용하여 만들 수 있습니다.

## <a name="workflow-file-overview"></a>워크플로 파일 개요

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일에서 정의됩니다. 이 정의는 워크플로를 구성하는 다양한 단계와 매개 변수를 포함합니다.

이 파일에는 GitHub Actions를 통해 인증하기 위한 두 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | 1. 서비스 주체를 정의합니다. <br /> 2. GitHub 비밀을 만듭니다. <br /> 3. 역할 할당을 추가합니다. |
|**Key Vault** | 1. 키 자격 증명 모음 작업을 추가합니다. <br /> 2. 키 자격 증명 모음 비밀을 참조합니다. |

## <a name="authentication"></a>인증

[Azure CLI](/cli/azure/)에서 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) 명령을 사용하여 [서비스 주체](/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)를 만들 수 있습니다. 이 명령은 Azure Portal에서 [Azure Cloud Shell](https://shell.azure.com/)을 사용하거나 **사용해 보세요** 단추를 선택하여 실행합니다.

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

위의 예제에서 자리 표시자를 구독 ID 및 리소스 그룹 이름으로 바꿉니다. `myApp` 자리 표시자를 애플리케이션 이름으로 바꿉니다. 출력은 아래와 비슷한 App Service 앱에 대한 액세스를 제공하는 역할 할당 자격 증명이 있는 JSON 개체입니다. 나중에 사용할 수 있도록 이 JSON 개체를 복사합니다. `clientId`, `clientSecret`, `subscriptionId` 및 `tenantId` 값이 포함된 섹션만 필요합니다. 

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

### <a name="configure-the-github-secret"></a>GitHub 비밀 구성

Azure 자격 증명, 리소스 그룹 및 구독에 대한 비밀을 만듭니다. 

1. [GitHub](https://github.com/)에서 리포지토리를 찾습니다.

1. **설정 > 비밀 > 새 비밀** 을 차례로 선택합니다.

1. Azure CLI 명령의 전체 JSON 출력을 비밀의 값 필드에 붙여넣습니다. 비밀 이름을 `AZURE_CREDENTIALS`로 지정합니다.

1. 나중에 사용할 수 있도록 `clientId`의 값을 복사합니다. 

### <a name="add-a-role-assignment"></a>역할 할당 추가 
 
`get` 및 `list` 작업에 대한 키 자격 증명 모음에 액세스할 수 있도록 Azure 서비스 주체에 대한 액세스 권한을 부여해야 합니다. 이렇게 하지 않으면 서비스 주체를 사용할 수 없습니다. 

`keyVaultName`을 키 자격 증명 모음의 이름으로 바꾸고, `clientIdGUID`를 `clientId`의 값으로 바꿉니다. 

```azurecli-interactive
    az keyvault set-policy -n {keyVaultName} --secret-permissions get list --spn {clientIdGUID}
```

## <a name="use-the-azure-key-vault-action"></a>Azure 키 자격 증명 모음 작업 사용

[Azure 키 자격 증명 모음 작업](https://github.com/marketplace/actions/azure-key-vault-get-secrets)을 사용하면 Azure 키 자격 증명 모음 인스턴스에서 하나 이상의 비밀을 가져와서 GitHub Action 워크플로에서 사용할 수 있습니다.

가져온 비밀은 출력으로 설정되고, 환경 변수로도 설정됩니다. 변수는 콘솔 또는 로그에 출력될 때 자동으로 마스킹됩니다.

```yaml
    - uses: Azure/get-keyvault-secrets@v1
      with:
        keyvault: "my Vault" # name of key vault in Azure portal
        secrets: 'mySecret'  # comma separated list of secret keys to fetch from key vault 
      id: myGetSecretAction # ID for secrets that you will reference
```

워크플로에서 키 자격 증명 모음을 사용하려면 키 자격 증명 모음 작업이 필요하고 해당 작업을 참조해야 합니다. 

다음 예제에서는 키 자격 증명 모음의 이름이 `containervault`입니다. 두 개의 키 자격 증명 모음 비밀(`containerPassword` 및 `containerUsername`)이 키 자격 증명 모음 작업을 사용하여 환경에 추가됩니다. 

키 자격 증명 모음 값은 나중에 접두사가 `steps.myGetSecretAction.outputs`인 docker 로그인 작업에서 참조됩니다. 

```yaml
name: Example key vault flow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - uses: actions/checkout@v2
    - uses: Azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - uses: Azure/get-keyvault-secrets@v1
      with: 
        keyvault: "containervault"
        secrets: 'containerPassword, containerUsername'
      id: myGetSecretAction
    - uses: azure/docker-login@v1
      with:
        login-server: myregistry.azurecr.io
        username: ${{ steps.myGetSecretAction.outputs.containerUsername }}
        password: ${{ steps.myGetSecretAction.outputs.containerPassword }}
    - run: |
        docker build . -t myregistry.azurecr.io/myapp:${{ github.sha }}
        docker push myregistry.azurecr.io/myapp:${{ github.sha }}     
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'myregistry.azurecr.io/myapp:${{ github.sha }}'
```

## <a name="clean-up-resources"></a>리소스 정리

Azure 앱, GitHub 리포지토리 및 키 자격 증명 모음이 더 이상 필요하지 않은 경우 앱, GitHub 리포지토리 및 키 자격 증명 모음에 대한 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Key Vault에 대해 자세히 알아보기](/azure/key-vault/general/overview)