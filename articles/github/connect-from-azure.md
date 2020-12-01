---
title: GitHub 및 Azure 연결
description: Azure 및 기타 서비스에서 GitHub에 연결하기 위한 리소스
author: N-Usha
ms.author: ushan
ms.topic: reference
ms.service: azure
ms.date: 11/17/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 5462d7ca3618869232296a9a6739ebe5adcefdb1
ms.sourcegitcommit: 4dac39849ba2e48034ecc91ef578d11aab796e58
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94983632"
---
# <a name="use-github-actions-to-connect-to-azure"></a>GitHub Actions를 사용하여 Azure에 연결

[Azure PowerShell](https://github.com/Azure/PowerShell) 또는 [Azure CLI](https://github.com/Azure/CLI)에서 [Azure 로그인](https://github.com/Azure/login)을 사용하여 Azure 리소스와 상호 작용하는 방법을 알아봅니다.

GitHub Actions 워크플로에서 Azure PowerShell 또는 Azure CLI를 사용하려면 먼저 [Azure 로그인](https://github.com/marketplace/actions/azure-login) 작업을 사용하여 로그인해야 합니다.
Azure 로그인 작업을 사용하면 [Azure AD 서비스 주체](/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)의 컨텍스트에서 워크플로의 명령을 실행할 수 있습니다.

로그인 작업을 설정한 후에는 Azure CLI 또는 Azure PowerShell을 사용할 수 있습니다.

기본적으로 이 작업은 Azure CLI를 사용하여 로그인한 다음, Azure CLI에 대한 GitHub 작업 실행기 환경을 설정합니다. Azure 로그인 작업의 `enable-AzPSSession` 속성을 통해 Azure PowerShell을 사용할 수 있습니다. 이 방식으로 Azure PowerShell 모듈을 사용하여 GitHub 작업 실행기 환경을 설정합니다.

## <a name="create-a-service-principal-and-add-it-to-github-secret"></a>서비스 주체를 만들어서 GitHub 비밀에 추가

[Azure 로그인](https://github.com/marketplace/actions/azure-login)을 사용하려면 먼저 Azure 서비스 주체를 GitHub 리포지토리에 비밀로 추가해야 합니다.

이 예제에서는 Azure에서 인증하는 데 사용할 수 있는 `AZURE_CREDENTIALS`라는 비밀을 만듭니다.  

1. 기존 애플리케이션이 없는 경우 서비스 주체에 사용할 [새 Active Directory 애플리케이션](/azure/active-directory/develop/howto-create-service-principal-portal#register-an-application-with-azure-ad-and-create-a-service-principal&preserve-view=true)을 등록합니다.

    ```azurecli-interactive
        appName="myApp"

        az ad app create \
        --display-name $appName \
        --homepage "http://localhost/$appName" \
        --identifier-uris http://localhost/$appName
    ```

1. Azure Portal에서 앱에 대한 [새 서비스 주체를 만듭니다](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest&preserve-view=true). 

    ```azurecli-interactive
        az ad sp create-for-rbac --name "myApp" --role contributor \
                                    --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                    --sdk-auth
    ```

1. 서비스 주체에 대한 JSON 개체를 복사합니다.

    ```json
    {
        "clientId": "<GUID>",
        "clientSecret": "<GUID>",
        "subscriptionId": "<GUID>",
        "tenantId": "<GUID>",
        (...)
    }
    ```

1. GitHub 리포지토리를 열고 **설정** 으로 이동합니다.

    :::image type="content" source="media/github-repo-settings.png" alt-text="탐색에서 설정 선택":::

1. **비밀** 과 **새 비밀** 을 차례로 선택합니다.

    :::image type="content" source="media/select-secrets.png" alt-text="비밀 추가 선택":::

1. 이름이 `AZURE_CREDENTIALS`인 서비스 주체에 대한 JSON 개체를 붙여넣습니다. 

    :::image type="content" source="media/azure-secret-add.png" alt-text="GitHub에서 비밀 추가":::

1. **비밀 추가** 를 선택하여 저장합니다.

## <a name="use-the-azure-login-action"></a>Azure 로그인 작업 사용

[Azure 로그인 작업](https://github.com/Azure/login)에서 서비스 주체 비밀을 사용하여 Azure에 인증합니다.

이 워크플로에서는 `secrets.AZURE_CREDENTIALS`에 저장된 서비스 주체 세부 정보를 Azure 로그인 작업에서 사용하여 인증합니다. 그런 다음, Azure CLI 작업을 실행합니다. 워크플로 파일의 GitHub 비밀 참조에 대한 자세한 내용은 GitHub Docs의 [워크플로에서 암호화된 비밀 사용](https://docs.github.com/en/free-pro-team@latest/actions/reference/encrypted-secrets#using-encrypted-secrets-in-a-workflow)을 참조하세요.

제대로 작동하는 Azure 로그인 단계가 있으면 [Azure PowerShell](https://github.com/Azure/PowerShell) 또는 [Azure CLI](https://github.com/Azure/CLI) 작업을 사용할 수 있습니다. [Azure 웹앱 배포](https://github.com/Azure/webapps-deploy) 및 [Azure 함수](https://github.com/Azure/functions-action)와 같은 다른 Azure 작업을 사용할 수도 있습니다.

```yaml
on: [push]

name: AzureLoginSample

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Log in with Azure
        uses: azure/login@v1
        with:
          creds: '${{ secrets.AZURE_CREDENTIALS }}'
```

## <a name="use-the-azure-powershell-action"></a>Azure PowerShell 작업 사용

이 예제에서는 [Azure 로그인 작업](https://github.com/Azure/login)을 사용하여 로그인한 다음, [Azure PowerShell 작업](https://github.com/azure/powershell)을 사용하여 리소스 그룹을 검색합니다.

```yaml
on: [push]

name: AzureLoginSample

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Log in with Azure
        uses: azure/login@v1
        with:
          creds: '${{ secrets.AZURE_CREDENTIALS }}'
          enable-AzPSSession: true
      - name: Azure PowerShell Action
        uses: Azure/powershell@v1
        with:
          inlineScript: Get-AzVM -ResourceGroupName "< YOUR RESOURCE GROUP >"
          azPSVersion: 3.1.0
```

## <a name="use-the-azure-cli-action"></a>Azure CLI 작업 사용

이 예제에서는 [Azure 로그인 작업](https://github.com/Azure/login)을 사용하여 로그인한 다음, [Azure CLI 작업](https://github.com/Azure/CLI)을 사용하여 리소스 그룹을 검색합니다.


```yaml
on: [push]

name: AzureLoginSample

jobs:
build-and-deploy:
    runs-on: ubuntu-latest
    steps:

    - name: Log in with Azure
        uses: azure/login@v1
        with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Azure CLI script
        uses: azure/CLI@v1
        with:
        azcliversion: 2.0.72
        inlineScript: |
            az account show
            az storage -h
```

## <a name="connect-with-other-azure-services"></a>다른 Azure 서비스에서 연결

다음 문서에서는 Azure 및 기타 서비스에서 GitHub에 연결하는 방법에 대해 자세히 설명합니다.  

### <a name="azure-active-directory"></a>Azure Active Directory 

- [Azure AD(Single Sign-On)를 사용하여 GitHub Enterprise에 로그인](/azure/active-directory/saas-apps/github-tutorial)   

### <a name="power-bi"></a>Power BI

- [GitHub와 Power BI 연결](/power-bi/service-connect-to-github)   

### <a name="connectors"></a>커넥터

- [Azure Logic Apps, Power Automate 및 Power Apps용 GitHub 커넥터](/connectors/github/)   

### <a name="azure-databricks"></a>Azure Databricks

- [GitHub를 Notebook 버전 제어로 사용](/azure/databricks/notebooks/github-version-control) 

> [!div class="nextstepaction"]
> [GitHub에서 Azure로 앱 배포](deploy-to-azure.md)
