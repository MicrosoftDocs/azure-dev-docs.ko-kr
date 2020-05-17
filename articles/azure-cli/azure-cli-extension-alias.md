---
title: Azure CLI 별칭 확장
description: Azure CLI 별칭 확장 사용 방법
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 4d324235360e017970a215226572e3effeb9b917
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82030855"
---
# <a name="the-azure-cli-alias-extension"></a>Azure CLI 별칭 확장

별칭 확장을 사용하면 기존 명령을 사용하여 Azure CLI에 대한 사용자 정의 명령을 정의할 수 있습니다. 별칭은 바로 가기를 허용하여 워크플로 단순하게 유지하는 데 도움이 됩니다. 별칭은 Jinja2 템플릿 엔진을 기반으로 하기 때문에 고급 인수 처리도 제공합니다.

> [!NOTE]
> 별칭 확장은 공개 미리 보기에 있습니다. 기능 및 구성 파일 형식이 변경될 수 있습니다.

## <a name="install-the-alias-extension"></a>별칭 확장 설치

별칭 확장을 사용하는 데 필요한 최소 Azure CLI 버전은 **2.0.28**입니다. CLI 버전을 확인하려면 `az --version`을 실행합니다. 설치를 업데이트해야 하는 경우 [Azure CLI 설치](./install-azure-cli.md)의 지침을 따릅니다.

