---
title: Tomcat 애플리케이션을 Azure Kubernetes Service의 컨테이너로 마이그레이션
description: 이 가이드에서는 Azure Kubernetes Service 컨테이너에서 실행되도록 기존 Tomcat 애플리케이션을 마이그레이션하려는 경우 알아야 할 사항에 대해 설명합니다.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 1/20/2020
ms.openlocfilehash: 8a6245f496b3a1371c43f764081f50b7b3f13dbd
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82988738"
---
# <a name="migrate-tomcat-applications-to-containers-on-azure-kubernetes-service"></a>Tomcat 애플리케이션을 Azure Kubernetes Service의 컨테이너로 마이그레이션

이 가이드에서는 AKS(Azure Kubernetes Service)에서 실행되도록 기존 Tomcat 애플리케이션을 마이그레이션하려는 경우 알아야 할 사항에 대해 설명합니다.

## <a name="pre-migration"></a>사전 마이그레이션

마이그레이션을 성공적으로 수행하려면 시작하기 전에 다음 섹션에서 설명하는 평가 및 인벤토리 단계를 완료합니다.

[!INCLUDE [inventory-external-resources](includes/inventory-external-resources.md)]

[!INCLUDE [inventory-secrets](includes/inventory-secrets.md)]

[!INCLUDE [determine-whether-and-how-the-file-system-is-used](includes/determine-whether-and-how-the-file-system-is-used.md)]

<!-- AKS-specific addendum to inventory-persistence-usage -->
[!INCLUDE [dynamic-or-internal-content-aks](includes/dynamic-or-internal-content-aks.md)]

### <a name="identify-session-persistence-mechanism"></a>세션 지속성 메커니즘 식별

사용 중인 세션 지속성 관리자를 확인하려면 애플리케이션 및 Tomcat 구성의 *context.xml* 파일을 검사합니다. `<Manager>` 요소를 찾은 다음, `className` 특성의 값을 확인합니다.

