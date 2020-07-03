---
title: Java CA 저장소에 Azure 루트 인증서 추가
description: Java CA(인증 기관) 루트 인증서(cacerts) 저장소에 Microsoft Azure용 CA 인증서를 추가하는 방법에 대해 알아봅니다.
documentationcenter: java
ms.assetid: d3699b0a-835c-43fb-844d-9c25344e5cda
ms.service: multiple
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/13/2018
ms.openlocfilehash: 0dc5459ab4898deecebbc199e62f5dccc8ee6f7d
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85790745"
---
# <a name="adding-a-root-certificate-to-the-java-ca-certificates-store"></a>Java CA 인증서 저장소에 루트 인증서 추가

Azure 서비스(예: Azure Service Bus)를 사용하는 애플리케이션은 Baltimore CyberTrust Root 인증서를 신뢰해야 합니다. 이 인증서는 시스템에 이미 설치되어 있을 수도 있지만, 이 자습서의 단계에서는 오라클의  **keytool**을 사용하여 Java CA(cacerts) 저장소에 Azure 서비스에 사용할 필요한 CA(인증 기관) 루트 인증서를 추가하는 방법을 보여줍니다.

오라클의 keytool 유틸리티는 _키 및 인증서 관리 도구_이며, 개발자는 Java에서 사용할 신뢰할 수 있는 인증서 목록을 관리할 수 있습니다. JDK를 압축하고 Azure 프로젝트의 *approot* 폴더에 추가하기 전에 keytool을 사용하여 CA 인증서를 추가하거나 keytool을 사용하여 인증서를 추가하는 Azure 시작 작업을 실행할 수 있습니다.

2013년 4월 15일부터 Azure는 GTE CyberTrust Global Root 인증서에서 Baltimore CyberTrust Root 인증서로 마이그레이션을 시작했습니다. 다음 단계에서는 keytool을 사용하여 Java CA 인증서(cacerts) 저장소에 Baltimore CyberTrust 루트 인증서를 추가하는 방법을 보여줍니다.

> [!NOTE]
> 이 문서의 단계를 사용하여 다른 신뢰할 수 있는 인증 기관의 루트 인증서를 신뢰하도록 Java SDK를 구성할 수 있습니다. 예를 들어, [GeoTrust 루트 인증서](https://www.geotrust.com/resources/root-certificates/)의 인증서 목록에서 루트 인증서를 선택할 수 있습니다.

## <a name="determining-which-root-certificates-are-installed"></a>어떤 루트 인증서가 설치되었는지 확인

Baltimore 인증서가 cacerts 저장소에 이미 설치되어 있을 수 있으므로 다음 단계를 사용하여 이미 설치되어 있는지 확인해야 합니다.

1. 관리자 명령 프롬프트에서 JDK의 *jdk\jre\lib \security* 폴더로 이동한 후 다음 명령을 실행하여 시스템에 설치된 인증서를 나열하십시오.

   ```shell
   keytool -list -keystore cacerts
   ```

1. 저장소 암호를 묻는 경우, 기본 암호는 `changeit`입니다.

   > [!NOTE]
   > 저장소 암호를 변경하려면 <https://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>의 keytool 설명서를 참조하세요.

1. `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`의 지문 인증서가 보이지 않으면, 다음 섹션의 단계를 사용하여 인증서를 다운로드하고 설치하십시오.

## <a name="to-add-a-root-certificate-to-the-cacerts-store"></a>cacerts 저장소에 루트 인증서를 추가하려면

1. <https://cacert.omniroot.com/bc2025.crt>에서 Baltimore CyberTrust 루트 인증서를 다운로드하고 *jdk\jre\lib\security* 폴더에 확장명이 *.crt*인 로컬 파일로 저장합니다. 이 예제에서는 Baltimore CyberTrust 루트 인증서 파일을 *bc2025.crt*로 다운로드한 것으로 가정합니다.

   > [!NOTE]
   > Baltimore CyberTrust 루트 인증서의 시리얼 번호는 `02:00:00:b9`이며, SHA1 지문은 `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`입니다.

2. 다음 명령을 사용하여 cacerts 저장소로 인증서를 가져오기 합니다:

   ```shell
   keytool -keystore cacerts -importcert -alias bc2025ca -file bc2025.crt
   ```

   위치:

   |  매개 변수   |                              Description                               |
   |--------------|------------------------------------------------------------------------|
   | `keystore`   | 인증서 저장소 지정.                                       |
   | `importcert` | 인증서를 가져오고 있다는 것을 지정.                        |
   | `alias`      | 해당 인증서의 별칭을 지정.                                |
   | `file`       | 가져오기 하는 루트 인증서의 파일명을 지정. |

3. 인증서를 신뢰하라는 메시지가 뜨면 지문을 `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`로 검증하고, 지문이 올바른 경우 `y`를 입력합니다.

4. 다음 명령을 실행하여 CA 인증서를 가져왔는지 확인합니다.

   ```shell
   keytool -list -keystore cacerts
   ```

JDK에 루트 인증서를 성공적으로 추가한 후에는 JDK의 내용을 압축하여 Azure 프로젝트의  *approot* 폴더에 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

keytool 유틸리티에 대한 자세한 내용은, <https://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>을 참조합니다.

Java에 대한 자세한 내용은 [Java 개발자용 Azure](/azure/developer/java)를 참조하세요.

Azure에서 개발하는 경우 사용 가능한 지원되는 JDK에 대한 자세한 내용은 <https://aka.ms/azure-jdks>를 참조하세요.