[az extension add](/cli/azure/extension#az-extension-add) 명령을 사용하여 확장을 설치합니다.

```azurecli-interactive
az extension add --name alias
```

[az 확장 목록](/cli/azure/extension#az-extension-list)을 사용하여 확장 설치를 확인합니다. 별칭 확장이 제대로 설치된 경우 명령 출력에 나열됩니다.

```azurecli-interactive
az extension list --output table --query '[].{Name:name}'
```

```output
Name
------
alias
```

## <a name="keep-the-extension-up-to-date"></a>확장을 최신 상태로 유지

별칭 확장은 현재 개발 중이며 새 버전은 정기적으로 릴리스됩니다. CLI를 업데이트하는 경우 새 버전이 설치되지 않습니다. [az 확장 업데이트](/cli/azure/extension#az-extension-update)를 사용하여 확장 업데이트를 설치합니다.

```azurecli-interactive
az extension update --name alias
```

## <a name="manage-aliases-for-the-azure-cli"></a>Azure CLI에 대한 별칭 관리

별칭 확장을 사용하여 다른 CLI 명령에 대한 별칭을 만들고 관리할 수 있습니다. 모든 사용 가능한 명령 및 매개 변수 세부 정보를 보려면 `--help`을 사용하여 별칭 명령을 실행합니다.

```azurecli-interactive
az alias --help
```

## <a name="create-simple-alias-commands"></a>간단한 별칭 명령 만들기

별칭을 사용하면 기존 명령 그룹이나 명령 이름을 단축할 수 있습니다. 예를 들어 `group` 명령 그룹을 `rg`로, `list` 명령을 `ls`로 단축할 수 있습니다.

```azurecli-interactive
az alias create --name rg --command group
az alias create --name ls --command list
```

이렇게 새로 정의된 별칭은 정의가 될 수 있는 어느 곳에서든 사용할 수 있습니다.

```azurecli-interactive
az rg list
az rg ls
az vm ls
```

`az`이 명령의 일부로 포함되지 않습니다.

별칭은 전체 명령의 바로 가기일 수도 있습니다. 다음 예는 사용 가능한 리소스 그룹과 해당 위치를 테이블로 출력하여 나열합니다.

```azurecli-interactive
az alias create --name ls-groups --command "group list --query '[].{Name:name, Location:location}' --output table"
```

이제 `ls-groups`는 다른 CLI 명령처럼 실행될 수 있습니다.

```azurecli-interactive
az ls-groups
```

## <a name="create-an-alias-command-with-arguments"></a>인수를 사용하여 별칭 명령 만들기

별칭 이름에 위치 인수를 `{{ arg_name }}`으로 포함하여 별칭 명령에 추가할 수도 있습니다. 중괄호 안의 공백이 필요 합니다.

```azurecli-interactive
az alias create --name "alias_name {{ arg1 }} {{ arg2 }} ..." --command "invoke_including_args"
```

다음 예제 별칭은 위치 인수를 사용하여 VM의 공용 IP 주소를 얻는 방법을 보여줍니다.

```azurecli-interactive
az alias create \
    --name "get-vm-ip {{ resourceGroup }} {{ vmName }}" \
    --command "vm list-ip-addresses --resource-group {{ resourceGroup }} --name {{ vmName }}
        --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress"
```

이 명령을 실행할 때 위치 인수에 값을 제공합니다.

```azurecli-interactive
az get-vm-ip MyResourceGroup MyVM
```

별칭 명령에서 환경 변수를 사용할 수도 있으며, 이 명령은 런타임에서 평가됩니다. 다음 예는 `eastus`에 리소스 그룹을 만들고 `owner` 태그를 추가하는 `create-rg` 별칭을 추가합니다. 이 태그에는 로컬 환경 변수 `USER`의 값이 지정됩니다.

```azurecli-interactive
az alias create \
    --name "create-rg {{ groupName }}" \
    --command "group create --name {{ groupName }} --location eastus --tags owner=\$USER"
```

별칭의 명령 내부 환경 변수를 등록하려면 달러 기호 `$`이 이스케이프 되어야 합니다.

## <a name="process-arguments-using-jinja2-templates"></a>Jinja2 템플릿을 사용하는 프로세스 인수

[Jinja2](http://jinja.pocoo.org/docs/2.10/)로 별칭 확장에서 인수 대체를 수행합니다. Jinja2 템플릿은 인수 조작을 허용합니다.

Jinja2 템플릿을 사용하면 기본 명령과 다른 인수 유형을 사용하는 별칭을 작성할 수 있습니다. 예를 들어 스토리지 URL을 사용하는 별칭을 만들 수 있습니다. 그런 다음 이 URL을 구문 분석하여 계정 및 컨테이너 이름을 스토리지 명령에 전달합니다.

```azurecli-interactive
az alias create \
    --name 'storage-ls {{ url }}' \
    --command "storage blob list
        --account-name {{ url.replace('https://', '').split('.')[0] }}
        --container-name {{ url.replace('https://', '').split('/')[1] }}"
```

Jinja2 템플릿 엔진에 대한 자세한 내용은 [Jinja2 설명서](http://jinja.pocoo.org/docs/2.10/templates/)를 참조하세요.

## <a name="alias-configuration-file"></a>별칭 구성 파일

별칭을 만들고 수정하는 또 다른 방법은 별칭 구성 파일을 변경하는 것입니다. 별칭 명령 정의는 `$AZURE_USER_CONFIG/alias`에있는 구성 파일에 작성됩니다. `AZURE_USER_CONFIG`의 기본값은 macOS와 Linux의 경우 `$HOME/.azure`이고 Windows의 경우 `%USERPROFILE%\.azure`입니다. 별칭 구성 파일은 INI 구성 파일 형식으로 작성됩니다. 별칭 명령의 형식은 다음과 같습니다.

```ini
[alias_name]
command = invoked_commands
```

위치 인수가 있는 별칭의 경우, 별칭 명령에 대한 형식은 다음과 같습니다.

```ini
[alias_name {{ arg1 }} {{ arg2 }} ...]
command = invoked_commands_including_args
```

## <a name="create-an-alias-command-with-arguments-via-the-alias-configuration-file"></a>별칭 구성 파일을 통해 인수를 갖는 별칭 명령 만들기

다음 예제에서는 인수를 사용하는 명령에 대한 별칭을 보여 줍니다. 이 명령은 VM에 대한 공용 IP 주소를 가져옵니다. 별칭이 지정된 명령은 단일 줄에 있어야 하며, 별칭 이름에 모든 인수를 사용합니다.

```ini
[get-vm-ip {{ resourceGroup }} {{ vmName }}]
command = vm list-ip-addresses --resource-group {{ resourceGroup }} --name {{ vmName }} --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress
```

## <a name="uninstall-the-alias-extension"></a>별칭 확장 제거

확장을 설치 제거하려면 [az extension remove](/cli/azure/extension#az-extension-remove) 명령을 사용합니다.

```azurecli-interactive
az extension remove --name alias
```

확장 관련 버그 또는 기타 문제로 인해 제거한 경우 [GitHub 문제를 제출](https://github.com/Azure/azure-cli-extensions/issues)하면 수정 사항을 받을 수 있습니다.
