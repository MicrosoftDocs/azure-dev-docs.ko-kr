---
title: Azure Active Directory B2C용 Spring Boot Starter 사용
description: Azure Active Directory B2C 스타터를 사용하여 Spring Boot Initializr 앱을 구성하는 방법을 알아봅니다.
services: active-directory-b2c
documentationcenter: java
author: panli
manager: kevinzha
ms.author: edburns
ms.date: 06/04/2020
ms.service: active-directory-b2c
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.openlocfilehash: 4a6463df852511ab37af772598f9ae525d8a02e9
ms.sourcegitcommit: 7474de4884bce076ce33ca77ae3584ba1598bbc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2020
ms.locfileid: "85069424"
---
# <a name="tutorial-secure-a-java-web-app-using-the-spring-boot-starter-for-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C용 Spring Boot Starter를 사용하여 Java 웹앱을 보호합니다.

## <a name="overview"></a>개요

이 문서에서는 Azure AD(Azure Active Directory)용 Spring Boot Starter를 사용하는 [Spring Initializr](https://start.spring.io/)를 통한 Java 앱 만들기를 보여줍니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Spring Initializr를 사용하여 Java 애플리케이션 만들기
> * Azure Active Directory B2C 구성
> * Spring Boot 클래스 및 주석을 사용하여 애플리케이션 보호
> * Java 애플리케이션 빌드 및 테스트

[Azure Active Directory](https://azure.microsoft.com/services/active-directory)는 Microsoft의 클라우드 규모 엔터프라이즈 ID 솔루션입니다. [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory/external-identities/b2c/)는 Azure Active Directory의 기능 세트를 보완하여 B2C(Business-to-Consumer) 애플리케이션에 대한 고객, 소비자 및 시민 액세스를 관리할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* 지원되는 JDK(Java Development Kit) Azure에서 개발하는 경우 사용할 수 있는 JDK에 대한 자세한 내용은 <https://aka.ms/azure-jdks>를 참조하세요.
* [Apache Maven](http://maven.apache.org/), 버전 3.0 이상

## <a name="create-an-app-using-spring-initializr"></a>Spring Initialzr를 사용하여 앱 만들기

1. [https://www.microsoft.com]\(<https://start.spring.io/>) 로 이동합니다.

2. 다음 지침에 따라 값을 입력합니다. 레이블 및 레이아웃은 여기에 표시된 이미지와 다를 수 있습니다.

    * **프로젝트** 아래에서 **Maven 프로젝트**를 선택합니다.
    * **언어** 아래에서 **Java**를 선택합니다.
    * **Spring Boot** 아래에서 **2.2.7**을 선택합니다.
    * **그룹**, **아티팩트** 및 **이름**에서 간단한 설명이 포함된 문자열을 사용하여 동일한 값을 입력합니다. 입력함에 따라 UI에서 이러한 항목 중 일부를 자동으로 채울 수 있습니다.
    * **종속성** 창에서 **종속성 추가**를 선택합니다. UI를 사용하여 **Spring 웹** 및 **Spring 보안**에 대한 종속성을 추가합니다.

   ![프로젝트를 생성하기 위한 값 입력](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/si-n.png)

3. **프로젝트 생성**을 선택한 다음, 프로젝트를 로컬 컴퓨터의 경로에 다운로드합니다. 다운로드한 파일을 프로젝트의 이름을 딴 디렉터리로 이동하고, 파일의 압축을 풉니다. 파일 레이아웃은 다음과 같으며, 이 경우 `yourProject` 대신 **그룹**에 대해 입력한 값을 사용합니다.

    ```
    .
    ├── HELP.md
    ├── mvnw
    ├── mvnw.cmd
    ├── pom.xml
    └── src
        ├── main
        │   ├── java
        │   │   └── yourProject
        │   │       └── yourProject
        │   │           └── YourProjectApplication.java
        │   └── resources
        │       ├── application.properties
        │       ├── static
        │       └── templates
        └── test
            └── java
                └── yourProject
                    └── yourProject
                        └── YourProjectApplicationTests.java
    ```

## <a name="create-and-initialize-an-azure-active-directory-instance"></a>Azure Active Directory 인스턴스 만들기 및 초기화

### <a name="create-the-active-directory-instance"></a>Active Directory 인스턴스 만들기

1. <https://portal.azure.com>에 로그인합니다.

2. **리소스 만들기**, **ID**, **모두 보기**를 차례로 선택합니다. **Azure Active Directory B2C**를 검색합니다.

    ![새 Azure Active Directory B2C 인스턴스 만들기](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-1-n.png)

3. **만들기**를 선택합니다.

    ![B2C 테넌트 이름 가져오기](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-5-n.png)

4. **새 Azure AD B2C 테넌트 만들기**를 선택합니다.

    ![새 Azure Active Directory 만들기](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-2-n.png)

5. **조직 이름** 및 **초기 도메인 이름**에 대해 적절한 값을 제공한 다음, **만들기**를 선택합니다.

    ![Azure Active Directory 선택](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-3-n.png)

6. Active Directory 만들기가 완료되면 새 디렉터리로 이동합니다. `b2c`를 검색하여 **Azure AD B2C**를 선택합니다.

    ![Azure Active Directory B2C 인스턴스 찾기](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-4-n.ng.png)

### <a name="add-an-application-registration-for-your-spring-boot-app"></a>Spring Boot 앱에 대한 애플리케이션 등록 추가

1. 왼쪽의 **관리** 창에서 **애플리케이션**, **추가**를 차례로 선택합니다.

    ![새 앱 등록 추가](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c1-n.png)

2. **이름** 필드에서 위의 **그룹**에 대한 값을 입력한 다음, **웹앱/웹 API 포함** 컨트롤을 **예**로 설정합니다.

3. **회신 URL**을 `http://localhost:8080/home`으로 설정합니다.

4. 다른 필드는 기본값으로 둡니다.

5. **만들기**를 선택합니다. 애플리케이션이 표시되는 데 약간의 시간이 걸릴 수 있습니다.

    ![애플리케이션 리디렉션 URI 추가](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c2-n.png)

6. **개요**, **애플리케이션**을 차례로 선택합니다.

7. 애플리케이션 테이블에서 프로젝트 이름이 있는 행을 선택합니다.

8. **일반** 창에서 [키]를 선택한 다음, **키 생성**을 선택합니다.

9. **앱 키**를 `yourGroupIdkey`로 설정하고, `yourGroupId`를 위에서 입력한 **그룹**에 대한 값으로 바꿉니다.

10. **저장**을 선택합니다. 앱 키 섹션에 키가 표시될 때까지 기다린 다음, 이 문서의 뒷부분에서 사용할 수 있도록 복사합니다.

    > [!NOTE]
    > **키** 섹션을 종료하고 다시 돌아오면 해당 키 값을 볼 수 없습니다. 이 경우 다른 키를 만들어 나중에 사용할 수 있도록 복사해야 합니다.
    > *application.yml* 파일에 포함되면 문제가 되는 문자(예: 백슬래시 또는 백틱)가 생성된 키에 포함되는 경우가 있습니다. 이 경우 해당 키를 삭제하고 다른 키를 생성하세요.

    ![비밀 만들기](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c3-n.png)

11. **개요**를 선택합니다.

12. 왼쪽 창의 **정책** 섹션에서 **사용자 흐름**, **새 사용자 흐름**을 차례로 선택합니다.

13. 이제 이 자습서를 종료하고, 다른 자습서를 실행하고, 완료되면 이 자습서로 돌아갑니다. 다른 자습서로 이동하는 경우 유의해야 할 몇 가지 사항은 다음과 같습니다.

    * **새 사용자 흐름**을 선택하도록 요청하는 단계부터 시작합니다.
    * 이 자습서에서 `webapp1`을 참조하는 경우 **그룹**에 대해 입력한 값을 대신 사용합니다.
    * 흐름에서 반환할 클레임을 선택하는 경우 **표시 이름**을 선택해야 합니다. 이 클레임을 사용하지 않으면 이 자습서에서 빌드된 앱이 작동하지 않습니다.
    * 사용자 흐름을 실행하라는 메시지가 표시되면 위에서 지정한 리디렉션 URL이 아직 활성 상태가 되지 않은 것입니다. 흐름은 계속 실행할 수 있지만 리디렉션이 성공적으로 완료되지 않습니다. 예상된 동작입니다.
    * "다음 단계"에 도달하면 이 자습서로 돌아갑니다.

    [자습서: Azure Active Directory B2C에서 사용자 흐름 만들기](/azure/active-directory-b2c/tutorial-create-user-flows)의 모든 단계를 수행하여 "가입 및 로그인", "프로필 편집" 및 "암호 재설정"에 대한 사용자 흐름을 만듭니다.

    AAD B2C는 로컬 계정 및 소셜 ID 공급자를 지원합니다. GitHub ID 공급자를 만드는 방법에 대한 예제는 [Azure Active Directory B2C를 사용하여 GitHub 계정으로 가입 및 로그인 설정](/azure/active-directory-b2c/identity-provider-github)을 참조하세요.

## <a name="configure-and-compile-your-app"></a>앱 구성 및 컴파일

이제 AAD B2C 인스턴스 및 일부 사용자 흐름을 만들었으므로 Spring 앱을 AAD B2C 인스턴스에 연결합니다.

1. 명령줄에서 cd를 사용하여 Spring Initializr에서 다운로드한 .zip 파일의 압축을 푼 디렉터리로 이동합니다.

2. 프로젝트에 대한 부모 폴더로 이동하여 텍스트 편집기에서 *pom.xml* Maven 프로젝트 파일을 엽니다.

3. Spring OAuth2 보안의 종속성을 *pom.xml*에 추가합니다.

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-active-directory-b2c-spring-boot-starter</artifactId>
        <version>See Below</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
        <version>See Below</version>
    </dependency>
    <dependency>
        <groupId>org.thymeleaf.extras</groupId>
        <artifactId>thymeleaf-extras-springsecurity5</artifactId>
        <version>See Below</version>
    </dependency>
    ```

    `azure-active-directory-b2c-spring-boot-starter`에 대해 사용 가능한 최신 버전을 사용합니다. 이는 [mvnrepository.com](https://mvnrepository.com/ artifact/com.microsoft.azure/azure-active-directory-spring-boot-starter)을 사용하여 확인할 수 있습니다. 이 문서를 작성할 당시 최신 버전은 `2.2.4`입니다.

    `spring-boot-starter-thymeleaf`에 대해 위에서 선택한 Spring Boot의 버전에 해당하는 버전을 사용합니다(예: `2.2.7.RELASE`).

    `thymeleaf-extras-springsecurity5`에 대해 사용 가능한 최신 버전을 사용합니다. 이는 [mvnrepository.com](https://mvnrepository.com/artifact/org.thymeleaf.extras/thymeleaf-extras-springsecurity5)을 사용하여 확인할 수 있습니다. 이 문서를 작성할 당시 최신 버전은 `3.0.4.RELEASE`입니다.

4. *pom.xml* 파일을 저장하고 닫습니다.

    * `mvn -DskipTests clean install`을 실행하여 종속성이 올바른지 확인합니다. `BUILD SUCCESS`가 표시되지 않으면 먼저 문제를 해결한 후에 계속 진행합니다.

5. 프로젝트에서 *src/main/resources* 폴더로 이동하여 텍스트 편집기에서 *application.yml* 파일을 만듭니다.

6. 이전에 생성한 값을 사용하여 앱 등록을 위한 설정을 지정합니다. 예를 들어,

    ```yaml
    azure:
      activedirectory:
        b2c:
          tenant: ejb0518domain
          client-id: ejb0518
          client-secret: '<yourAppKey>'
          reply-url: http://localhost:8080/home
          logout-success-url: http://localhost:8080/home
          user-flows:
            sign-up-or-sign-in: B2C_1_signupsignin1
            profile-edit: B2C_1_profileediting1
            password-reset: B2C_1_passwordreset1
    ```

    `client-secret` 값은 작은따옴표로 묶습니다. 이는 YAML에 있을 때 작은따옴표 안에 있어야 하는 일부 문자가 `<yourAppKey>`의 값에 거의 확실히 포함되므로 필요합니다.

    > [!NOTE]
    > 이 문서를 작성할 당시 *application.yml*에서 사용할 수 있는 Active Directory B2C Spring 통합 값의 전체 목록은 다음과 같습니다.
    >
    > ```
    > azure:
    >   activedirectory:
    >     b2c:
    >       tenant:
    >       oidc-enabled:
    >       client-id:
    >       client-secret:
    >       reply-url:  # should be absolute url.
    >       logout-success-url:
    >       user-flows:
    >         sign-up-or-sign-in:
    >         profile-edit: # optional
    >         password-reset: # optional
    > ```
    >
    > *application.yml* 파일은 GitHub의 [Azure Active Directory B2C Spring Boot 샘플](https://github.com/microsoft/azure-spring-boot/blob/master/azure-spring-boot-samples/azure-active-directory-b2c-oidc-spring-boot-sample/src/main/resources/application.yml)에서 사용할 수 있습니다.

7. *application.yml* 파일을 저장하고 닫습니다.

8. *src/main/java/<yourGroupId>/<yourGroupId>* 에서 *controller*라는 폴더를 만들고, `<yourGroupId>`를 **그룹**에 대해 입력한 값으로 바꿉니다.

9. *WebController.java*라는 새 Java 파일을 *controller* 폴더에 만들고 텍스트 편집기에서 엽니다.

10. 다음 코드를 입력하고, `yourGroupId`를 적절하게 변경한 다음, 파일을 저장하고 닫습니다.

    ```java
    package yourGroupId.yourGroupId.controller;

    import org.springframework.security.oauth2.client.authentication.OAuth2AuthenticationToken;
    import org.springframework.security.oauth2.core.user.OAuth2User;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.GetMapping;

    @Controller
    public class WebController {

        private void initializeModel(Model model, OAuth2AuthenticationToken token) {
            if (token != null) {
                final OAuth2User user = token.getPrincipal();

                model.addAttribute("grant_type", user.getAuthorities());
                model.addAllAttributes(user.getAttributes());
            }
        }

        @GetMapping(value = "/")
        public String index(Model model, OAuth2AuthenticationToken token) {
            initializeModel(model, token);

            return "home";
        }

        @GetMapping(value = "/greeting")
        public String greeting(Model model, OAuth2AuthenticationToken token) {
            initializeModel(model, token);

            return "greeting";
        }

        @GetMapping(value = "/home")
        public String home(Model model, OAuth2AuthenticationToken token) {
            initializeModel(model, token);

            return "home";
        }
    }
    ```

    컨트롤러의 모든 메서드에서 `initializeModel()`을 호출하고 해당 메서드에서 `model.addAllAttributes(user.getAttributes());`를 호출하므로 *src/main/resources/templates*의 모든 HTML 페이지에서 `${name}`, `${grant_type}` 또는 `${auth_time}`과 같은 특성에 액세스할 수 있습니다. `user.getAttributes()`에서 반환되는 값은 실제로 인증에 대한 `id_token`의 클레임입니다. 사용 가능한 클레임의 전체 목록은 [Microsoft ID 플랫폼 ID 토큰](/azure/active-directory/develop/id-tokens#payload-claims)에 나와 있습니다.

11. *src/main/java/<yourGroupId>/<yourGroupId>* 에서 *security*라는 폴더를 만들고, `yourGroupId`를 **그룹**에 대해 입력한 값으로 바꿉니다.

12. *WebSecurityConfiguration.java*라는 새 Java 파일을 *security* 폴더에 만들고 텍스트 편집기에서 엽니다.

13. 다음 코드를 입력하고, `yourGroupId`를 적절하게 변경한 다음, 파일을 저장하고 닫습니다.

    ```java
    package yourGroupId.yourGroupId.security;

    import com.microsoft.azure.spring.autoconfigure.b2c.AADB2COidcLoginConfigurer;
    import org.springframework.security.config.annotation.web.builders.HttpSecurity;
    import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
    import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

    @EnableWebSecurity
    public class WebSecurityConfiguration extends WebSecurityConfigurerAdapter {

        private final AADB2COidcLoginConfigurer configurer;

        public WebSecurityConfiguration(AADB2COidcLoginConfigurer configurer) {
            this.configurer = configurer;
        }

        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http
                    .authorizeRequests()
                    .anyRequest()
                    .authenticated()
                    .and()
                    .apply(configurer)
            ;
        }
    }
    ```

14. *greeting.html* 및 *home.html* 파일을 [Azure AD B2C Spring Boot 샘플](https://github.com/Microsoft/azure-spring-boot/tree/master/azure-spring-boot-samples/azure-active-directory-b2c-oidc-spring-boot-sample/src/main/resources/templates)에서 *src/main/resources/templates*로 복사하고, `${your-profile-edit-user-flow}` 및 `${your-password-reset-user-flow}`를 이전에 만든 사용자 흐름의 이름으로 바꿉니다.

## <a name="build-and-test-your-app"></a>앱 빌드 및 테스트

1. 명령 프롬프트를 열고 디렉터리를 앱의 *pom.xml* 파일이 위치한 폴더로 변경합니다.

2. Maven을 사용하여 Spring Boot 애플리케이션을 빌드하고 실행합니다. 예:

    > [!NOTE]
    > 로컬 Spring Boot 앱이 실행되는 시스템 클록에 따른 시간은 정확해야 합니다. OAuth 2.0을 사용하는 경우 클록 스큐의 허용 오차가 거의 없습니다. 3분 동안에도 정확하지 않은 경우 `[invalid_id_token] An error occurred while attempting to decode the Jwt: Jwt used before 2020-05-19T18:52:10Z`와 비슷한 오류와 함께 로그인이 실패할 수 있습니다. 이 문서를 작성할 당시 [time.gov](https://time.gov/)에는 클록이 실제 시간에서 떨어져 있는 정도를 나타내는 표시기가 있습니다. 앱은 0.019초 이상의 스큐를 통해 성공적으로 실행되었습니다.

    ```shell
    mvn -DskipTests clean package
    mvn -DskipTests spring-boot:run
    ```

3. 애플리케이션이 Maven에 의해 빌드되고 시작된 후 웹 브라우저에서 `http://localhost:8080/`을 엽니다. 로그인 페이지로 이동됩니다.

    ![로그인 페이지](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/lo1-n.png)

4. 로그인과 관련된 텍스트가 있는 링크를 선택합니다. 인증 프로세스를 시작하도록 Azure AD B2C가 리디렉션됩니다.

5. 성공적으로 로그인한 후 브라우저에서 샘플 `home page`가 표시됩니다.

    ![로그인 성공](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/lo3-n.png)

## <a name="summary"></a>요약

이 자습서에서는 Azure Active Directory B2C 스타터를 사용하여 새 Java 웹 애플리케이션을 만들고, 새 Azure AD B2C 테넌트를 구성하고, 새 애플리케이션을 이 테너트에 등록한 다음, Spring 주석 및 클래스를 사용하여 웹앱을 보호하도록 애플리케이션을 구성했습니다.

## <a name="next-steps"></a>다음 단계

Spring과 Azure에 대한 자세한 사항은 Azure의 Spring 설명서 센터를 참조합니다.

> [!div class="nextstepaction"]
> [Azure의 Spring](/azure/developer/java/spring-framework)
