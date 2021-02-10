---
title: Java용 Azure SDK 사용 시의 네트워킹 문제 해결
description: Java용 Azure SDK 사용과 관련된 네트워킹 문제를 해결하는 방법에 대한 개요입니다.
author: alzimmermsft
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: alzimmer
ms.openlocfilehash: 63852f253a648e473ba91ac361bc5d9d0629b8f1
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528588"
---
# <a name="troubleshoot-networking-issues"></a>네트워킹 문제 해결

이 문서에서는 다양하게 복잡한 네트워킹 문제를 진단할 수 있는 몇 가지 도구에 대해 설명합니다. 이러한 문제에는 서비스의 예기치 않은 응답 값 문제 해결부터 연결이 닫힌 예외의 근본 원인에 이르기까지 다양한 시나리오가 포함됩니다.

클라이언트 쪽 문제를 해결하기 위해 Java용 Azure 클라이언트 라이브러리는 [Java용 Azure SDK에서 로깅 구성](logging-overview.md)에서 설명한 대로 일관되고 강력한 로깅 사례를 제공합니다. 그러나 클라이언트 라이브러리는 다양한 프로토콜을 통해 네트워크 호출을 수행하므로 제공된 문제 해결 범위를 벗어나는 문제 해결 시나리오가 발생할 수 있습니다. 이러한 문제가 발생하는 경우 해결 방법은 이 문서에서 설명하는 외부 도구를 사용하여 네트워킹 문제를 진단하는 것입니다.

## <a name="fiddler"></a>Fiddler

