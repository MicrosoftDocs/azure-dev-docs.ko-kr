---
title: Eclipse용 Azure 도구 키트에 대한 로그인 지침
description: Eclipse용 Azure 도구 키트를 사용하여 Microsoft Azure에 로그인하는 방법을 알아봅니다.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 3be8734755455dfaebcf1478f4441058496f7999
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379977"
---
# <a name="sign-in-instructions-for-the-azure-toolkit-for-eclipse"></a>Eclipse용 Azure 도구 키트에 대한 로그인 지침

Eclipse용 Azure 도구 키트는 Azure 계정에 로그인하는 두 가지 방법을 제공합니다.

  - [디바이스 로그인에 의해 Azure 계정에 로그인](#sign-in-to-your-azure-account-by-device-login)
  - [서비스 주체에 의해 Azure 계정에 로그인](#sign-in-to-your-azure-account-by-service-principal)

[**로그아웃**](#sign-out-of-your-azure-account) 방법도 제공됩니다.

[!INCLUDE [prerequisites](includes/prerequisites.md)]

## <a name="sign-in-to-your-azure-account-by-device-login"></a>디바이스 로그인에 의해 Azure 계정에 로그인

디바이스 로그인에 의해 Azure에 로그인하려면 다음을 수행합니다.

1. Eclipse로 프로젝트를 엽니다.

2. **도구**를 클릭한 다음 **Azure**를 클릭하고 **로그인**을 클릭합니다.
   ![Azure 로그인을 위한 Eclipse 메뉴][I01]

3. **Azure 로그인** 창에서 **디바이스 로그인**을 선택한 다음, **로그인**을 클릭합니다.

   ![디바이스 로그인을 선택한 Azure 로그인 창][I02]

4. **Azure 디바이스 로그인** 대화 상자에서 **복사 및 열기**를 클릭합니다.

   ![Azure 로그인 대화 상자 창][I03]

> [!NOTE]
>
> 브라우저가 열리지 않는 경우 Eclipse를 Internet Explorer, Firefox 또는 Chrome과 같은 외부 브라우저를 사용하도록 구성합니다.
>
> 1. 기본 설정 -> 일반 -> 웹 브라우저 -> Eclipse에서 외부 웹 브라우저 사용을 엽니다.
>
> 2. 즐겨 사용하는 브라우저를 선택합니다.
>

5. 브라우저에서, 마지막 단계에서 **복사 및 열기**를 클릭할 때 복사한 디바이스 코드를 붙여넣은 후 **다음**을 클릭합니다.

   ![디바이스 로그인 브라우저][I04]

6. 끝으로 **구독 선택** 대화 상자에서 사용하려는 구독을 선택한 다음, **확인**을 클릭합니다.

   ![구독 선택 대화 상자][I05]

## <a name="sign-in-to-your-azure-account-by-service-principal"></a>서비스 주체에 의해 Azure 계정에 로그인

이 섹션에서는 서비스 주체 데이터를 포함하는 자격 증명 파일을 만드는 과정을 안내합니다. 이 프로세스를 완료하면 Eclipse에서는 사용자가 프로젝트를 열 때 해당 자격 증명 파일을 사용해서 Azure에 자동으로 로그인합니다.

1. Eclipse로 프로젝트를 엽니다.

2. **도구**를 클릭한 다음 **Azure**를 클릭하고 **로그인**을 클릭합니다.
   ![Eclipse Azure 로그인 명령][A01]

3. **Azure 로그인** 창에서 **서비스 주체**를 선택합니다. 서비스 주체 인증 파일이 아직 없는 경우 **새로 만들기**를 클릭하여 만듭니다. 또는 **찾아보기**를 클릭하여 열고 8단계로 이동할 수 있습니다.

   ![서비스 주체를 선택한 Azure 로그인 창][A02]

4. **Azure 디바이스 로그인** 대화 상자에서 **복사 및 열기**를 클릭합니다.

   ![Azure 로그인 대화 상자 창][A08]

> [!NOTE]
>
> 브라우저가 열리지 않은 경우 Eclipse를 IE 또는 Chrome과 같은 외부 브라우저를 사용하도록 구성합니다.
>
> 1. 기본 설정 -> 일반 -> 웹 브라우저 -> Eclipse에서 외부 웹 브라우저 사용을 엽니다.
>
> 2. 즐겨 사용하는 브라우저를 선택합니다.
>

5. 브라우저에서, 마지막 단계에서 **복사 및 열기**를 클릭할 때 복사한 디바이스 코드를 붙여넣은 후 **다음**을 클릭합니다.

   ![디바이스 로그인 브라우저][A03]

6. **인증 파일 만들기** 창에서 사용할 구독을 선택하고 대상 디렉터리를 선택한 다음, **시작**을 클릭합니다.

   ![인증 파일 만들기 창][A04]

7. **서비스 주체 만들기 상태** 대화 상자에서 파일을 성공적으로 만든 후 **확인**을 클릭합니다.

   ![서비스 주체 만들기 상태 대화 상자][A05]

8. 생성된 파일의 주소가 **Azure 로그인** 창에 자동으로 채워진 후 **로그인**을 클릭합니다.

   ![Azure 로그인 대화 상자][A06]

9. 끝으로 **구독 선택** 대화 상자에서 사용하려는 구독을 선택한 다음, **확인**을 클릭합니다.

   ![구독 선택 대화 상자][A07]

## <a name="sign-out-of-your-azure-account"></a>Azure 계정에서 로그아웃

이전 단계에서 계정을 구성한 후에는 Eclipse를 시작할 때마다 자동으로 로그인됩니다. 그러나 Azure 계정에서 로그아웃하려면 다음 단계를 수행합니다.

1. Eclipse에서 **도구**를 클릭한 다음 **Azure**를 클릭하고 **로그아웃**을 클릭합니다.

   ![Azure 로그아웃을 위한 Eclipse 메뉴][L01]

2. **Azure 로그아웃** 대화 상자가 나타나면 **예**를 클릭합니다.

   ![로그아웃 대화 상자][L02]

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

[L01]: media/sign-in-instructions/L01.png
[L02]: media/sign-in-instructions/L02.png
[L03]: media/sign-in-instructions/L03.png
