---
title: Java MicroProfile 서비스 배포
titleSuffix: Azure Web App for Containers
description: Docker 및 Azure Web App for Containers를 사용하는 MicroProfile 서비스를 배포하는 방법 알아보기
services: container-registry;app-service
documentationcenter: java
author: jonathangiles
ms.author: jogiles
ms.date: 09/07/2018
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: devx-track-java
ms.openlocfilehash: a9b0721c7a8d409d661c8e53963ca9b3bc2f52dd
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379867"
---
# <a name="deploy-a-java-based-microprofile-service-to-azure-web-app-for-containers"></a>Java 기반 MicroProfile 서비스를 Azure Web App for Containers에 배포합니다.

MicroProfile은 매우 작은 Java 애플리케이션을 빌드하여 [Azure Web App for Containers](https://azure.microsoft.com/services/app-service/containers/)와 같은 서비스에 빠르고 쉽게 배포할 수 있는 훌륭한 방법입니다. 이 자습서에서는 MicroProfile 기반 마이크로서비스를 만든 다음에 Docker 컨테이너로 컨테이너화하여 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)로 배포하고, Azure Web App for Containers를 사용하여 호스팅하고 있습니다.

> [!NOTE]
> Docker 컨테이너 이미지가 자체 실행 가능(즉, 런타임을 포함)한 경우, 이 절차는MicroProfile.io의 모든 구현을 사용하여 실행됩니다.

더 구체적으로는, 이 예제에서는 [Payara Micro](https://www.payara.fish/payara_micro) 및 [MicroProfile 1.3](https://microprofile.io/)을 사용하여 매우 작은 Java war 파일(작성자 머신에서 5,085 바이트)을 만들고 Docker 이미지(약 174 메가바이트)로 패키지화합니다. 이 Docker 이미지는 이 웹앱의 완전히 컨테이너화된 배포에 필요한 모든 것을 포함합니다.

Docker가 작동하는 방식으로 인해 Docker는 차이점만 업로드하기 때문에 애플리케이션 소스 코드가 변경될 때마다 전체 174 메가바이트 Docker 이미지를 다시 배포할 필요가 없는 경우가 종종 있습니다. 따라서 CI/CD 파이프라인을 통해 MicroProfile 애플리케이션의 새 릴리스를 실행하는 프로세스가 효율적이고 신속하게 이루어지며 마찰을 줄이고 신속한 개발 반복을 가능하게 합니다.

먼저 로컬에서 코드를 생성하고 실행하여 이 자습서를 수행한 다음, Azure에 웹앱으로 배포합니다. 두 경우 모두에서 작업을 단순화하고 표준화하기 위해 Docker를 사용하게 됩니다. 시작하기 전에 Docker 컨테이너를 저장하기 위해 Azure Container Registry를 만듭니다.

## <a name="creating-an-azure-container-registry"></a>Azure Container Registry 만들기

Azure Container Registry를 만드는 데 [Azure Portal](https://portal.azure.com)을 사용하지만 Azure CLI와 같은 대안이 있습니다. 새 Azure Container Registry를 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하여 새 Azure Container Registry 리소스를 만듭니다. 레지스트리 이름을 입력합니다(이 이름은 *pom.xml*의 `docker.registry` 속성으로 설정되어야 함). 기본값을 원하는 대로 변경한 다음, **만들기**를 클릭합니다.

1. 컨테이너 레지스트리가 실행되면(**만들기**을 클릭한 후 약 30초 후), 컨테이너 레지스트리를 클릭하고 왼쪽 메뉴 영역의 **액세스 키** 링크를 클릭합니다. 여기에서 **관리 사용자** 설정을 활성화하여, 이 컨테이너 레지스트리를 컴퓨터(Docker 컨테이너 푸시)에서 액세스하고 또한 곧 설치할 Azure Web Apps for Containers 인스턴스에서 액세스할 수 있도록 설정해야 합니다.

1. **액세스 키** 영역에 있는 동안, `username` 및 `password` 값을 확인합니다. 이 값을 글로벌 Maven *settings.xml* 파일에 복사하여 붙여넣을 것입니다(Maven 설정에 대한 자세한 정보는 [Apache Maven Project](https://maven.apache.org/settings.html) 웹 사이트 참조). 참조용으로, 작성자 시스템에 *${user.home}/.m2/settings.xml* 파일의 난독 처리된 버전이 있습니다.

    ```xml
    <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                          https://maven.apache.org/xsd/settings-1.0.0.xsd">
        <servers>
          <server>
            <id>jogilescr.azurecr.io</id>
            <username>jogilescr</username>
            <password>ojoirshois.this-isn't-real.hrihslirhlishrglih</password>
          </server>
        </servers>
    </settings>
    ```

이제 MicroProfile 애플리케이션을 로컬에서 빌드하고 실행하는 것으로 이동할 수 있습니다.

## <a name="creating-our-microprofile-application"></a>MicroProfile 애플리케이션 만들기

이 예제는 GitHub에서 사용할 수 있는 예제 애플리케이션을 기반으로 하므로, 코드를 복제한 다음, 코드 단계를 따릅니다. 컴퓨터에 코드를 복제하려면 다음 단계를 수행합니다.

1. `git clone https://github.com/Azure-Samples/microprofile-docker-helloworld.git`

1. `cd microprofile-docker-helloworld`

이 디렉터리에는 Maven 빌드 도구가 사용하는 형식으로 프로젝트를 지정하는 데 사용되는 *pom.xml* 파일이 있습니다. 이 파일은 사용자 고유의 요구에 맞게 편집할 수 있습니다. 특히, Azure Container Registry가 설정되었을 때 `docker.registry` 및 `docker.name` 속성이 `docker.registry` 및 `docker.name`으로 변경되어야 합니다.

이 디렉터리에서 확인할 다른 파일은 아래에서 재생되는 Dockerfile입니다.

```dockerfile
FROM payara/micro

ARG WAR_FILE
COPY target/${WAR_FILE} $DEPLOY_DIR

EXPOSE 8080
```

이 Dockerfile은 단순히 Payara Micro Docker Container를 기반으로 새 Docker 컨테이너를 만들고 빌드 프로세스의 일부로 생성된 *.war* 파일 복사본을 만듭니다. 이는 또한 Docker 컨테이너 내에서 실행되면 서비스에 액세스할 수 있도록 8080 포트를 노출합니다.

*src* 디렉터리를 살펴보면 결국 `Application` 클래스가 아래에 재생되었음을 발견하게 됩니다.

```java
package com.microsoft.azure.samples.microprofile.docker.helloworld;

import javax.ws.rs.ApplicationPath;

@ApplicationPath("/api")
public class Application extends javax.ws.rs.core.Application { }
```

`@ApplicationPath("/api")` 주석은 이 마이크로서비스의 기본 엔드포인트를 지정합니다. 즉, 모든 엔드포인트에는 특정 REST 엔드포인트에 액세스하는 데 필요한 나머지 URL 앞에 `/api`가 옵니다.

`api` 패키지 내에는 `API`로 명명된 클래스가 있으며 다음 코드를 포함합니다.

```java
package com.microsoft.azure.samples.microprofile.docker.helloworld.api;

import javax.enterprise.context.ApplicationScoped;
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;

import static javax.ws.rs.core.MediaType.TEXT_HTML;

@ApplicationScoped
@Path("/")
public class API {

    @GET
    @Path("/helloworld")
    @Produces(TEXT_HTML)
    public String info() {
        return "Hello, world!";
    }
}
```

`@Path("/helloworld")` 주석을 사용하여, `Application` 클래스에 지정된 `/api`와 결합 될 때, 이 REST 엔드포인트가 `/api/helloworld`임을 알 수 있습니다. HTTP GET 요청을 사용하여 이 엔드포인트을 호출하면 메서드에서 text/html을 생성하며 실제로는 단순히 하드 코딩된 문자열인 "Hello, world!"라는 사실을 알 수 있습니다.

이제 MicroProfile를 사용하여 마이크로 서비스를 만드는 데 필요한 모든 코드를 설명했습니다. 이제 Maven을 사용하여 빌드하고, Docker 컨테이너로 컨테이너화하여 로컬로 실행할 수 있습니다. 이 작업은 다음 단계를 수행하여 할 수 있습니다.

1. `mvn clean package`를 실행하고 성공적으로 완료될 때까지 기다립니다.

1. `docker run -it --rm -p 8080:8080 <docker.registry>/<docker.name>:latest`을 실행, 예를 들어 `docker.registry`가 `jogilescr.azurecr.io`이고 `docker.name`이 `samples/docker-helloworld`인 경우, `docker run -it --rm -p 8080:8080 jogilescr.azurecr.io/samples/docker-helloworld:latest`입니다.

1. 웹 브라우저에서 `http://localhost:8080/microprofile/api/helloworld` 및 `http://localhost:8080/health`에 액세스합니다. "Hello, world!" 예상된 응답(및 `/health` 엔드포인트에 대한 상태 관련 정보)이 표시되면 MicroProfile 애플리케이션이 로컬 컴퓨터에 성공적으로 배포된 것입니다.

## <a name="pushing-to-the-azure-container-registry"></a>Azure Container Registry로 푸시하기

이제 로컬 시스템에서 MicroProfile 애플리케이션을 성공적으로 빌드하고 실행했으므로 다음 단계는 이 컨테이너를 컨테이너 레지스트리에 푸시하는 것입니다. 이 자습서에서는 Azure Container Registry를 사용하지만 모든 컨테이너 레지스트리가 작동합니다(`pom.xml` 파일을 편집하여 관련 위치를 가리키는 경우).

1. `mvn clean package`를 실행하여 클린, 컴파일하고 로컬 docker 이미지를 만듭니다.

2. Azure Container Registry에 푸시하기 위해 `mvn dockerfile:push`를 실행합니다.

이 단계에서 Docker 컨테이너 이미지를 Azure Container Registry에 업로드했으나 Azure Web App for Containers 인스턴스에 배포해야 하므로 아직 실행되지 않았습니다. 이제는 이 작업을 시작합니다.

## <a name="creating-an-azure-web-app-for-containers-instance"></a>Web App for Containers 인스턴스 만들기

1. [Azure Portal](https://portal.azure.com)로 돌아가서 새 Web App for Containers 인스턴스를 만듭니다(메뉴의 '웹 + 모바일' 제목 아래에 있음). 몇 가지 주의사항을 다룹니다.

   1. 여기에 지정한 이름은 웹 응용 프로그램의 공용 URL이 됩니다(원하는 경우 나중에 사용자 정의 도메인을 추가할 수 있음). 따라서 쉽게 기억할 수 있는 이름을 선택하는 것이 좋습니다.

   1. '컨테이너 구성' 섹션으로 이동하면 '이미지 소스'에 대해 'Azure Container Registry'를 선택한 다음 드롭 다운 목록에서 올바른 이미지를 선택할 수 있습니다.

   1. '시작 파일' 필드에 값을 지정할 필요가 없습니다.

1. 인스턴스가 생성되면(다시 한번 말하면 매우 빠름) 클릭하고 '애플리케이션 설정' 메뉴 항목을 클릭하십시오. 여기서 키가 `WEBSITES_PORT`이고 값이 `8080`인 새 애플리케이션 설정을 추가해야 합니다. Azure에게 컨테이너에서 노출시키고자 하는 포트를 알려주게 되고, 외부적으로는 포트 80에 매핑됩니다.

1. 또는 'Docker 컨테이너' 링크를 클릭하고 '지속적인 배포'를 활성화하면 Azure Container Registry 이미지를 업데이트할 때마다 Azure Web App for Containers 인스턴스에서 자동으로 업데이트됩니다.

1. `http://<appname>.azurewebsites.net/microprofile/api/helloworld`, `http://<appname>.azurewebsites.net/health`에서 Azure 호스트 인스턴스에 액세스할 수 있습니다.

## <a name="summary"></a>요약

이 자습서를 통해 간단한 MicroProfile 기반의 마이크로 서비스를 생성하고 Docker 컨테이너로 컨테이너화한 다음 로컬로 실행하여 Azure에 게시했습니다. 더 유용한 기능을 제공하기 위해 마이크로 서비스를 확장하는 것은 이 자습서의 범위를 벗어나지만, 인터넷에는 MicroProfile에 대한 풍부한 자습서와 조언이 있으며 [MicroProfile.io](https://microprofile.io/)에서 더 많은 내용을 검토할 것을 권장합니다.
