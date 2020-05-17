---
title: Linux용 Azure CLI 수동 설치
description: Linux용 Azure CLI를 수동으로 설치하는 방법
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 09/09/2018
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 9a98da54f397c1fd03a7cc6b581a769afe84ef88
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82031075"
---
# <a name="install-azure-cli-on-linux-manually"></a>Linux에 Azure CLI 수동 설치

배포를 위한 Azure CLI용 패키지가 없다면 스크립트를 실행하여 수동으로 CLI를 설치하십시오.

[!INCLUDE [current-version](includes/current-version.md)]

> [!NOTE]
> 패키지 관리자를 사용하여 CLI를 설치하는 것이 좋습니다. 패키지 관리자가 항상 최신 업데이트를 갖추도록 확인하고 CLI 구성 요소의 안정성을 보장합니다. 수동으로 설치하기 전에 배포에 필요한 패키지가 있는지 확인합니다.

## <a name="prerequisites"></a>사전 요구 사항

CLI에는 다음과 같은 소프트웨어가 필요합니다.

* [Python 3.6.x, 3.7.x 또는 3.8.x](https://www.python.org/downloads/) 
* [libffi](https://sourceware.org/libffi/)
* [OpenSSL 1.0.2](https://www.openssl.org/source/)

> [!IMPORTANT]
>
> `2.1.0` 버전 이후 CLI에서 Python 2.7을 지원하지 않습니다. 따라서 새 버전은 더 이상 Python 2.7에서 올바르게 실행되도록 보장하지 않습니다.

## <a name="install-or-update"></a>설치 또는 업데이트

CLI 설치 및 업데이트 모두 설치 스크립트를 다시 실행해야 합니다. `curl`을 실행하여 CLI를 설치합니다.

```bash
curl -L https://aka.ms/InstallAzureCli | bash
```

또한 스크립트를 다운로드하여 로컬로 실행할 수 있습니다. 변경 내용을 적용하려면 셸을 다시 시작해야 합니다.

그런 다음 `az` 명령을 사용하여 Azure CLI를 실행할 수 있습니다. 로그인하려면, [az login](/cli/azure/reference-index#az-login) 명령을 사용합니다.

[!INCLUDE [interactive-login](includes/interactive-login.md)]

다른 인증 방법에 대한 자세한 내용은 [Azure CLI로 로그인](authenticate-azure-cli.md)을 참조하세요.

## <a name="troubleshooting"></a>문제 해결

수동 설치 중에 몇 가지 공통 문제가 발견됐습니다. 여기서 다루지 않은 문제가 발생하면 [GitHub에서 문제를 제출](https://github.com/Azure/azure-cli/issues)하세요.

### <a name="curl-object-moved-error"></a>"개체 이동됨" curl 오류

`-L` 매개 변수와 관련된 `curl`에서 오류가 발생하거나 "개체 이동됨"이라는 텍스트가 포함된 오류 메시지가 표시되면 `aka.ms` 리디렉션 전체 URL을 사용해 보세요.

```bash
curl https://azurecliprod.blob.core.windows.net/install | bash
```

### <a name="az-command-not-found"></a>`az` 명령을 찾을 수 없음

설치 후 명령을 실행할 수 없는 경우 `bash` 또는 `zsh`를 이용해 셸의 명령 해시 캐시를 지웁니다. 다음을 실행합니다.

```bash
hash -r
```

한 다음 문제가 해결되었는지 확인합니다.

설치 후 셸을 다시 시작하지 않아도 이 문제가 발생할 수 있습니다. `az` 명령의 위치가 `$PATH`여야 합니다. `az` 명령의 위치는 다음과 같습니다.

```bash
<install path>/bin
```

### <a name="proxy-blocks-connection"></a>프록시 연결 차단

[!INCLUDE[configure-proxy](includes/configure-proxy.md)]

설치 스크립트를 가져오려면 프록시에서 다음 주소에 대한 HTTPS 연결을 허용해야 합니다.

* `https://aka.ms/`
* `https://azurecliprod.blob.core.windows.net/`
* `https://pypi.python.org`
* 배포의 패키지 관리자(있는 경우)에서 사용하는 코어 패키지에 대한 엔드포인트

[!INCLUDE[troubleshoot-wsl.md](includes/troubleshoot-wsl.md)]

## <a name="uninstall"></a>제거

[!INCLUDE [uninstall-boilerplate.md](includes/uninstall-boilerplate.md)]

설치 시 선택한 위치에서 파일을 직접 삭제하여 CLI를 제거합니다. 기본 설치 위치는 `$HOME`입니다.

1. 설치된 CLI 파일을 제거합니다.

   ```bash
   rm -r <install location>/lib/azure-cli
   rm <install location>/bin/az
   ```

2. `$HOME/.bash_profile` 파일을 수정하여 다음 선을 제거합니다.

   ```text
   <install location>/lib/azure-cli/az.completion
   ```

3. `bash` 또는 `zsh`를 사용하는 경우 셸의 명령 캐시를 다시 로드합니다.

   ```bash
   hash -r
   ```

## <a name="next-steps"></a>다음 단계

Azure CLI를 설치한 후 해당 기능 및 일반 명령을 잠시 둘러보세요.

> [!div class="nextstepaction"]
> [Azure CLI 시작](get-started-with-azure-cli.md)
