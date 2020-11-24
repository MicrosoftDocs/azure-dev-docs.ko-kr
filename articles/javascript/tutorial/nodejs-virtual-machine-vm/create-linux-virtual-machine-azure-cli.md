---
title: Linux 가상 머신 만들기
description: Azure CLI를 사용하여 가상 머신을 만들고 구성합니다. 자습서를 여기까지 진행했다면 현재 터미널 창이 열려 있고, 가상 머신을 만들려는 구독에서 Azure CLI를 통해 Azure 클라우드에 로그인되어 있을 것입니다.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: d2da4cac2a93ad953ada9cd98de679abba034908
ms.sourcegitcommit: a2a51e0c6530eb5794a2fe667cf4c9a60b2a7470
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625032"
---
# <a name="3-create-linux-virtual-machine-using-azure-cli"></a>3. Azure CLI를 사용하여 Linux 가상 머신 만들기

자습서의 이 섹션에서는 Azure CLI를 사용하여 가상 머신을 만들고 구성합니다. 자습서를 여기까지 진행했다면 현재 터미널 창이 열려 있고, 가상 머신을 만들려는 구독에서 Azure 클라우드에 로그인되어 있을 것입니다. 

Azure CLI의 단일 인스턴스에서 모든 Azure CLI 단계를 완료할 수 있습니다. 창을 닫거나 현재 Azure CLI를 사용 중인 위치를 변경할 경우(예를 들어 Cloud Shell과 로컬 터미널 사이에서 전환) 다시 로그인해야 합니다. 

## <a name="create-a-cloud-init-file-to-expedite-linux-virtual-machine-creation"></a>Linux 가상 머신을 신속하게 만들 수 있도록 cloud-init 파일 만들기

이 자습서에서는 cloud-init 구성 파일을 사용하여 NGINX 역방향 프록시 서버와 Express.js 서버를 만듭니다. NGINX는 Express.js 포트(3000)를 공용 포트(80)로 전달하는 데 사용됩니다. 

`runcmd`에는 다음과 같은 여러 작업이 있습니다.
* Node.js를 다운로드하여 설치
* 샘플 Express.js 리포지토리 복제
* Express.js 종속성 설치
* PM2를 사용하여 Express.js 앱 시작

1. `cloud-init-github.txt`라는 로컬 파일을 만들고 다음 콘텐츠를 이 파일에 저장하거나 [리포지토리의 파일](https://github.com/Azure-Samples/js-e2e-vm/blob/main/cloud-init-github.txt)을 로컬 컴퓨터에 저장할 수 있습니다. [cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/examples.html#yaml-examples) 형식의 파일은 Azure CLI 명령의 터미널 경로와 동일한 폴더에 있어야 합니다.

    :::code language="yaml" source="~/../js-e2e-vm/cloud-init-github.txt" :::

## <a name="create-a-virtual-machine-resource"></a>가상 머신 리소스 만들기 

1. 터미널에서 [Azure CLI 명령](/cli/azure/vm?view=azure-cli-latest#az_vm_create)을 입력하여 Linux 가상 머신의 Azure 리소스를 만듭니다. 이 명령은 cloud-init 파일로 VM을 만들고 SSH 키를 생성합니다. 실행 중인 명령은 키가 저장되는 위치를 표시합니다. 

    ```azurecli
    az vm create \
      --resource-group rg-demo-vm-eastus \
      --name demo-vm \
      --location eastus \
      --image UbuntuLTS \
      --admin-username azureuser \
      --generate-ssh-keys \
      --custom-data cloud-init-github.txt
    ```

    이 프로세스에 몇 분 정도 걸릴 수 있습니다. 프로세스가 완료되면 Azure CLI가 새 리소스에 대한 정보를 반환합니다. `publicIpAddress` 값을 그대로 유지합니다. 브라우저에서 웹앱을 살펴보고 VM에 연결하는 데 필요합니다. 
     

1. 생성된 가상 머신은 처음에는 열려 있는 포트가 _없습니다_. 웹앱을 공개적으로 사용할 수 있도록 다음 [Azure CLI 명령](/cli/azure/vm?view=azure-cli-latest#az_vm_open_port)을 사용하여 80 포트를 엽니다.

    ```azurecli
    az vm open-port \
      --port 80 \
      --resource-group rg-demo-vm-eastus \
      --name demo-vm
    ```

1. 웹 브라우저에서 공용 IP 주소를 사용하여 가상 머신이 사용 가능하고 현재 실행 중인지 확인합니다. `publicIpAddress`의 값을 사용하도록 URL을 변경합니다.

    ```HTTP
    http://YOUR-PUBLIC-IP-ADDRESS
    ```

    다음 이미지는 웹앱을 보여주지만, 여러분의 실제 앱에서는 다른 IP 주소가 사용될 것입니다. 리소스에서 게이트웨이 오류가 발생할 경우 1분 후 다시 시도하세요. 웹앱이 시작하는 데 1분 정도 걸릴 수 있습니다. 

    :::image type="content" source="../../media/tutorial-vm/basic-web-app.png" alt-text="Azure의 Linus 가상 머신에서 제공하는 간단한 앱":::

    웹앱에 대한 초기 코드 파일에는 NGINX 프록시를 통해 전달되는 클라이언트 IP 주소를 표시하는 단일 경로가 있습니다. 

    :::code language="JavaScript" source="~/../js-e2e-vm/index.js" :::

1. 터미널을 계속 열어 둡니다. 이 자습서 전체에서 사용할 것입니다.

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [SSH를 통해 VM에 연결](connect-linux-virtual-machine-ssh.md) 