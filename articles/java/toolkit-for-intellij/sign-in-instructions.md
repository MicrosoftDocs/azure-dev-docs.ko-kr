---
title: IntelliJ용 Azure 도구 키트에 대한 로그인 지침
description: IntelliJ용 Azure 도구 키트를 사용하여 Microsoft Azure에 로그인하는 방법을 알아봅니다.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: c5830ab871b78e586b502e0c6e2331700fa0149d
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379957"
---
# <a name="sign-in-instructions-for-the-azure-toolkit-for-intellij"></a>IntelliJ용 Azure 도구 키트에 대한 로그인 지침

[설치되면](https://www.jetbrains.com/help/idea/managing-plugins.html)[Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053)는 Azure 계정에 로그인하는 두 가지 방법을 제공합니다.

  - [디바이스 로그인에 의해 Azure 계정에 로그인](#sign-in-to-your-azure-account-by-device-login)
  - [서비스 주체에 의해 Azure 계정에 로그인](#sign-in-to-your-azure-account-by-service-principal)

[**로그아웃**](#sign-out-of-your-azure-account) 방법도 제공됩니다.

[!INCLUDE [basic-prerequisites](includes/basic-prerequisites.md)]

## <a name="sign-in-to-your-azure-account-by-device-login"></a>디바이스 로그인에 의해 Azure 계정에 로그인

디바이스 로그인에 의해 Azure에 로그인하려면 다음을 수행합니다.

1. IntelliJ IDEA로 프로젝트를 엽니다.

2. 사이드바 **Azure Explorer**를 연 다음, 위쪽 막대에서(또는 IDEA 메뉴 **도구/Azure/Azure 로그인**에서) **Azure 로그인** 아이콘을 클릭합니다.

   ![IntelliJ Azure 로그인 명령][I01]

3. **Azure 로그인** 창에서 **디바이스 로그인**을 선택한 다음, **로그인**을 클릭합니다.

   ![디바이스 로그인을 선택한 Azure 로그인 창][I02]

4. **Azure 디바이스 로그인** 대화 상자에서 **복사 및 열기**를 클릭합니다.

   ![Azure 로그인 대화 상자 창][I03]

5. 브라우저에서, 마지막 단계에서 **복사 및 열기**를 클릭할 때 복사한 디바이스 코드를 붙여넣은 후 **다음**을 클릭합니다.

   ![디바이스 로그인 브라우저][I04]

6. **구독 선택** 대화 상자에서 사용하려는 구독을 선택한 다음, **확인**을 클릭합니다.

   ![구독 선택 대화 상자][I05]

## <a name="sign-in-to-your-azure-account-by-service-principal"></a>서비스 주체에 의해 Azure 계정에 로그인

이 섹션에서는 서비스 주체 데이터를 포함하는 자격 증명 파일을 만드는 과정을 안내합니다. 이 프로세스를 완료하면 IntelliJ에서는 사용자가 프로젝트를 열 때 해당 자격 증명 파일을 사용해서 Azure에 자동으로 로그인합니다.

1. IntelliJ IDEA로 프로젝트를 엽니다.

1. 사이드바 **Azure Explorer**를 연 다음, 위쪽 막대에서(또는 IDEA 메뉴 **도구/Azure/Azure 로그인**에서) **Azure 로그인** 아이콘을 클릭합니다.
   ![IntelliJ Azure 로그인 명령][A01]

1. **Azure 로그인** 창에서 **서비스 주체**를 선택한 다음 **새로 만들기**를 클릭합니다.

   ![서비스 주체를 선택한 Azure 로그인 창][A02]

1. **Azure 디바이스 로그인** 대화 상자에서 **복사 및 열기**를 클릭합니다.

   ![Azure 로그인 대화 상자 창][A03]

1. 브라우저에서, 마지막 단계에서 **복사 및 열기**를 클릭할 때 복사한 디바이스 코드를 붙여넣은 후 **다음**을 클릭합니다.

   ![디바이스 로그인 브라우저][A04]

1. **인증 파일 만들기** 창에서 사용할 구독을 선택하고 대상 디렉터리를 선택한 다음, **시작**을 클릭합니다.

   ![인증 파일 만들기 창][A05]

1. **서비스 주체 만들기 상태** 대화 상자에서 파일을 성공적으로 만든 후 **확인**을 클릭합니다.

   ![서비스 주체 만들기 상태 대화 상자][A06]

1. **Azure 로그인** 창에서 **로그인**을 클릭합니다. 

   ![Azure 로그인 대화 상자][A07]

1. **구독 선택** 대화 상자에서 사용하려는 구독을 선택한 다음, **확인**을 클릭합니다.

   ![구독 선택 대화 상자][A08]

> 서비스 주체 인증 파일을 만든 후 8단계에서 시작하여 인증 파일을 선택하고 로그인할 수 있습니다.

## <a name="sign-out-of-your-azure-account"></a>Azure 계정에서 로그아웃

이전 단계에서 계정을 구성한 후에는 IntelliJ IDEA를 시작할 때마다 자동으로 로그인됩니다. 그러나 Azure 계정에서 로그아웃하려면 다음을 수행합니다.

* IntelliJ IDEA에서 Azure Explorer 사이드바를 열고 **Azure 로그아웃** 아이콘을 클릭하고 확인합니다(또는 IDEA 메뉴 **도구/Azure/Azure 로그아웃**에서).

   ![IntelliJ Azure 로그아웃 명령][L01]

## <a name="next-steps"></a>다음 단계

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

<!-- IMG List -->

[I01]: media/sign-in-instructions/I01.png
[I02]: media/sign-in-instructions/I02.png
[I03]: media/sign-in-instructions/I03.png
[I04]: media/sign-in-instructions/I04.png
[I05]: media/sign-in-instructions/I05.png

[A01]: media/sign-in-instructions/A01.png
[A02]: media/sign-in-instructions/A02.png
[A03]: media/sign-in-instructions/A03.png
[A04]: media/sign-in-instructions/A04.png
[A05]: media/sign-in-instructions/A05.png
[A06]: media/sign-in-instructions/A06.png
[A07]: media/sign-in-instructions/A07.png
[A08]: media/sign-in-instructions/A08.png
[A09]: media/sign-in-instructions/A09.png

[L01]: media/sign-in-instructions/L01.png
[L02]: media/sign-in-instructions/L02.png
[L03]: media/sign-in-instructions/L03.png
