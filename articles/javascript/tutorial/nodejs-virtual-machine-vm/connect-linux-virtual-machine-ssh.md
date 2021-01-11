---
title: 가상 머신에 SSH로 연결
description: SSH를 사용하여 Linux 가상 머신에 연결합니다.  최신 Mac, Windows 또는 Linux 운영 체제를 사용하는 경우 터미널 기반 클라이언트 SSH가 이미 설치되어 있을 것입니다.
ms.topic: tutorial
ms.date: 01/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: c4b9577c93e37c28145abe8e976a93cd6ff39197
ms.sourcegitcommit: 075f39972e390e79ed09a3fcfdbfc776727e08fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97952495"
---
# <a name="4-connect-to-linux-virtual-machine-using-ssh"></a>4. SSH를 사용하여 Linux 가상 머신에 연결

자습서의 이 섹션에서는 터미널에서 SSH를 사용하여 가상 머신에 연결합니다. [SSH](https://www.ssh.com/ssh/)는 Azure Cloud Shell을 비롯한 여러 최신 셸에 제공되는 일반적인 도구입니다. 

## <a name="connect-with-ssh-and-change-web-app"></a>SSH를 통해 연결하고 웹앱 변경

이전 단계와 동일한 터미널 또는 셸 창을 사용합니다. 

1. 다음 명령을 사용하여 원격 가상 머신에 연결합니다. 이 프로세스에서는 VM을 만들 때 생성되어 로컬 머신에 배치된 SSH 키를 SSH 클라이언트가 찾을 수 있다고 가정합니다. 연결을 계속할지 묻는 메시지가 표시되면 `yes`로 대답합니다. 연결이 완료되면 터미널 프롬프트가 원격 가상 머신을 나타내도록 변경됩니다. 

    `YOUR-PUBLIC-IP-ADDRESS`를 자신의 가상 머신 공용 IP로 바꿉니다. 

    ```console
    ssh azureuser@YOUR-PUBLIC-IP-ADDRESS
    ``` 

1. 다음 명령을 사용하여 가상 머신에서 현재 있는 위치를 파악합니다. 현재 azureuser 루트 `/home/azureuser`에 있을 것입니다. 

    ```bash
    pwd
    ```

1. 웹앱은 하위 디렉터리 `myapp`에 있습니다. `myapp` 디렉터리로 변경하고 콘텐츠를 나열합니다.

    ```bash
    cd myapp && ls -l
    ```

    가상 머신과 npm 패키지 파일에 복제되는 GitHub 리포지토리를 나타내는 다음과 같은 콘텐츠가 표시됩니다.
    
    ```console
    -rw-r--r--   1 root root   891 Nov 11 20:23 cloud-init-github.txt
    -rw-r--r--   1 root root  1347 Nov 11 20:23 index-logging.js
    -rw-r--r--   1 root root   282 Nov 11 20:23 index.js
    drwxr-xr-x 190 root root  4096 Nov 11 20:23 node_modules
    -rw-r--r--   1 root root 84115 Nov 11 20:23 package-lock.json
    -rw-r--r--   1 root root   329 Nov 11 20:23 package.json
    -rw-r--r--   1 root root   697 Nov 11 20:23 readme.md
    ```

## <a name="install-monitoring-sdk"></a>모니터링 SDK 설치

[Application Insights용 Azure SDK 클라이언트 라이브러리](https://www.npmjs.com/package/applicationinsights)를 설치합니다.

```bash
sudo npm install --save applicationinsights
```

## <a name="add-monitoring-instrumentation-key"></a>모니터링 계측 키 추가

1. [Nano](https://www.nano-editor.org/dist/latest/nano.html#Editor-Basics) 편집기를 사용하여 `package.json` 파일을 변경합니다.

    ```bash
    sudo nano package.json
    ```

1. 환경 변수를 포함하도록 파일의 시작 스크립트를 편집합니다. `REPLACE-WITH-YOUR-KEY`를 해당하는 계측 키 값으로 바꿉니다.

    ```json
    "start": "APPINSIGHTS_INSTRUMENTATIONKEY=REPLACE-WITH-YOUR-KEY pm2 start index.js --watch --log /var/log/pm2.log"
    ```

1. 다음 명령을 사용하여 PM2를 중지했다가 다시 시작합니다.

    ```bash
    sudo npm run-script stop && sudo npm start
    ```

    Azure 클라이언트 라이브러리는 이제 _node_modules_ 디렉터리에 있으며 키는 앱에 환경 변수로 전달됩니다. 다음 단계는 필요한 코드를 `index.js`에 추가하는 것입니다. 

1. 터미널을 VM에 연결한 상태로 계속 열어 둡니다. 다음 단계에서도 사용할 것입니다.

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure cloud에 기록하도록 Azure SDK 클라이언트 코드 추가](azure-monitor-application-insights-nodejs-expressjs-code.md) 