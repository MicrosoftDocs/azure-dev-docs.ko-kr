---
title: Azure Kubernetes Service에서 실행할 Spring Boot 애플리케이션 마이그레이션
description: 이 가이드에서는 Azure Kubernetes Service 컨테이너에서 실행할 기존 Spring Boot 애플리케이션을 마이그레이션하려는 경우 알아야 할 사항에 대해 설명합니다.
author: mnriem
ms.author: manriem
ms.topic: conceptual
ms.date: 4/10/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 77ad38a4fb1290e392ee933a04aaf802a910e577
ms.sourcegitcommit: 1ddcb0f24d2ae3d1f813ec0f4369865a1c6ef322
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92689044"
---
# <a name="migrate-spring-boot-applications-to-azure-kubernetes-service"></a>Spring Boot 애플리케이션을 Azure Kubernetes Service로 마이그레이션

이 가이드에서는 AKS(Azure Kubernetes Service)에서 실행할 기존 Spring Boot 애플리케이션을 마이그레이션하려는 경우 알아야 할 사항에 대해 설명합니다.

## <a name="pre-migration"></a>사전 마이그레이션

마이그레이션을 성공적으로 수행하려면 시작하기 전에 다음 섹션에서 설명하는 평가 및 인벤토리 단계를 완료합니다.

### <a name="validate-that-the-supported-java-version-works-correctly"></a>지원되는 Java 버전이 제대로 작동하는지 확인

AKS에서 Spring Boot 애플리케이션을 실행하는 경우 지원되는 Java 버전을 사용하는 것이 좋습니다. 지원되는 버전을 사용하여 애플리케이션이 올바르게 실행되는지 확인합니다.

[!INCLUDE [note-obtain-your-current-java-version](includes/note-obtain-your-current-java-version.md)]

### <a name="determine-whether-and-how-the-file-system-is-used"></a>파일 시스템의 사용 여부 및 방법 확인

Spring Boot 애플리케이션에서 파일 시스템을 사용하려면 다시 구성하거나 드물게 아키텍처를 변경해야 합니다. 다음 섹션에서 설명하는 시나리오의 일부 또는 전부를 식별할 수 있습니다.

[!INCLUDE [static-content](includes/static-content.md)]

