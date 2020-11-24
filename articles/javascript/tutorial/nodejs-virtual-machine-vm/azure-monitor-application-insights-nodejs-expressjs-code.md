---
title: Application Insights 클라이언트 라이브러리 설치
description: Azure 클라우드에서 앱 로그 수집을 시작할 수 있도록 가상 머신의 코드에 Azure SDK 클라이언트 라이브러리를 추가합니다.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: a6fb29dd059922ead67b4cef5107c9407f40e294
ms.sourcegitcommit: ed749b136f0d6b876fd5866ba4a151c73af5b71f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94674713"
---
# <a name="5-install-azure-sdk-client-library-to-monitor-web-app"></a>5. 웹앱을 모니터링할 수 있도록 Azure SDK 클라이언트 라이브러리 설치

이 단계에서는 Azure 클라우드에서 앱 로그 수집을 시작할 수 있도록 가상 머신의 코드에 Azure SDK 클라이언트 라이브러리를 추가합니다.

## <a name="edit-indexjs-for-logging-with-azure-monitor-application-insights"></a>Azure Monitor Application Insights를 사용하여 로깅하도록 index.js 편집

1. 가상 머신에 제공된 [Nano](https://www.nano-editor.org/dist/latest/nano.html#Editor-Basics) 텍스트 편집기를 사용하여 `index.js`를 편집합니다. 

    ```bash
    sudo nano index.js
    ```

1. `index.js` 파일을 편집하여 아래에 강조 표시된 클라이언트 라이브러리와 로깅 코드를 추가합니다. 많은 bash 셸을 복사하여 nano에 직접 붙여넣을 수 있습니다. 

    :::code language="JavaScript" source="~/../js-e2e-vm/index-logging.js" highlight="5-28" :::

1. 마쳤으면 `Control+x`를 사용하여 종료한 다음, `y`를 사용하여 변경 내용을 저장합니다. 웹앱의 변경 내용은 PM2에 의해 감시됩니다. 이 변경으로 인해 VM을 다시 시작할 필요 없이 앱이 다시 시작됩니다. 

1. 웹 브라우저에서 새 `trace` 경로를 사용하여 앱을 테스트합니다.

    ```http
    http://REPLACE-WITH-YOUR-IP/trace
    ```

    브라우저에 IP 주소와 함께 `tracing...` 응답이 표시됩니다.

## <a name="viewing-the-vm-logs-for-nginx-and-pm2"></a>NGINX 및 PM2에 대한 VM 로그 보기

VM은 NGINX 및 PM2에 대한 로그를 수집하며, 이러한 로그를 볼 수 있습니다.

| 서비스 | 로그 위치|
|--|--|
|NGINX| /var/log/nginx/access.log|
|PM2| /var/log/pm2.log|

1. NGINX 프록시 서비스에 대한 VM 로그를 봅니다. 동일한 bash 셸에서 다음 명령을 사용하여 로그를 봅니다.

    ```bash
     cat /var/log/nginx/access.log
    ```

    로그에는 로컬 컴퓨터의 호출이 포함되어 있습니다. 

    ```console
     "GET /trace HTTP/1.1" 200 10 "-"
    ```

1. PM2 서비스에 대한 VM 로그를 봅니다. 동일한 bash 셸에서 다음 명령을 사용하여 로그를 봅니다.

    ```bash
     cat /var/log/pm2.log
    ```

    로그에는 로컬 컴퓨터의 호출이 포함되어 있습니다. 

    ```console
    Hello world app listening on port 3000!
    testing from trace route 76.22.73.183
    ```

1. 이 자습서에서는 VM에 다시 연결하지 않습니다. bash 셸에서 다음 명령을 사용하여 ssh 연결을 종료합니다. 

    ```bash
    exit
    ```

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Portal에서 로그 보기](azure-monitor-application-insights-logs.md) 