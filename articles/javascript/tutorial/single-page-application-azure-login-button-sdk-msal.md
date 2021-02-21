---
title: '자습서: React SPA에 Microsoft 로그인 단추 추가'
description: 이 자습서에 제공된 Azure Active Directory 인증은 로그인 및 로그아웃 단추이며, 사용자의 사용자 이름(이메일)에 대한 액세스를 제공합니다. Azure 클라이언트 쪽 SDK `@azure/msal-browser`를 사용하여 TypeScript 애플리케이션을 개발하고, SPA(단일 페이지 애플리케이션)에서 사용자의 상호 작용을 관리합니다.
ms.topic: tutorial
ms.date: 02/16/2020
ms.custom: devx-track-js, "azure-sdk-javascript-@azure/msal-browser-2.7.0"
ms.history:
- 20210216:fix public issue 443
ms.openlocfilehash: d5d64f1de3637c79a081e64dbf3cd632e15a3ef3
ms.sourcegitcommit: 450cfb580f0fef7aabd38a7db17ecf7502126ce4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548844"
---
# <a name="add-microsoft-login-button-to-a-single-page-application-for-authentication"></a>인증에 사용할 단일 페이지 애플리케이션에 Microsoft 로그인 단추 추가

이 TypeScript 자습서에 제공된 Azure 인증은 로그인 및 로그아웃 단추이며, 사용자 계정에 대한 액세스를 제공합니다. Azure 클라이언트 쪽 SDK `@azure/msal-browser`를 사용하여 애플리케이션을 개발하고, SPA(단일 페이지 애플리케이션)에서 사용자의 상호 작용을 관리합니다.