[!INCLUDE [dynamic-or-internal-content-aks](includes/dynamic-or-internal-content-aks.md)]

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs](includes/determine-whether-your-application-relies-on-scheduled-jobs.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

[!INCLUDE [identify-spring-boot-versions](includes/identify-spring-boot-versions.md)]

Spring Boot 1.x를 사용하는 애플리케이션의 경우 [Spring Boot 2.0 마이그레이션 가이드](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide)에 따라 지원되는 Spring Boot 버전으로 업데이트합니다.

### <a name="review-your-database-properties"></a>데이터베이스 속성 검토

애플리케이션에서 데이터베이스를 사용하는 경우 AKS로 마이그레이션한 후에 *application.properties* 파일에서 데이터베이스 속성을 검토하여 Spring Boot 애플리케이션에서 데이터베이스에 계속 액세스할 수 있는지 확인합니다. 데이터베이스가 온-프레미스에 있는 경우 해당 데이터베이스를 클라우드로 마이그레이션하거나 온-프레미스 데이터베이스에 대한 연결을 설정해야 합니다.

### <a name="identify-log-aggregation-solutions"></a>로그 집계 솔루션 확인

마이그레이션하는 애플리케이션에서 사용 중인 로그 집계 솔루션을 확인합니다.

### <a name="identify-application-performance-management-apm-agents"></a>APM(애플리케이션 성능 관리) 에이전트 확인

애플리케이션에서 사용 중인 애플리케이션 성능 모니터링 에이전트(예: Dynatrace 및 Datadog)를 확인합니다. Dockerfile 또는 Jib 구성에 포함되거나 Application Insights 프로세스 내 Java 에이전트를 사용하도록 이러한 APM 에이전트를 다시 구성해야 합니다.

### <a name="identify-zipkin-dependencies"></a>Zipkin 종속성 확인

애플리케이션에 Zipkin에 대한 명시적 종속성이 있는지 여부를 확인합니다. Maven 또는 Gradle 종속성에서 `io.zipkin.java` 그룹에 대한 종속성을 찾습니다.

### <a name="inventory-external-resources"></a>인벤토리 외부 리소스

데이터 원본, JMS 메시지 브로커, 다른 서비스의 URL 등과 같은 외부 리소스를 확인합니다. Spring Boot 애플리케이션에서 이러한 리소스의 구성은 일반적으로 *src/main/directory* 폴더의 *application.properties* 또는 *application.yml* 이라는 파일에서 찾을 수 있습니다. 또한 프로덕션 배포의 환경 변수에서 관련 구성 설정을 확인합니다.

[!INCLUDE [inventory-databases-spring-boot](includes/inventory-databases-spring-boot.md)]

[!INCLUDE [identify-jms-brokers-in-spring](includes/identify-jms-brokers-in-spring.md)]

사용 중인 broker가 확인되면 해당 설정을 찾습니다. Spring Boot 애플리케이션에서 이러한 설정은 일반적으로 애플리케이션 디렉터리의 *application.properties* 및 *application.yml* 파일에서 찾을 수 있습니다.

[!INCLUDE [jms-broker-settings-examples-in-spring](includes/jms-broker-settings-examples-in-spring.md)]

[!INCLUDE [identify-external-caches-azure-spring-cloud](includes/identify-external-caches-azure-spring-cloud.md)]

[!INCLUDE [inventory-configuration-sources-and-secrets-spring-boot](includes/inventory-configuration-sources-and-secrets-spring-boot.md)]

[!INCLUDE [inspect-the-deployment-architecture-spring-boot](includes/inspect-the-deployment-architecture-spring-boot.md)]

#### <a name="identity-providers"></a>ID 공급자

인증 및/또는 권한 부여가 필요한 모든 ID 공급자 및 모든 Spring Boot 애플리케이션을 확인합니다. ID 공급자를 구성하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* OAuth 또는 OAuth2 Spring Security 구성은 [Spring Security](https://spring.io/projects/spring-security)를 참조하세요.
* Auth0 Spring Security 구성은 [Auth0 Spring Security 설명서](https://auth0.com/docs/quickstart/backend/java-spring-security5/01-authorization)를 참조하세요.
* PingFederate Spring Security 구성은 [Auth0 PingFederate 지침](https://auth0.com/authenticate/java-spring-security/ping-federate/)을 참조하세요.

#### <a name="resources-configured-through-vmware-tanzu-application-service-tas-formerly-pivotal-cloud-foundry"></a>VMware TAS(Tanzu Application Service) (이전의 Pivotal Cloud Foundry)를 통해 구성된 리소스입니다.

TAS를 사용하여 관리되는 애플리케이션의 경우 앞에서 설명한 리소스를 포함한 외부 리소스는 TAS 서비스 바인딩을 통해 구성되는 경우가 많습니다. 이러한 리소스에 대한 구성을 검사하려면 [TAS(Cloud Foundry) CLI](https://docs.cloudfoundry.org/cf-cli/)를 사용하여 애플리케이션에 대한 `VCAP_SERVICES` 변수를 확인합니다.

```bash
# Log into TAS, if needed (enter credentials when prompted)
cf login -a <API endpoint>

# Set the organization and space containing the application, if not already selected during login.
cf target org <Organization Name>
cf target space <Space Name>

# Display variables for the application
cf env <Application Name>
```

`VCAP_SERVICES` 변수를 검사하여 애플리케이션에 바인딩된 외부 서비스의 구성 설정을 확인합니다. 자세한 내용은 [TAS(Cloud Foundry) 설명서](https://docs.cloudfoundry.org/devguide/deploy-apps/environment-variable.html#VCAP-SERVICES)를 참조하세요.

### <a name="in-place-testing"></a>내부 테스트

컨테이너 이미지를 만들기 전에 애플리케이션을 AKS에서 사용하려는 JDK 및 Spring Boot 버전으로 마이그레이션합니다. 호환성과 성능을 보장하기 위해 애플리케이션을 철저히 테스트합니다.

## <a name="migration"></a>마이그레이션

[!INCLUDE [provision-azure-container-registry-and-azure-kubernetes-service](includes/provision-azure-container-registry-and-azure-kubernetes-service.md)]

### <a name="create-a-docker-image-for-spring-boot"></a>Spring Boot용 Docker 이미지 만들기

Dockerfile을 만들려면 다음과 같은 필수 조건을 갖추어야 합니다.

* 지원되는 JDK
* JVM 런타임 옵션.
* 환경 변수를 전달하는 방법(해당하는 경우).

그런 다음, 해당하는 경우 다음 섹션에서 설명하는 단계를 수행할 수 있습니다. Dockerfile 및 Spring Boot 애플리케이션의 시작 지점으로 [Spring Boot 컨테이너 빠른 시작 리포지토리](https://github.com/Azure/spring-boot-container-quickstart)를 사용할 수 있습니다.

#### <a name="configure-azure-key-vault-provider-for-secrets-store-csi-driver"></a>비밀 저장소 CSI 드라이버용 Azure Key Vault 공급자 구성

Azure KeyVault를 만들고 필요한 모든 비밀을 채웁니다. 자세한 내용은 [빠른 시작: Azure CLI를 사용하여 Azure Key Vault에서 비밀을 설정하고 검색합니다](/azure/key-vault/quick-create-cli). 그런 다음, Pod에서 비밀에 액세스할 수 있도록 [비밀 저장소 CSI 드라이버용 Azure Key Vault 공급자](https://github.com/Azure/secrets-store-csi-driver-provider-azure)를 구성합니다.

Spring Boot 애플리케이션을 부트스트랩하는 데 사용되는 시작 스크립트도 업데이트해야 합니다. 이 스크립트는 애플리케이션을 시작하기 전에 인증서를 Spring Boot에서 사용하는 키 저장소로 가져와야 합니다.

### <a name="build-and-push-the-docker-image-to-azure-container-registry"></a>Docker 이미지를 빌드하고 Azure Container Registry로 푸시

Dockerfile을 만든 후 Docker 이미지를 빌드하고 Azure Container Registry에 게시해야 합니다.

[Spring Boot 컨테이너 빠른 시작 GitHub 리포지토리](https://github.com/Azure/spring-boot-container-quickstart)를 사용한 경우 이미지를 빌드하여 Azure Container Registry로 푸시하는 프로세스는 다음 세 가지 명령을 호출하는 것과 같습니다.

이 예제에서 `MY_ACR` 환경 변수는 Azure Container Rregistry의 이름을 포함하고 `MY_APP_NAME` 변수는 Azure Container Rregistry에서 사용하려는 웹 애플리케이션의 이름을 포함합니다.

배포 파일을 빌드합니다.

```bash
mvn package
```

Azure Container Registry에 로그인합니다.

```azurecli
az acr login -n ${MY_ACR}
```

이미지를 빌드하고 푸시합니다.

```azurecli
az acr build -t ${MY_ACR}.azurecr.io/${MY_APP_NAME} .
```

또는 다음 명령과 같이 Docker CLI를 사용하여 먼저 이미지를 로컬로 빌드하고 테스트할 수 있습니다. 이 방법을 사용하면 ACR에 처음 배포하기 전에 이미지를 테스트하고 구체화하는 작업을 간소화할 수 있습니다. 그러나 Docker CLI를 설치하고 Docker 데몬이 실행 중인지 확인해야 합니다.

이미지를 빌드합니다.

```bash
docker build -t ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

로컬에서 이미지를 실행합니다.

```bash
docker run -it -p 8080:8080 ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

이제 `http://localhost:8080`에서 애플리케이션에 액세스할 수 있습니다.

Azure Container Registry에 로그인합니다.

```azurecli
az acr login -n ${MY_ACR}
```

Azure Container Registry로 이미지를 푸시합니다.

```bash
docker push ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Azure에서 컨테이너 이미지를 빌드하고 저장하는 방법에 대한 자세한 내용은 [Azure Container Registry를 사용하여 컨테이너 이미지 빌드 및 저장](/learn/modules/build-and-store-container-images/) 학습 모듈을 참조하세요.

[Spring Boot 컨테이너 빠른 시작 GitHub 리포지토리](https://github.com/Azure/spring-boot-container-quickstart)를 사용한 경우 시작 시 JVM에 추가되는 사용자 지정 키 저장소를 포함할 수도 있습니다. 키 저장소 파일을 */opt/spring-boot/mycert.crt* 에 배치하면 이 추가 작업이 수행됩니다. 앞에서 설명한 대로 이 작업은 파일을 Docker 파일에 직접 추가하거나 비밀 저장소 CSI 드라이버용 Azure Key Vault 공급자를 사용하여 수행할 수 있습니다.

[Spring Boot 컨테이너 빠른 시작 GitHub 리포지토리](https://github.com/Azure/spring-boot-container-quickstart)를 사용한 경우 Kubernetes 배포 파일에서 `APPLICATIONINSIGHTS_CONNECTION_STRING` 환경 변수를 설정하여 Application Insights를 사용하도록 설정할 수도 있습니다(환경 변수의 값은 `InstrumentationKey=00000000-0000-0000-0000-000000000000`이어야 함). 자세한 내용은 [Java 코드리스 애플리케이션 모니터링 - Azure Monitor Application Insights](/azure/azure-monitor/app/java-in-process-agent)를 참조하세요.

Docker 이미지를 사용자 지정할 필요가 없는 경우 다른 방법으로 [Maven Jib 플러그 인](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) 사용을 검색하거나 AKS에 배포할 수 있습니다. 자세한 내용은 [Azure Kubernetes Service에 Spring Boot 애플리케이션 배포](../spring-framework/deploy-spring-boot-java-app-on-kubernetes.md)를 참조하세요.

[!INCLUDE [provision-a-public-ip-address](includes/provision-a-public-ip-address.md)]

### <a name="deploy-to-aks"></a>AKS에 배포

Kubernetes YAML 파일을 만들고 적용합니다. 자세한 내용은 [빠른 시작: Azure CLI를 사용하여 Azure Kubernetes Service 클러스터 배포](/azure/aks/kubernetes-walkthrough#run-the-application)를 참조하세요. 외부 부하 분산 장치를 만드는 경우(애플리케이션 또는 수신 컨트롤러에 있는지 여부에 관계없이) 이전 섹션에서 프로비저닝된 IP 주소를 `LoadBalancerIP`로 제공해야 합니다.

표면화된 매개 변수를 환경 변수로 포함합니다. 자세한 내용은 [컨테이너의 환경 변수 정의](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/)를 참조하세요.

배포 YAML을 만들 때는 컨테이너의 크기가 적절히 조정되도록 메모리 및 CPU 설정을 포함해야 합니다.

### <a name="ensure-console-logging-and-configure-diagnostic-settings"></a>콘솔 로깅 확인 및 진단 설정 구성

모든 애플리케이션에서 파일이 아니라 콘솔에 기록하도록 로깅을 구성합니다.

애플리케이션이 Azure Kubernetes Service에 배포되면 `kubectl`을 사용하여 로그를 볼 수 있습니다.

#### <a name="logstashelk-stack"></a>LogStash/ELK Stack

LogStash/ELK Stack을 로그 집계에 사용하는 경우 콘솔 출력을 [Azure Event Hub](/azure/event-hubs/)로 스트림하도록 진단 설정을 구성합니다. 그런 다음, [LogStash EventHub 플러그 인](https://github.com/logstash-plugins/logstash-input-azure_event_hubs)을 사용하여 기록된 이벤트를 LogStash에 수집합니다.

#### <a name="splunk"></a>Splunk

Splunk를 로그 집계에 사용하는 경우 콘솔 출력을 [Azure Blob Storage](/azure/storage/blobs/)로 스트림하도록 진단 설정을 구성합니다. 그런 다음, [Microsoft Cloud Services용 Splunk 추가 기능](https://splunkbase.splunk.com/app/3757/)을 사용하여 기록된 이벤트를 Splunk에 수집합니다.

### <a name="migrate-and-enable-the-identity-provider"></a>ID 공급자 마이그레이션 및 사용

Spring Boot 애플리케이션 중에서 인증 또는 권한 부여가 필요한 애플리케이션이 있는 경우 ID 공급자에 액세스하도록 구성되어 있는지 확인합니다.

* ID 공급자가 Azure Active Directory인 경우 변경할 필요가 없습니다.
* ID 공급자가 온-프레미스 Active Directory 포리스트인 경우 Azure Active Directory를 사용하여 하이브리드 ID 솔루션을 구현하는 것이 좋습니다. 지침은 [하이브리드 ID 설명서](/azure/active-directory/hybrid/)를 참조하세요.
* ID 공급자가 다른 온-프레미스 솔루션(예: PingFederate)인 경우 [Azure AD Connect 설치 사용자 지정](/azure/active-directory/hybrid/how-to-connect-install-custom) 항목을 참조하여 Azure Active Directory와 페더레이션하도록 구성합니다. 또는 Spring Security를 사용하여 [OAuth2/OpenID Connect](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#oauth2) 또는 [SAML](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-saml2)을 통해 ID 공급자를 사용하는 것이 좋습니다.

### <a name="configure-persistent-storage"></a>영구 스토리지 구성

애플리케이션에 비휘발성 스토리지가 필요한 경우 하나 이상의 [영구 볼륨](/azure/aks/azure-disks-dynamic-pv)을 구성합니다.

[!INCLUDE [migrate-scheduled-jobs-aks](includes/migrate-scheduled-jobs-aks.md)]

## <a name="post-migration"></a>마이그레이션 후 작업

이제 애플리케이션이 AKS로 마이그레이션되었으므로 예상대로 작동하는지 확인해야 합니다. 이 작업이 완료되면 애플리케이션을 클라우드 네이티브로 만들 수 있는 몇 가지 추천 사항이 있습니다.

### <a name="recommendations"></a>권장 사항

* 수신 컨트롤러 또는 애플리케이션 부하 분산 장치에 할당된 IP 주소에 DNS 이름을 추가하는 것이 좋습니다. 자세한 내용은 [AKS에서 고정 공용 IP 주소를 사용하여 수신 컨트롤러 만들기](/azure/aks/ingress-static-ip)를 참조하세요.

* 애플리케이션에 대한 [HELM 차트](https://helm.sh/docs/topics/charts/)를 추가하는 것이 좋습니다. HELM 차트를 사용하면 더 다양한 고객 세트에서 사용하고 사용자 지정할 수 있도록 애플리케이션 배포를 매개 변수화할 수 있습니다.

* DevOps 전략을 설계하고 구현합니다. 개발 속도를 높이는 동시에 안정성을 유지하기 위해 Azure Pipelines를 사용하여 배포를 자동화하고 테스트하는 것이 좋습니다. 자세한 내용은 [빌드하고 AKS에 배포](/azure/devops/pipelines/ecosystems/kubernetes/aks-template)를 참조하세요.

* [Azure 모니터링을 클러스터에 사용](/azure/azure-monitor/insights/container-insights-enable-existing-clusters)하도록 설정하여 컨테이너 로그 수집, 사용량 추적 등을 허용합니다.

* Prometheus를 통해 애플리케이션별 메트릭을 공개하는 것이 좋습니다. Prometheus는 Kubernetes 커뮤니티에서 광범위하게 채택된 오픈 소스 메트릭 프레임워크입니다. 사용자 고유의 Prometheus 서버를 호스팅하는 대신 [Azure Monitor에서 스크랩하는 Prometheus 메트릭](/azure/azure-monitor/insights/container-insights-prometheus-integration)을 구성하여 애플리케이션에서 메트릭 집계를 사용하도록 설정하고 비정상 조건에 대한 자동화된 응답 또는 에스컬레이션을 수행할 수 있습니다.

* 비즈니스 연속성 및 재해 복구 전략을 설계하고 구현합니다. 중요 업무용 애플리케이션의 경우 [다중 지역 배포 아키텍처](/azure/aks/operator-best-practices-multi-region)를 고려하세요.

* [Kubernetes 버전 지원 정책](/azure/aks/supported-kubernetes-versions#kubernetes-version-support-policy)을 검토합니다. 항상 지원되는 버전을 실행하도록 [AKS 클러스터를 계속 업데이트](/azure/aks/upgrade-cluster)해야 합니다.

* 클러스터 관리 및 애플리케이션 개발을 담당하는 모든 팀 멤버가 관련 [AKS 모범 사례](/azure/aks/best-practices)를 검토하도록 합니다.

* 배포 파일에 롤링 업데이트 수행 방법이 지정되어 있는지 확인합니다. 자세한 내용은 Kubernetes 설명서의 [롤링 업데이트 배포](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-update-deployment)를 참조하세요.

* 피크 타임 부하를 처리하도록 자동 크기 조정을 설정합니다. 자세한 내용은 [AKS에서 애플리케이션 수요에 맞게 자동으로 클러스터 크기 조정](/azure/aks/cluster-autoscaler)을 참조하세요.

* 성능을 더 최적화하려면 [코드 캐시 크기를 모니터링](https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm)하고 `-XX:InitialCodeCacheSize` 및 `-XX:ReservedCodeCacheSize` 매개 변수를 Dockerfile의 `JAVA_OPTS` 변수에 추가하는 것이 좋습니다.