[Fiddler](https://docs.telerik.com/fiddler-everywhere/introduction)는 전달된 요청과 응답을 있는 그대로 기록할 수 있도록 하는 HTTP 디버깅 프록시입니다. 캡처한 원시 요청 및 응답은 서비스에서 예기치 않은 요청을 받거나 클라이언트에서 예기치 않은 응답을 받는 시나리오의 문제를 해결하는 데 도움이 될 수 있습니다. Fiddler를 사용하려면 HTTP 프록시를 사용하여 클라이언트 라이브러리를 구성해야 합니다. HTTPS를 사용하는 경우 암호 해독된 요청 및 응답 본문을 검사해야 하는 추가 구성이 필요합니다.

### <a name="add-an-http-proxy"></a>HTTP 프록시 추가

HTTP 프록시를 추가하려면 [Java용 Azure SDK에서 프록시 구성](proxying.md)의 지침을 따릅니다. 8888 포트에서 기본 Fiddler 주소인 `localhost`를 사용해야 합니다.

### <a name="enable-https-decryption"></a>HTTPS 암호 해독 사용

Fiddler는 기본적으로 HTTP 트래픽만 캡처할 수 있습니다. 애플리케이션에서 HTTPS를 사용하는 경우 HTTPS 트래픽을 캡처할 수 있도록 Fiddler의 인증서를 신뢰하는 추가 단계를 수행해야 합니다. 자세한 내용은 Fiddler 설명서의 [HTTPS Menu(HTTPS 메뉴)](https://docs.telerik.com/fiddler-everywhere/user-guide/settings/https)를 참조하세요.

다음 절차에서는 JRE(Java Runtime Environment)를 사용하여 인증서를 신뢰하는 방법에 대해 설명합니다. 인증서를 신뢰하지 않으면 Fiddler를 통한 HTTPS 요청이 보안 경고와 함께 실패할 수 있습니다.

**Linux/macOS**

1. Fiddler의 인증서를 내보냅니다.
1. JRE의 keytool(일반적으로 `jre/bin`)을 찾습니다.
1. JRE의 cacert(일반적으로 `jre/lib/security`)를 찾습니다.
1. Bash 창을 열고, 다음 명령을 실행하여 인증서를 가져옵니다.

   ```bash
   sudo keytool -import -file <location of Fiddler certificate> -keystore <location of cacert> -alias Fiddler
   ```

1. 암호를 입력합니다.
1. 인증서를 신뢰합니다.

**Windows**

1. Fiddler의 인증서를 내보냅니다.
1. JRE의 keytool(일반적으로 `jre\bin`)을 찾습니다.
1. JRE의 cacert(일반적으로 `jre\lib\security`)를 찾습니다.
1. 명령 프롬프트를 열고, 다음 명령을 실행하여 인증서를 가져옵니다.

   ```cmd
   keytool.exe -import -file <location of Fiddler certificate> -keystore <location of cacert> -alias Fiddler
   ```

1. 암호를 입력합니다.
1. 인증서를 신뢰합니다.

## <a name="wireshark"></a>Wireshark

[Wireshark](https://www.wireshark.org/)는 애플리케이션 코드를 변경하지 않고도 네트워크 트래픽을 캡처할 수 있는 네트워크 프로토콜 분석기입니다. Wireshark는 쉽게 구성할 수 있으며, 하위 수준의 특정 네트워크 트래픽을 광범위하게 캡처할 수 있습니다. 이 기능은 원격 호스트에서 연결을 닫거나 작업 중에 연결이 닫히는 경우와 같은 문제 해결 시나리오에 유용합니다. Wireshark GUI는 TCP 재전송, RST 등과 같은 고유한 캡처 사례를 식별하는 색 구성표를 사용하여 캡처를 표시합니다. 또한 캡처 시간 또는 분석 중에 캡처를 필터링할 수도 있습니다.

### <a name="configure-a-capture-filter"></a>캡처 필터 구성

캡처 필터는 분석을 위해 캡처되는 네트워크 호출 수를 줄입니다. 캡처 필터를 사용하지 않으면 Wireshark에서 네트워크 인터페이스를 통과하는 모든 트래픽을 캡처합니다. 이 동작은 대부분이 조사하는 데 노이즈가 될 수 있는 대량의 데이터를 생성할 수 있습니다. 캡처 필터를 사용하면 캡처되는 네트워크 트래픽의 범위를 우선적으로 지정하여 조사 대상을 지정하는 데 도움이 됩니다. 자세한 내용은 Wireshark 설명서의 [Capturing Live Network Data(라이브 네트워크 데이터 캡처)](https://www.wireshark.org/docs/wsug_html_chunked/ChapterCapture.html)를 참조하세요.

다음 예에서는 캡처 필터를 추가하여 특정 호스트에서 보내거나 받는 네트워크 트래픽을 캡처합니다.

Wireshark에서 **Capture(캡처) > Capture Filters(캡처 필터)...** 로 차례로 이동하고, 값이 `host <host IP or hostname>`인 새 필터를 추가합니다. 이 필터는 해당 호스트에서 들어오고 나가는 트래픽만 캡처합니다. 애플리케이션에서 여러 호스트와 통신하는 경우 여러 캡처 필터를 추가하거나 'OR' 연산자를 사용하여 호스트 IP/호스트 이름을 추가하여 더 느슨한 캡처 필터링을 제공할 수 있습니다.

### <a name="capture-to-disk"></a>디스크로 캡처

오랫동안 애플리케이션을 실행하여 예기치 않은 네트워킹 예외를 재현하고 이 예외로 이어지는 트래픽을 확인해야 할 수 있습니다. 또한 모든 캡처를 메모리에 유지 관리하지 못할 수도 있습니다. 다행히도 Wireshark는 후처리에 사용할 수 있도록 캡처를 디스크에 기록할 수 있습니다. 이 방법은 문제를 재현하는 동안 메모리가 부족할 수 있는 위험을 방지합니다. 자세한 내용은 Wireshark 설명서의 [File Input, Output, And Printing(파일 입력, 출력 및 인쇄)](https://www.wireshark.org/docs/wsug_html_chunked/ChapterIO.html)을 참조하세요.

다음 예에서는 캡처를 여러 파일로 디스크에 유지하도록 Wireshark를 설정합니다. 여기서 파일은 100,000개 캡처 또는 50MB 크기로 분할됩니다.

Wireshark에서 **Capture(캡처) > Options(옵션)** 로 차례로 이동하고, **Output(출력)** 탭을 찾은 다음, 사용할 파일 이름을 입력합니다. 이렇게 구성하면 Wireshark에서 캡처를 단일 파일에 유지합니다. 여러 파일에 캡처하도록 설정하려면 **Create a new file automatically(자동으로 새 파일 만들기)** 를 선택한 다음, **after 100000 packets(100,000개 패킷 이후)** 및 **after 50 megabytes(50MB 이후)** 를 선택합니다. 이렇게 구성하면 조건자 중 하나가 일치할 때 Wireshark에서 새 파일을 만듭니다. 각각의 새 파일은 입력한 파일 이름과 동일한 기본 이름을 사용하고 고유 식별자를 추가합니다. Wireshark에서 만들 수 있는 파일 수를 제한하려면 **Use a ring buffer with X files(X개 파일에 링 버퍼 사용)** 를 선택합니다. 이 옵션은 지정된 수의 파일에만 기록하도록 Wireshark를 제한합니다. 이 파일 수에 도달하면 Wireshark에서 가장 오래된 파일부터 덮어쓰기 시작합니다.

### <a name="filter-captures"></a>캡처 필터링

예를 들어 애플리케이션에서 다양한 프로토콜을 사용하여 여러 호스트와 통신하는 경우와 같이 Wireshark에서 캡처하는 트래픽의 범위를 엄격하게 지정할 수 없는 경우도 있습니다. 이 시나리오에서는 일반적으로 위에서 설명한 지속형 캡처를 사용하여 네트워크 캡처 후 분석을 더 쉽게 실행할 수 있습니다. Wireshark는 캡처 분석을 위해 필터와 비슷한 구문을 지원합니다. 자세한 내용은 Wireshark 설명서의 [Working With Captured Packets(캡처한 패킷으로 작업)](https://www.wireshark.org/docs/wsug_html_chunked/ChapterWork.html)를 참조하세요.

다음 예에서는 지속형 캡처 파일을 로드하고, `ip.src_host==<IP>`를 기준으로 필터링합니다.

Wirelesshark에서 **File(파일) > Open(열기)** 으로 차례로 이동하고, 위에서 사용한 파일 위치에서 지속형 캡처를 로드합니다. 파일이 메뉴 모음 아래에 로드되면 필터 입력이 표시됩니다. 필터 입력에서 `ip.src_host==<IP>`를 입력합니다. 이 필터는 원본이 `<IP>` IP의 호스트에 있는 캡처만 표시하도록 캡처 보기를 제한합니다.

## <a name="next-steps"></a>다음 단계

이 항목에서는 Java용 Azure SDK를 사용할 때 다양한 도구를 사용하여 네트워킹 문제를 진단하는 방법을 설명했습니다. 이제 고급 사용 시나리오에 익숙해졌으므로 SDK 자체에 대한 검색을 시작할 수 있습니다. 사용 가능한 API에 대한 자세한 내용은 [Java용 Azure SDK 라이브러리](azure-sdk-library-package-index.md)를 참조하세요.