* [예제 코드](https://github.com/Azure-Samples/js-e2e-client-azure-login-button)

## <a name="application-architecture-and-functionality"></a>애플리케이션 아키텍처 및 기능

이 자습서에서 빌드하는 SPA는 다음과 같은 작업을 수행하는 React 앱(create-react-app)입니다.

- Office 365 또는 Outlook.com 같은 Microsoft 지원 로그인을 사용하여 로그인
- 애플리케이션에서 로그오프

빠르고 간단한 단일 페이지 애플리케이션을 제공하기 위해 이 샘플에서는 **create-react-app** 과 TypeScript를 사용합니다. 이 프런트 엔드 프레임워크는 Azure SDK를 사용하는 일반적인 클라이언트 개발에서 다음과 같은 몇 가지 바로 가기를 제공합니다.

- 번들 - 클라이언트 애플리케이션에서 사용되는 Azure SDK에 필요
- `.env` 파일의 환경 변수

## <a name="1-set-up-development-environment"></a>1. 개발 환경 설정

로컬 컴퓨터에 다음 소프트웨어가 설치되었는지 확인합니다.

- 활성 구독이 포함된 Azure 사용자 계정 [체험 계정 만들기](https://azure.microsoft.com/free/)
- [Node.js 및 npm](https://nodejs.org/en/download) - 로컬 컴퓨터에 설치
- [Visual Studio Code](https://code.visualstudio.com/) 또는 Bash 셸이나 터미널을 제공하는 동등한 IDE - 로컬 컴퓨터에 설치 

## <a name="2-keep-value-for-environment-variable"></a>2. 환경 변수 값 유지

텍스트 파일 등 앱 등록의 클라이언트 ID 값을 복사할 위치를 따로 설정합니다. 다음 섹션의 5단계에서 이 클라이언트 ID를 가져올 것입니다. 이 값은 웹앱에 대한 환경 변수로 사용됩니다.  

## <a name="3-create-app-registration-for-authentication"></a>3. 인증에 사용할 앱 등록 만들기

1. 기본 디렉터리의 앱 등록을 위해 [Azure Portal](https://portal.azure.com/?quickstart=True#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)에 **로그인** 합니다.
1. **+ 새 등록** 을 선택합니다.
1. 다음 표를 사용하여 **앱 등록 데이터를 입력** 합니다.

   | 필드                   | 값                                                                                                                                                                      |설명|
   | ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |--|
   | Name                    | `Simple Auth Tutorial`|사용자가 앱에 로그인할 때 권한 양식에 표시되는 앱 이름입니다.                                                 |
   | 지원되는 계정 유형 | **모든 조직 디렉터리(모든 Azure AD 디렉터리 - 다중 테넌트)의 계정 및 개인 Microsoft 계정**|대부분의 계정 형식을 포함합니다. |
   | 리디렉션 URI 형식           | **SPA(단일 페이지 애플리케이션)**                                                                                        | |
   | 리디렉션 URI 값           | `http://localhost:3000` | 인증이 성공 또는 실패한 후에 반환할 url입니다.                                                                                        |

    :::image type="content" source="../media/tutorial-login-button/azure-active-directory-create-new-app-registration.png" alt-text="Azure 새 앱 등록":::  

1. **등록** 을 선택합니다. 앱 등록 프로세스가 완료될 때까지 기다립니다.
1. 앱 등록의 [개요] 섹션에서 **애플리케이션(클라이언트) ID를 복사** 합니다. 나중에 클라이언트 앱의 환경 변수에 이 값을 추가할 것입니다.

## <a name="4-create-react-single-page-application-for-typescript"></a>4. TypeScript에 사용할 React 단일 페이지 애플리케이션 만들기

1. Bash 셸에서 TypeScript를 언어로 사용하여 **create-react-app을 만듭니다**.

   ```bash
   npx create-react-app tutorial-demo-login-button --template typescript
   ```

1. 새 디렉터리로 변경하고 `@azure/msal-browser` 인증 패키지를 설치합니다.

   ```bash
   cd tutorial-demo-login-button && npm install @azure/msal-browser
   ```

1. 루트 수준에서 `.env` 파일을 만들고 다음 줄을 추가합니다.

    :::code language="env" source="~/../js-e2e-client-azure-login-button/.env"  :::

    `.env` 파일은 create-react-app 프레임워크의 일부로 읽힙니다. 이 파일은 로컬 개발을 위해 클라이언트 ID를 저장할 수 있는 위치입니다. 

1. 애플리케이션(클라이언트) ID를 두 번째 값에 복사합니다.

## <a name="5-add-login-and-logoff-buttons"></a>5. 로그인 및 로그오프 단추 추가

1. `./src` 폴더 내에 Azure 관련 파일에 대한 `azure`라는 하위 폴더를 만듭니다. 

1. `azure` 폴더에 인증 구성을 위한 `azure-authentication-config.ts`라는 새 파일을 만들고 다음 코드를 복사합니다.

    :::code language="typescript" source="~/../js-e2e-client-azure-login-button/src/azure/azure-authentication-config.ts"  highlight="3-4,, 11-12":::

    이 파일은 `.env` 파일에서 애플리케이션 ID를 읽어 오고, 쿠키 대신 세션을 브라우저 스토리지로 설정하고, 개인 정보를 고려하는 로깅을 제공합니다.

1. `azure` 폴더에 Azure 인증 미들웨어를 위한 `azure-authentication-context.ts`라는 새 파일을 만들고 다음 코드를 복사합니다.

    :::code language="typescript" source="~/../js-e2e-client-azure-login-button/src/azure/azure-authentication-context.ts"  highlight="43, 58, 65":::

    이 파일은 `login` 및 `logout` 함수를 사용하는 사용자를 위해 Azure 인증을 제공합니다.

1. `azure` 폴더에 사용자 인터페이스를 위한 `azure-authentication-component.tsx`라는 새 파일을 만들고 다음 코드를 복사합니다.

   :::code language="typescript" source="~/../js-e2e-client-azure-login-button/src/azure/azure-authentication-component.tsx"  highlight="3, 11, 23, 29, 36":::

   이 단추 구성 요소는 사용자를 로그인하고, 사용자 계정을 호출/상위 구성 요소에 다시 전달합니다.

   단추 텍스트 및 기능은 사용자의 현재 로그인 여부에 따라 전환되며, `onAuthenticated` 함수를 통해 구성 요소에 전달된 속성으로 캡처됩니다.

   사용자가 로그인하면 이 단추는 Azure 인증 라이브러리 메서드 `authenticationModule.login`을 호출하고 콜백 함수로 `returnedAccountInfo`를 호출합니다. 반환된 사용자 계정은 `onAuthenticated` 함수를 통해 다시 상위 구성 요소에 전달됩니다.


1. `./src/App.tsx` 파일을 열고 모든 코드를 다음 코드로 바꾸어 로그인/로그아웃 단추 구성 요소를 통합합니다.

   :::code language="typescript" source="~/../js-e2e-client-azure-login-button/src/App.tsx"  highlight="10, 37-42":::

    사용자가 로그온하면 인증이 다시 이 앱으로 리디렉션되고 currentUser 개체가 표시됩니다. 

## <a name="6-run-react-spa-app-with-login-button"></a>6. 로그인 단추로 React SPA 앱 실행

1. Visual Studio Code 터미널에서 앱을 시작합니다.

    ```bash
    npm run start
    ```

1. 웹 브라우저에서 **로그인** 단추를 선택합니다. 

    :::image type="content" source="../media/tutorial-login-button/create-react-app-before-authentication-login-button-display.png" alt-text="**로그인** 단추를 선택합니다.":::  

1. 사용자 계정을 선택합니다. Azure Portal에 액세스할 때 사용한 것과 동일한 계정일 필요는 없지만 Microsoft 인증을 제공하는 계정이어야 합니다.

    :::image type="content" source="../media/tutorial-login-button/authentication-popup-select-user-account.png" alt-text="사용자 계정을 선택합니다. Azure Portal에 액세스할 때 사용한 것과 동일한 계정일 필요는 없지만 Microsoft 인증을 제공하는 계정이어야 합니다.":::

1. 1\) 사용자 이름, 2) 앱 이름, 3) 동의하는 권한을 보여주는 팝업을 검토한 다음, **예** 를 선택합니다.

    :::image type="content" source="../media/tutorial-login-button/authentication-popup-let-this-app-access-your-info.png" alt-text="1) 사용자 이름, 2) 앱 이름, 3) 동의하는 권한을 보여주는 팝업을 검토한 다음, `예`를 선택합니다.":::

1. 사용자 계정 정보를 검토합니다. 

    :::image type="content" source="../media/tutorial-login-button/create-react-app-after-authentication-login-button-succeeds.png" alt-text="사용자 계정 정보를 검토합니다.":::  

1. 앱에서 **로그아웃** 단추를 선택합니다. 또한 이 앱은 사용 권한을 철회할 수 있도록 Microsoft 사용자 앱에 대한 간편한 링크도 제공합니다. 

## <a name="7-clean-up-resources"></a>7. 리소스 정리

이 자습서를 마친 후에는 Azure Portal의 [앱 등록 목록](https://portal.azure.com/?quickstart=True#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)에서 애플리케이션을 삭제합니다.

앱을 유지하되 특정 사용자 계정으로 앱에 부여한 권한을 철회하려면 다음 링크 중 하나를 사용합니다.

* [AAD 권한 철회](https://myapps.microsoft.com/)
* [소비자 권한 철회](https://account.live.com/consent/manage)

## <a name="next-steps"></a>다음 단계

이 앱은 앱에 대한 사용자 인증을 제공하고 사용자 정보를 반환합니다. 인증 기능을 여기까지만 구현하고 간단한 앱 버전을 사용할 수도 있고, 앱의 사용자 관리 및 사용자 권한 부여 기능을 앱에 추가할 수도 있습니다. 

사용자 관리는 선택하는 기능과 도구에 따라 Azure Active Directory 또는 자신의 데이터베이스에 저장할 수 있습니다. 

사용자 권한 부여는 Azure에서 제공할 수도 있고, Azure 없이 권한 부여를 개발할 수도 있고, 두 가지 방법을 결합하여 권한 부여, 역할 및 앱 기능으로 이루어진 사용자 지정 환경을 만들 수도 있습니다. 

* 계속해서 [MSAL 라이브러리](/azure/active-directory/develop/msal-overview)를 사용하여 사용자 프로필을 가져오고 자동 로그온을 제공하세요.
* [Microsoft Graph](/graph/overview)를 추가하여 이메일 및 일정 약속을 포함하는 Microsoft 365의 사용자 계정에 액세스합니다.