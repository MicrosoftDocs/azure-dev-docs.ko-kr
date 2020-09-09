---
author: yevster
ms.author: yebronsh
ms.date: 8/25/2020
ms.openlocfilehash: 6b2ad5c8490cd4b1c450426f8e7d728cd39eaea3
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89062066"
---
마이그레이션을 완료했으므로, 애플리케이션이 예상대로 작동하는지 확인합니다. 그 후에는 다음 권장 사항에 따라 애플리케이션을 클라우드 네이티브로 만들 수 있습니다.

* 애플리케이션이 Spring Cloud Registry에서 작동하도록 설정하는 방안을 고려해 보세요. 이렇게 하면 배포된 다른 마이크로서비스 및 클라이언트에서 애플리케이션을 동적으로 검색할 수 있습니다. 자세한 내용은 [자습서: 배포할 Java Spring 앱 준비](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment) 그런 다음, Spring 클라이언트 부하 분산 장치를 사용하도록 애플리케이션 클라이언트를 수정합니다. 이렇게 하면 클라이언트는 실행 중인 모든 애플리케이션 인스턴스의 주소를 가져오고, 다른 인스턴스가 손상되거나 응답하지 않을 때 작동하는 인스턴스를 찾을 수 있습니다. 자세한 내용은 Spring 블로그의 [Spring 팁: Spring Cloud Load Balancer](https://spring.io/blog/2020/03/25/spring-tips-spring-cloud-loadbalancer)를 참조하세요.

* 애플리케이션을 공개로 설정하는 대신 [Spring Cloud Gateway](https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/current/reference/html/) 인스턴스를 추가하는 것이 좋습니다. Spring Cloud Gateway는 Azure Spring Cloud 인스턴스에 배포된 모든 애플리케이션/마이크로서비스에 대한 단일 엔드포인트를 제공합니다. Spring Cloud Gateway가 이미 배포된 경우에는 새로 배포된 애플리케이션으로 트래픽을 라우팅하도록 구성해야 합니다.

* Spring Cloud Config 서버를 추가하여 모든 Spring Cloud 마이크로서비스의 구성을 중앙에서 관리하고 버전을 제어하는 것이 좋습니다. 먼저 구성을 저장할 Git 리포지토리를 만들고 이 구성을 사용하도록 Azure Spring Cloud 인스턴스를 구성합니다. 자세한 내용은 [자습서: 서비스용 Spring Cloud Config 서버 인스턴스 설정](/azure/spring-cloud/spring-cloud-tutorial-config-server)을 참조하세요. 그 후에는 다음 단계에 따라 구성을 마이그레이션합니다.

  1. 애플리케이션의 *src/main/resources* 디렉터리 내에 다음 콘텐츠가 포함된 *bootstrap.yml* 파일을 만듭니다.

        ```yml
          spring:
            application:
              name: <your-application-name>
        ```

  1. 구성 Git 리포지토리에서 *<your-application-name>.yml* 파일을 만듭니다. 여기서 `your-application-name`은 이전 단계와 동일합니다. *src/main/resources*에서 *application.yml* 파일의 설정을 방금 만든 새 파일로 이동합니다. 설정이 이전에 *.properties* 파일에 있었으면 먼저 YAML로 변환합니다. 온라인 도구 또는 IntelliJ 플러그인을 찾아서 이 변환을 수행할 수 있습니다.

  1. 위의 디렉터리에 *application.yml* 파일을 만듭니다. 이 파일을 사용하여 Azure Spring Cloud 인스턴스의 모든 애플리케이션 간에 공유되는 설정과 리소스를 정의할 수 있습니다. 이러한 항목에는 일반적으로 데이터 원본, 로깅 설정, Spring Boot Actuator 구성 등이 포함됩니다.

  1. 변경 내용을 Git 리포지토리에 커밋하고 푸시합니다.

  1. 애플리케이션에서 *application.properties* 또는 *application.yml* 파일을 제거합니다.

* 일관된 자동 배포를 위해 배포 파이프라인을 추가하는 것이 좋습니다. [Azure Pipelines](/azure/spring-cloud/spring-cloud-howto-cicd), [GitHub Actions](/azure/spring-cloud/spring-cloud-howto-github-actions) 및 [Jenkins](/azure/jenkins/tutorial-jenkins-deploy-cli-spring-cloud-service)에 대한 지침을 사용할 수 있습니다.

* 일부 또는 모든 최종 사용자가 사용할 수 있기 전에 스테이징 배포를 사용하여 프로덕션 환경에서 코드 변경을 테스트하는 것이 좋습니다. 자세한 내용은 [Azure Spring Cloud에서 스테이징 환경 설정](/azure/spring-cloud/spring-cloud-howto-staging-environment)을 참조하세요.

* 서비스 바인딩을 추가하여 애플리케이션을 지원되는 Azure 데이터베이스에 연결하는 것이 좋습니다. 이러한 서비스 바인딩을 사용하면 자격 증명을 포함한 연결 정보를 Spring Cloud 애플리케이션에 제공할 필요가 없습니다.

* 분산 추적 및 Azure App Insights를 사용하여 애플리케이션의 성능과 상호 작용을 모니터링하는 것이 좋습니다. 자세한 내용은 [Azure Spring Cloud에서 분산 추적 사용](/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)을 참조하세요.

* Azure Monitor 경고 규칙 및 작업 그룹을 추가하여 이상 상태를 빠르게 검색하여 처리하는 것이 좋습니다. 자세한 내용은 [자습서: 경고 및 작업 그룹을 사용하여 Spring Cloud 리소스 모니터링](/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)을 참조하세요.

* Azure Spring Cloud 배포를 다른 지역에 복제하여 대기 시간을 줄이고 안정성과 내결함성을 높이는 것이 좋습니다. [Azure Traffic Manager](/azure/traffic-manager)를 사용하여 배포 간의 부하를 분산하거나 [Azure Front Door](/azure/frontdoor)를 사용하여 DDoS 보호가 있는 SSL 오프로딩 및 웹 애플리케이션 방화벽을 추가합니다.

* 지역 복제가 필요하지 않은 경우 [Azure Application Gateway](/azure/application-gateway)를 추가하여 DDoS 보호가 있는 SSL 오프로딩 및 웹 애플리케이션 방화벽을 추가합니다.
