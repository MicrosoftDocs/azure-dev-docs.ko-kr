---
title: Azure Active Directory에 Spring Boot Starter를 사용하는 방법
description: Azure Active Directory 스타터에 Spring Boot Initializer 앱을 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: java
ms.date: 03/05/2020
ms.service: active-directory
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.custom: devx-track-java
ms.openlocfilehash: f449df07048dfcc20e173bc4103995ac989e38fe
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240945"
---
# <a name="tutorial-secure-a-java-web-app-using-the-spring-boot-starter-for-azure-active-directory"></a>자습서: Azure Active Directory용 Spring Boot Starter를 사용하여 Java 웹앱 보호하기

## <a name="overview"></a>개요

이 문서에서는 Azure Active Directory(Azure AD)용 Spring Boot Starter를 사용하는 **[Spring Initializr]** 를 통한 Java 앱 만들기를 보여 줍니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

 * Spring Initializr를 사용하여 Java 애플리케이션 만들기
 * Azure Active Directory 구성
 * Spring Boot 클래스 및 주석을 사용하여 애플리케이션 보호
 * Java 애플리케이션 빌드 및 테스트

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 완료하려면 다음 필수 구성 요소가 필요합니다.

* 지원되는 JDK(Java Development Kit) Azure에서 개발하는 경우 사용할 수 있는 JDK에 대한 자세한 내용은 <https://aka.ms/azure-jdks>를 참조하세요.
* [Apache Maven](http://maven.apache.org/), 버전 3.0 이상

## <a name="create-an-app-using-spring-initializr"></a>Spring Initialzr를 사용하여 앱 만들기

1. [https://www.microsoft.com]\(<https://start.spring.io/>) 로 이동합니다.

1. **Java**에서 **Maven** 프로젝트를 생성한다고 지정하고, 애플리케이션에 대한 **그룹** 및 **아티팩트** 이름을 입력합니다.

   ![그룹 및 아티팩트 이름 지정][create-spring-app-01]

1. 아래로 스크롤하여 **Spring Web**, **Azure Active Directory** 및 **Spring Security**에 대한 **Dependencies(종속성)** 를 추가합니다.

1. 페이지 아래쪽에서 **Generate(생성)** 단추를 클릭합니다.

   ![보안, 웹 및 Azure Active Directory 스타터를 선택합니다][create-spring-app-02]

1. 메시지가 표시되면 로컬 컴퓨터의 경로에 프로젝트를 다운로드합니다.

## <a name="create-azure-active-directory-instance"></a>Azure Active Directory 인스턴스 만들기

### <a name="create-the-active-directory-instance"></a>Active Directory 인스턴스 만들기

1. <https://portal.azure.com>에 로그인합니다.

1. **+리소스 만들기**, **ID**, **Azure Active Directory**를 차례로 클릭합니다.

   ![새 Azure Active Directory 인스턴스 만들기][create-directory-01]

1. **조직 이름**과 **초기 도메인 이름**을 입력합니다. 사용자 디렉터리의 전체 URL을 복사합니다. 이는 자습서의 뒷부분에서 사용자 계정을 추가 하는 데 사용됩니다. (예: `wingtiptoysdirectory.onmicrosoft.com`) 

    사용자 디렉터리의 전체 URL을 복사합니다. 이는 자습서의 뒷부분에서 사용자 계정을 추가 하는 데 사용됩니다. (예: wingtiptoysdirectory.onmicrosoft.com)

    완료되면 **만들기** 를 클릭합니다. 새 리소스를 만드는 데 몇 분 정도 걸립니다.

   ![Azure Active Directory 이름 지정][create-directory-02]

1. 완료되면 클릭하여 새 디렉터리에 액세스합니다.

   ![Azure 계정 이름을 선택합니다.][create-directory-03]

1. **테넌트 ID**를 복사합니다. 이 값은 이 자습서의 뒷부분에서 *application.properties* 파일을 구성하는 데 사용됩니다.

   ![테넌트 ID 복사][create-directory-05]

### <a name="add-an-application-registration-for-your-spring-boot-app"></a>Spring Boot 앱에 대한 애플리케이션 등록 추가

1. 포털 메뉴에서 **앱 등록**을 클릭한 다음, **애플리케이션 등록**을 클릭합니다.

   ![새 앱 등록 추가][create-app-registration-01]

1. 애플리케이션을 지정한 다음, **등록**을 클릭합니다.

   ![새 앱 등록 만들기][create-app-registration-02]

1. 앱 등록 페이지가 표시되면 **애플리케이션 ID** 및 **테넌트 ID**를 복사합니다. 이러한 값은 이 자습서의 뒷부분에서 *application.properties* 파일을 구성하는 데 사용됩니다.

   ![앱 등록 키 만들기][create-app-registration-03]

1. 왼쪽 탐색 창에서 **인증서 및 비밀**을 클릭합니다.  그런 다음, **새 클라이언트 암호**를 클릭합니다.

   ![앱 등록 키 만들기][create-app-registration-03-5]

1. **설명**을 추가하고, **만료** 목록에서 기간을 선택합니다.  **추가**를 클릭합니다. 키 값이 자동으로 채워집니다.

   ![앱 등록 키 매개 변수 지정][create-app-registration-04]

1. 이 자습서의 뒷부분에서 *application.properties* 파일을 구성하려면 이 클라이언트 암호의 값을 복사하여 저장합니다. 이 값은 나중에 검색할 수 없습니다.

   ![앱 등록 키 매개 변수 지정][create-app-registration-04-5]

1. 왼쪽 탐색 창에서 **API 사용 권한**을 클릭합니다. 

1. **Microsoft Graph**를 클릭하고 **로그인한 사용자 권한으로 디렉터리에 액세스** 및 **로그인 및 사용자 프로필 읽기**를 선택합니다. 메시지가 표시되면 **권한 부여...** 및 **예**를 클릭합니다.

   ![액세스 권한 부여][create-app-registration-08]

1. 앱 등록의 기본 페이지에서 **인증**을 클릭하고 **플랫폼 추가**를 클릭합니다.  그런 다음, **웹 애플리케이션**을 클릭합니다.

    ![회신 URL 편집][create-app-registration-09]

1. 새 **리디렉션 URI**로 <http:<span></span>//localhost:8080/login/oauth2/code/azure>를 입력한 다음, **구성**을 클릭합니다.

    ![새 회신 URL 추가][create-app-registration-10]

1. 앱 등록에 대한 기본 페이지에서 **매니페스트**를 클릭하고 `oauth2AllowImplicitFlow` 매개 변수의 값을 `true`로 설정하고 **저장**을 클릭합니다.

    ![앱 매니페스트 구성][create-app-registration-11]

    > [!NOTE]
    > `oauth2AllowImplicitFlow` 매개 변수 및 다른 애플리케이션 설정에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 매니페스트][AAD app manifest]를 참조하세요.

### <a name="add-a-user-account-to-your-directory-and-add-that-account-to-a-group"></a>디렉터리에 사용자 계정을 추가하고 해당 계정을 그룹에 추가합니다

1. Active Directory의 **개요** 페이지에서 **모든 사용자**를, 그리고 **새 사용자**를 클릭합니다.

   ![새 사용자 계정 추가하기][create-user-01]

1. **사용자** 패널이 표시되면, **사용자 이름** 및 **이름**을 입력합니다.  그런 다음, **만들기**를 클릭합니다.

   ![사용자 계정 정보 입력][create-user-02]

   > [!NOTE]
   > 사용자 이름을 입력하려면 자습서의 앞부분에 나온 디렉터리 URL을 지정해야 합니다. 예를 들어,
   >
   > `wingtipuser@wingtiptoysdirectory.onmicrosoft.com`

1. **그룹**을 클릭한 다음, 애플리케이션에서 권한을 부여하는 데 사용할 **새 그룹을 만듭니다**.

1. 그런 다음, **선택한 멤버가 없음**을 클릭합니다. (이 자습서에서는 *users*라는 그룹을 만듭니다.)  이전 단계에서 만든 사용자를 검색합니다.  **선택**을 클릭하여 사용자를 그룹에 추가합니다.  그런 다음, **만들기**를 클릭하여 새 그룹을 만듭니다.

   ![그룹에 대한 사용자 선택][create-user-03]

1. **사용자** 패널로 돌아가서 테스트 사용자를 선택하고, **암호 재설정**을 클릭하고, 암호를 복사합니다. 이 암호는 이 자습서의 뒷부분에서 애플리케이션에 로그인할 때 사용됩니다.

   ![암호 표시][create-user-04]

## <a name="configure-and-compile-your-app"></a>앱 구성 및 컴파일

1. 이 자습서의 앞부분에서 작성하고 다운로드한 프로젝트 아카이브의 파일을 디렉터리로 추출합니다.

1. 프로젝트에서 상위 폴더로 이동하고 텍스트 편집기에서 `pom.xml`Maven 프로젝트 파일을 엽니다.

1. Spring OAuth2 보안의 종속성을 `pom.xml`에 추가합니다.

   ```xml
   <dependency>
      <groupId>org.springframework.security</groupId>
      <artifactId>spring-security-oauth2-client</artifactId>
   </dependency>
   <dependency>
      <groupId>org.springframework.security</groupId>
      <artifactId>spring-security-oauth2-jose</artifactId>
   </dependency>
   ```

1. *pom.xml* 파일을 저장하고 닫습니다.

1. 프로젝트에서 *src/main/resources* 폴더로 이동하고 텍스트 편집기에서 *application.properties* 파일을 엽니다.

1. 이전에 생성한 값을 사용하여 앱 등록을 위한 설정을 지정합니다. 예를 들어,

   ```yaml
   # Specifies your Active Directory ID:
   azure.activedirectory.tenant-id=22222222-2222-2222-2222-222222222222

   # Specifies your App Registration's Application ID:
   spring.security.oauth2.client.registration.azure.client-id=11111111-1111-1111-1111-1111111111111111

   # Specifies your App Registration's secret key:
   spring.security.oauth2.client.registration.azure.client-secret=AbCdEfGhIjKlMnOpQrStUvWxYz==

   # Specifies the list of Active Directory groups to use for authorization:
   azure.activedirectory.user-group.allowed-groups=Users
   ```

   위치:

   | 매개 변수 | Description |
   |---|---|
   | `azure.activedirectory.tenant-id` | 앞에 나온 Active Directory의 **디렉터리 ID** 포함합니다. |
   | `spring.security.oauth2.client.registration.azure.client-id` | 앞에서 완료한 앱 등록의 **애플리케이션 ID**를 포함합니다. |
   | `spring.security.oauth2.client.registration.azure.client-secret` | 앞에서 완료한 앱 등록 키의 **값**을 포함합니다. |
   | `azure.activedirectory.user-group.allowed-groups` | 권한 부여에 사용할 Active Directory 그룹 목록을 포함합니다. |

   > [!NOTE]
   > *application.properties* 파일에서 사용할 수 있는 값의 전체 목록은 GitHub에서 [Azure Active Directory Spring Boot 샘플][AAD Spring Boot Sample]을 참조하세요.

1. *application.properties* 파일을 저장하고 닫습니다.

1. 애플리케이션에 대해 Java 소스 폴더에 이름이 *controller*인 폴더를 만듭니다. 예를 들어 *src/main/java/com/wingtiptoys/security/controller*입니다.

1. *controller* 폴더에 이름이 *HelloController.java*인 새 Java 파일을 만들고 텍스트 편집기에서 엽니다.

1. 다음 코드를 입력한 다음 저장하고 파일을 닫습니다.

   ```java
   package com.wingtiptoys.security;

   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.security.access.prepost.PreAuthorize;
   import org.springframework.security.oauth2.client.OAuth2AuthorizedClient;
   import org.springframework.security.oauth2.client.authentication.OAuth2AuthenticationToken;
   import org.springframework.ui.Model;

   @RestController
   public class HelloController {
      @Autowired
      @PreAuthorize("hasRole('Users')")
      @RequestMapping("/")
      public String helloWorld() {
         return "Hello World!";
      }
   }
   ```

   > [!NOTE]
   > `@PreAuthorize("hasRole('')")` 메서드에 지정한 그룹 이름은 *application.properties* 파일의 `azure.activedirectory.user-group.allowed-groups` 필드에 지정한 그룹 중 하나를 포함해야 합니다.
   >
   > 다른 요청 매핑에 대한 다른 권한 부여 설정을 지정할 수도 있습니다. 예를 들어:
   >
   > ``` java
   > public class HelloController {
   >    @Autowired
   >    @PreAuthorize("hasRole('Users')")
   >    @RequestMapping("/")
   >    public String helloWorld() {
   >       return "Hello Users!";
   >    }
   >    @PreAuthorize("hasRole('Group1')")
   >    @RequestMapping("/Group1")
   >    public String groupOne() {
   >       return "Hello Group 1 Users!";
   >    }
   >    @PreAuthorize("hasRole('Group2')")
   >    @RequestMapping("/Group2")
   >    public String groupTwo() {
   >       return "Hello Group 2 Users!";
   >    }
   > }
   > ```

1. 애플리케이션에 대해 Java 소스 폴더에 이름이 *security*인 폴더를 만듭니다. 예를 들어 *src/main/java/com/wingtiptoys/security/security*입니다.

1. *security* 폴더에 이름이 *WebSecurityConfig.java*인 새 Java 파일을 만들고 텍스트 편집기에서 엽니다.

1. 다음 코드를 입력한 다음 저장하고 파일을 닫습니다.

    ```java
    package com.wingtiptoys.security;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
    import org.springframework.security.config.annotation.web.builders.HttpSecurity;
    import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
    import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
    import org.springframework.security.oauth2.client.oidc.userinfo.OidcUserRequest;
    import org.springframework.security.oauth2.client.userinfo.OAuth2UserService;
    import org.springframework.security.oauth2.core.oidc.user.OidcUser;

    @EnableWebSecurity
    @EnableGlobalMethodSecurity(prePostEnabled = true)
    public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
        @Autowired
        private OAuth2UserService<OidcUserRequest, OidcUser> oidcUserService;

        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http
                .authorizeRequests()
                .anyRequest().authenticated()
                .and()
                .oauth2Login()
                .userInfoEndpoint()
                .oidcUserService(oidcUserService);
        }
    }
    ```

## <a name="build-and-test-your-app"></a>앱 빌드 및 테스트

1. 명령 프롬프트를 열고 디렉터리를 앱의 *pom.xml* 파일이 위치한 폴더로 변경합니다.

1. Maven을 사용하여 Spring Boot 애플리케이션을 빌드하고 실행합니다. 예:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

   ![사용 중인 애플리케이션 빌드][build-application]

1. 애플리케이션이 Maven에서 빌드 및 시작되면 웹 브라우저에서 http:<span></span>//localhost:8080을 엽니다. 그러면 사용자 이름과 암호를 입력하라는 메시지가 표시됩니다.

   ![애플리케이션에 로그인][application-login]

   > [!NOTE]
   > 새 사용자 계정에서 처음 로그인한 경우라면, 암호를 변경하라는 메시지가 표시될 수 있습니다.
   >
   > ![암호 변경][update-password]

1. 사용자가 성공적으로 로그인한 후 컨트롤러에서 샘플 "Hello World" 텍스트가 표시됩니다.

   ![로그인 성공][hello-world]

   > [!NOTE]
   > 승인되지 않은 사용자 계정인 경우 **HTTP 403 Unauthorized** 메시지가 나타납니다.

## <a name="summary"></a>요약

이 자습서에서는 Azure Active Directory 스타터를 사용하여 새 Java 웹 애플리케이션을 만들고, 새로 구성한 Azure AD 테넌트에 새 애플리케이션을 등록하고, 애플리케이션이 웹을 보호하기 위한 Spring 주석 및 클래스를 사용하도록 구성합니다.

## <a name="see-also"></a>참고 항목
* 새 UI 옵션에 대한 자세한 내용은 [새 Azure Portal 앱 등록 학습 가이드](/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Spring과 Azure에 대한 자세한 사항은 Azure의 Spring 설명서 센터를 참조합니다.

> [!div class="nextstepaction"]
> [Azure의 Spring](/azure/developer/java/spring-framework)

<!-- URL List -->

[Azure Active Directory Documentation]: /azure/active-directory/
[AAD app manifest]: /azure/active-directory/develop/active-directory-application-manifest
[Get started with Azure AD]: /azure/active-directory/get-started-azure-ad
[Azure for Java Developers]: /azure/developer/java/
[free Azure account]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/
[MSDN subscriber benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[AAD Spring Boot Sample]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-boot-sample-active-directory-backend

<!-- IMG List -->

[create-spring-app-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-spring-app-01.png
[create-spring-app-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-spring-app-02.png
[create-spring-app-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-spring-app-03.png

[create-directory-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-01.png
[create-directory-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-02.png
[create-directory-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-03.png
[create-directory-04]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-04.png
[create-directory-05]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-05.png

[create-app-registration-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-01.png
[create-app-registration-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-02.png
[create-app-registration-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-03.png
[create-app-registration-03-5]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-03-5.png
[create-app-registration-04]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-04.png
[create-app-registration-04-5]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-04-5.png
[create-app-registration-05]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-05.png
[create-app-registration-06]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-06.png
[create-app-registration-07]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-07.png
[create-app-registration-08]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-08.png
[create-app-registration-09]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-09.png
[create-app-registration-10]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-10.png
[create-app-registration-11]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-11.png

[create-user-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-01.png
[create-user-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-02.png
[create-user-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-03.png
[create-user-04]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-04.png

[application-login]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/application-login.png
[build-application]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/build-application.png
[hello-world]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/hello-world.png
[update-password]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/update-password.png