[StandardManager](https://tomcat.apache.org/tomcat-9.0-doc/config/manager.html#Standard_Implementation) 또는 [FileStore](https://tomcat.apache.org/tomcat-9.0-doc/config/manager.html#Nested_Components)와 같은 Tomcat의 기본 제공 [PersistentManager](https://tomcat.apache.org/tomcat-9.0-doc/config/manager.html) 구현은 Kubernetes와 같은 분산된 확장 플랫폼에서 사용하도록 설계되지 않았습니다. AKS는 여러 Pod 간에 부하를 분산하고 언제든지 Pod를 투명하게 다시 시작할 수 있으므로 변경 가능한 상태를 파일 시스템에 유지하지 않는 것이 좋습니다.

세션 지속성이 필요한 경우 외부 데이터 저장소에 쓰는 대체 `PersistentManager` 구현(예: Redis Cache를 사용하는 Pivotal 세션 관리자)을 사용해야 합니다. 자세한 내용은 [Tomcat을 사용하여 Redis를 세션 캐시로 사용](/azure/app-service/containers/configure-language-java#use-redis-as-a-session-cache-with-tomcat)을 참조하세요.

### <a name="special-cases"></a>특수 사례

특정 프로덕션 시나리오에는 추가 변경이 필요하거나 추가 제한 사항이 있을 수 있습니다. 이러한 시나리오는 드물게 발생할 수 있지만, 애플리케이션에 적용할 수 없거나 올바르게 해결되었는지 확인해야 합니다.

#### <a name="determine-whether-application-relies-on-scheduled-jobs"></a>애플리케이션에서 예약된 작업을 사용하는지 확인

Quartz 스케줄러 작업 또는 cron 작업과 같은 예약된 작업은 컨테이너화된 Tomcat 배포에서 사용할 수 없습니다. 애플리케이션이 확장되는 경우 하나의 예약된 작업이 예약된 기간마다 두 번 이상 실행될 수 있습니다. 이 경우 의도하지 않은 결과가 발생할 수 있습니다.

모든 예약된 작업을 애플리케이션 서버 내부 또는 외부에 인벤토리합니다.

#### <a name="determine-whether-your-application-contains-os-specific-code"></a>OS 관련 코드가 애플리케이션에 포함되어 있는지 확인

애플리케이션이 실행되는 OS를 수용하는 코드가 애플리케이션에 포함되어 있는 경우 기본 OS를 사용하지 않도록 애플리케이션을 리팩터링해야 합니다. 예를 들어 파일 시스템 경로에서 `/` 또는 `\`를 사용하는 경우 [`File.Separator`](https://docs.oracle.com/javase/8/docs/api/java/io/File.html#separator) 또는 [`Path.get`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Paths.html#get-java.lang.String-java.lang.String...-)으로 바꿔야 할 수 있습니다.

#### <a name="determine-whether-memoryrealm-is-used"></a>MemoryRealm이 사용되는지 확인

[MemoryRealm](https://tomcat.apache.org/tomcat-9.0-doc/api/org/apache/catalina/realm/MemoryRealm.html)에는 지속형 XML 파일이 필요합니다. Kubernetes에서는 이 파일을 컨테이너 이미지에 추가하거나 [컨테이너에 사용할 수 있는 공유 스토리지](#identify-session-persistence-mechanism)에 업로드해야 합니다. 이에 따라 `pathName` 매개 변수를 적절하게 수정해야 합니다.

`MemoryRealm`이 현재 사용되고 있는지 확인하려면 *server.xml* 및 * context.xml* 파일을 검사하고 `className` 특성이 `org.apache.catalina.realm.MemoryRealm`으로 설정된 `<Realm>` 요소를 찾습니다.

#### <a name="determine-whether-ssl-session-tracking-is-used"></a>SSL 세션 추적이 사용되는지 확인

컨테이너화된 배포에서 SSL 세션은 일반적으로 수신 컨트롤러를 통해 애플리케이션 컨테이너 외부에 오프로드됩니다. 애플리케이션에 [SSL 세션 추적](https://tomcat.apache.org/tomcat-9.0-doc/servletapi/javax/servlet/SessionTrackingMode.html#SSL)이 필요한 경우 SSL 트래픽이 애플리케이션 컨테이너로 직접 전달되어야 합니다.

#### <a name="determine-whether-accesslogvalve-is-used"></a>AccessLogValve가 사용되는지 확인

[AccessLogValve](https://tomcat.apache.org/tomcat-9.0-doc/api/org/apache/catalina/valves/AccessLogValve.html)를 사용하는 경우 `directory` 매개 변수를 [탑재된 Azure Files 공유](/azure/aks/azure-files-dynamic-pv) 또는 해당 하위 디렉터리 중 하나로 설정해야 합니다.

### <a name="in-place-testing"></a>내부 테스트

컨테이너 이미지를 만들기 전에 애플리케이션을 AKS에서 사용하려는 JDK 및 Tomcat으로 마이그레이션합니다. 호환성과 성능을 보장하기 위해 애플리케이션을 철저히 테스트합니다.

### <a name="parameterize-the-configuration"></a>구성 매개 변수화

마이그레이션 전에 *server.xml* 및 *context.xml* 파일에서 데이터 원본과 같은 비밀 및 외부 종속성을 식별했을 가능성이 높습니다. 따라서 식별된 각 항목에 대해 사용자 이름, 암호, 연결 문자열 또는 URL을 환경 변수로 바꿉니다.

예를 들어 *context.xml* 파일에 다음 요소가 포함되어 있다고 가정합니다.

```xml
<Resource
    name="jdbc/dbconnection"
    type="javax.sql.DataSource"
    url="jdbc:postgresql://postgresdb.contoso.com/wickedsecret?ssl=true"
    driverClassName="org.postgresql.Driver"
    username="postgres"
    password="t00secure2gue$$"
/>
```

이 경우 다음 예제와 같이 변경할 수 있습니다.

```xml
<Resource
    name="jdbc/dbconnection"
    type="javax.sql.DataSource"
    url="${postgresdb.connectionString}"
    driverClassName="org.postgresql.Driver"
    username="${postgresdb.username}"
    password="${postgresdb.password}"
/>
```

## <a name="migration"></a>마이그레이션

첫 번째 단계("컨테이너 레지스트리 및 AKS 프로비저닝")를 제외하고는 마이그레이션하려는 각 애플리케이션(WAR 파일)에 대해 아래 단계를 개별적으로 수행하는 것이 좋습니다.

> [!NOTE]
> 일부 Tomcat 배포에는 단일 Tomcat 서버에서 실행되는 여러 애플리케이션이 있을 수 있습니다. 배포의 경우 각 애플리케이션을 별도의 Pod에서 실행하는 것이 좋습니다. 이렇게 하면 각 애플리케이션에 대해 리소스 사용률을 최적화하면서 복잡성과 결합을 최소화할 수 있습니다.

### <a name="provision-container-registry-and-aks"></a>컨테이너 레지스트리 및 AKS 프로비저닝

서비스 주체가 레지스트리에서 읽기 권한자 역할을 갖는 컨테이너 레지스트리와 Azure Kubernetes 클러스터를 만듭니다. 클러스터의 네트워킹 요구 사항에 [적합한 네트워크 모델을 선택](/azure/aks/operator-best-practices-network#choose-the-appropriate-network-model)해야 합니다.

```bash
az group create -g $resourceGroup -l eastus
az acr create -g $resourceGroup -n $acrName --sku Standard
az aks create -g $resourceGroup -n $aksName --attach-acr $acrName --network-plugin azure
```

### <a name="prepare-the-deployment-artifacts"></a>배포 아티팩트 준비

[컨테이너의 Tomcat 빠른 시작 GitHub 리포지토리](https://github.com/Azure/tomcat-container-quickstart)를 복제합니다. 여기에는 추천되는 여러 최적화가 있는 Dockerfile 및 Tomcat 구성 파일이 포함되어 있습니다. 아래 단계에서는 컨테이너 이미지를 빌드하여 AKS에 배포하기 전에 이러한 파일을 수정해야 하는 사항에 대해 간략하게 설명합니다.

#### <a name="open-ports-for-clustering-if-needed"></a>필요한 경우 클러스터링을 위한 포트 열기

AKS에서 [Tomcat 클러스터링](https://tomcat.apache.org/tomcat-9.0-doc/cluster-howto.html)을 사용하려는 경우 필요한 포트 범위가 Dockerfile에 공개되어 있는지 확인합니다. *server.xml*에서 서버 IP 주소를 지정하려면 컨테이너 시작 시 초기화되는 변수의 값을 Pod의 IP 주소로 사용해야 합니다.

또는 복제본에서 사용할 수 있도록 세션 상태를 [대체 위치로 유지](#identify-session-persistence-mechanism)할 수 있습니다.

애플리케이션에서 클러스터링을 사용하는지 확인하려면 *server.xml* 파일에서 `<Host>` 요소 또는 `<Engine>` 요소 내의 `<Cluster>` 요소를 찾습니다.

#### <a name="add-jndi-resources"></a>JNDI 리소스 추가

*server.xml*을 편집하여 마이그레이션 전 단계에서 준비한 리소스(예: 데이터 원본)를 추가합니다.

다음은 그 예입니다.

```xml
<!-- Global JNDI resources
      Documentation at /docs/jndi-resources-howto.html
-->
<GlobalNamingResources>
    <!-- Editable user database that can also be used by
         UserDatabaseRealm to authenticate users
    -->
    <Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml"
               />

    <!-- Migrated datasources here: -->
    <Resource
        name="jdbc/dbconnection"
        type="javax.sql.DataSource"
        url="${postgresdb.connectionString}"
        driverClassName="org.postgresql.Driver"
        username="${postgresdb.username}"
        password="${postgresdb.password}"
    />
    <!-- End of migrated datasources -->
</GlobalNamingResources>
```

[!INCLUDE[Tomcat datasource additional instructions](includes/tomcat-datasource-additional-instructions.md)]

### <a name="build-and-push-the-image"></a>이미지 빌드 및 푸시

AKS에서 사용할 수 있도록 이미지를 빌드하고 ACR(Azure Container Registry)에 업로드하는 가장 간단한 방법은 `az acr build` 명령을 사용하는 것입니다. 이 명령을 사용하기 위해 Docker를 컴퓨터에 설치할 필요가 없습니다. 예를 들어 현재 디렉터리에 위의 Dockerfile과 *petclinic.war* 애플리케이션 패키지가 있는 경우 ACR에서 한 단계만으로 컨테이너 이미지를 빌드할 수 있습니다.

```bash
az acr build -t "${acrName}.azurecr.io/petclinic:{{.Run.ID}}" -r $acrName --build-arg APP_FILE=petclinic.war --build-arg=prod.server.xml .
```

WAR 파일의 이름이 *ROOT.war*인 경우 `--build-arg APP_FILE...` 매개 변수를 생략할 수 있습니다. 서버 XML 파일의 이름이 *server.xml*인 경우 `--build-arg SERVER_XML...` 매개 변수를 생략할 수 있습니다. 두 파일은 모두 *Dockerfile*과 동일한 디렉터리에 있어야 합니다.

또는 Docker CLI를 사용하여 이미지를 로컬로 빌드할 수 있습니다. 이 방법을 사용하면 ACR에 처음 배포하기 전에 이미지를 테스트하고 구체화하는 작업을 간소화할 수 있습니다. 그러나 Docker CLI를 설치하고 Docker 디먼을 실행해야 합니다.

```bash
# Build the image locally
sudo docker build . --build-arg APP_FILE=petclinic.war -t "${acrName}.azurecr.io/petclinic:1"

# Run the image locally
sudo docker run -d -p 8080:8080 "${acrName}.azurecr.io/petclinic:1"

# Your application can now be accessed with a browser at http://localhost:8080.

# Log into ACR
sudo az acr login -n $acrName

# Push the image to ACR
sudo docker push "${acrName}.azurecr.io/petclinic:1"
```

Azure에서 컨테이너 이미지를 빌드하고 저장하는 방법에 대한 자세한 내용은 해당 [Microsoft Learn 과정](/learn/modules/build-and-store-container-images/)을 참조하세요.

### <a name="provision-a-public-ip-address"></a>공용 IP 주소 프로비저닝

내부 또는 가상 네트워크 외부에서 애플리케이션에 액세스할 수 있는 경우 공용 정적 IP 주소가 필요합니다. 이 IP 주소는 클러스터의 노드 리소스 그룹 내에 프로비저닝해야 합니다.

```bash
nodeResourceGroup=$(az aks show -g $resourceGroup -n $aksName --query 'nodeResourceGroup' -o tsv)
publicIp=$(az network public-ip create -g $nodeResourceGroup -n applicationIp --sku Standard --allocation-method Static --query 'publicIp.ipAddress' -o tsv)
echo "Your public IP address is ${publicIp}."
```

### <a name="deploy-to-aks"></a>AKS에 배포

[Kubernetes YAML 파일을 만들고 적용](/azure/aks/kubernetes-walkthrough#run-the-application)합니다. 외부 부하 분산 장치를 만드는 경우(애플리케이션 또는 수신 컨트롤러에 있는지 여부에 관계없이) 이전 섹션에서 프로비저닝된 IP 주소를 `LoadBalancerIP`로 제공해야 합니다.

[표면화된 매개 변수를 환경 변수로](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) 포함합니다. 암호, API 키 및 JDBC 연결 문자열과 같은 비밀은 포함하지 않습니다. 비밀은 [KeyVault FlexVolume 구성](#configure-keyvault-flexvolume) 섹션에서 설명하고 있습니다.

### <a name="configure-persistent-storage"></a>영구 스토리지 구성

애플리케이션에 비휘발성 스토리지가 필요한 경우 하나 이상의 [영구 볼륨](/azure/aks/azure-disks-dynamic-pv)을 구성합니다.

Tomcat 로그 디렉터리( */tomcat_logs*)에 탑재된 Azure Files를 사용하여 영구 볼륨을 만들어 로그를 중앙에서 유지할 수 있습니다. 자세한 내용은 [AKS(Azure Kubernetes Service)에서 Azure Files를 사용하여 영구 볼륨을 동적으로 만들어 사용](/azure/aks/azure-files-dynamic-pv)을 참조하세요.

### <a name="configure-keyvault-flexvolume"></a>KeyVault FlexVolume 구성

[Azure KeyVault를 만들고](/azure/key-vault/quick-create-cli) 필요한 모든 비밀을 채웁니다. 그런 다음, Pod에서 이러한 비밀을 액세스할 수 있도록 [KeyVault FlexVolume](https://github.com/Azure/kubernetes-keyvault-flexvol/blob/master/README.md)을 구성합니다.

인증서를 컨테이너의 로컬 키 저장소로 가져오려면 시작 스크립트([컨테이너의 Tomcat](https://github.com/Azure/tomcat-container-quickstart) GitHub 리포지토리에 있는 *startup.sh*)를 수정해야 합니다.

### <a name="migrate-scheduled-jobs"></a>예약된 작업 마이그레이션

AKS 클러스터에서 예약된 작업을 실행하려면 필요에 따라 [Cron 작업](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/)을 정의합니다.

## <a name="post-migration"></a>마이그레이션 후 작업

이제 애플리케이션이 AKS로 마이그레이션되었으므로 예상대로 작동하는지 확인해야 합니다. 이 작업이 완료되면 애플리케이션을 클라우드 네이티브로 만들 수 있는 몇 가지 추천 사항이 있습니다.

* 수신 컨트롤러 또는 애플리케이션 부하 분산 장치에 할당된 IP 주소에 DNS 이름을 추가하는 것이 좋습니다. 자세한 내용은 [AKS에서 고정 공용 IP 주소를 사용하여 수신 컨트롤러 만들기](/azure/aks/ingress-static-ip)를 참조하세요.

* [애플리케이션에 대한 Helm 차트를 추가](https://helm.sh/docs/topics/charts/)하는 것이 좋습니다. HELM 차트를 사용하면 더 다양한 고객 세트에서 사용하고 사용자 지정할 수 있도록 애플리케이션 배포를 매개 변수화할 수 있습니다.

* DevOps 전략을 설계하고 구현합니다. 개발 속도를 높이는 동시에 안정성을 유지하기 위해 [Azure Pipelines를 사용하여 배포를 자동화하고 테스트](/azure/devops/pipelines/ecosystems/kubernetes/aks-template)하는 것이 좋습니다.

* [클러스터에 대해 Azure 모니터링을 사용](/azure/azure-monitor/insights/container-insights-enable-existing-clusters)하도록 설정하여 컨테이너 로그 수집, 사용률 추적 등을 허용합니다.

* Prometheus를 통해 애플리케이션별 메트릭을 공개하는 것이 좋습니다. Prometheus는 Kubernetes 커뮤니티에서 광범위하게 채택된 오픈 소스 메트릭 프레임워크입니다. 사용자 고유의 Prometheus 서버를 호스팅하는 대신 [Azure Monitor에서 스크랩하는 Prometheus 메트릭](/azure/azure-monitor/insights/container-insights-prometheus-integration)을 구성하여 애플리케이션에서 메트릭 집계를 사용하도록 설정하고 비정상 조건에 대한 자동화된 응답 또는 에스컬레이션을 수행할 수 있습니다.

* 비즈니스 연속성 및 재해 복구 전략을 설계하고 구현합니다. 중요 업무용 애플리케이션의 경우 [다중 지역 배포 아키텍처](/azure/aks/operator-best-practices-multi-region)를 고려하세요.

* [Kubernetes 버전 지원 정책](/azure/aks/supported-kubernetes-versions#kubernetes-version-support-policy)을 검토합니다. 항상 지원되는 버전을 실행하도록 [AKS 클러스터를 계속 업데이트](/azure/aks/upgrade-cluster)해야 합니다.

* 클러스터 관리 및 애플리케이션 개발을 담당하는 모든 팀 멤버가 관련 [AKS 모범 사례](/azure/aks/best-practices)를 검토하도록 합니다.

* *logging.properties* 파일의 항목을 평가합니다. 성능을 향상시키기 위해 일부 로깅 출력을 제거하거나 줄이는 것이 좋습니다.

* 성능을 더 최적화하려면 [코드 캐시 크기를 모니터링](https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm)하고 `-XX:InitialCodeCacheSize` 및 `-XX:ReservedCodeCacheSize` 매개 변수를 Dockerfile의 `JAVA_OPTS` 변수에 추가하는 것이 좋습니다.
