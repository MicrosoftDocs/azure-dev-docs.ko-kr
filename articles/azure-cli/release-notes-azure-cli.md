---
title: Azure CLI 릴리스 정보
description: Azure CLI 최신 업데이트 알아보기
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 04/21/2020
ms.topic: article
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 4dcc1bcfb6e42089e221a1ebd7eb5abc0d37a4e3
ms.sourcegitcommit: 324da872a9dfd4c55b34739824fc6a6598f2ae12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89374554"
---
# <a name="azure-cli-release-notes"></a>Azure CLI 릴리스 정보

## <a name="april-21-2020"></a>2020년 4월 21일

버전 2.4.0

### <a name="acr"></a>ACR

* `az acr run --cmd`: 작업 디렉터리 재정의 사용 안 함
* 전용 데이터 엔드포인트 지원

### <a name="aks"></a>AKS

* `az aks list -o table`은 프라이빗 클러스터의 fqdn인 privateFqdn을 표시합니다.
* --uptime-sla 추가
* containerservice 패키지 업데이트
* 노드 공용 IP 지원 추가
* help 명령의 오타 수정

### <a name="appconfig"></a>AppConfig

* kv list 및 export 명령에 대한 주요 자격 증명 모음 참조 확인
* 목록 키 값에 대한 버그 수정

### <a name="appservice"></a>AppService

* `az functionapp create`: dotnet linux 함수 앱에 linuxFxVersion이 설정되는 방식이 변경되었습니다. 따라서 dotnet linux 소비 앱이 생성되지 않게 하는 버그가 수정됩니다.
* [호환성이 손상되는 변경] `az webapp create`: az webapp create를 사용하여 기존 AppSettings를 유지하도록 수정
* [호환성이 손상되는 변경] `az webapp up`: -g 플래그를 사용하는 경우 az webapp up 명령에 대한 RG를 만들도록 수정
* [호환성이 손상되는 변경] `az webapp config`: az webapp config connection-string list를 사용하여 비 JSON 출력에 대한 값을 표시하도록 수정

### <a name="arm"></a>ARM

* `az deployment create/validate`: ARM 템플릿의 매개 변수 누락 프롬프트 건너뛰기를 지원하는 `--no-prompt` 매개 변수 추가
* `az deployment group/mg/sub/tenant validate`: 배포 매개 변수 파일에서 주석 지원
* `az deployment`: `--handle-extended-json-format` 매개 변수의 `is_preview` 제거
* `az deployment group/mg/sub/tenant cancel`: ARM 템플릿에 대한 배포 취소 지원
* `az deployment group/mg/sub/tenant validate`: 배포 확인 실패 시 오류 메시지 개선
* `az deployment-scripts`: DeploymentScripts에 대한 새 명령 추가
* `az resource tag`: 리소스에 대한 태그를 증분 방식으로 추가하도록 지원하는 `--is-incremental` 매개 변수 추가

### <a name="aro"></a>ARO

* `az aro`:  Azure RedHat OpenShift V4 ARO 명령 모듈 추가

### <a name="batch"></a>Batch

* Batch API 업데이트

### <a name="compute"></a>컴퓨팅

* `az sig image-version create`: 스토리지 계정 유형 Premium_LRS 추가
* `az vmss update`: 종료 알림 업데이트 문제 해결
* `az vm/vmss create`: 특수 이미지 버전에 대한 지원 추가
* SIG API 버전 2019-12-01
* `az sig image-version create`: --target-region-encryption 추가
* 테스트를 순차적으로 실행할 때 키 자격 증명 모음 이름이 글로벌 메모리 내 캐시에서 중복되어 발생하는 오류 해결

### <a name="cosmosdb"></a>CosmosDB

* `az cosmosdb private-link-resource/private-endpoint-connection` 지원

### <a name="iot-central"></a>IoT Central

* `az iotcentral` 사용 중단
* `az iot central` 명령 모듈 추가

### <a name="monitor"></a>모니터

* 모니터에 대한 프라이빗 링크 시나리오 지원
* test_monitor_general_operations.py에서 잘못된 모의 방식 수정

### <a name="network"></a>네트워크

* sku for public ip update 명령 사용 중단
* `az network private-endpoint`: private dns zone group 지원
* vnet/subnet 매개 변수에 대한 로컬 컨텍스트 기능 사용
* test_nw_flow_log_delete에서 잘못된 사용 예제 수정

### <a name="packaging"></a>패키징

* Ubuntu/Disco 패키지에 대한 드롭 지원

### <a name="rbac"></a>RBAC

* `az ad app create/update`: --optional-claims를 매개 변수로 지원

### <a name="rdbms"></a>RDBMS

* PostgreSQL 및 MySQL에 대한 Azure Active Directory 관리자 명령 추가

### <a name="service-fabric"></a>Service Fabric

* #12891 해결: `az sf application update --application-parameters`는 요청에 없는 이전 매개 변수를 제거합니다.
* #12470 az sf create cluster 해결, 업데이트 내구성 및 안정성 버그 해결, 노드 유형 이름이 지정된 경우 코드를 통해 vmss를 올바르게 검색

### <a name="sql"></a>SQL

* `az sql mi op list`, `az sql mi op get`, `az sql mi op cancel` 추가
* `az sql midb`: 장기 보존 정책 업데이트/표시, 장기 보존 백업 표시/삭제, 장기 보존 백업 복원

### <a name="storage"></a>스토리지

* azure-mgmt-storage를 9.0.0으로 업그레이드
* `az storage logging off`: 스토리지 계정에 대한 로깅 해제 지원
* `az storage account update`: CMK에 키 자동 회전 사용
* `az storage account encryption-scope create/update/list/show`: 암호화 범위 사용자 지정에 대한 지원 추가
* `az storage container create`: 컨테이너 수준의 암호화 범위를 설정하는 --default-encryption-scope 및 --deny-encryption-scope-override 추가

### <a name="survey"></a>설문 조사

* 설문 조사 링크를 해제하는 스위치 추가

## <a name="april-01-2020"></a>2020년 4월 1일

버전 2.3.1

### <a name="acr"></a>ACR

* Linux용 azure-mgmt-containerregistry의 잘못된 버전 수정

### <a name="profile"></a>프로필

* az login: `latest` 이외의 클라우드 프로필로 로그인 실패 수정

## <a name="march-31-2020"></a>2020년 3월 31일

버전 2.3.0

### <a name="acr"></a>ACR

* 'az acr task update': Null 포인터 예외
* `az acr import`: --source 및 --registry 사용법을 명확히 하기 위해 도움말 및 오류 메시지 수정
* 'registry_name' 인수에 대한 유효성 검사기 추가
* `az acr login`: '--expose-token'에서 미리 보기 플래그 제거
* [호환성이 손상되는 변경] 'az acr task create/update' 분기 매개 변수가 제거됨
* 'az acr task update' 고객은 이제 컨텍스트, git-token 또는 트리거를 개별적으로 업데이트할 수 있게 됨
* 'az acr agentpool': 새로운 기능

### <a name="aks"></a>AKS

* --api-server-authorized-ip-ranges를 업데이트할 때 apiServerAccessProfile 수정
* aks 업데이트: 업데이트 시 입력 값을 사용하여 아웃바운드 IP 재정의
* MSI 클러스터용 SPN을 생성하지 않고 MSI 클러스터에 acr 첨부 지원

### <a name="ams"></a>AMS

* #12469 수정: 'ask' 매개 변수의 문제로 인해 Fairplay content-key-policy 추가 실패

### <a name="appconfig"></a>AppConfig

* kv 내보내기에 --skip-keyvault 추가

### <a name="appservice"></a>AppService

* #12509 수정: 기본적으로 az webapp 태그 제거
* az functionapp create: 사용자가 dotnet에 --runtime-version을 지정할 때 --runtime-version 도움말 메뉴가 업데이트되고 경고가 추가됨
* az functionapp create: Windows 함수 앱에 대해 javaVersion을 설정하는 방식이 업데이트됨

### <a name="arm"></a>ARM

* az deployment create/validate: 기본적으로 --handle-extended-json-format 사용
* az lock create: 도움말 문서에서 하위 리소스를 만드는 예제 추가
* az deployment {group/mg/sub/tenant} list: provisioningState 필터링 지원
* az deployment: 마지막 인수에 있는 주석의 구문 분석 버그 수정

### <a name="backup"></a>Backup

* 여러 파일 복원 기능 추가됨
* OS 디스크만 백업하기 위한 지원 추가됨
* 관리되지 않는 복원을 지정하기 위해 restore-as-unmanaged-disk 매개 변수 추가됨

### <a name="compute"></a>컴퓨팅

* az vm create: --nsg-rule의 NONE 옵션 추가
* az vmss create/update: vmss 자동 복구 미리 보기 태그 제거
* az vm update: --workspace 지원
* VirtualMachineScaleSetExtension 초기화 코드의 버그 수정
* VMAccessAgent 버전을 2.4로 업그레이드
* az vmss set-orchestration-service-state: vmss 집합 오케스트레이션 서비스 상태 지원
* 디스크 API 버전을 2019-11-01로 업그레이드
* az disk create: add --disk-iops-read-only, --disk-mbps-read-only, --max-shares, --image-reference, --image-reference-lun, --gallery-image-reference, --gallery-image-reference-lun

### <a name="cosmos-db"></a>Cosmos DB

* 사용 중단 리디렉션 시 누락된 --type 옵션 수정

### <a name="docker"></a>Docker

* Alpine 3.11 및 Python 3.6.10으로 업데이트

### <a name="extension"></a>내선 번호

* 패키지를 통해 시스템 경로에 확장을 로드할 수 있도록 허용

### <a name="hdinsight"></a>HDInsight

* (az hdinsight create:) 지원 고객은 매개 변수 `--minimal-tls-version`을 사용하여 지원되는 최소 tls 버전을 지정합니다. 허용되는 값은 1.0,1.1,1.2입니다.

### <a name="iot"></a>IoT

* codeowner 추가
* az iot hub create: 기본 sku를 F1에서 S1로 변경
* iot hub: 2019-03-01-hybrid의 프로필에서 IotHub 지원

### <a name="iotcentral"></a>IoTCentral

* 오류 세부 정보 업데이트, 기본 애플리케이션 템플릿 및 프롬프트 메시지 업데이트

### <a name="keyvault"></a>KeyVault

* 인증서 백업/복원 지원
* keyvault create/update: --retention-days 지원
* 나열하는 동안 더 이상 관리 키/비밀을 표시하지 않음
* az keyvault create: 자격 증명 모음을 만드는 동안 네트워크 규칙을 지정하는 데 `--network-acls`, `--network-acls-ips` 및 `--network-acls-vnets` 지원

### <a name="lock"></a>잠금

* az lock delete fix bug: az lock delete가 Microsoft.DocumentDB에서 작동하지 않음

### <a name="monitor"></a>모니터

* az monitor clone: 한 리소스에서 다른 리소스로 메트릭 규칙을 복제하도록 지원
* Fix IcM179210086: Application Insights 메트릭에 대한 사용자 정의 메트릭 경고를 만들 수 없음

### <a name="netappfiles"></a>NetAppFiles

* az volume create: 복제 작업을 추가하여 데이터 보호 볼륨 허용: 승인, 일시 중단, 계속, 상태, 제거

### <a name="network"></a>네트워크

* az network application-gateway waf-policy managed-rule rule-set add: Microsoft_BotManagerRuleSet 지원
* network watcher flow-log show: 잘못된 사용 중단 정보 수정
* 애플리케이션 게이트웨이 수신기에서 호스트 이름 지원
* az network nat gateway: 공용 IP 또는 공용 IP 접두사 없이 빈 리소스 생성 지원
* vpn 게이트웨이 생성 지원
* `az network dns record-set {} add-record`에서 `--if-none-match` 지원

### <a name="packaging"></a>패키징

* Python 3.5 지원 중단

### <a name="profile"></a>프로필

* az login: MFA 오류에 대해 경고 표시

### <a name="rdbms"></a>RDBMS

* PostgreSQL 및 MySQL에 대한 서버 데이터 암호화 키 관리 명령 추가

## <a name="march-10-2020"></a>2020년 3월 10월

2\.2.0 버전

### <a name="acr"></a>ACR

* 수정: `az acr login`에서 잘못된 오류 발생
* 새 명령 `az acr helm install-cli` 추가
* 프라이빗 링크 및 CMK 지원 추가
* 'private-link-resource list' 명령 추가

### <a name="aks"></a>AKS

* Cloud Shell에서 aks browse 수정
* az aks: 모니터링 추가 기능 및 agentpool NoneType 오류 수정
* azure kubernetes 클러스터를 만들 때 노드 풀에 --nodepool-tags 추가
* 클러스터에 nodepool을 추가하거나 업데이트할 때 --tags 추가
* aks create: `--enable-private-cluster` 추가
* azure kubernetes 클러스터를 만들 때 --nodepool-labels 추가
* azure kubernetes 클러스터에 새 nodepool을 추가할 때 --labels 추가
* 대시보드 URL에 missing / 추가
* 관리 ID를 사용하도록 설정하는 aks 클러스터 만들기 지원
* az aks: 네트워크 플러그 인이 "azure"인지 아니면 "kubenet"인지 확인
* az aks: AAD 세션 키 지원 추가
* [호환성이 손상되는 변경] omsagent의 GF 및 BF에 대한 msi 변경 지원(컨테이너 모니터링)(#1)
* az aks use-dev-spaces: Azure Dev Spaces 컨트롤러에서 만든 엔드포인트를 사용자 지정할 수 있도록 use-dev-spaces 명령에 엔드포인트 유형 옵션을 추가

### <a name="appconfig"></a>AppConfig

* keyvault 참조 및 기능을 추가할 수 있도록 "kv set" 사용 차단 해제...

### <a name="appservice"></a>AppService

* az webapp create: --runtime으로 명령을 실행할 때 발생하는 문제 해결
* az functionapp deployment source config-zip: 리소스 그룹 또는 함수 이름이 유효하지 않거나 존재하지 않는 경우 오류 메시지 추가
* functionapp create: `--functions_version` 플래그를 인용하지만 플래그 이름에 `-` 대신 `_`을 잘못 사용하는 `functionapp create`와 함께 표시되는 경고 메시지 수정
* az functionapp create: Linux 함수 앱에 대해 linuxFxVersion과 컨테이너 이미지 이름이 설정되는 방식이 업데이트됨
* az functionapp deployment source config-zip: zip 배포 중 앱 설정 변경 경합 조건으로 인해 발생하는 문제(배포 중 5xx 오류 발생) 해결
* 수정: #5720946: az webapp backup이 이름을 설정하지 못함

### <a name="arm"></a>ARM

* az resource: 리소스 모듈의 예제 향상
* az policy assignment list: 관리 그룹 범위에서 정책 할당 목록 지원
* 리소스 그룹에서 템플릿 배포를 위해 `az deployment group` 및 `az deployment operation group`을 추가합니다. `az group deployment` 및 `az group deployment operation`과 중복됩니다.
* 구독 범위에서 템플릿 배포를 위해 `az deployment sub` 및 `az deployment operation sub`를 추가합니다. `az deployment` 및 `az deployment operation`과 중복됩니다.
* 관리 그룹에서 템플릿 배포를 위해 `az deployment mg` 및 `az deployment operation mg`를 추가합니다.
* 테넌트 범위에서 템플릿 배포를 위해 `az deployment tenant` 및 `az deployment operation tenant`를 추가합니다.
* az policy assignment create: `--location` 매개 변수에 대한 설명 추가
* az group deployment create: 교차 테넌트를 지원하도록 매개 변수 `--aux-tenants` 추가

### <a name="cdn"></a>CDN

* CDN WAF 명령 추가

### <a name="compute"></a>컴퓨팅

* az sig image-version: --data-snapshot-luns 추가
* az ppg show: 근접 배치 그룹에 있는 모든 리소스의 공동 배치 상태를 가져올 수 있도록 --colocation-status 추가
* az vmss create/update: 자동 복구 지원
* [호환성이 손상되는 변경] az image template: 이름을 템플릿에서 빌더로 변경
* az image builder create: --image-template 추가

### <a name="cosmos-db"></a>Cosmos DB

* SQL 저장 프로시저, udf 및 트리거 cmdlet 추가
* az cosmosdb create: 키 자격 증명 모음 암호화 정보 추가를 지원하도록 --key-uri 추가

### <a name="keyvault"></a>KeyVault

* keyvault create: 기본적으로 일시 삭제가 가능하도록 설정

### <a name="monitor"></a>모니터

* az monitor metrics alert create: `--condition`에서 `~` 지원

### <a name="network"></a>네트워크

* az network application-gateway rewrite-rule create: 지원 URL 구성
* az network dns zone import: --zone-name은 앞으로 대/소문자를 구분하지 않습니다.
* az network private-endpoint/private-link-service: 미리 보기 레이블 제거
* az network bastion: bastion 지원
* az network vnet list-available-ips: vnet에서 사용 가능한 IP 목록 지원
* az network watcher flow-log create/list/delete/update: 감시자 흐름 로그를 관리하고 감시자를 명시적으로 식별하도록 --location을 노출하는 새 명령 추가
* az network watcher flow-log configure: 사용되지 않음
* az network watcher flow-log show: ARM 형식의 결과(더 이상 사용되지 않는 이전 형식 출력)를 가져오도록 --location 및 --name 지원

### <a name="policy"></a>정책

* az policy assignment create: 자동으로 생성된 정책 할당 이름이 제한을 초과하는 버그 수정

### <a name="rbac"></a>RBAC

* az ad group show: regex 문제로 처리된 --group 값 수정

### <a name="rdbms"></a>RDBMS

* azure-mgmt-rdbms SDK 버전을 2.0.0으로 범프
* az postgres private-endpoint-connection: postgres 프라이빗 엔드포인트 연결 관리
* az postgres private-link-resource: postgres 프라이빗 링크 리소스 관리
* az mysql private-endpoint-connection: mysql 프라이빗 엔드포인트 연결 관리
* az mysql private-link-resource: mysql 프라이빗 링크 리소스 관리
* az mariadb private-endpoint-connection: mariadb 프라이빗 엔드포인트 연결 관리
* az mariadb private-link-resource: mariadb 프라이빗 링크 리소스 관리
* RDBMS 프라이빗 엔드포인트 테스트 업데이트

### <a name="sql"></a>SQL

* Sql midb Add: list-deleted, show-deleted, update-retention, show-retention
* (sql server create:) 선택적인 public-network-access 'Enable'/'Disable' 플래그를 sql server create에 추가
* (sql server update:) 고객 관련 사항 변경
* MI 및 SQL DB에 대한 minimal_tls_version 속성 추가

### <a name="storage"></a>스토리지

* az storage blob delete-batch: 오작동하는 `--dryrun` 플래그
* az storage account network-rule add(버그 수정): 추가 작업은 idempotent여야 함
* az storage account create/update: 라우팅 기본 설정 추가 지원
* azure-mgmt-storage 버전을 8.0.0으로 업그레이드
* az storage container immutability create: --allow-protected-append-write 매개 변수 추가
* az storage account private-link-resource list: 스토리지 계정에 대한 프라이빗 링크 리소스를 나열하는 지원 추가
* az storage account private-endpoint-connection approve/reject/show/delete: 프라이빗 엔드포인트 연결을 관리하도록 지원
* az storage account blob-service-properties update: --enable-restore-policy 및 --restore-days 추가
* az storage blob restore: Blob 범위를 복원하도록 지원 추가

## <a name="february-18-2020"></a>2020년 2월 18일

버전 2.1.0

### <a name="acr"></a>ACR

* `az acr login`에 새 `--expose-token` 인수 추가
* 잘못된 `az acr task identity show -n Name -r Registry -o table` 출력 수정
* az acr login: docker 명령에서 반환된 오류가 있는 경우 CLIError를 throw함

### <a name="acs"></a>ACS

* aks create/update: `--vnet-subnet-id` 유효성 검사 추가

### <a name="aladdin"></a>추가 기능

* 생성된 예제를 명령의 _help.py로 구문 분석

### <a name="ams"></a>AMS

* az ams는 이제 GA임

### <a name="appconfig"></a>AppConfig

* 지원되지 않는 키/레이블 필터를 제외하도록 도움말 메시지 수정
* 관리 ID 및 기능 플래그를 제외한 대부분의 명령에 대한 preview 태그 제거
* 스토어를 업데이할 때 고객 관리 키 추가

### <a name="appservice"></a>AppService

* az webapp list-runtimes: 목록 런타임 버그 수정
* az webapp|functionapp config ssl create 추가
* v3 함수 앱 및 노드 12에 대한 지원 추가

### <a name="arm"></a>ARM

* az policy assignment create: 잘못된 `--policy` 매개 변수에 대한 오류 메시지 수정
* az group deployment create: 큰 parameters.json 파일을 사용할 때 발생하는 "stat: Windows에 대한 경로가 너무 깁니다." 오류 수정

### <a name="backup"></a>Backup

* OLR에서 항목 수준 복구 흐름 수정
* SQL 및 SAP Databases에 대한 파일 지원으로 복원 추가

### <a name="compute"></a>컴퓨팅

* vm/vmss/availability-set update: ProximityPlacementGroup을 업데이트할 수 있도록 --ppg 추가
* vmss create: --data-disk-iops 및 --data-disk-mbps 추가
* az vm host: `vm host` 및 `vm host group`에 대한 preview 태그 제거
* [호환성이 손상되는 변경] #10728 해결: `az vm create`: VNet이 지정되어 있지만 서브넷이 없는 경우 서브넷을 자동으로 만듦
* vm image list의 견고성 향상

### <a name="eventhub"></a>Eventhub

* 2019-03-01-hybrid 프로필에 대한 Azure Stack 지원

### <a name="keyvault"></a>KeyVault

* az keyvault key create: `--ops` 매개 변수에 새 `import` 값 추가
* az keyvault key list-versions: 키 지정에 `--id` 매개 변수 지원
* 프라이빗 엔드포인트 연결 지원

### <a name="network"></a>네트워크

* azure-mgmt-network 9.0.0으로 승격
* az network private-link-service update/create: --enable-proxy-protocol 지원
* 연결 모니터 V2 기능 추가

### <a name="packaging"></a>패키징

* [호환성이 손상되는 변경] Python 2.7 지원 중단

### <a name="profile"></a>프로필

* 미리 보기: 새 `homeTenantId` 및 `managedByTenants` 특성이 구독 계정에 추가되었습니다. 변경 내용을 적용하려면 `az login`을 다시 실행하세요.
* az login: 구독이 둘 이상의 테넌트에서 나열되지만 기본값이 첫 번째 테넌트인 경우 경고가 표시됩니다. 이 구독을 액세스할 때 특정 테넌트를 선택하려면 `--tenant`를 `az login`에 포함하세요.

### <a name="role"></a>역할

* az role assignment create: 역할을 표시 이름으로 서비스 주체에 할당하면 HTTP 400이 생성되는 오류 수정

### <a name="sql"></a>SQL

* SQL Managed Instance cmdlet인 `az sql mi update`가 tier 및 family의 두 가지 새 매개 변수로 업데이트

### <a name="storage"></a>스토리지

* [호환성이 손상되는 변경] `az storage account create`: 기본 스토리지 계정 종류가 StorageV2로 변경

## <a name="february-04-2020"></a>2020년 2월 4일

버전 2.0.81

### <a name="acs"></a>ACS

* 표준 부하 분산 장치에서 아웃바운드 할당된 포트 및 유휴 시간 제한을 설정하는 지원 추가
* API 버전 2019-11-01 업데이트

### <a name="acr"></a>ACR

* [호환성이 손상되는 변경]  `az acr delete` 메시지가 표시됨
* [호환성이 손상되는 변경] 'az acr task delete' 메시지가 표시됨
* taskrun 관리를 위한 새 명령 그룹 'az acr taskrun show/list/delete' 추가

### <a name="aks"></a>AKS

* 각 클러스터는 격리를 향상하기 위한 별도의 서비스 주체 보유

### <a name="appconfig"></a>AppConfig

* appservice에서 키 자격 증명 모음 참조 가져오기/내보내기 지원
* appconfig에서 appconfig로 모든 레이블 가져오기/내보내기 지원
* 키 및 기능 이름을 가져오기 전에 유효성 검사
* 구성 저장소에 대한 sku 수정 노출
* 관리 ID에 대한 명령 그룹 추가

### <a name="appservice"></a>AppService

* Azure Stack: 2019-03-01-hybrid 프로필 아래에 명령 표시
* functionapp: Linux에서 Java 함수 앱을 만드는 기능 추가

### <a name="arm"></a>ARM

* 이슈 #10246 해결: 전달된 `--ids` 매개 변수가 리소스 그룹 ID인 경우 `az resource tag`가 충돌
* 이슈 #11658 해결: `az group export` 명령은 `--query` 및 `--output` 매개 변수를 지원하지 않음
* 이슈 #10279 해결: 확인이 실패하면 `az group deployment validate`의 종료 코드는 0
* 이슈 #9916 해결: `az resource list` 명령의 태그와 기타 필터 조건 간의 충돌 오류 메시지 개선
* `az group create` 명령의 managedBy 정보를 추가할 수 있도록 새 매개 변수 `--managed-by` 추가

### <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

* Azure Red Hat OpensShift 클러스터에서 Log Analytics 모니터링을 관리할 `monitor` 하위 그룹 추가

### <a name="botservice"></a>BotService

* 이슈 #11697 해결: `az bot create`는 멱등원(idempotent)이 아님
* 라이브 모드에서만 실행되도록 이름-수정 테스트 변경

### <a name="cdn"></a>CDN

* rulesEngine 기능에 대한 지원 추가
* 규칙을 관리하는 새 명령 그룹 'cdn endpoint rule' 추가
* api 버전 2019-04-15를 사용하도록 azure-mgmt-cdn 버전을 4.0.0으로 업데이트

### <a name="deployment-manager"></a>배포 관리자

* 모든 리소스에 대한 나열 작업을 추가합니다.
* 새 단계 유형에 대한 단계 리소스를 향상합니다.
* 0\.2.0 버전을 사용하도록 azure-mgmt-deploymentmanager 패키지를 업데이트합니다.

### <a name="iot"></a>IoT

* 'IoT hub Job' 명령을 사용 중단합니다.

### <a name="iot-central"></a>IoT Central

* 새 sku 이름 ST0, ST1, ST2를 사용하여 앱 만들기/업데이트를 지원합니다.

### <a name="key-vault"></a>Key Vault

* 키를 다운로드하는 새 명령 `az keyvault key download`를 추가합니다.

### <a name="misc"></a>기타

* #6371 해결: Bash에서 파일 이름 및 환경 변수 완료 지원

### <a name="network"></a>네트워크

* #2092 해결: az network dns record-set add/remove: 레코드 세트를 찾을 수 없을 때 표시할 경고를 추가합니다. 나중에 이 자동 생성을 확인하는 추가 인수를 지원할 예정입니다.

### <a name="policy"></a>정책

* 다양한 정책 메타데이터 리소스를 검색하는 새 명령 `az policy metadata` 추가
* `az policy remediation create`: `--resource-discovery-mode` 매개 변수를 사용하여 재구성하기 전에 규정 준수를 다시 평가해야 하는지 여부를 지정

### <a name="profile"></a>프로필

* `az account get-access-token`: 구독을 지정할 필요 없이 테넌트의 토큰을 직접 획득할 수 있도록 `--tenant` 매개 변수 추가

### <a name="rbac"></a>RBAC

* [호환성이 손상되는 변경] #11883 해결: `az role assignment create`: 빈 범위에서 오류 메시지 표시

### <a name="security"></a>보안

* 스토리지 계정의 고급 위협 방지 설정을 살펴보고 관리할 수 있는 새 명령 `az atp show` 및 `az atp update`를 추가합니다.

### <a name="sql"></a>SQL

* `sql dw create`: `--zone-redundant` 및 `--read-replica-count` 매개 변수를 사용 중단합니다. 이러한 매개 변수는 DataWarehouse에는 적용되지 않습니다.
* [호환성이 손상되는 변경] `az sql db create`: "az sql db create --sample-name"에 허용되는 값으로 설명된 "WideWorldImportersStd" 및 "WideWorldImportersFull"을 제거합니다. 이러한 샘플 데이터베이스는 항상 만들기가 실패하는 원인이 됩니다.
* SQL 데이터베이스의 민감도 분류를 관리하는 새 명령 `sql db classification show/list/update/delete` 및 `sql db classification recommendation list/enable/disable`을 추가합니다.
* `az sql db audit-policy`: 빈 감사 작업 및 그룹에 대한 픽스

### <a name="storage"></a>스토리지

* Azure 파일 공유 관리 작업에 Microsoft.Storage 리소스 공급자를 사용하도록 새 명령 그룹 `az storage share-rm`을 추가합니다.
* 이슈 #11415: `az storage blob update`에 대한 권한 오류 해결
* Azcopy 10.3.3을 통합하고 Win32를 지원합니다.
* `az storage copy`: `--include-path`, `--include-pattern`, `--exclude-path` 및 `--exclude-pattern` 매개 변수 추가
* `az storage remove`: `--inlcude` 및 `--exclude` 매개 변수를 `--include-path`, `--include-pattern`, `--exclude-path` 및 `--exclude-pattern` 매개 변수로 변경
* `az storage sync`: `--include-pattern`, `--exclude-path` 및 `--exclude-pattern` 매개 변수 추가

### <a name="servicefabric"></a>ServiceFabric

* 애플리케이션 및 서비스를 관리하는 새 명령이 추가됩니다.

## <a name="january-13-2020"></a>2020년 1월 13일

버전 2.0.80

### <a name="compute"></a>컴퓨팅

* 디스크 업데이트: --disk-encryption-set 및 --encryption-type 추가
* 스냅샷 만들기/업데이트: --disk-encryption-set 및 --encryption-type 추가

### <a name="storage"></a>스토리지

* azure-mgmt-storage 버전을 7.1.0으로 업그레이드
* `az storage account create`: 테이블 및 큐 암호화 서비스를 지원하기 위해 `--encryption-key-type-for-table` 및 `--encryption-key-type-for-queue` 추가

## <a name="january-07-2020"></a>2020년 1월 7일

버전 2.0.79

### <a name="acr"></a>ACR

* [호환성이 손상되는 변경] 'acr build', 'acr task create/update', 'acr run' 및 'acr pack'에서 '--os' 매개 변수 제거. 대신 '--platform'을 사용하세요.

### <a name="appconfig"></a>AppConfig

* 기능 플래그 가져오기/내보내기에 대한 지원 추가
* keyvault 참조를 만들려면 새 명령 'az appconfig kv set-keyvault' 추가
* 기능 플래그를 파일로 내보낼 때 다양한 명명 규칙 지원

### <a name="appservice"></a>AppService

* 이슈 #7154 해결: 작은따옴표 대신 백틱을 사용하도록 명령 <>에 대한 설명서 업데이트
* 이슈 #11287 해결: webapp up: up을 사용하여 만든 앱은 기본적으로 'SSL 사용'으로 설정되어 있어야 합니다.
* 이슈 #11592 해결: html 정적 사이트에 대한 az webapp up 플래그 추가

### <a name="arm"></a>ARM

* `az resource tag` 해결: Recovery Services 자격 증명 모음 태그를 업데이트할 수 없습니다.

### <a name="backup"></a>Backup

* IaasVM 워크로드의 일시 삭제 기능을 지원하기 위해 새 명령 'backup protection undelete' 추가
* backup-properties 명령을 설정하기 위해 새 매개 변수 '--soft-delete-feature-state' 추가
* IaasVM 워크로드에 대한 디스크 제외 지원 추가

### <a name="compute"></a>컴퓨팅

* Azure Stack 프로필에서 `vm create` 오류 해결.
* vm monitor metrics tail/list-definitions: vm에 쿼리 메트릭 및 목록 정의 지원
* az vm의 새로운 다시 적용 명령 작업 추가

### <a name="hdinsight"></a>HDInsight

* Kafka Rest 프록시로 Kafka 클러스터 생성 지원
* azure-mgmt-hdinsight를 1.3.0으로 업그레이드

### <a name="misc"></a>기타

* 기본적으로 JSON 형식 또는 --output으로 구성된 형식으로 Azure CLI 모듈 및 확장 버전을 표시하기 위해 미리 보기 명령 `az version show` 추가

### <a name="event-hubs"></a>Event Hubs

* [호환성이 손상되는 변경] 'az eventhubs eventhub update' 및 'az eventhubs eventhub create' 명령에서 'ReceiveDisabled' 상태 옵션 제거. 이 옵션은 이벤트 허브 항목에 적합하지 않습니다.

### <a name="service-bus"></a>Service Bus

* [호환성이 손상되는 변경] 'az servicebus topic create', 'az servicebus topic update', 'az servicebus queue create' 및 'az servicebus queue update' 명령에서 'ReceiveDisabled' 상태 옵션 제거. 이 옵션은 Service Bus 항목 및 큐에 적합하지 않습니다.

### <a name="rbac"></a>RBAC

* #11712 해결: 애플리케이션 또는 서비스 주체가 없을 때 `az ad app/sp show`가 종료 코드 3을 반환하지 않습니다.

### <a name="storage"></a>스토리지

* `az storage account create`: --enable-hierarchical-namespace 매개 변수에 대한 미리 보기 플래그 제거
* api 버전 2019-06-01을 사용하도록 azure-mgmt-storage 버전이 7.0.0으로 업데이트됨
* 스토리지 계정 blob-service-properties에 대한 보존 정책 삭제 관리를 지원하도록 새 매개 변수 `--enable-delete-retention` 및 `--delete-retention-days` 추가

## <a name="december-17-2019"></a>2019년 12월 17일

2.0.78

### <a name="acr"></a>ACR

* acr task run에 로컬 컨텍스트 지원 추가

### <a name="acs"></a>ACS

* [호환성이 손상되는 변경]az openshift create: `--workspace-resource-id`에서 `--workspace-id`로 이름이 변경됨.

### <a name="ams"></a>AMS

* 리소스를 찾을 수 없을 때 3을 반환하도록 show 명령 업데이트

### <a name="appconfig"></a>AppConfig

* 요청 url에 api-version을 추가할 때 버그 해결. 기존 해결책은 페이지 매김과 함께 작동하지 않습니다.
* 백 엔드 서비스에서 세계화를 위한 유니코드를 지원함에 따라 영어 이외의 언어를 표시하는 지원 기능이 추가되었습니다.

### <a name="appservice"></a>AppService

* 이슈 #11217 해결: webapp: az webapp config ssl upload가 슬롯 매개 변수를 지원합니다.
* 이슈 #10965 해결: 오류: 이름을 비워 둘 수 없습니다. ip_address 및 subnet으로 제거 허용
* Key Vault `az webapp config ssl import`에서 인증서 가져오기에 대한 지원 추가

### <a name="arm"></a>ARM

* 6\.0.0을 사용하도록 azure-mgmt-resource 패키지가 업데이트됨
* 새 매개 변수 `--aux-subs`를 추가하여 `az group deployment create` 명령에 대한 교차 테넌트 지원
* 정책 세트 정의에 대한 메타데이터 정보 추가를 지원하기 위해 새 매개 변수 `--metadata` 추가

### <a name="backup"></a>Backup

* SQL 및 SAP Hana 워크로드에 대한 Backup 지원 추가

### <a name="botservice"></a>BotService

* [호환성이 손상되는 변경] 미리 보기 명령 'az bot create'에서 '--version' 플래그 제거. v4 SDK 봇만 지원됩니다.
* 'az bot create'에 대한 이름 가용성 검사 추가
* 'az bot update'를 통해 봇의 아이콘 URL 업데이트에 대한 지원 추가
* 'az bot directline update'를 통해 Direct Line 채널을 업데이트하는 기능에 대한 지원 추가
* 'az bot directline create'에 '--enable-enhanced-auth' 플래그 지원 추가
* 'az bot authsetting' 명령 그룹은 미리 보기 상태가 아니라 GA입니다.
* 'az bot'의 'create', 'prepare-deploy', 'show', 'delete', 'update' 명령은 미리 보기 상태가 아니라 GA입니다.
* '--proj-file-path' 값을 소문자로 변경(예: "Test.csproj"를 "test.csproj"로)하는 'az bot prepare-deploy' 이슈 해결

### <a name="compute"></a>컴퓨팅

* vmss create/update: VMSS를 축소할 때 어떤 가상 머신을 제거할지 결정하는 --scale-in-policy 추가
* vm/vmss update: --priority 추가
* vm/vmss update: --max-price 추가
* disk-encryption-set 명령 그룹(create, show, update, delete, list) 추가
* disk create: --encryption-type 및 --disk-encryption-set 추가
* vm/vmss create: --os-disk-encryption-set 및 --data-disk-encryption-sets 추가

### <a name="core"></a>핵심

* Python 3.4에 대한 지원 제거
* 여러 명령에 HaTS 설문 조사 연결

### <a name="dls"></a>DLS

* ADLS sdk 버전이 업데이트되었습니다(0.0.48).

### <a name="install"></a>설치

* 설치 스크립트 지원(python 3.8)

### <a name="iot"></a>IOT

* [호환성이 손상되는 변경] manual-failover에서 --failover-region 매개 변수 제거. 이제 할당된, 지리적으로 연결된 보조 지역으로 장애 조치(failover)됩니다.

### <a name="key-vault"></a>Key Vault

* #8095 해결: `az keyvault storage remove`: 도움말 메시지 개선
* #8921 해결: `az keyvault key/secret/certificate list/list-deleted/list-versions`: 매개 변수 `--maxresults`의 유효성 검사 버그 해결
* #10512 해결: `az keyvault set-policy`: `--object-id`, `--spn` 또는 `--upn`이 지정된 경우 오류 메시지 개선
* #10846 해결: `az keyvault secret show-deleted`: `--id`가 지정된 경우 `--name/-n`이 필요 없습니다.
* #11084 해결: `az keyvault secret download`: 매개 변수 `--encoding`의 도움말 메시지 개선

### <a name="network"></a>네트워크

* az network application-gateway probe: 생성 및 업데이트 시 백엔드 서버를 검색할 포트를 지정하는 --port 옵션에 대한 지원 추가
* az network application-gateway url-path-map create/update: `--waf-policy`에 대한 버그 해결
* az network application-gateway: `--rewrite-rule-set` 지원 추가
* az network list-service-aliases: 서비스 엔드포인트 정책에 사용할 수 있는 지원 목록 서비스 별칭 추가
* az network dns zone import: 레코드 이름에 .@ 지원 추가

### <a name="packaging"></a>패키징

* pip 설치를 위한 백 에지 빌드 추가
* Ubuntu eoan 패키지 추가

### <a name="policy"></a>정책

* 정책 API 버전 2019-09-01에 대한 지원이 추가되었습니다.
* az policy set-definition: 정책 세트 정의 내에서 `--definition-groups` 매개 변수를 사용한 그룹화 지원 추가

### <a name="redis"></a>Redis

* `az redis create` 명령에 `--replicas-per-master` 미리 보기 매개 변수 추가
* azure-mgmt-redis가 6.0.0에서 7.0.0rc1로 업데이트됨

### <a name="servicefabric"></a>ServiceFabric

* #10963을 포함한 노드 유형 추가 논리 이슈 해결: 내구성 수준이 Gold인 새 노드 유형을 추가할 때마다 CLI 오류가 발생합니다.
* 생성 템플릿에서 ServiceFabricNodeVmExt 버전이 1.1로 업데이트됨

### <a name="sql"></a>SQL

* 읽기 확장 관리를 지원하도록 sql db create 및 update 명령에 "--read-scale" 및 "--read-replicas" 매개 변수 추가

### <a name="storage"></a>스토리지

* 스토리지 계정 만들기 및 업데이트 명령의 GA 릴리스 대용량 파일 공유 속성
* GA 릴리스 사용자 위임 SAS 토큰 지원
* 스토리지 계정의 blob service 속성을 관리하기 위해 새 명령 `az storage account blob-service-properties show` 및 `az storage account blob-service-properties update --enable-change-feed` 추가
* [예정된 호환성이 손상되는 변경] `az storage copy`: `*` 문자는 더 이상 URL에서 와일드카드로 지원되지 않지만 `*` 와일드카드 지원과 함께 새 매개 변수 --include-pattern 및 --exclude-pattern이 추가될 예정입니다.
* 이슈 #11043 해결: `az storage remove` 명령에서 전체 컨테이너/공유를 제거하는 지원 기능 추가

## <a name="november-26-2019"></a>2019년 11월 26일

버전 2.0.77

### <a name="acr"></a>ACR

* acr task create/update의 `--branch` 매개 변수가 더 이상 사용되지 않음

### <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

* `--workspace-resource-id` 플래그가 추가되어 모니터링으로 Azure Red Hat Openshift 클러스터 생성 가능
* 모니터링으로 Azure Red Hat OpenShift 클러스터를 만드는 `monitor_profile`이 추가됨

### <a name="aks"></a>AKS

* "az aks rotate-certs"를 사용한 지원 클러스터 인증서 순환 작업이 추가됨

### <a name="appconfig"></a>AppConfig

* ":"을 `as az appconfig kv import` 구분 기호로 사용하는 지원이 추가됨
* Null 레이블을 포함하여 여러 레이블이 있는 키 값을 나열하는 문제가 해결되었습니다. 
* 관리 평면 sdk, azure-mgmt-appconfiguration이 버전 0.3.0으로 업데이트되었습니다. 

### <a name="appservice"></a>AppService

* #11100 문제가 해결됨: 서비스 계획을 만들 때 az webapp up의 AttributeError
* az webapp up: 지원되는 언어를 위해 사이트를 만들거나 배포하도록 적용하면 기본값이 사용되지 않습니다.
* App Service Environment에 대한 지원 추가t: az appservice ase show | list | list-addresses | list-plans | create | update | delete

### <a name="backup"></a>Backup

* az backup policy list-associated-items 관련 문제가 해결되었습니다. 선택적 BackupManagementType 매개 변수가 추가되었습니다.

### <a name="compute"></a>컴퓨팅

* 컴퓨팅, 디스크, 스냅샷의 API 버전이 2019-07-01로 업그레이드되었습니다.
* vmss create: --orchestration-mode 개선
* sig image-definition create: --os-state가 추가되어 이 이미지로 생성된 가상 머신이 'Generalized'인지 아니면 'Specialized'인지 지정 가능
* sig image-definition create: --hyper-v-generation이 추가되어 하이퍼바이저 세대 지정 가능
* sig image-version create: 지원 --os-snapshot 및 --data-snapshots 추가됨
* image create: --data-disk-caching이 추가되어 데이터 디스크의 캐싱 설정을 지정할 수 있음
* Python Compute SDK를 10.0.0으로 업그레이드
* vm/vmss create: 'Priority' 열거형 속성에 'Spot' 추가
* [호환성이 손상되는 변경] Swagger 및 Powershell cmdlet과 일치하도록 VM 및 VMSS에 대한 '--max-billing' 매개 변수의 이름이 '--max-price'로 변경됨
* vm monitor log show: 연결된 로그 분석 작업 영역을 통한 로그 쿼리에 대한 지원이 추가되었습니다.

### <a name="iot"></a>IOT

* #2531 수정됨: 허브 업데이트에 대한 편의 인수가 추가되었습니다.
* #8323 수정됨: 스토리지 사용자 지정 엔드포인트를 만드는 누락된 매개 변수가 추가되었습니다.
* 재발 버그 수정: 기본 스토리지 엔드포인트를 재정의하는 변경 사항을 되돌렸습니다.

### <a name="key-vault"></a>Key Vault

* #11121 수정됨: `az keyvault certificate list`를 사용하는 경우 `--include-pending`을 전달할 때 이제 `true` 또는 `false` 값이 필요하지 않음

### <a name="netappfiles"></a>NetAppFiles

* azure-mgmt-netapp이 0.7.0으로 업그레이드되고, 여기에는 향후 복제 작업과 연결된 몇 가지 추가 볼륨 속성이 포함됨

### <a name="network"></a>네트워크

* application-gateway waf-config: 사용되지 않음
* application-gateway waf-policy: 관리형 규칙 집합과 제외 규칙을 관리하는 하위 그룹 managed-rules가 추가됨
* application-gateway waf-policy: waf-policy의 글로벌 구성을 관리하는 하위 그룹 policy-setting이 추가됨
* [호환성이 손상되는 변경] application-gateway waf-policy: 하위 그룹 규칙의 이름이 custom-rule로 변경됨
* application-gateway http-listener: 생성 시 --firewall-policy가 추가됨
* application-gateway url-path-map 규칙: 생성 시 --firewall-policy가 추가됨

### <a name="packaging"></a>패키징

* az wrapper가 Python으로 다시 작성됨
* Python 3.8에 대한 지원이 추가됨
* RPM 패키지의 경우 Python 3으로 변경됨

### <a name="profile"></a>프로필

* Microsoft 계정으로 `az login -u {} -p {}` 실행 시 오류가 수정됨
* 자체 서명된 루트 인증서로 프록시 뒤에서 `az login` 실행 시 `SSLError`가 수정됨
* #10578 수정됨: Windows 또는 WSL에서 동시에 둘 이상의 인스턴스가 시작되면 `az login`이 중단됨
* #11059 수정됨: 테넌트에 구독이 있는 경우`az login --allow-no-subscriptions`가 실패함
* #11238 수정됨: 구독 이름을 바꾼 후 MSI로 로그인하면 동일한 구독이 두 번 나타남

### <a name="rbac"></a>RBAC

* #10996 수정됨: `--password`가 지정되지 않은 경우 `az ad user update`의 `--force-change-password-next-login`에 대한 오류가 수정됨

### <a name="redis"></a>Redis

* #2902 수정됨: 기본 SKU 캐시를 업데이트하는 동안 메모리 구성을 설정하지 마십시오

### <a name="reservations"></a>예약

* SDK 버전을 0.6.0으로 업그레이드
* Get-Gatalogs를 호출한 후 billingplan 세부 정보가 추가됨
* 예약 가격을 계산하는 새 명령 `az reservations reservation-order calculate`가 추가됨
* 새 예약을 구입하는 `az reservations reservation-order purchase`가 추가됨

### <a name="rest"></a>REST
* `az rest`가 GA로 변경됨

### <a name="sql"></a>SQL

* azure-mgmt-sql이 버전 0.15.0으로 업그레이드됨

### <a name="storage"></a>스토리지

* storage account create: Blob service에서 파일 시스템 의미 체계를 지원하는 --enable-hierarchical-namespace가 추가됨
* 오류 메시지에서 관련되지 않은 예외가 제거됨
* 잘못된 오류 메시지 "이 작업을 수행하는 데 필요한 권한이 없습니다." 관련 문제가 해결됨 네트워크 규칙 또는 AuthenticationFailed로 차단된 경우

## <a name="november-4-2019"></a>2019년 11월 4일

버전 2.0.76

### <a name="acr"></a>ACR

* `az acr pack build` 명령에 `--pack-image-tag` 미리 보기 매개 변수를 추가했습니다.
* 레지스트리 생성에 대한 감사를 가능하게 하는 지원 추가
* 리포지토리 범위 RBAC에 대한 지원 추가

### <a name="aks"></a>AKS

* 노드 풀에 대한 클러스터 자동 크기 조정기를 사용할 수 있도록 `--enable-cluster-autoscaler`, `--min-count` 및 `--max-count`를 `az aks create` 명령에 추가했습니다.
* 위의 플래그뿐만 아니라 클러스터 자동 크기 조정기를 업데이트할 수 있도록 `--update-cluster-autoscaler` 및 `--disable-cluster-autoscaler`도 `az aks update` 명령에 추가했습니다.

### <a name="appconfig"></a>AppConfig

* App Configuration에 저장된 기능 플래그를 관리하는 appconfig 기능 명령 그룹을 추가했습니다.
* appconfig kv export to file 명령에 대한 사소한 버그를 수정했습니다. 내보내는 동안 대상 파일 콘텐츠 읽기를 중지합니다.

### <a name="appservice"></a>AppService

* `az appservice plan create`: appservice plan create에서 'persitescaling'을 설정하는 지원을 추가했습니다.
* webapp config ssl bind 작업이 리소스에서 기존 태그를 제거하는 문제를 수정했습니다.
* 함수 앱을 배포하는 동안 원격 빌드 작업을 지원하기 위해 `az functionapp deployment source config-zip`에 대해 `--build-remote` 플래그를 추가했습니다.
* Windows의 경우 함수 앱의 기본 노드 버전을 ~10으로 변경했습니다.
* `az functionapp create`에 `--runtime-version` 속성 추가됨

### <a name="arm"></a>ARM

* `az deployment/group deployment validate`: 배포 시 json 템플릿에서 여러 줄과 주석을 지원하기 위해 `--handle-extended-json-format` 매개 변수를 추가했습니다.
* azure-mgmt-resource가 2019-07-01로 향상됨

### <a name="backup"></a>Backup

* AzureFiles 백업 지원이 추가됨

### <a name="compute"></a>컴퓨팅

* `az vm create`: 가속화된 네트워킹 및 기존 NIC를 함께 지정하는 경우 경고를 추가했습니다.
* `az vm create`: 가상 머신을 할당해야 하는 기존 가상 머신 확장 집합을 지정하는 `--vmss`를 추가했습니다.
* `az vm/vmss create`: 제한된 네트워크 환경에서 액세스할 수 있도록 이미지 별칭 파일의 로컬 복사본을 추가했습니다.
* `az vmss create`: 확장 집합에서 가상 머신을 관리하는 방법을 지정하는 `--orchestration-mode`를 추가했습니다.
* `az vm/vmss update`: Ultra SSD 설정 업데이트를 허용하는 `--ultra-ssd-enabled`를 추가했습니다.
* [호환성이 손상되는 변경] `az vm extension set`: 사용자가 `--ids`를 사용하여 VM에서 확장을 설정할 수 없는 버그를 수정했습니다.
* Azure Marketplace 이미지 용어를 관리하는 `az vm image terms accept/cancel/show` 명령을 새로 추가했습니다.
* VMAccessForLinux가 버전 1.5로 업데이트됨

### <a name="cosmosdb"></a>CosmosDB

* [호환성이 손상되는 변경] `az sql container create`: `--partition-key-path`가 필수 매개 변수로 변경됨
* [호환성이 손상되는 변경] `az gremlin graph create`: `--partition-key-path`가 필수 매개 변수로 변경됨
* `az sql container create`: `--unique-key-policy` 및 `--conflict-resolution-policy`가 추가됨
* `az sql container create/update`: `--idx` 기본 스키마가 업데이트됨
* `gremlin graph create`: `--conflict-resolution-policy`가 추가됨
* `gremlin graph create/update`: `--idx` 기본 스키마가 업데이트됨
* 도움말 메시지 내 오류 해결
* 데이터베이스: 사용 중단 정보가 추가됨
* 컬렉션: 사용 중단 정보가 추가됨

### <a name="iot"></a>IoT

* 새 라우팅 원본 유형이 추가됨: DigitalTwinChangeEvents
* `az iot hub create`에서 누락된 기능이 수정됨

### <a name="key-vault"></a>Key Vault

* 인증서 파일이 없을 때 발생하는 예기치 않은 오류가 수정됨
* `az keyvault recover/purge`가 작동하지 않는 문제 수정됨

### <a name="netappfiles"></a>NetAppFiles

* API 버전 2019-07-01을 사용 하도록 azure-mgmt-netapp을 0.6.0으로 업그레이드했습니다. 이 새로운 API 버전에는 다음이 포함됩니다.

    - 볼륨 만들기 `--protocol-types`에서 이제는 "NFSv4"가 아닌 "NFSv4.1"이 사용됩니다.
    - 볼륨 내보내기 정책 속성의 이름은 이제 'nfsv4'가 아닌 'nfsv41'입니다.
    - 볼륨 `--creation-token`의 이름이 `--file-path`로 바뀌었습니다.
    - 스냅샷 생성 날짜가 이제 'created'라는 이름으로만 지정됩니다.

### <a name="network"></a>네트워크

* `az network private-dns link vnet create/update`: 교차 테넌트 가상 네트워킹 연결을 지원합니다.
* [호환성이 손상되는 변경] `az network vnet subnet list`: `--resource-group` 및 `--vnet-name`이 이제 필수 항목으로 변경되었습니다.
* `az network public-ip prefix create`: 생성 시 IP 주소 버전(IPv4, IPv6)을 지정하는 지원이 추가됨
* azure-mgmt-network를 7.0.0으로 향상시키고 api-version을 2019-09-01로 증가함
* `az network vrouter`: 새 서비스 가상 라우터 및 가상 라우터 피어링에 대한 지원이 추가됨
* `az network express-route gateway connection`: `--internet-security`에 대한 지원이 추가됨

### <a name="profile"></a>프로필

* `az account get-access-token --resource-type ms-graph`가 작동하지 않는 문제 수정됨
* `az login`에서 경고 제거됨

### <a name="rbac"></a>RBAC

* `az ad app update --id {} --display-name {}`이 작동하지 않는 문제 수정됨

### <a name="servicefabric"></a>ServiceFabric

* `az sf cluster create`: service fabric linux 및 windows template.json compute vmss를 표준에서 관리 디스크로 수정하여 문제 수정됨

### <a name="sql"></a>SQL

* 새 SQL Database 제품에 대한 CRUD 작업을 지원하기 위해 `--compute-model`, `--auto-pause-delay` 및 `--min-capacity` 매개 변수를 추가했습니다. 서버리스 컴퓨팅 모델.

### <a name="storage"></a>스토리지

* `az storage account create/update`: --enable-files-adds 매개 변수 및 Azure Active Directory 속성 인수 그룹이 추가되어 Azure Files Active Directory 도메인 서비스 인증을 지원합니다.
* 스토리지 계정의 Kerberos 키 나열 또는 다시 생성을 지원하도록 `az storage account keys list/renew`를 확장했습니다.

## <a name="october-15-2019"></a>2019년 10월 15일

버전 2.0.75

### <a name="aks"></a>AKS

* Kubernetes 버전에서 지원되는 경우 `--load-balancer-sku` 기본값이 `standard`로 변경됨
* Kubernetes 버전에서 지원되는 경우 `--vm-set-type` 기본값이 `virtualmachinescalesets`로 변경됨

### <a name="ams"></a>AMS

* [호환성이 손상되는 변경]`job start`의 이름이 `job create`로 변경됨
* [호환성이 손상되는 변경] UTF8 대신 32자 16진수 문자열을 사용하도록 `content-key-policy create`의 `--ask` 매개 변수가 변경됨

### <a name="appservice"></a>AppService

* `webapp config access-restriction show|set|add|remove` 명령이 추가됨
* `webapp up`에 더 나은 오류 처리 기능이 추가됨
* `Isolated` SKU에 대한 지원이 `appservice plan update`에 추가됨

### <a name="arm"></a>ARM

* json 템플릿에서 여러 줄과 주석을 지원하기 위해 `--handle-extended-json-format` 매개 변수가 `deployment create`에 추가됨

### <a name="compute"></a>컴퓨팅

* `--enable-agent` 매개 변수가 `vm create`에 추가됨
* 영역 사용 시 표준 공용 IP SKU를 자동으로 사용하도록 `vm create`이 변경됨
* VM에 대해 유효한 컴퓨터 이름을 자동으로 만들도록 `vm create`이 변경됨(제공되지 않은 경우)
* VMSS에서 가상 머신의 사용자 지정 컴퓨터 이름 접두사를 지원하기 위해 `vmss create`에 `--computer-name-prefix` 매개 변수가 추가됨
* 로그 분석 작업 영역을 자동으로 사용하도록 `vm create`에 `--workspace` 매개 변수가 추가됨
* 갤러리 API 버전이 2019-07-01로 업데이트됨

### <a name="core"></a>핵심

* 일반 업데이트 명령에 `--set` 매개 변수에 대한 구문 검사가 추가됨

### <a name="iot"></a>IoT

* `iot hub show`에서 "리소스를 찾을 수 없음" 오류가 잘못 발생하는 문제가 해결됨

### <a name="monitor"></a>모니터

* CRUD에 대한 지원이 `monitor log-analytics workspace`에 추가됨

### <a name="network"></a>네트워크

* 교차 테넌트 가상 연결에 대한 지원이 `network private-dns link vnet [create|update]`에 추가됨
* [호환성이 손상되는 변경]`--resource-group` 및 `--vnet-name` 매개 변수가 필수 매개 변수가 되도록 `network vnet subnet list`가 변경됨

### <a name="sql"></a>SQL

* 관리형 인스턴스에서 AAD 관리자 설정을 지원하는 명령이 `sql mi ad-admin`에 추가됨

### <a name="storage"></a>스토리지

* 서비스에서 서비스로 복사하는 동안 액세스 계층을 유지하기 위해 `storage copy`에 `--preserve-s2s-access-tier` 매개 변수가 추가됨
* 스토리지 계정에 대한 대용량 파일 공유를 지원하기 위해 `storage account [create|update]`에 `--enable-large-file-share` 매개 변수가 추가됨

## <a name="september-24-2019"></a>2019년 9월 24일

버전 2.0.74

### <a name="acr"></a>ACR

* `acr config retention update`에 필수 `--type` 매개 변수가 추가되었습니다.
* [호환성이 손상되는 변경] `--name -n` 매개 변수가 `acr config` 명령 그룹에 대한 `--registry -r `로 변경되었습니다.

### <a name="aks"></a>AKS

* `aks create` 명령에 `--load-balancer-sku` 매개 변수가 추가되어 SLB로 AKS 클러스터를 만들 수 있게 되었습니다.
* `aks [create|update]` 명령에 `--load-balancer-managed-outbound-ip-count`, `--load-balancer-outbound-ips` 및 `--load-balancer-outbound-ip-prefixes` 매개 변수가 추가되어 SLB로 AKS 클러스터의 부하 분산 장치 프로필을 업데이트할 수 있게 되었습니다.
* `aks create` 명령에 `--vm-set-type` 매개 변수가 추가되어 AKS 클러스터의 vm 유형을 지정할 수 있게 되었습니다(vmas 또는 vmss).

### <a name="arm"></a>ARM

* json 템플릿에서 여러 줄과 주석을 지원할 수 있도록 `group deployment create` 명령에 `--handle-extended-json-format` 매개 변수가 추가되었습니다.

### <a name="compute"></a>컴퓨팅

* 예약된 이벤트 종료 구성 기능을 지원하기 위해 `vmss [create|update]` 명령에 `--terminate-notification-time` 매개 변수가 추가되었습니다.
* 예약된 이벤트 종료 구성 기능을 지원하기 위해 `vmss update` 명령에 `--enable-terminate-notification` 매개 변수가 추가되었습니다.
* `[vm|vmss] create` 명령에 `--priority,` `--eviction-policy,` `--max-billing` 매개 변수 추가
* 디스크 업로드의 정확한 크기를 지정할 수 있도록 `disk create`가 변경되었습니다.
* 관리 디스크의 증분 스냅샷에 대한 지원이 `snapshot create`에 추가되었습니다.

### <a name="cosmos-db"></a>Cosmos DB

* 키, 읽기 전용 키 또는 연결 문자열을 표시하도록 `cosmosdb keys list` 명령에 `--type <key-type>` 매개 변수가 추가되었습니다.
* `cosmosdb keys regenerate` 명령이 추가됨
* [사용 되지 않음]`cosmosdb list-connection-strings`, `cosmosdb regenerate-key` 및 `cosmosdb list-read-only-keys` 명령이 사용되지 않습니다.

### <a name="eventgrid"></a>EventGrid

* 오른쪽 매개 변수를 참조하도록 엔드포인트 도움말 텍스트가 수정되었습니다.

### <a name="key-vault"></a>Key Vault

* 테넌트(`login -t`)로 로그인하면 `keyvault create`가 실패할 수 있는 이슈가 해결되었습니다.

### <a name="monitor"></a>모니터

* `--condition` 인수에서 `:` 문자가 허용되지 않아 `monitor metrics alert create` 할 수 없는 이슈가 해결되었습니다.

### <a name="policy"></a>정책

* 정책 API 버전 2019-06-01에 대한 지원이 추가되었습니다.
* `policy assignment create` 명령에 `--enforcement-mode` 매개 변수가 추가되었습니다.

### <a name="storage"></a>스토리지

* `az storage copy` 명령에 `--blob-type` 매개 변수가 추가되었습니다.

## <a name="september-10-2019"></a>2019년 9월 10일

### <a name="acr"></a>ACR

* 보존 정책을 구성하는 `acr config retention` 명령 그룹이 추가됨

### <a name="aks"></a>AKS

* 다음 명령을 통해 ACR 통합 지원이 추가됨
  * AKS 클러스터에 ACR을 연결하는 `--attach-acr` 매개 변수가 `aks [create|update]`에 추가됨
  * AKS 클러스터에서 ACR을 분리하는 `--detach-acr` 매개 변수가 `aks update`에 추가됨

### <a name="arm"></a>ARM

* 2019-05-10 API 버전을 사용하도록 업데이트됨

### <a name="batch"></a>Batch

* `batch pool create`에 대한 `--json-file`에 새 JSON 구성 설정이 추가됨
  * 파일 시스템을 탑재하기 위한 `MountConfigurations`가 추가됨(자세한 내용은 https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body 참조)
  * `NetworkConfiguration`에 풀의 공용 IP에 대한 선택적 속성 `publicIPs`가 추가됨(자세한 내용은 https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body 참조)
* 공유 이미지 갤러리에 대한 지원이 `--image`에 추가됨
* [호환성이 손상되는 변경]`batch pool create`의 기본값 `--start-task-wait-for-success`가 `true`로 변경됨
* [호환성이 손상되는 변경]`AutoUserSpecification`의 `Scope` 기본값이 항상 풀이 되도록 변경됨(Windows 노드에서는 `Task`, Linux 노드에서는 `Pool`이었음)
  * 이 인수는 `--json-file`을 사용하여 JSON 구성에서만 설정 가능

### <a name="hdinsight"></a>HDInsight

* GA 릴리스
* [호환성이 손상되는 변경]`az hdinsight resize`의 `--workernode-count/-c` 매개 변수가 필수 항목으로 변경됨

### <a name="key-vault"></a>Key Vault

* 네트워크 규칙에서 서브넷을 삭제할 수 없는 이슈 해결
* 중복 서브넷 및 IP 주소를 네트워크 규칙에 추가할 수 있는 이슈 해결

### <a name="network"></a>네트워크

* 트래픽 분석 간격 값을 설정하는 `--interval` 매개 변수가 `network watcher flow-log`에 추가됨
* 게이트웨이 ID를 관리하는 `network application-gateway identity`가 추가됨
* Key Vault ID 설정 지원이 `network application-gateway ssl-cert`에 추가됨
* `network express-route peering peer-connection [show|list]`가 추가됨

### <a name="policy"></a>정책

* 2019-01-01 API 버전을 사용하도록 업데이트됨

## <a name="august-27-2019"></a>2019년 8월 27일

버전 2.0.72

### <a name="acr"></a>ACR

* [호환성이 손상되는 변경]`classic` SKU 지원이 제거되었습니다.

### <a name="api-management"></a>API Management

* [미리 보기] `apim` 명령 그룹이 추가됨

### <a name="appservice"></a>AppService

* 슬롯 지정 시의 `webapp webjob continuous start` 명령 관련 문제가 해결됨
* `env` 폴더를 검색하고 배포에 사용된 파일에서 제거하도록 `webapp up`이 변경됨

### <a name="keyvault"></a>Keyvault

* `--expires` 인수를 무시한 `keyvault secret set`의 버그가 수정됨

### <a name="network"></a>네트워크

* `--private-ip-address-version` 인수에 IPv6 주소 지원이 추가됨
* 프라이빗 엔드포인트를 관리하기 위한 새 `network private-endpoint [create|update|list-types]` 명령이 추가됨
* `network private-link-service` 명령 그룹이 추가됨
* `--private-endpoint-network-policies` 및 `--private-link-service-network-policies` 인수를 `network vnet subnet update`에 추가

### <a name="rbac"></a>RBAC

* 홈페이지가 업데이트되지 않는 `ad app update --homepage` 명령 관련 문제가 해결됨

### <a name="servicefabric"></a>ServiceFabric

* 대/소문자가 혼합된 Key Vault 이름 지원이 추가됨
* Key Vault에서 인증서를 사용할 때 발생하는 문제가 해결됨
* PFX 인증서 파일의 사용 관련 문제가 해결됨
* Key Vault 리소스 그룹이 지정되지 않은 `sf cluster certificate add` 관련 문제가 해결됨
* `sf cluster set`가 작동하지 않는 문제가 해결됨

### <a name="signalr"></a>SignalR

* 새 명령이 추가됨:
  * `signalr cors`: SignalR CORS 관리
  * `signalr restart`: SignalR Service 다시 시작
  * `signalr update`: SignalR Service 업데이트
* `--service-mode` 인수를 `signalr create`에 추가

### <a name="storage"></a>스토리지

* `storage account revoke-delegation-keys` 명령이 추가됨

## <a name="august-13-2019"></a>2019년 8월 13일

버전 2.0.71

### <a name="appservice"></a>AppService

* 슬롯에 대해 `webapp webjob continuous` 명령이 실패하는 문제가 해결됨

### <a name="botservice"></a>BotService

* [호환성이 손상되는 변경] v3 SDK 봇 만들기에 대한 지원이 제거됨

### <a name="cognitiveservices"></a>CognitiveServices

* `cognitiveservices account network-rule` 명령이 추가됨

### <a name="cosmos-db"></a>Cosmos DB

* 여러 쓰기 위치 업데이트 시의 경고가 제거됨
* CosmosDB SQL, MongoDB, Cassandra, Gremlin 및 Table 리소스 및 리소스 처리량에 대한 CRUD 명령이 추가됨

### <a name="hdinsight"></a>HDInsight

이 릴리스에는 호환성이 손상되는 변경이 많이 포함되어 있습니다.

* [호환성이 손상되는 변경]`hdinsight create`에 대한 매개 변수의 이름이 변경됨
  * `--storage-default-container`에서 `--storage-container`로 이름이 변경됨
  * `--storage-default-filesystem`에서 `--storage-filesystem`로 이름이 변경됨
* [호환성이 손상되는 변경]`application create`의 `--name` 인수에서 클러스터 이름 대신 애플리케이션 이름을 나타내도록 변경됨
* 이전 `--name` 기능을 대체하기 위해 `--cluster-name` 인수가 `application create`에 추가됨
* [호환성이 손상되는 변경]`application create`에 대한 매개 변수의 이름이 변경됨
  * `--application-type`에서 `--type`로 이름이 변경됨
  * `--marketplace-identifier`에서 `--marketplace-id`로 이름이 변경됨
  * `--https-endpoint-access-mode`에서 `--access-mode`로 이름이 변경됨
  * 이름이 `--https-endpoint-destination-port`에서 `--destination-port`로 변경됨
* [호환성이 손상되는 변경]`application create`에 대한 매개 변수가 제거됨
  * `--https-endpoint-location`
  * `--https-endpoint-public-port`
  * `--ssh-endpoint-destination-port`
  * `--ssh-endpoint-location`
  * `--ssh-endpoint-public-port`
* [호환성이 손상되는 변경] `hdinsight resize`에 대한 이름이 `--target-instance-count`에서 `--workernode-count`로 변경됨
* [호환성이 손상되는 변경]`hdinsight script-action` 그룹의 모든 명령에서 `--name` 매개 변수를 스크립트 작업 이름으로 사용하도록 변경됨.
* 이전 `--name` 기능을 대체하기 위해 `--cluster-name` 인수가 모든 `hdinsight script-action` 명령에 추가됨
* [호환성이 손상되는 변경] 모든 `hdinsight script-action` 명령에 대한 이름이 `--script-execution-id`에서 `--execution-id`로 변경됨
* [호환성이 손상되는 변경]`hdinsight script-action show`에서 `hdinsight script-action show-execution-details`로 이름이 변경됨
* [호환성이 손상되는 변경] `hdinsight script-action execute --roles`의 매개 변수에서 쉼표로 구분하는 대신 공백으로 구분하도록 변경됨
* [호환성이 손상되는 변경]`hdinsight script-action list`의 `--persisted` 매개 변수가 제거됨
* `hdinsight create --cluster-configurations` 매개 변수에서 로컬 JSON 파일 또는 JSON 문자열에 대한 경로를 허용하도록 변경됨
* 명령 `hdinsight script-action list-execution-history` 추가됨
* `hdinsight monitor enable --workspace`에서 Log Analytics 작업 영역 ID 또는 작업 영역 이름을 허용하도록 변경됨
* 작업 영역 ID가 매개 변수로 제공되는 경우 필요한 `hdinsight monitor enable --primary-key` 인수가 추가됨
* 도움말 메시지에 대한 추가 예제 및 업데이트된 설명이 추가됨

### <a name="interactive"></a>대화형

* 로드 오류가 수정됨

### <a name="kubernetes"></a>Kubernetes

* 대시보드 컨테이너 포트에서 `https`를 사용하는 경우 `https`를 사용하도록 변경됨

### <a name="network"></a>네트워크

* `--yes` 인수가 `network dns record-set cname delete`에 추가됨

### <a name="profile"></a>프로필

* 리소스 액세스 토큰을 가져오기 위해 `--resource-type` 인수가 `account get-access-token`에 추가됨

### <a name="servicefabric"></a>ServiceFabric

* sf 클러스터를 만드는 데 지원되는 모든 os 버전이 추가됨
* 기본 인증서 유효성 검사 버그가 수정됨

### <a name="storage"></a>스토리지

* 명령 `storage copy` 추가됨

## <a name="july-30-2019"></a>2019년 7월 30일

버전 2.0.70

### <a name="acr"></a>ACR

* #9952 문제(`acr pack build` 명령의 회귀)가 해결됨
* `acr pack build`의 기본 작성기 이미지 이름이 제거됨

### <a name="appservice"></a>App Service

* 리소스를 찾을 수 없는 경우 메시지를 표시 하도록 `webapp config ssl`을 변경
* `functionapp create`에서 `Standard_RAGRS` 스토리지 계정 유형을 허용하지 않는 문제가 해결됨
* 이전 버전의 python을 사용하여 실행할 경우 `webapp up`이(가) 실패하는 문제가 해결됨

### <a name="network"></a>네트워크

* `network nic ip-config add`에서 잘못된 매개 변수 `--ids` 제거됨(#9861 수정)
* #9604 수정. 사용자가 신뢰할 수 있는 루트 인증서를 연결할 수 있도록 `network application-gateway http-settings [create|update]`에 `--root-certs` 매개 변수를 추가했습니다.
* `network dns record-set ns create`(#9965)에 대해 인수 `--subscription`을 수정

### <a name="rbac"></a>RBAC

* `user update` 명령이 추가됨
* [사용 되지 않음] 사용자 관련 명령 중 `--upn-or-object-id`가 사용되지 않음
    * 대체 인수 `--id` 사용
* 사용자 관련 명령에 `--id` 인수가 추가됨

### <a name="sql"></a>SQL

* 관리형 인스턴스 키 및 TDE 보호기에 대한 관리 명령이 추가됨

### <a name="storage"></a>스토리지

* `storage remove` 명령이 추가됨
* `storage blob update` 문제가 해결됨

### <a name="vm"></a>VM

* 새로운 API 버전을 사용하여 영역 세부 정보를 출력하도록 `list-skus`를 변경
* `vmss create`에 대한 `--single-placement-group`의 기본값을 `false`로 변경
* `[snapshot|disk] create`에 대한 ZRS 스토리지 SKU를 선택하는 기능이 추가됨
* 전용 호스트를 지원하는 새로운 명령 그룹 `vm host`가 추가됨
* VM 전용 호스트를 설정하기 위해 `vm create`에 매개 변수 `--host` 및 `--host-group` 추가됨

## <a name="july-16-2019"></a>2019년 7월 16일

Version 2.0.69

### <a name="appservice"></a>App Service

* ResourceGroupName 또는 애플리케이션 이름이 유효하지 않은 경우 올바른 오류 메시지를 반환하도록 `webapp identity` 명령이 변경됨
* ResourceGroup이 제공되지 않은 경우 numberOfSites에 대한 올바른 값을 반환하도록 `webapp list` 수정
* `appservice plan create` 및 `webapp create`의 부작용 수정

### <a name="core"></a>핵심

* 적용할 수 없음에도 불구하고 `--subscription`이 나타나는 문제가 수정됨

### <a name="batch"></a>Batch

* [호환성이 손상되는 변경]`batch pool node-agent-skus list`를 `batch pool supported-images list`로 대체
* `batch pool create network`의 `--json-file` 옵션을 사용할 때 트래픽의 소스 포트를 기반으로 풀에 대한 네트워크 액세스를 차단하는 보안 규칙에 대한 지원이 추가됨
* `batch task create`의 `--json-file` 옵션을 사용할 때 컨테이너 작업 디렉터리 또는 일괄 처리 작업 디렉터리에서 작업을 실행하도록 지원이 추가됨
* `batch pool create`의 `--application-package-references` 옵션에서 기본값으로만 작동하는 오류가 수정됨

### <a name="eventhubs"></a>Event Hubs

* `authorizationrule` 명령의 `--rights` 매개 변수에 대한 유효성 검사를 추가

### <a name="rdbms"></a>RDBMS

* 복제본 명령을 만들기 위해 복제본 SKU를 지정하는 선택적 매개 변수가 추가됨
* MySQL 복제본 생성 시 CI 테스트 실패 문제 수정

### <a name="relay"></a>릴레이

* 클라이언트 인증이 비활성화[#8775](https://github.com/azure/azure-cli/issues/8775)된 경우의 하이브리드 연결 문제 해결
* `--requires-transport-security` 매개 변수가 `relay wcfrelay create`에 추가됨

### <a name="servicebus"></a>Servicebus

* `authorizationrule` 명령의 `--rights` 매개 변수에 대한 유효성 검사를 추가

### <a name="storage"></a>스토리지

* 스토리지 계정 업데이트를 위해 파일 AADDS 사용 설정
* 문제 `storage blob service-properties update --set` 해결

## <a name="july-2-2019"></a>2019년 7월 2일

Version 2.0.68

### <a name="core"></a>핵심

* 명령 모듈은 이제 단일 Python 배포 패키지로 통합됩니다. 따라서 PyPI의 많은 `azure-cli-` 패키지를 직접 사용하지 않습니다.
  이를 통해 설치 크기를 줄이고 `pip`를 통해 직접 설치한 사용자에게만 영향을 주게 됩니다.

### <a name="acr"></a>ACR

* 작업에 타이머 트리거에 대한 지원 추가

### <a name="appservice"></a>App Service

* 기본값으로 애플리케이션 인사이트를 사용하도록 `functionapp create` 변경
* [호환성이 손상되는 변경] 사용되지 않는 `functionapp devops-build` 제거
  *  대신 새 명령 `az functionapp devops-pipeline` 사용
* `functionapp deployment config-zip`에 Linux 사용 함수 앱 계획 지원 추가

### <a name="cosmos-db"></a>Cosmos DB

* TTL을 사용하지 않도록 설정하는 지원 추가

### <a name="dls"></a>DLS

* 업데이트된 ADLS 버전(0.0.45)

### <a name="feedback-command"></a>피드백 명령

* 실패한 확장 명령을 보고할 때, `az feedback`은 이제 브라우저에서 인덱스 확장의 프로젝트/리포지토리 URL을 열려고 시도합니다.

### <a name="hdinsight"></a>HDInsight

* [호환성이 손상되는 변경]`oms` 명령 그룹 이름을 `monitor`로 변경
* [호환성이 손상되는 변경] 필수 매개 변수로 `--http-password/-p` 변경 
* `--cluster-admin-account` 및 `cluster-users-group-dns` 매개 변수 완성자에 대한 완성자 추가 
* `—esp`가 있을 때 `cluster-users-group-dns` 매개 변수가 필수가 되도록 변경
* 모든 기존 인수 자동-완성자에 대한 시간 제한 추가
* 리소스 이름을 리소스 ID로 변환하기 위한 시간 제한 추가
* 자동 완성자를 모든 리소스 그룹에서 리소스를 선택하도록 변경했습니다. `-g`로 지정한 리소스 그룹과 다를 수 있습니다.
* `hdinsight application create` 명령에서 `--sub-domain-suffix` 및 `--disable_gateway_auth` 매개 변수에 대한 지원이 추가됨

### <a name="managed-services"></a>관리 서비스

* 미리 보기 관리 서비스 명령 모듈 소개

### <a name="profile"></a>프로필
* 로그 아웃 명령에 대한 `--subscription` 인수 표시하지 않음

### <a name="rbac"></a>RBAC

* [호환성이 손상되는 변경]`create-for-rbac`에 대한 `--password` 인수가 제거됨
* AAD 그래프 서버 복제 대기 시간으로 인한 일시적인 실패를 피하기 위해 `create` 명령에 `--assignee-principal-type` 매개 변수가 추가됨
* 소유 개체를 나열할 때 `ad signed-in-user`에서의 충돌 수정
* `ad sp`가 서비스 주체로부터 올바른 애플리케이션을 찾지 못하는 문제가 해결됨

### <a name="rdbms"></a>RDBMS

* MariaDB에 대한 복제 지원 추가

### <a name="sql"></a>SQL

* `sql db create --sample-name`에 허용되는 값이 문서화됨

### <a name="storage"></a>스토리지

* `--as-user`를 사용하여 `storage blob generate-sas`에 사용자 위임 SAS 토큰 지원을 추가함 
* `--as-user`를 사용하여 `storage container generate-sas`에 사용자 위임 SAS 토큰 지원을 추가함 

### <a name="vm"></a>VM

* `vmss create`가 `--no-wait`와 실행될 때 오류 메시지를 반환하는 버그가 수정됨
* `vmss create --single-placement-group`에 대한 클라이언트 측 유효성 검사 제거 `--single-placement-group`이 `true`로 설정되고 `--instance-count`이 100보다 크거나 가용성 영역이 지정되면 실패하지 않지만, 이 유효성 검사는 컴퓨팅 서비스에 남겨 둡니다.
* `[vm|vmss] extension image list`가 `--latest`와 함께 사용하면 실패하는 버그 수정


## <a name="june-18-2019"></a>2019년 6월 18일

2\.0.67 버전

### <a name="core"></a>핵심

이 릴리스에서는 명령 그룹, 명령 또는 인수가 미리 보기 상태에 있을 때 고객에게 보다 명확하게 알릴 수 있는 새로운 [미리 보기] 태그가 도입되었습니다. 이것은 이전에 도움말 텍스트에서 호출되었거나 명령 모듈 버전 번호에 의해 암시적으로 전달되었습니다.
CLI는 앞으로 개별 패키지의 버전 번호를 제거합니다. 명령이 미리 보기 상태이면 해당 인수도 모두 같습니다. 명령 그룹이 미리 보기로 레이블링된 경우 모든 명령과 인수도 미리 보기로 간주됩니다.

이 변경으로 인해 여러 명령 그룹이 "갑자기" 이 릴리스의 미리 보기 상태에 있는 것처럼 보일 수 있습니다. 실제로는 대부분의 패키지가 미리 보기 상태였지만 이 릴리스에서는 GA로 간주됩니다.

### <a name="acr"></a>ACR
* 'acr check-health' 명령이 추가됨
* AAD 토큰 및 외부 명령 검색의 오류 처리 개선

### <a name="acs"></a>ACS
* 사용되지 않는 ACS 명령이 도움말 보기에서 숨겨짐

### <a name="ams"></a>AMS
* [호환성이 손상되는 변경] archive-window-length 및 key-frame-interval-duration에 대한 ISO 8601 시간 문자열을 반환하도록 변경됨

### <a name="appservice"></a>AppService
* `webapp deleted list` 및 `webapp deleted restore`에 대한 위치 기반 라우팅을 추가
* Azure Cloud Shell에서 웹앱의 업로깅된 대상 URL("...에서 앱을 시작할 수 있습니다")을 클릭할 수 없는 이슈가 수정됨
* AlwaysOn 오류로 일부 SKU가 포함된 앱을 만들지 못하는 이슈가 해결됨
* 추가 사전 유효성 검사를 `[appservice|webapp] create`에 추가
* 올바른 actionHostName을 사용하도록 `[webapp|functionapp] traffic-routing` 수정
* `functionapp` 명령에 대한 슬롯 지원이 추가됨

### <a name="batch"></a>Batch
* 공유 키 인증에 대한 과도한 오류 보고로 인해 AAD 인증 회귀가 수정됨

### <a name="batchai"></a>BatchAI
* BatchAI 명령은 이제 사용되지 않고 숨겨집니다

### <a name="botservice"></a>BotService
* v3 SDK를 지원하는 명령에 대한 "지원 중단"/ "유지 관리 모드" 경고 메시지 추가

### <a name="cosmosdb"></a>CosmosDB
* [사용 되지 않음]`cosmosdb list-keys` 명령이 사용되지 않음
* `cosmosdb keys list` 명령이 추가됨 - `cosmosdb list-keys` 명령을 대체
* `cosmsodb create/update`: "isZoneRedundant"속성을 설정할 수 있도록 --location에 대한 새로운 형식이 추가되었습니다. 이전 형식은 사용되지 않음

### <a name="eventgrid"></a>EventGrid
* 도메인 CRUD 작업에 `eventgrid domain` 명령이 추가됨
* 도메인 토픽 CRUD 작업에 `eventgrid domain topic` 명령이 추가됨
* OData 구문을 사용하여 결과를 필터링하기 위해 `eventgrid [topic|event-subscription] list`에 `--odata-query` 인수가 추가됨
* `event-subscription create/update`: `--endpoint-type` 매개 변수의 새 값으로 servicebusqueue 추가
* [호환성이 손상되는 변경]`eventgrid event-subscription [create|update]`를 사용하여 `--included-event-types All`에 대한 지원 제거

### <a name="hdinsight"></a>HDInsight
* `hdinsight create` 명령에서 `--ssh-public-key` 매개 변수에 대한 지원 추가

### <a name="iot"></a>IoT
* 권한 부여 정책 키를 다시 생성하기 위한 지원 추가
* DigitalTwin Repository Provisioning Service에 대한 SDK 및 지원 추가

### <a name="network"></a>네트워크
* Nat 게이트웨이에 대한 영역 지원 추가
* 명령 `network list-service-tags` 추가됨
* 사용자가 와일드카드 A 레코드를 가져올 수 없는 `dns zone import` 이슈가 해결됨
* 특정 영역에서 흐름 로깅을 활성화할 수 없는 `watcher flow-log configure` 이슈가 해결됨

### <a name="resource"></a>리소스
* REST 호출 마킹을 위한 `az rest` 명령 추가
* 리소스 그룹 또는 구독 수준 `--scope`에 `policy assignment list`를 사용할 때의 오류 수정

### <a name="servicebus"></a>ServiceBus
* `servicebus topic create --max-size` [#9319](https://github.com/azure/azure-cli/issues/9319) 이슈가 해결됨

### <a name="sql"></a>SQL
* `sql [server|mi] create`에 대해 `--location`을 선택 사항으로 변경했습니다 - 지정되지 않은 경우 리소스 그룹 위치를 사용합니다.
* `sql db list-editions --available`에 대해 “’NoneType’ 객체 반복 불가” 오류를 수정했습니다.

### <a name="sqlvm"></a>SQLVm
* [호환성이 손상되는 변경] `--license-type` 매개 변수가 필수 매개 변수가 되도록 `sql vm create` 변경
* sql vm을 만들거나 업데이트하는 경우 SQL 이미지 SKU 설정을 허용하도록 변경

### <a name="storage"></a>스토리지
* `storage container generate-sas`에 대한 계정 키 누락 이슈가 해결됨
* Linux에서 `storage blob sync` 이슈 해결

### <a name="vm"></a>VM
* [미리 보기] VM 이미지 작성을 위해 `vm image template` 명령이 추가됨

## <a name="june-4-2019"></a>2019년 6월 4일

버전 2.0.66

### <a name="core"></a>핵심
* `--output yaml`을 `--query`와 함께 사용하는 경우 명령이 실패하는 버그가 수정됨

### <a name="acr"></a>ACR
* 빌드 팩을 사용하여 빠른 빌드 작업을 만드는 'acr pack' 명령 그룹이 추가됨

### <a name="acs"></a>ACS
* AKS kube-dashboard 추가 기능에 대한 사용/사용 안 함 설정이 허용됨
* 구독이 Azure Red Hat OpenShift를 사용하기 위한 허용 목록에 없는 경우 친숙한 메시지가 출력됨

### <a name="batch"></a>Batch
* 계정에 로그인되지 않는 \[[#9165](https://github.com/Azure/azure-cli/issues/9165)\]\[[#8978](https://github.com/Azure/azure-cli/issues/8978)\] 오류에 대한 처리가 향상됨

### <a name="iot"></a>IoT
* 수동 장애 조치 지원이 추가됨

### <a name="network"></a>네트워크
* 사용자 지정 WAF 규칙을 지원하는 `network application-gateway waf-policy` 명령이 추가됨
* `--waf-policy` 및 `--max-capacity` 인수를 `network application-gateway [create|update]`에 추가 

### <a name="resource"></a>리소스
* 사용 가능한 TTY가 없을 때 `deployment create`에서 나타나는 오류 메시지가 향상됨

### <a name="role"></a>역할
* 도움말 텍스트가 업데이트됨

### <a name="compute"></a>컴퓨팅
* 0에서 시작하지 않거나 숫자를 건너뛰는 데이터 디스크 LUN이 있는 관리형 이미지의 VM에 대한 `vm create` 지원이 추가됨

## <a name="may-21-2019"></a>2019년 5월 21일

버전 2.0.65

### <a name="core"></a>핵심
* 인증 오류에 대한 더 나은 피드백이 추가됨
* CLI가 코어 버전과 호환되지 않는 확장을 로드하는 문제가 해결됨
* `clouds.config`가 손상된 경우 시작과 관련된 문제가 해결됨

### <a name="acr"></a>ACR
* Tasks에 관리 ID가 지원됨

### <a name="acs"></a>ACS
* 고객 AAD 클라이언트에서 사용되는 `openshift create` 명령의 문제가 해결됨

### <a name="appservice"></a>AppService
* [사용 되지 않음]`functionapp devops-build` 명령이 사용되지 않음 - 다음 릴리스에서 제거됨
* `functionapp devops-pipeline`에서 Azure DevOps의 빌드 로그를 자세한 정보 표시 모드로 가져오도록 변경됨
* [호환성이 손상되는 변경]`functionapp devops-pipeline` 명령에서 `--use_local_settings` 플래그가 제거됨 - 작동하지 않음
* `--logs`를 사용하지 않으면 `webapp up`에서 JSON 출력을 반환하도록 변경됨
* `webapp up`에 대한 로컬 구성에 기본 리소스를 작성할 수 있도록 지원됨
* `webapp up`에서 `--location` 인수를 사용하지 않고 앱을 다시 배포할 수 있도록 지원됨
* Linux SKU ASP 평가판을 만들 때 작동하지 않는 SKU 값을 Free로 사용하는 문제가 해결됨

### <a name="botservice"></a>BotService
* 명령에 대한 `--lang` 매개 변수에서 모든 대/소문자 구분을 허용하도록 변경됨
* 명령 모듈에 대한 설명이 업데이트됨

### <a name="consumption"></a>Consumption
* `consumption usage list --billing-period-name`을 실행할 때 누락된 필수 매개 변수가 추가됨

### <a name="iot"></a>IoT
* 모든 키를 나열하도록 지원됨

### <a name="network"></a>네트워크
* [호환성이 손상되는 변경]: Removed `network interface-endpoints` command group - use `network private-endpoints` 
* NAT 게이트웨이에 연결하기 위해 `network vnet subnet [create|update]`에 `--nat-gateway` 인수가 추가됨
* 레코드 이름이 레코드 유형과 일치하지 않는 `dns zone import` 문제가 해결됨

### <a name="rdbms"></a>RDBMS
* 지역 복제에 postgres 및 mysql이 지원됨

### <a name="rbac"></a>RBAC
* `role assignment`에 관리 그룹 범위 지원이 추가됨

### <a name="storage"></a>스토리지
* `storage blob sync`: 스토리지 Blob에 대한 sync 명령이 추가됨

### <a name="compute"></a>컴퓨팅
* VM의 컴퓨터 이름을 설정하기 위해 `--computer-name`이 `vm create`에 추가됨
* `[vm|vmss] create`에 대한 `--ssh-key-value` 이름이 `--ssh-key-values`로 변경됨 - 이제 여러 개의 ssh 공개 키 값 또는 경로를 허용할 수 있음
  * __참고__: 호환성이 손상되는 변경이 **아님** - `--ssh-key-value`가 `--ssh-key-values`와만 일치하므로 올바르게 구문 분석됨
* `ppg create`의 `--type` 인수가 선택적 항목으로 변경됨

## <a name="may-6-2019"></a>2019년 5월 6일

Version 2.0.64

### <a name="acs"></a>ACS
* [호환성이 손상되는 변경]`openshift` 명령에서 `--fqdn` 플래그가 제거됨
* Azure Red Hat Openshift GA API 버전을 사용하도록 변경됨
* `customer-admin-group-id` 플래그가 `openshift create`에 추가되었습니다.
* [GA] `aks create` 옵션인 `--network-policy`에서 `(PREVIEW)`가 제거됨

### <a name="appservice"></a>App Service
* [사용 되지 않음]`functionapp devops-build` 명령이 사용되지 않음
  * `functionapp devops-pipeline`으로 이름이 변경됨
* `webapp up` 실패를 야기하는 cloudshell의 올바른 사용자 이름을 가져오는 문제가 수정됨
* 지원되는 appserviceplans를 반영하도록 업데이트된 `appservice plan --sku` 설명서가 업데이트됨
* 리소스 그룹 및 계획을 위한 선택적 인수가 `webapp up`에 추가됨
* `webapp ssh`에 환경 변수 `AZURE_CLI_DISABLE_CONNECTION_VERIFICATION`에 대한 지원이 추가됨
* Linux Free SKU에 대한 `appserviceplan create` 지원이 추가됨
* Kudu 콜드 스타트 처리를 위해 `SCM_DO_BUILD_DURING_DEPLOYMENT=true`를 설정한 후 `webapp up`이 30초 동안 일시 중지하도록 변경됨
* Windows에서 `powershell` 런타임에 대한 지원이 `functionapp create`에 추가됨
* `create-remote-connection` 명령이 추가됨

### <a name="batch"></a>Batch
* `--application-package-references` 옵션에 대한 유효성 검사기의 버그가 수정됨

### <a name="botservice"></a>Botservice
* [호환성이 손상되는 변경]`bot create -v v4 -k webapp`에서 기본적으로 빈 Web App 봇을 만들도록 변경됨(즉, 봇이 App Service에 배포되지 않음)
* `-v v4`에서 이전 동작을 사용하도록 `--echo` 플래그가 `bot create`에 추가됨
* [호환성이 손상되는 변경]`--version`의 기본값이 `v4`로 변경됨
  * __참고:__ `bot prepare-publish`는 이전의 기본값을 계속 사용함
* [호환성이 손상되는 변경]`--lang`에서 `Csharp`이 더 이상 기본값으로 설정되지 않도록 변경됨 명령에 `--lang`이 필요하지만 제공되지 않으면 이제 명령에서 오류가 발생함
* [호환성이 손상되는 변경]`bot create`에서 `--appid` 및 `--password` 인수가 필수 항목이 되도록 변경되었으며 이제 `ad app create`를 통해 만들어질 수 있음
* `--appid` 및 `--password` 유효성 검사가 추가됨
* [호환성이 손상되는 변경]`bot create -v v4`에서 Storage 계정 또는 Application Insights를 만들거나 사용하지 않도록 변경됨
* [호환성이 손상되는 변경]`bot create -v v3`에서 Application Insights를 사용할 수 있는 지역을 요구하도록 변경됨
* [호환성이 손상되는 변경]`bot update`에서 이제 봇의 특정 속성에만 영향을 주도록 변경됨
* [호환성이 손상되는 변경]`--lang` 플래그에서 `Node` 대신 `Javascript`를 허용하도록 변경됨
* [호환성이 손상되는 변경]`Node`가 허용되는 `--lang` 값으로 제거됨
* [호환성이 손상되는 변경]`bot create -v v4 -k webapp`에서 `SCM_DO_BUILD_DURING_DEPLOYMENT`가 더 이상 true로 설정되지 않도록 변경됨. Kudu를 통한 모든 배포가 해당 기본 동작에 따라 작동함
* `.bot` 파일이 없는 봇에 대한 `bot download`에서 해당 봇에 대한 애플리케이션 설정 값을 사용하여 언어별 구성 파일을 만들도록 변경됨
* `bot prepare-deploy`에 `Typescript` 지원이 추가됨
* `--code-dir`에 `package.json`이 포함되어 있지 않은 경우 `Javascript` 및 `Typescript` 봇에 대한 경고 메시지가 `bot prepare-deploy`에 추가됨
* 성공하면 `bot prepare-deploy`에서 `true`를 반환하도록 변경됨
* `bot prepare-deploy`에 자세한 정보 로깅이 추가됨
* `az bot create -v v3`에 더 많은 사용 가능한 Application Insights 지역이 추가됨

### <a name="configure"></a>구성
* 폴더 기반 인수 기본값 구성이 지원됨

### <a name="eventhubs"></a>Event Hubs
* `namespace network-rule` 명령이 추가됨
* 네트워크 규칙에 대한 `--default-action` 인수가 `namespace [create|update]`에 추가됨

### <a name="network"></a>네트워크
* [호환성이 손상되는 변경]`vnet [create|update]`에 대한 `--cache` 인수가 `--defer`로 바뀜 

### <a name="policy-insights"></a>Policy Insights
* `--expand PolicyEvaluationDetails`에서 리소스에 대한 정책 평가 세부 정보를 쿼리하도록 지원됨

### <a name="role"></a>역할
* [사용 되지 않음]`create-for-rbac` hide '- password' 인수 변경 - 2019년 5월에 지원이 제거됩니다.

### <a name="service-bus"></a>Service Bus
* `namespace network-rule` 명령이 추가됨
* 네트워크 규칙에 대한 `--default-action` 인수가 `namespace [create|update]`에 추가됨
* `topic [create|update]`의 `--max-size`에서 프리미엄 SKU를 통해 10, 20, 40 및 80GB 값을 지원할 수 있도록 수정됨

### <a name="sql"></a>SQL
* `sql virtual-cluster [list|show|delete]` 명령이 추가됨

### <a name="vm"></a>VM
* VMSS VM 인스턴스의 보호 정책 업데이트를 사용하도록 설정하기 위해 `--protect-from-scale-in` 및 `--protect-from-scale-set-actions`가 `vmss update`에 추가됨
* VMSS VM 인스턴스의 일반 업데이트를 사용하도록 설정하기 위해 `--instance-id`가 `vmss update`에 추가됨
* `vmss wait`에 `--instance-id` 추가
* 근접 배치 그룹 관리를 위해 새 `ppg` 명령 그룹이 추가됨
* PPG 관리를 위해 `--ppg`가 `[vm|vmss] create` 및 `vm availability-set create`에 추가됨
* `--hyper-v-generation` 매개 변수가 `image create`에 추가됨

## <a name="april-23-2019"></a>2019년 4월 23일

Version 2.0.63

### <a name="acs"></a>ACS
* `aks get-credentials`를 중복 값을 덮어쓸지 묻는 메시지로 변경
* Dev Space 명령 "aks use-dev-spaces" 및 "aks remove-dev-spaces"명령에서 `(PREVIEW)` 제거

### <a name="ams"></a>AMS
* 자산 및 계정 필터 업데이트 버그 수정

### <a name="appservice"></a>AppService
* `webapp ssh`에 ASE 및 시간 제한에 대한 지원 추가
* Github 리포지토리에서 함수 앱에 이르는 Azure DevOps 파이프라인에 CI CD를 설치할 수 있도록 지원 추가
* Github 개인용 액세스 토큰을 받기 위해 `functionapp devops-build create`에 `--github-pat` 인수 추가
* functionapp 소스 코드가 포함된 Github 리포지토리를 수락하기 위해 `functionapp devops-build create`에 `--github-repository` 인수 추가
* `az webapp up --logs`가 오류로 실패하고 기본 .NETCORE 버전을 2.1로 업데이트하는 문제가 해결됨
* 소비 계획이 있는 함수 앱을 만들 때 불필요한 functionapp 설정을 제거
* 기본 ASP 문자열이 끝에 숫자를 추가하여 SKU 옵션에 따라 새로운 ASP를 만들도록 `webapp up`을 변경
* 브라우저에서 앱을 실행하기 위해 `-b`를 `webapp up`에 대한 옵션으로 추가
* `AZURE_CLI_DISABLE_CONNECTION_VERIFICATION` 환경 변수를 처리하도록 `webapp deployment source config zip` 변경

### <a name="deployment-manager"></a>배포 관리자
* [PREVIEW] 출시를 지원하는 아티팩트 생성 및 관리

### <a name="lab"></a>랩
* 조기 종료를 유발하는 버그 수정

### <a name="network"></a>네트워크
* 자식 영역 생성 중 부모의 `dns zone create`에 자동 이름 서버 위임이 추가됨

### <a name="resource"></a>리소스
* [사용 되지 않음]`resource link`의 사용되지 않는 `--link-id`, `--target-id` 및 `--filter-string` 인수
  * 대신 `--link`, `--target` 및 `--filter` 인수를 사용하세요.
* `resource link [create|update]` 명령이 작동하지 않는 문제가 해결됨
* 오류가 발생하여 리소스 ID를 사용하는 삭제가 중단되는 문제가 해결됨

### <a name="sql"></a>SQL
* 관리형 인스턴스에 사용자 지정 표준 시간대 지원 추가
* 탄력적 풀 이름을 `sql db update`와 함께 사용할 수 있도록 변경
* `sql server [create|update]`에 `--no-wait` 지원이 추가됨
* 명령 `sql server wait` 추가됨

### <a name="storage"></a>스토리지
* `storage blob generate-sas`의 이중 인코딩 SAS 토큰으로 인한 문제 해결

### <a name="vm"></a>VM
* 종료하지 않고 VM 전원을 끄기 위해 `--skip-shutdown` 플래그를 `vm|vmss stop`에 추가
* 게시 프로필의 계정 유형을 설정하기 위해 `--storage-account-type` 인수가 `sig image-version create`에 추가됨
* Azure 지역별 스토리지 계정 유형을 설정할 수 있도록 `sig image-version create`에 `--target-regions` 인수가 추가됨

## <a name="april-9-2019"></a>2019년 4월 9일

### <a name="core"></a>핵심
* 일부 확장이 버전을 `Unknown`으로 표시하고 업데이트할 수 없었던 문제 해결

### <a name="acr"></a>ACR
* 이미지를 컨텍스트 없이 실행하는 지원 추가

### <a name="ams"></a>AMS
* [사용 되지 않음]: Deprecated the `--bitrate` parameter of `account-filter` and `asset-filter`
* [호환성이 손상되는 변경]: Renamed the `--bitrate` parameter to `--first-quality`
* `ams streaming-policy create`에 새로운 암호화 매개 변수 지원이 추가됨
* 새로운 매개 변수 `--filters`가 `ams streaming-locator create`에 추가됨

### <a name="appservice"></a>AppService
* `webapp up`에 `--logs` 지원이 추가됨
* `functionapp devops-build create` 명령 `azure-pipelines.yml` 생성 문제 해결
* `unctionapp devops-build create` 오류 처리 및 표시기 개선
* [호환성이 손상되는 변경]`devops-build` 명령에 대한 `--local-git` 플래그 제거, Azure DevOps 파이프라인을 만드는 경우 강제 로컬 git 검색 및 처리
* Linux 함수 플랜을 만들기 위한 지원 추가
* `functionapp update --plan`을 사용하여 함수 앱 아래에 계획을 전환하는 기능이 추가됨
* Azure Functions 프리미엄 플랜 확장 설정에 대한 지원 추가

### <a name="cdn"></a>CDN
* `Microsoft_Standard` 및 `Standard_ChinaCdn`에 대한 지원이 추가됨

### <a name="feedback-command"></a>피드백 명령
* 최근 실행한 명령에 대한 메타데이터를 표시하도록 `feedback`을 변경
* 브라우저를 열고 문제 템플릿을 사용하여 문제 생성 프로세스에서 도움이 되는 프롬프트를 사용자에게 표시하도록 `feedback`을 변경
* '--verbose'를 사용하여 실행할 때 문제 본문을 출력하도록 `feedback`을 변경

### <a name="monitor"></a>모니터
* `metrics alert [create|update]`에서 "count"가 허용된 값이 아닌 문제가 해결됨 

### <a name="network"></a>네트워크
* `vnet-gateway list-bgp-peer-status`로 테이블 형식이 표시되지 않는 문제가 해결됨
* `list-request-headers` 및 `list-response-headers` 명령을 `application-gateway rewrite-rule`에 추가
* `list-server-variables` 명령을 `application-gateway rewrite-rule condition`에 추가
* 급행 경로 포트 상의 링크 상태를 업데이트할 때 알 수 없는 속성 예외 `express-route port update`가 발생하는 문제 수정

### <a name="privatedns"></a>PrivateDNS
* 프라이빗 DNS 영역에 대한 `network private-dns` 추가

### <a name="resource"></a>리소스
* `deployment create` 및 `group deployment create`에서 빈 매개 변수 세트를 포함하는 매개 변수 파일이 작동하지 않을 수 있는 문제 해결

### <a name="role"></a>역할
* `--years`를 올바르게 처리하도록 `create-for-rbac` 수정
* [호환성이 손상되는 변경] 구독 아래의 모든 할당을 무조건 삭제하는 경우 프롬프트를 표시하도록 `role assignment delete` 변경

### <a name="sql"></a>SQL
* 속성 proxyOverride 및 publicDataEndpointEnabled로 `sql mi [create|update]` 업데이트

### <a name="storage"></a>스토리지
* [호환성이 손상되는 변경]`storage blob delete`의 결과 제거
* SAS를 포함하는 BLOB에 대해 전체 URI를 만드는 `--full-uri`을 `storage blob generate-sas`에 추가
* 스냅샷에서 파일을 복사하는 `--file-snapshot`을 `storage file copy start`에 추가
* NoPendingCopyOperation에 대해 예외 대신 오류만 표시하도록 `storage blob copy cancel` 변경

## <a name="march-26-2019"></a>2019년 3월 26일


### <a name="core"></a>핵심
* 개발 확장이 호환되지 않는 문제가 수정되었습니다.
* 이제 오류 처리 시 고객에게 문제 페이지를 가리킵니다.

### <a name="cloud"></a>클라우드
* `cloud set`의 '구독을 찾을 수 없음' 오류가 수정되었습니다.

### <a name="acr"></a>ACR
* 이미지 가져오기에서 중복 소스 수정
* `--auth-mode`가 `acr build`, `acr run`, `acr task create` 및 `acr task update` 명령에 추가되었습니다.
* 작업에 대한 자격 증명을 관리하는 'acr task credential' 명령 그룹이 추가되었습니다.
* '--no-wait'가 `acr build` 명령에 추가되었습니다.

### <a name="appservice"></a>AppService
* `webapp up`가 빈 디렉터리 또는 알 수 없는 코드 시나리오에서 제대로 실행되지 않는 버그가 수정되었습니다.
* 슬롯이 `[webapp|functionapp] config ssl bind`에서 작동하지 않는 버그가 수정되었습니다.

### <a name="bot-service"></a>BOT Service
* `webapp`을 통한 봇 배포를 준비하는 `bot prepare-deploy`가 추가되었습니다.
* 암호가 제공되지 않으면 암호를 표시하도록 `bot create --kind registration`이 변경되었습니다.
* [호환성이 손상되는 변경]`bot create --kind registration`의 `--endpoint`가 기본적으로 필수 문자열 대신 빈 문자열로 지정되도록 변경되었습니다.
* v4 웹앱 봇에 대한 ARM 템플릿의 애플리케이션 설정에 `SCM_DO_BUILD_DURING_DEPLOYMENT`를 추가

### <a name="cdn"></a>CDN
* `--no-wait`에 대한 지원이 `cdn endpoint [create|update|start|stop|delete|load|purge]`에 추가됨  
* [호환성이 손상되는 변경]: `cdn endpoint create` 기본 쿼리 문자열 캐싱 동작이 변경되었습니다. 더 이상 "IgnoreQueryString"이 기본값으로 지정되지 않습니다. 이제 서비스에 의해 설정됩니다.

### <a name="cosmosdb"></a>Cosmosdb
* 계정 업데이트 시 `--enable-multiple-write-locations` 지원이 추가되었습니다.
* Cosmos DB 계정의 VNET 규칙을 관리하기 위한 `add`, `remove` 및 `list` 명령으로 `network-rule` 하위 그룹이 추가됨

### <a name="interactive"></a>대화형
* azdev를 통해 설치된 대화형 확장과 호환되지 않는 문제가 해결되었습니다.

### <a name="monitor"></a>모니터
* `monitor metrics alert [create|update]`에 `*` 차원 값을 허용하도록 변경되었습니다.

### <a name="network"></a>네트워크
* `rewrite-rule` 명령 그룹이 `application-gateway`에 추가되었습니다.

### <a name="profile"></a>프로필
* 관리 서비스 ID에 대한 테넌트 수준 계정 지원이 `login`에 추가되었습니다.

### <a name="postgres"></a>Postgres 
* postgresql `replica` 명령 및 `restart server` 명령이 추가되었습니다.
* 서버를 만드는 데 제공되지 않은 경우 리소스 그룹에서 기본 위치를 가져오고 보존 기간(일)에 대한 유효성 검사를 추가하도록 변경되었습니다.

### <a name="resource"></a>리소스
* `deployment [create|list|show]`의 테이블 출력이 향상되었습니다.
* secureObject 형식이 인식되지 않는 `deployment [create|validate]` 관련 문제가 해결되었습니다.

### <a name="graph"></a>그래프
* `--end-date`에 대한 지원이 `ad [app|sp] credential reset`에 추가됨
* `ad app permission add`를 사용하여 권한을 추가할 수 있도록 지원됩니다.
* 권한이 없는 `ad app permission list` 관련 버그가 수정되었습니다.
* 현재 계정에 구독이 없는 경우 역할 할당 삭제를 건너뛰도록 `ad sp delete`가 변경되었습니다.
* 목록이 제공되지 않는 경우 `--identifier-uris`에서 기본적으로 빈 목록을 지정하도록 `ad app create`가 변경되었습니다.

### <a name="storage"></a>스토리지
* 공유 스냅샷에서 다운로드할 수 있도록 `--snapshot`이 `storage file download-batch`에 추가되었습니다.
* 자세한 정보를 줄이고 현재 Blob을 표시하도록 `storage blob [download-batch|upload-batch]` 진행 표시줄이 변경되었습니다.
* 암호화 매개 변수를 업데이트하는 `storage account update` 관련 문제가 해결되었습니다.
* oauth(`--auth-mode=login`)를 사용하면 `storage blob show`가 실패하는 문제가 해결되었습니다.

### <a name="vm"></a>VM
* `image update` 명령이 추가됨

## <a name="march-12-2019"></a>2019년 3월 12일

2\.0.60 버전

### <a name="core"></a>핵심

* 구독이 발견되지 않는 문제를 `cloud set`에서 수정

### <a name="acr"></a>ACR

* 이미지 가져오기에서 중복 소스 수정

### <a name="acs"></a>ACS

* kubectl에서 지원되지 않는 경우 `aks browse`에 대한 `--listen-address` 매개 변수를 무시하도록 변경 

### <a name="appservice"></a>AppService

* Kudu 게시 url 및 해당 자격 증명을 가져오도록 `[webapp|functionapp] deployment list-publishing-credentials` 추가
* `webapp auth update`에 대한 잘못된 인쇄 문 삭제
* Linux App Service 계획에서 런타임에 올바른 이미지를 설정하도록 `functionapp` 수정
* `webapp up`에 대한 미리보기 태그 제거 및 명령 개선 사항 추가

### <a name="botservice"></a>Botservice

* v4 웹앱 봇에 대한 ARM 템플릿의 애플리케이션 설정에 `SCM_DO_BUILD_DURING_DEPLOYMENT`를 추가
* v4 웹앱 봇에 대한 ARM 템플릿의 애플리케이션 설정에 `Microsoft-BotFramework-AppId` 및 `Microsoft-BotFramework-AppPassword` 추가
* `bot create`의 끝에 `bot publish` 명령 출력에서 작은 따옴표 제거
* `bot publish`를 비동기로 변경

### <a name="container"></a>컨테이너

* `--no-wait` 인수를 `container [start|restart]`에 추가

### <a name="eventhub"></a>EventHub

* 캡처에서 빈 보관을 지원하기 위해 `--skip-empty-archives` 플래그를 `eventhub create|update`에 추가

### <a name="find"></a>찾기

* 주요 기능 업데이트

### <a name="hdinsight"></a>HDInsight

* ADLS Gen2 MSI를 지원하기 위해 `hdinsight create`에 `--storage-account-managed-identity` 매개 변수가 추가됨

### <a name="network"></a>네트워크

* 다른 구독의 게이트웨이 간 VPN 연결을 업데이트하지 못하는 `vpn-connection update` 문제가 해결됨

### <a name="rdbms"></a>Rdbms

* 서버 생성 시 제공되지 않은 경우 리소스 그룹에서 기본 위치를 얻고 재방문 주기에 대한 유효성 검사를 추가하는 사소한 수정

### <a name="role"></a>역할

* 정의를 올바르게 확인하기 위해 ID를 사용하도록 `role definition update` 수정
* `ad app credential reset`을 앱의 서비스 사용자가 항상 존재한다는 가정을 제거하도록 변경

### <a name="service-fabric"></a>Service Fabric

* `sf cluster list`가 반복 가능하지 않은 문제 해결

## <a name="february-26-2019"></a>2019년 2월 26일

버전 2.0.59

### <a name="core"></a>핵심

* `--subscription NAME`을 사용하는 일부 인스턴스에서 예외가 발생하는 문제 해결

### <a name="acr"></a>ACR

* `acr build`, `acr task create` 및 `acr task update` 명령에 대한 `--target` 매개 변수 추가됨
* Azure에 로그인하지 않은 경우 런타임 명령에 대한 오류 처리 향상

### <a name="acs"></a>ACS

* `--listen-address` 옵션을 `aks port-forward`에 추가

### <a name="appservice"></a>AppService

* `functionapp devops-build` 명령이 추가됨

### <a name="batch"></a>Batch
* [호환성이 손상되는 변경]`batch pool upgrade os` 명령 제거됨
* [호환성이 손상되는 변경]`Application` 응답에서 `Pacakges` 속성 제거됨
* 애플리케이션 패키지를 나열하는 `batch application package list` 명령이 추가됨
* [호환성이 손상되는 변경] 모든 `batch application` 명령에서 `--application-id`가 `--application-name`으로 변경됨 
* 원시 API 응답을 요청하는 명령에 `--json-file` 인수 추가됨
* 모든 엔드포인트에 `https://`가 누락된 경우 이를 자동으로 포함하도록 유효성 검사 업데이트됨

### <a name="cosmosdb"></a>CosmosDB

* Cosmos DB 계정의 VNET 규칙을 관리하기 위한 `add`, `remove` 및 `list` 명령으로 `network-rule` 하위 그룹이 추가됨

### <a name="kusto"></a>Kusto

* [호환성이 손상되는 변경] 포맷하는 동안 데이터베이스의 `hot_cache_period` 및 `soft_delete_period` 유형이 ISO8601로 변경됨

### <a name="network"></a>네트워크

* `--express-route-gateway-bypass` 인수를 `vpn-connection [create|update]`에 추가
* `express-route` 확장의 명령 그룹 추가됨
* `express-route gateway` 및 `express-route port` 명령 그룹 추가됨
* `express-route peering [create|update]`에 추가된 인수: `--legacy-mode` 
* `--allow-classic-operations` 및 `--express-route-port` 인수를 `express-route [create|update]`에 추가됨
* `--gateway-default-site` 인수를 `vnet-gateway [create|update]`에 추가
* `ipsec-policy` 명령이 `vnet-gateway`에 추가됨

### <a name="resource"></a>리소스

* 유형 입력란이 대소문자를 구분하는 `deployment create` 관련 문제 해결
* URI 기반 매개 변수 파일에 대한 지원이 `policy assignment create`에 추가됨
* URI 기반 매개 변수 및 정의에 대한 지원이 `policy set-definition update`에 추가됨
* `policy definition update`에 대한 매개 변수 및 규칙 처리 해결
* 교차 구독 ID가 구독 ID를 제대로 인식하지 않는 `resource show/update/delete/tag/invoke-action` 문제 해결

### <a name="role"></a>역할

* 앱 역할에 대한 지원이 `ad app [create|update]`에 추가됨

### <a name="vm"></a>VM

* Ubuntu 18.0에서 `vm create where ` --accelerated-networking`이 기본값으로 사용되지 않는 문제 해결

## <a name="february-12-2019"></a>2019년 2월 12일

버전 2.0.58

### <a name="core"></a>핵심

* 업데이트할 수 있는 패키지가 있는 경우 이제 `az --version`에서 알림을 표시합니다.
* JSON 출력에서 `--ids`를 더 이상 사용할 수 없었던 회귀가 수정되었습니다.

### <a name="acr"></a>ACR
* [호환성이 손상되는 변경]`acr build-task` 명령 그룹이 제거되었습니다.
* [호환성이 손상되는 변경]`acr repository delete`에서 `--tag` 및 `--manifest` 옵션이 제거되었습니다.

### <a name="acs"></a>ACS
* 대/소문자를 구분하지 않는 이름에 대한 지원이 `aks [enable-addons|disable-addons]`에 추가되었습니다.
* `aks update-credentials --reset-aad`를 사용하여 Azure Active Directory를 업데이트할 수 있도록 지원합니다.
* `aks get-credentials`에 대한 `--output`은 무시된다는 설명이 추가되었습니다.

### <a name="ams"></a>AMS
* `ams streaming-endpoint [start | stop | create | update] wait` 명령이 추가됨
* `ams live-event [create | start | stop | reset] wait` 명령이 추가됨

### <a name="appservice"></a>App Service
* ACR 컨테이너를 사용하여 함수를 만들고 구성할 수 있는 기능이 추가되었습니다.
* json을 통해 웹앱 구성을 업데이트할 수 있도록 지원합니다.
* `appservice-plan-update`에 대한 도움말이 향상되었습니다.
* App Insights에서 함수 앱을 만들 수 있도록 지원합니다.
* 웹앱 SSH 관련 문제가 해결되었습니다.

### <a name="botservice"></a>Botservice
* `bot publish`에 대한 UX가 향상되었습니다.
* `az bot publish` 중에 `npm install`을 실행할 때 시간 제한에 대한 경고가 추가되었습니다.
* `az bot create`의 `--name`에서 잘못된 `.` 문자가 제거되었습니다.
* Azure Storage, App Service 계획, Function/Web App 및 Application Insights를 만들 때 리소스 이름에 대한 임의 지정을 중지하도록 변경되었습니다.
* [사용 되지 않음]`--proj-file-path`를 위해 `--proj-name` 인수가 사용되지 않음
* 가져온 IIS Node.js 배포 파일이 아직 없으면 `az bot publish`에서 이를 제거하도록 변경되었습니다.
* App Service에서 `node_modules` 폴더를 삭제하지 않도록 `--keep-node-modules` 인수가 `az bot publish`에 추가되었습니다.
* Azure Function 또는 Web App 봇을 만들 때의 `az bot create`의 출력에 `"publishCommand"` 키-값 쌍이 추가되었습니다.
  * `"publishCommand"` 값은 새로 만든 봇을 게시하는 데 필요한 매개 변수가 미리 채워진 `az bot publish` 명령입니다.
* v4 SDK 봇에서 8.9.4 대신 10.14.1을 사용하도록 ARM 템플릿의 `"WEBSITE_NODE_DEFAULT_VERSION"`이 업데이트되었습니다.

### <a name="key-vault"></a>Key Vault
* `--id`를 사용할 때 일부 사용자가 `unexpected_keyword` 오류를 받은 `keyvault secret backup` 관련 문제가 해결되었습니다.

### <a name="monitor"></a>모니터
* `monitor metrics alert [create|update]`에서 `*` 차원 값을 허용하도록 변경되었습니다.

### <a name="network"></a>네트워크
* `dns zone export`에서 내보낸 CNAME이 FQDN인지 확인하도록 변경되었습니다.
* 애플리케이션 게이트웨이 백 엔드 주소 풀을 지원하도록 `--gateway-name` 매개 변수가 `nic ip-config address-pool [add|remove]`에 추가되었습니다.
* Log Analytics 작업 영역을 통해 트래픽을 분석할 수 있도록 `--traffic-analytics` 및 `--workspace` 인수가 `network watcher flow-log configure`에 추가되었습니다.
* `--idle-timeout` 및 `--floating-ip`가 `lb inbound-nat-pool [create|update]`에 추가되었습니다.

### <a name="policy-insights"></a>Policy Insights
* 리소스 정책 업데이트 관리 기능을 지원하는 `policy remediation` 명령이 추가되었습니다.

### <a name="rdbms"></a>RDBMS
* 도움말 메시지 및 명령 매개 변수가 향상되었습니다.

### <a name="redis"></a>Redis
* 방화벽 규칙을 관리하기 위한 명령(create, update, delete, show, list)이 추가되었습니다.
* 서버 링크를 관리하기 위한 명령(create, delete, show, list)이 추가되었습니다.
* 패치 일정을 관리하기 위한 명령(create, update, delete, show)이 추가되었습니다.
* 가용성 영역 및 최소 TLS 버전에 대한 지원이 'redis create'에 추가되었습니다.
* [호환성이 손상되는 변경]`redis update-settings` 및 `redis list-all` 명령이 제거되었습니다.
* [호환성이 손상되는 변경]`redis create`: '테넌트 설정' 매개 변수가 key[=value] 형식으로 허용되지 않습니다.
* [사용 되지 않음]`redis import-method` 명령이 사용되지 않는다는 경고 메시지 추가

### <a name="role"></a>역할
* [호환성이 손상되는 변경]`vm` 명령에서 `az identity` 명령으로 전환되었습니다.

### <a name="sql-vm"></a>SQL VM
* [사용 되지 않음] 오타로 인해 `--boostrap-acc-pwd` 인수가 사용되지 않음

### <a name="vm"></a>VM
* `vm list-skus`에서 `--all true` 대신 `--all`을 사용할 수 있도록 변경되었습니다.
* `vmss run-command [invoke | list | show]`가 추가됨
* 이전에 실행하면 `vmss encryption enable`이 실패했던 버그가 수정되었습니다.
* [호환성이 손상되는 변경]`az identity` 명령이 `role` 명령으로 전환되었습니다.

## <a name="january-31-2019"></a>2019년 1월 31일

버전 2.0.57

### <a name="core"></a>핵심

* [8399 문제](https://github.com/Azure/azure-cli/issues/8399)에 대한 핫 픽스입니다.

## <a name="january-28-2019"></a>2019년 1월 28일

버전 2.0.56

### <a name="acr"></a>ACR
* VNet/IP 규칙에 대한 지원이 추가되었습니다.

### <a name="acs"></a>ACS
* 가상 노드 미리 보기 추가
* Managed OpenShift 명령이 추가되었습니다.
* `aks update-credentials -reset-service-principal`을 사용하여 서비스 주체를 업데이트할 수 있도록 지원합니다.

### <a name="ams"></a>AMS
* [호환성이 손상되는 변경]`ams asset get-streaming-locators`에서 `ams asset list-streaming-locators`로 이름이 변경됨
* [호환성이 손상되는 변경]`ams streaming-locator get-content-keys`에서 `ams streaming-locator list-content-keys`로 이름이 변경됨

### <a name="appservice"></a>App Service
* App Insights에서 `functionapp create`를 지원할 수 있습니다.
* App Service 계획 만들기(탄력성 프리미엄 포함)에 대한 지원이 함수 앱에 추가되었습니다.
* 탄력적 프리미엄 요금제와 관련된 앱 설정 문제가 해결되었습니다.

### <a name="container"></a>컨테이너
* `container start` 명령이 추가됨
* 컨테이너를 만드는 동안 10진수 값을 CPU에 사용할 수 있도록 변경되었습니다.

### <a name="eventgrid"></a>EventGrid
* `--deadletter-endpoint` 매개 변수가 `event-subscription [create|update]`에 추가됨
* storagequeue 및 hybridconnection이 'event-subscription [create|update] --endpoint-type'에 대한 새 값으로 추가되었습니다.
* 이벤트에 대한 재시도 정책을 지정하는 `--max-delivery-attempts` 및 `--event-ttl` 매개 변수가 `event-subscription create`에 추가되었습니다.
* 이벤트 구독에 대상으로 웹후크를 사용하는 경우에 대한 경고 메시지가 `event-subscription [create|update]`에 추가되었습니다.
* 모든 이벤트 구독 관련 명령에 대한 source-resource-id 매개 변수가 추가되었고, 다른 모든 원본 리소스 관련 매개 변수가 더 이상 사용되지 않는 것으로 표시되었습니다.

### <a name="hdinsight"></a>HDInsight
* [호환성이 손상되는 변경]`hdinsight [application] create`에서 `--virtual-network` 및 `--subnet-name` 매개 변수가 제거되었습니다.
* [호환성이 손상되는 변경]`hdinsight create --storage-account`에서 Blob 엔드포인트 대신 스토리지 계정의 이름 또는 ID를 허용하도록 변경되었습니다.
* `hdinsight create`에 `--vnet-name` 및 `--subnet-name` 매개 변수 추가
* Enterprise Security Package 및 디스크 암호화에 대한 지원이 `hdinsight create`에 추가되었습니다. 
* `hdinsight rotate-disk-encryption-key` 명령이 추가됨
* `hdinsight update` 명령이 추가됨

### <a name="iot"></a>IoT
* 인코딩 형식이 routing-endpoint 명령에 추가되었습니다.

### <a name="kusto"></a>Kusto
* 미리 보기 릴리스

### <a name="monitor"></a>모니터
* ID 비교에서 대/소문자를 구분하지 않도록 변경되었습니다.

### <a name="profile"></a>프로필
* `login`에서 관리 서비스 ID에 대한 테넌트 수준 계정을 사용하도록 설정합니다.

### <a name="network"></a>네트워크
* `--bandwidth` 인수가 무시되었던 `express-route update`: 관련 문제가 수정되었습니다.
* 집합 이해력으로 인해 스택 추적이 발생했던 `ddos-protection update` 관련 문제가 수정되었습니다.

### <a name="resource"></a>리소스
* URI 매개 변수 파일에 대한 지원이 `group deployment create`에 추가되었습니다.
* 관리 ID에 대한 지원이 `policy assignment [create|list|show]`에 추가되었습니다.

### <a name="sql-virtual-machine"></a>SQL Virtual Machine
* 미리 보기 릴리스

### <a name="storage"></a>스토리지
* 수정 프로그램을 통해 동일한 개체에서 변경된 속성만 업데이트하도록 변경되었습니다.
* 반환될 때 2진수 데이터가 Base 64로 인코딩되는 #8021 문제가 해결되었습니다.

### <a name="vm"></a>VM
* `vm encryption enable`에서 디스크 암호화 키 자격 증명 모음의 유효성을 검사하고 해당 키 암호화 키 자격 증명 모음이 있는지 확인하도록 변경되었습니다.
* `--force` 플래그가 `vm encryption enable`에 추가되었습니다.

## <a name="january-15-2019"></a>2019년 1월 15일

버전 2.0.55

### <a name="acr"></a>ACR
* 존재하지 않는 helm 차트를 강제로 푸시하도록 변경되었습니다.
* ARM 요청 없이 런타임 작업을 허용하도록 변경되었습니다.
* [사용 되지 않음] 다음 명령의 `--resource-group` 매개 변수가 사용되지 않음:
  * `acr login`
  * `acr repository`
  * `acr helm`

### <a name="acs"></a>ACS
* 새 ACI 지역에 대한 지원이 추가되었습니다.

### <a name="appservice"></a>App Service
* ASE RG 및 App RG가 다른 ASE에서 호스팅되는 앱에 대한 인증서 업로드 관련 문제가 수정되었습니다.
* `webapp up`에서 SKU P1V1을 Linux에 대한 기본값으로 사용하도록 변경되었습니다.
* 배포가 실패하면 `[webapp|functionapp] deployment source config-zip`에서 올바른 오류 메시지를 표시하도록 수정했습니다. 
* `webapp ssh` 명령이 추가됨

### <a name="botservice"></a>Botservice
* 배포 상태 업데이트가 `bot create`에 추가되었습니다.

### <a name="configure"></a>구성
* `none`이 구성 가능한 출력 형식으로 추가되었습니다.

### <a name="cosmosdb"></a>CosmosDB
* 공유 처리량을 사용하여 데이터베이스를 만들 수 있도록 지원합니다.

### <a name="hdinsight"></a>HDInsight
* 애플리케이션 관리 명령이 추가되었습니다.
* 스크립트 작업 관리 명령이 추가되었습니다.
* OMS(Operation Management Suite) 관리 명령이 추가되었습니다.
* 지역별 사용량 나열에 대한 지원이 `hdinsight list-usage`에 추가되었습니다.
* [호환성이 손상되는 변경]`hdinsight create`에서 기본 클러스터 유형이 제거되었습니다.

### <a name="network"></a>네트워크
* `--custom-headers` 및 `--status-code-ranges` 인수를 `traffic-manager profile [create|update]`에 추가
* 새 라우팅 유형으로 Subnet 및 Multivalue가 추가되었습니다.
* `--custom-headers` 및 `--subnets` 인수를 `traffic-manager endpoint [create|update]`에 추가  
* `--vnets ""`를 `ddos-protection update`에 제공함으로써 오류가 발생하는 문제가 해결되었습니다.

### <a name="role"></a>역할
* [사용 되지 않음]`create-for-rbac`에 대한 `--password` 인수가 사용되지 않음 대신 CLI에서 생성된 보안 암호를 사용합니다.

### <a name="security"></a>보안
* 초기 릴리스

### <a name="storage"></a>스토리지
* [호환성이 손상되는 변경]`storage [blob|file|container|share] list`의 기본 결과 수가 5,000개가 되도록 변경되었습니다. 모든 결과를 반환하는 원래 동작에는 `--num-results *`를 사용합니다.
* `--marker` 매개 변수가 `storage [blob|file|container|share] list`에 추가됨
* 다음 페이지에 대한 로그 표식이 `storage [blob|file|container|share] list`의 STDERR에 추가되었습니다. 
* 정적 웹 사이트를 지원하는 `storage blob service-properties update` 명령이 추가되었습니다.

### <a name="vm"></a>VM
* `vm [disk|unmanaged-disk]` 및 `vmss disk`에서 더 일관된 매개 변수를 사용하도록 변경되었습니다.
* `[vm|vmss] create`를 참조하는 테넌트 간 이미지에 대한 지원이 추가되었습니다.
* `vm diagnostics get-default-config --windows-os`에서 기본 구성과 관련된 버그가 수정되었습니다.
* 설정되기 전에 프로비저닝해야 하는 확장을 정의하기 위해 `--provision-after-extensions` 인수가 `vmss extension set`에 추가되었습니다.
* 기본 복제 수를 설정하기 위해 `--replica-count` 인수가 `sig image-version update`에 추가되었습니다.
* 전체 리소스 ID가 제공되는 경우에도 원본 OS 디스크가 동일한 이름의 VM으로 잘못 인식되는 `image create --source`와 관련된 버그가 수정되었습니다.

## <a name="december-20-2018"></a>2018년 12월 20일

버전 2.0.54
### <a name="appservice"></a>App Service
* `webapp up`의 재배포 실패 문제가 해결됨
* 웹앱 스냅샷 목록 및 복원에 대한 지원이 추가됨
* Windows 함수 앱 `--runtime` 플래그에 대한 지원이 추가됨

### <a name="iotcentral"></a>IoTCentral
* 업데이트 명령 API 호출이 수정됨

### <a name="role"></a>역할
* [호환성이 손상되는 변경] 기본적으로 처음 100개의 개체만 목록으로 표시하도록 `ad [app|sp] list`를 변경함

### <a name="sql"></a>SQL
* 관리되는 인스턴스에서의 사용자 지정 데이터 정렬에 대한 지원이 추가됨

### <a name="vm"></a>VM
* `---os-type` 매개 변수가 `disk create`에 추가됨

## <a name="december-18-2018"></a>2018년 12월 18일

버전 2.0.53
### <a name="acr"></a>ACR
* 외부 컨테이너 레지스트리에서 이미지 가져오기에 대한 지원이 추가됨
* 작업 목록의 표 레이아웃을 좁게 축소함
* Azure DevOps URL에 대한 지원이 추가됨

### <a name="acs"></a>ACS
* 가상 노드 미리 보기 추가
* `aks create`에 대한 AAD 인수에서 "(미리 보기)"를 제거함
* [사용 되지 않음]`az acs` 명령이 사용되지 않음 ACS 서비스가 2020년 1월 31일 사용 중지됩니다.
* 새 AKS 클러스터를 만들 때 네트워크 정책에 대한 지원이 추가됨
* 노드 풀이 하나뿐일 경우 `aks scale`에 `--nodepool-name` 인수 요구사항 삭제

### <a name="appservice"></a>App Service
* `webapp config container`에서 `--slot` 매개 변수를 적용할 수 없던 문제 수정

### <a name="botservice"></a>Botservice
* `bot show`를 호출할 때 `.bot` 파일 구문 분석에 대한 지원이 추가됨
* AppInsights 프로비전 버그를 수정함
* 파일 경로를 처리할 때 공백 버그를 수정함
* Kudu 네트워크 호출이 감소함
* 일반 명령 UX 향상

### <a name="consumption"></a>Consumption
* 알림을 표시하도록 예산 API 버그가 수정됨

### <a name="cosmosdb"></a>CosmosDB
* 다중 마스터에서 단일 마스터로의 계정 업데이트에 대한 지원이 추가됨

### <a name="maps"></a>지도
* S1 SKU에 대한 지원이 `maps account [create|update]`에 추가됨

### <a name="network"></a>네트워크
* `--format` 및 `--log-version`에 대한 지원이 `watcher flow-log configure`에 추가됨
* ""을(를) 사용하여 해결과 등록을 지워도 VNet이 작동하지 않을 경우 `dns zone update`을(를) 통해 문제를 해결함

### <a name="resource"></a>리소스
* `policy assignment [create|list|delete|show|update]`에서 관리 그룹에 대한 범위 매개 변수 처리가 수정됨 
* 새 명령 `resource wait`이 추가됨

### <a name="storage"></a>스토리지
*  스토리지 서비스를 위한 로그 스키마 버전 업데이트 기능이 `storage logging update`에 추가됨

### <a name="vm"></a>VM
* 지정된 vm에 할당된 관리 서비스가 없는 경우 `vm identity remove`에서 크래시가 해결됨

## <a name="december-4-2018"></a>2018년 12월 4일

버전 2.0.52
### <a name="core"></a>핵심
* 다중 테넌트 서비스 주체에 대한 교차 테넌트 리소스 프로 비전에 대한 지원 추가
* tsv 출력을 사용한 명령에서 파이프된 id의 구문 분석이 잘못되는 버그 수정

### <a name="appservice"></a>App Service
* [미리 보기] 애플리케이션에 콘텐츠 생성 및 배포를 돕는 `webapp up` 명령 추가
* 백 엔드 변경으로 인해 컨테이너 기반의 Windows 앱에서 버그 수정

### <a name="network"></a>네트워크
* WAF 제외를 지원하기 위해 `--exclusion` 인수를 `application-gateway waf-config set`에 추가

### <a name="role"></a>역할
* 암호 자격 증명을 위해 사용자 지정 식별자에 대해 지원 추가 

### <a name="vm"></a>VM
* [사용 되지 않음]`vm extension [show|wait] --expand` 매개 변수가 사용되지 않음
* 응답 없는 VM을 다시 배포하기 위해 `--force` 매개 변수를 `vm restart`에 추가
* 암호와 SSH 인증을 사용하여 VM을 생성하기 위해 "all"을 허용하도록 `[vm|vmss] create --authentication-type` 변경
* 이미지에 OS 디스크 캐싱을 설정하기 위해 `image create --os-disk-caching` 매개 변수 추가

## <a name="november-20-2018"></a>2018년 11월 20일

Version 2.0.51
### <a name="core"></a>핵심
* ID에서 구독 이름을 재사용하지 않도록 MSI 로그인 변경

### <a name="acr"></a>ACR
* 작업 단계에 대해 컨텍스트 토큰 추가
* acr 작업을 미러링하도록 acr에서 비밀을 설정하는 것에 대한 지원 추가
* `show-tags` 및 `show-manifests` 명령에 대한 `--top` 및 `--orderby`에 대한 지원 향상

### <a name="appservice"></a>App Service
* zip 배포 기본 시간 제한을 변경하여 해당 상태에 대한 폴링이 5 분으로 증가하고 시간 제한 속성을 추가하여 이 값을 사용자 지정합니다.
* 기본값을 `node_version`으로 업데이트 했습니다. 2단계 스왑 중에 슬롯 스왑 동작을 재설정하면 모든 appsettings 및 연결 문자열이 보존됩니다.
* Linux App Service 계획 만들기에 대한 클라이언트 쪽 SKU 확인 제거
* Zipdeploy 상태를 가져오기 하려고 할 때의 오류가 수정됨

### <a name="iotcentral"></a>IotCentral
* IoT Central 애플리케이션을 만들 때 하위 도메인 가용성 확인 추가

### <a name="keyvault"></a>KeyVault
* 오류가 무시되었을 수 있는 버그 수정

### <a name="network"></a>네트워크
* 신뢰할 수 있는 루트 인증서를 처리하기 위해 `root-cert` 하위 명령을 `application-gateway`에 추가
* `--min-capacity` 및 `--custom-error-pages` 옵션을 `application-gateway [create|update]`에 추가:
* `application-gateway create`에 가용성 영역 지원을 위해 `--zones` 추가 
* `application-gateway waf-config set`에 추가된 인수: `--file-upload-limit`, `--max-request-body-size`, `--request-body-check`

### <a name="rdbms"></a>Rdbms
* mariadb vnet 명령 추가

### <a name="rbac"></a>Rbac
* `ad app update`에서 변경할 수 없는 자격 증명을 업데이트 하려는 시도 관련 문제 해결
* `ad [app|sp] list`에 대한 가까운 장래의 호환성이 손상되는 변경을 알리는 출력 경고가 추가되었습니다. 

### <a name="storage"></a>스토리지
* 스토리지 복사 명령에 대한 코너 케이스의 처리가 향상됨
* 대상 계정과 원본 계정이 같을 때 로그인 자격 증명을 사용하지 않는 `storage blob copy start-batch` 문제가 해결되었습니다.
* `sas_token`이 URL에 통합되지 않은 `storage [blob|file] url`의 버그 수정
* `[blob|container] list`에 호환성이 손상되는 변경 경고가 추가됨: 기본적으로 처음 5000개의 결과만 출력됩니다.

### <a name="vm"></a>VM
* 관리되는 OS 및 데이터 디스크에 대한 스토리지 계정 SKU를 별도로 지정하도록 `[vm|vmss] create --storage-sku`에 대한 지원이 추가되었습니다.
* `sig image-version`에 대한 버전 이름 매개 변수를 `--image-version -e`가 되도록 변경
* `--image-version-name`에 대한 `sig image-version` 인수가 사용되지 않으며 `--image-version`으로 대체됨
* `[vm|vmss] create --ephemeral-os-disk`에 로컬 OS 디스크를 사용하는 지원이 추가됨
* `--no-wait`에 대한 지원이 `snapshot create/update`에 추가됨
* `snapshot wait` 명령이 추가됨
* `[vm|vmss] extension set --extension-instance-name` 인스턴스 이름을 사용하는 것에 대한 지원 추가

## <a name="november-6-2018"></a>2018년 11월 6일

버전 2.0.50

### <a name="core"></a>핵심
* 서비스 주체 sn+issuer auth에 대한 지원이 추가됨

### <a name="acr"></a>ACR
* 작업 소스 트리거에 대한 커밋 및 끌어오기 요청 git 이벤트에 대한 지원이 추가됨
* 빌드 명령에서 기본 Dockerfile이 지정되지 않은 경우 기본 Dockerfile을 사용하도록 변경됨

### <a name="acs"></a>ACS
* [호환성이 손상되는 변경] 기본적으로 'az aks browse'을 기본적으로 사용하기 위해 `enable_cloud_console_aks_browse` 제거됨

### <a name="advisor"></a>Advisor
* GA 릴리스

### <a name="ams"></a>AMS
* 새 명령 그룹이 추가됨:
  *  `ams account-filter`
  *  `ams asset-filter`
  *  `ams content-key-policy`
  *  `ams live-event`
  *  `ams live-output`
  *  `ams streaming-endpoint`
  *  `ams mru`
* 새 명령이 추가됨:
  * `ams account check-name`
  * `ams job update`
  * `ams asset get-encryption-key`
  * `ams asset get-streaming-locators`
  * `ams streaming-locator get-content-keys`
* 암호화 매개 변수 지원이 `ams streaming-policy create`에 추가됨
* 이제 제거할 출력 인덱스를 전달하여 `ams transform output remove`에 대한 추가 지원을 수행할 수 있음
* `ams job` 명령 그룹에 `--correlation-data` 및 `--label` 인수가 추가됨
* `ams asset` 명령 그룹에 `--storage-account` 및 `--container` 인수가 추가됨
* `ams asset get-sas-url` 명령에서 만료 시간(현재+23h) 및 사용 권한(읽기)의 기본값이 추가됨 
* [호환성이 손상되는 변경]`ams streaming locator` 명령이 `ams streaming-locator`로 대체됨
* [호환성이 손상되는 변경]`ams streaming locator`의 `--content-keys` 인수가 업데이트됨
* [호환성이 손상되는 변경]`ams streaming locator` 명령에서 `--content-policy-name`에서 `--content-key-policy-name`로 이름이 변경됨
* [호환성이 손상되는 변경]`ams streaming policy` 명령이 `ams streaming-policy`로 대체됨
* [호환성이 손상되는 변경]`ams transform` 명령 그룹에서 `--preset-names` 인수가 `--preset`으로 대체됨 이제 한 번에 1개의 출력/사전 설정만 설정할 수 있습니다(더 추가하려면 `ams transform output add`를 실행해야 함). 또한 사용자 정의 JSON에 경로를 전달하여 사용자 정의 StandardEncoderPreset을 설정할 수 있습니다
* [호환성이 손상되는 변경]`ams job start` 명령에서 `--output-asset-names `에서 `--output-assets`로 이름이 변경됨 이제 'assetName = label' 형식으로 공백으로 구분된 자산 목록을 허용합니다. 레이블이 없는 자산은 'assetName='과 같이 보낼 수 있습니다.

### <a name="appservice"></a>AppService
* 백업 스케쥴이 아직 설정되지 않은 경우 백업 스케쥴 설정을 방해하는 `az webapp config backup update`의 버그가 수정됨

### <a name="configure"></a>구성
* 출력 형식 옵션에 YAML이 추가됨

### <a name="container"></a>컨테이너
* yaml에 컨테이너 그룹을 내보낼 때 ID를 표시하도록 변경됨

### <a name="eventhub"></a>EventHub
* `eventhub namespace [create|update]`에서 Kafka를 지원하기 위해 `--enable-kafka` 플래그가 추가됨

### <a name="interactive"></a>대화형
* 이제 대화형이 `interactive` 확장을 설치하여 업데이트 및 지원이 더 빨라집니다.

### <a name="monitor"></a>모니터
* `monitor metrics alert [create|update]`의 `--condition`에 슬래시(/)와 마침표(.) 문자를 포함하는 메트릭 이름에 대한 지원이 추가됨

### <a name="network"></a>네트워크
* `network private-endpoint`를 위해 `network interface-endpoint` 명령 이름 사용되지 않음
* `express-route peering connection create`의 `--peer-circuit` 인수가 ID를 허용하지 않는 문제가 해결됨
* `--ip-tags`가 `public-ip create`와 함께 제대로 작동하지 않는 문제가 해결됨 

### <a name="profile"></a>프로필
* cert auto-rolls로 서비스 주체 로그인을 위해 `--use-cert-sn-issuer`가 `az login`에 추가됨

### <a name="rdbms"></a>RDBMS
* mysql 복제본 명령 추가됨

### <a name="resource"></a>리소스
* 관리 그룹 및 구독에 대한 지원이 `policy definition|set-definition` 명령에 추가됨

### <a name="role"></a>역할
* API 권한 관리, 로그인 사용자 및 애플리케이션 암호 및 인증서 자격 증명 관리에 대한 지원이 추가됨
* displayName과 서비스 주체 이름 간의 혼동을 방지하기 위해 `ad sp create-for-rbac`을 변경함
* AAD 앱에 권한을 부여하는 지원이 추가됨

### <a name="storage"></a>스토리지
* `Configure Azure Storage connection strings <https://docs.microsoft.com/azure/storage/common/storage-configure-connection-string>`에 설명된 대로 SAS 및 엔드포인트(계정 이름 또는 키 없이)로만 스토리지 서비스에 연결하는 지원이 추가됨

### <a name="vm"></a>VM
* 이미지의 기본 스토리지 계정 유형 설정을 위해 `image create`에 `storage-sku` 인수가 추가됨
* `vm resize`가 `--no-wait` 옵션으로 인해 명령이 충돌하는 버그가 수정됨
* `vm encryption show` 테이블 출력 형식을 상태 표시로 변경됨
* `vm secret format`이 json/jsonc 출력을 요구하도록 변경됨 원하지 않는 출력 형식이 선택되면 사용자에게 경고하고 json을 기본값으로 출력
* `vm create --image`에 대한 인수 유효성 검사가 개선됨

## <a name="october-23-2018"></a>2018년 10월 23일

버전 2.0.49

### <a name="core"></a>핵심
* `--subscription`이 `--ids`의 구독보다 우선적으로 적용되는 `--ids` 관련 문제가 해결되었습니다.
* `--ids`를 사용하여 매개 변수가 무시되는 경우 명시적 경고가 추가되었습니다.

### <a name="acr"></a>ACR
* Python2에서 ACR Build 인코딩 문제가 해결되었습니다.

### <a name="cdn"></a>CDN
* [호환성이 손상되는 변경] "IgnoreQueryString"를 더 이상 기본값으로 설정하지 않도록 `cdn endpoint create`의 기본 쿼리 문자열 캐싱 동작이 변경되었습니다. 이제 서비스에 의해 설정됩니다.

### <a name="container"></a>컨테이너
* '--ip-address'를 전달하기 위해 `Private`이 올바른 유형으로 추가되었습니다.
* 컨테이너 그룹에 대한 가상 네트워크를 설정하기 위해 서브넷 ID만 사용할 수 있도록 변경되었습니다.
* 다른 리소스 그룹의 VNet을 사용하기 위해 VNet 이름 또는 리소스 ID를 사용할 수 있도록 변경되었습니다.
* MSI ID를 컨테이너 그룹에 추가하기 위해 `--assign-identity`가 추가되었습니다.
* 시스템 할당 MSI ID에 대한 역할 할당을 만들기 위해 `--scope`가 추가되었습니다.
* 장기 실행 프로세스 없이 이미지를 사용하여 컨테이너 그룹을 만들 때 경고가 추가되었습니다.
* `list` 및 `show` 명령에 대한 테이블 출력 문제가 해결되었습니다.

### <a name="cosmosdb"></a>CosmosDB
* `cosmosdb create`에 `--enable-multiple-write-locations` 지원이 추가됨

### <a name="interactive"></a>대화형
* 글로벌 구독 매개 변수가 매개 변수에서 나타나는지 확인하도록 변경되었습니다.

### <a name="iot-central"></a>IoT Central
* IoT Central 애플리케이션 생성을 위해 템플릿 및 표시 이름 옵션이 추가되었습니다.
* [호환성이 손상되는 변경] F1 SKU에 대한 지원이 제거되었습니다. 대신 S1 SKU를 사용합니다.

### <a name="monitor"></a>모니터
* `monitor activity-log list`에 대한 변경 내용:
  * 구독 수준에서 모든 이벤트를 나열하는 지원이 추가되었습니다.
  * 시간 쿼리를 더 쉽게 만들기 위해 `--offset` 매개 변수가 추가되었습니다.
  * 넓은 범위의 ISO8601 형식 및 사용자에게 친숙한 날짜/시간 서식을 사용하기 위해 `--start-time` 및 `--end-time`에 대한 유효성 검사가 개선되었습니다.
  * `--namespace`가 사용되지 않는 옵션 `--resource-provider`에 대한 별칭으로 추가되었습니다.
  * 강력한 형식의 옵션이 포함되지 않은 값이 서비스에서 지원되지 않으므로 `--filters`가 사용되지 않습니다.
* `monitor metrics list`에 대한 변경 내용:
  * 시간 쿼리를 더 쉽게 만들기 위해 `--offset` 매개 변수가 추가되었습니다.
  * 넓은 범위의 ISO8601 형식 및 사용자에게 친숙한 날짜/시간 서식을 사용하기 위해 `--start-time` 및 `--end-time`에 대한 유효성 검사가 개선되었습니다.
* `monitor diagnostic-settings create`을 위한 `--event-hub` 및 `--event-hub-rule` 인수에 대한 유효성 검사가 개선되었습니다.

### <a name="network"></a>네트워크
* NIC에 애플리케이션 게이트웨이 백 엔드 주소 풀을 추가하도록 지원하기 위해 `--app-gateway-address-pools` 및 `--gateway-name` 인수가 `nic create`에 추가되었습니다.
* NIC에 애플리케이션 게이트웨이 백 엔드 주소 풀을 추가하도록 지원하기 위해 `--app-gateway-address-pools` 및 `--gateway-name` 인수가 `nic ip-config create/update`에 추가되었습니다.

### <a name="servicebus"></a>ServiceBus
* 현재 Service Bus Standard를 Premium 네스페이스 마이그레이션 상태로 표시하기 위해 읽기 전용 `migration_state`가 MigrationConfigProperties에 추가되었습니다.

### <a name="sql"></a>SQL
* 수동 장애 조치 정책을 사용하기 위해 `sql failover-group create` 및 `sql failover-group update`가 수정되었습니다.

### <a name="storage"></a>스토리지
* 모든 항목이 올바른 "서비스" 키를 표시하도록 `az storage cors list` 출력 서식 지정이 수정되었습니다.
* 불변성 정책 차단 컨테이너를 삭제하기 위해 `--bypass-immutability-policy` 매개 변수가 추가되었습니다.

### <a name="vm"></a>VM
* `[vm|vmss] create`에서 머신의 Lv/Lv2 시리즈에 대해 디스크 캐싱 모드가 `None`이 되도록 적용합니다.
* `vm create`에 대해 지원되는 크기 목록 지원 네트워킹 가속기가 업데이트되었습니다.
* `disk create`에서 ultrassd iops 및 mbps configs에 대한 강력한 형식 인수가 추가되었습니다.

## <a name="october-16-2018"></a>2018년 10월 16일

버전 2.0.48

### <a name="vm"></a>VM
* Homebrew 설치가 실패하게 된 SDK 문제가 해결되었습니다.

## <a name="october-9-2018"></a>2018년 10월 9일

버전 2.0.47

### <a name="core"></a>핵심
* "잘못된 요청" 오류의 오류 처리를 향상

### <a name="acr"></a>ACR
* helm 클라이언트와 유사한 테이블 형식에 대한 지원 추가

### <a name="acs"></a>ACS
* `aks [create|scale] --nodepool-name`을 추가하여 nodepool 이름 구성, 12 문자로 잘림, 기본값 - nodepool1 
* Parimiko가 실패할 때 'scp'로 되돌아가도록 수정
* `aks create`가 `--aad-tenant-id`를 요구하지 않도록 변경
* 중복된 항목이 있을 때 Kubernetes 자격 증명에 대한 병합 향상

### <a name="container"></a>컨테이너
* 특정 런타임을 사용하여 Linux 소비 계획 형식 만들기를 지원하도록 `functionapp create` 변경
* [미리 보기] Windows 컨테이너에 웹앱을 호스트하기 위한 지원 추가

### <a name="event-hub"></a>이벤트 허브
* `eventhub update` 명령 수정됨
* [호환성이 손상되는 변경] 빈 목록을 표시하는 대신 일반적인 방법으로 resource(s) NotFound(404)에 대한 오류를 처리하도록 `list` 명령을 변경

### <a name="extensions"></a>확장
* 이미 설치되어 있는 확장을 추가하려고 시도할 때의 문제 해결

### <a name="hdinsight"></a>HDInsight
* 초기 릴리스

### <a name="iot"></a>IoT
* 첫 번째 실행 배너에 확장 설치 명령 추가

### <a name="keyvault"></a>KeyVault
* 최신 API 프로필에 keyvault 스토리지 명령을 제한하도록 변경

### <a name="network"></a>네트워크
* `network dns zone create` 수정됨: 사용자가 기본 위치를 구성한 경우에도 명령은 성공합니다. #6052 참조
* `network vnet peering create`에 `--remote-vnet-id`이 사용되지 않도록 함
* 이름 또는 ID를 허용하는 `network vnet peering create`에 `--remote-vnet` 추가
* 서브넷에서 `--subnet-prefixes`를 사용하는 여러 주소 접두사에 대한 지원이 `network vnet create`에 추가되었습니다.
* 서브넷에서 `--address-prefixes`를 사용하는 여러 주소 접두사에 대한 지원이 `network vnet subnet [create|update]`에 추가되었습니다.
* `WAF_v2` 또는 `Standard_v2` SKU로 게이트웨이를 만들지 못하던 `network application-gateway create` 문제가 해결되었습니다.
* `--service-endpoint-policy` 편의 인수가 `network vnet subnet update`에 추가되었습니다.

### <a name="role"></a>역할
* `ad app owner`에 Azure AD 앱 소유자를 나열하기 위한 지원이 추가되었습니다.
* `ad sp owner`에 Azure AD 서비스 주체 소유자를 나열하기 위한 지원이 추가되었습니다.
* 역할 정의 작성 및 업데이트 명령이 여러 권한 구성을 허용하도록 변경되었습니다.
* 홈 페이지 URI가 항상 "https"가 되도록 `ad sp create-for-rbac`가 변경되었습니다.

### <a name="service-bus"></a>Service Bus
* [호환성이 손상되는 변경] 빈 목록을 표시하는 대신 일반적인 방법으로 resource(s) NotFound(404)에 대한 오류를 처리하도록 `list` 명령을 변경

### <a name="vm"></a>VM
* `disk grant-access` 내 빈 `accessSas` 수정
* 오버프로비저닝을 처리하기 위해 충분히 큰 프런트 엔드 포트 범위를 예약하도록 `vmss create`를 변경했습니다.
* `sig`에 대한 업데이트 명령을 수정했습니다.
* `sig`에 이미지 버전 관리에 대한 `--no-wait` 지원이 추가되었습니다.
* 공용 IP 주소 가용성 영역을 표시하도록 `vm list-ip-addresses`가 변경되었습니다.
* 디스크의 기본 lun을 첫 번째 사용 가능한 지점으로 설정하도록 `[vm|vmss] disk attach`를 변경했습니다.

## <a name="september-21-2018"></a>2018년 9월 21일

버전 2.0.46

### <a name="acr"></a>ACR
* ACR 작업 명령 추가
* 빠른 실행 명령 추가
* `build-task` 명령 그룹 사용되지 않음
* ACR에서 Helm 차트 관리를 지원하기 위해 `helm` 명령 그룹 추가
* 관리되는 레지스트리의 명등원 만들기를 위한 지원 추가
* 빌드 로그 표시를 위한 비형식 플래그 추가

### <a name="acs"></a>ACS
* AKS 마스터 FQDN 설정을 위한 `install-connector` 명령 변경
* 서비스 주체 및 skip-role-assignemnt를 지정하지 않은 경우의 vnet-subnet-id에 대한 역할 할당 만들기 수정

### <a name="appservice"></a>AppService

* 웹 작업(연속 및 트리거) 작업 관리 지원 추가
* az webapp config set 지원 --fts-state 속성. functionapp config set 및 show 지원 추가
* 웹앱에 대한 Bring Your Own Storage 지원 추가
* 삭제된 웹앱 나열 및 복원을 위한 지원 추가

### <a name="batch"></a>Batch
* AddTaskCollectionParameter 구문 지원을 위해 `--json-file`을 통한 작업 추가 변경
* 수락된 `--json-file` 형식에 대한 설명서 업데이트
* `batch pool create`에 `--max-tasks-per-node-option` 추가
* 옵션이 지정되지 않은 경우 현재 로그인된 계정을 표시하도록 `batch account` 동작 변경

### <a name="batch-ai"></a>Batch AI 
* `batchai cluster create` 명령에서 자동 스토리지 계정 만들기 오류 해결

### <a name="cognitive-services"></a>Cognitive Services
* `--sku`, `--kind`, `--location` 인수에 대한 완료자 추가
* 명령 `cognitiveservices account list-usage` 추가됨
* 명령 `cognitiveservices account list-kinds` 추가됨
* 명령 `cognitiveservices account list` 추가됨
* `cognitiveservices list` 사용되지 않음
* `cognitiveservices account list-skus`에 대해 선택 사항으로 `--name` 변경

### <a name="container"></a>컨테이너
* 실행 중인 컨테이너 그룹 다시 시작 및 중지 기능 추가
* 네트워크 프로필 전달을 위한 `--network-profile` 추가
* VNET에서 컨테이너 그룹 생성을 허용하도록 `--subnet`, `--vnet_name` 추가
* 컨테이너 그룹의 상태를 표시하도록 테이블 출력 변경

### <a name="datalake"></a>Datalake
* 가상 네트워크 규칙에 대한 명령 추가

### <a name="interactive-shell"></a>대화형 셸
* 명령 실행이 실패하는 Windows 오류 해결
* 사용되지 않는 개체로 인해 발생하는 대화식 명령 로드 문제 해결

### <a name="iot"></a>IoT
* IoT 허브 라우팅을 위한 지원 추가

### <a name="key-vault"></a>Key Vault
* RSA 키에 대한 Key Vault 키 가져오기 수정

### <a name="network"></a>네트워크
* 공용 IP 접두사 기능을 지원하기 위한 `network public-ip prefix` 명령 추가
* 서비스 엔드포인트 정책 기능을 지원하기 위한 `network service-endpoint` 명령 추가
* 표준 Load Balancer 아웃바운드 규칙 생성을 지원하기 위한 `network lb outbound-rule` 명령 추가
* 공용 IP 접두사를 사용한 프런트 엔드 IP 구성 지원을 위해 `network lb frontend-ip create/update`에 `--public-ip-prefix` 추가
* `network lb rule/inbound-nat-rule/inbound-nat-pool create/update`에 `--enable-tcp-reset` 추가
* `network lb rule create/update`에 `--disable-outbound-snat` 추가
* 클래식 NSG에 `network watcher flow-log show/configure` 사용 허용
* `network watcher run-configuration-diagnostic` 명령 추가
* `network watcher test-connectivity` 명령 수정 및 `--method`, `--valid-status-codes` 및 `--headers` 속성 추가
* `network express-route create/update`: `--allow-global-reach` 플래그 추가
* `network vnet subnet create/update`: `--delegation`에 대한 지원 추가
* `network vnet subnet list-available-delegations` 명령이 추가됨
* `network traffic-manager profile create/update`: 모니터 구성을 위해 `--interval`, `--timeout`, `--max-failures`에 대한 지원이 추가됨 `--path`, `--port`, `--protocol`을(를) 위해 `--monitor-path`, `--monitor-port`, `--monitor-protocol` 옵션은 사용되지 않음
* `network lb frontend-ip create/update`: 프라이빗 IP 할당 방법을 설정하는 논리가 수정됨. 개인 IP 주소가 제공되는 경우 정적 할당이 설정됨. 개인 IP 주소가 제공되지 않는 경우나 개인 IP 주소에 빈 문자열이 주어질 경우 동적 할당이 설정됨.
* `dns record-set * create/update`: `--target-resource`에 대한 지원 추가
* 인터페이스 엔드포인트 개체를 쿼리하기 위한 `network interface-endpoint` 명령 추가
* 네트워크 프로필의 부분 관리를 위한 `network profile show/list/delete` 추가
* ExpressRoute 간 피어링 연결을 관리하기 위한 `network express-route peering connection` 명령 추가

### <a name="rdbms"></a>RDBMS
* MariaDB 서비스 지원 추가

### <a name="reservation"></a>예약
* 예약된 리소스 열거형 형식에 CosmosDb 추가
* 패치 모델에서 이름 속성 추가

### <a name="manage-app"></a>앱 관리
* 마켓플레이스 관리 앱의 인스턴스 생성이 충돌하는 `managedapp create --kind MarketPlace` 버그 해결
* 지원되는 프로필로 제한되도록 `feature` 명령 변경

### <a name="role"></a>역할
* 사용자 그룹 멤버 자격을 나열하기 위한 지원 추가

### <a name="signalr"></a>SignalR
* 첫번째 릴리스

### <a name="storage"></a>스토리지
* blob 및 큐 권한 부여에 대한 사용자 로그자인 격 증명 사용을 위해 `--auth-mode login` 매개 변수 추가
* 변경할 수 없는 스토리지 관리를 위한 `storage container immutability-policy/legal-hold` 추가

### <a name="vm"></a>VM
* 공개 키 파일이 누락된 경우 `vm create --generate-ssh-keys`가 프라이빗 키 파일을 덮어쓰는 문제 해결(#4725, #6780)
* `az sig`를 통한 공유 이미지 갤러리에 대한 지원 추가

## <a name="august-28-2018"></a>2018년 8월 28일

Version 2.0.45

### <a name="core"></a>핵심

* 빈 구성 파일을 로드하는 문제 해결
* Azure Stack에 대해 `2018-03-01-hybrid` 프로필에 대한 지원 추가

### <a name="acr"></a>ACR

* ARM 요청 없이 런타임 작업에 대한 해결 방법 추가
* 기본적으로 `build` 명령에서 버전 제어 파일 (예:.git,.gitignore)을 업로드된 tar에서 제외하도록 변경

### <a name="acs"></a>ACS

* `aks create`의 기본값을 `Standard_DS2_v2` VM으로 변경
* 이제 새 api를 호출하여 클러스터 자격 증명을 가져오도록 `aks get-credentials` 변경

### <a name="appservice"></a>AppService

* Functionapp 및 Webapp에서 CORS 지원 추가
* 명령 생성에 대한 ARM 태그 지원이 추가되었습니다.
* 누락된 리소스 발생 시 코드 3을 사용하여 종료하도록 `[webapp|functionapp] identity show` 변경

### <a name="backup"></a>Backup

* 누락된 리소스 발생 시 코드 3을 사용하여 종료하도록 `backup vault backup-properties show` 변경

### <a name="bot-service"></a>Bot Service

* 초기 Bot Service CLI 릴리스

### <a name="cognitive-services"></a>Cognitive Services

* 일부 서비스를 만드는 데 필요한 새 매개 변수 `--api-properties,` 추가

### <a name="iot"></a>IoT

* 연결된 허브 연관 문제 해결

### <a name="monitor"></a>모니터

* 근 실시간 메트릭 경고에 대한 `monitor metrics alert` 명령 추가
* 사용되지 않는 `monitor alert` 명령

### <a name="network"></a>네트워크

* 누락된 리소스 발생 시 코드 3을 사용하여 종료하도록 `network application-gateway ssl-policy predefined show` 변경

### <a name="resource"></a>리소스

* 누락된 리소스 발생 시 코드 3을 사용하여 종료하도록 `provider operation show` 변경

### <a name="storage"></a>스토리지

* 누락된 리소스 발생 시 코드 3을 사용하여 종료하도록 `storage share policy show` 변경

### <a name="vm"></a>VM

* 누락된 리소스 발생 시 코드 3을 사용하여 종료하도록 `vm/vmss identity show` 변경 
* `vm create`에 `--storage-caching`이 사용되지 않도록 함

## <a name="auguest-14-2018"></a>2018년 8월 14일

Version 2.0.44

### <a name="core"></a>핵심

* `table` 출력에 숫자 표시 해결
* 추가된 YAML 출력 형식

### <a name="telemetry"></a>원격 분석

* 향상된 원격 분석 보고

### <a name="acr"></a>ACR

* `content-trust policy` 명령이 추가됨
* `.dockerignore`가 제대로 처리되지 않는 문제 해결

### <a name="acs"></a>ACS

* Windows에서 `%USERPROFILE%\.azure-kubectl` 하에서 설치하도록 `az acs/aks install-cli` 변경
* 클러스터에 RBAC가 있는지 감지하여 ACI 커넥터를 적절하게 구성하도록 `az aks install-connector` 변경
* 제공되는 서브넷에 대한 역할 할당 변경
* 서브넷에 대해 제공하는 경우 "역할 할당 건너뛰기" 새 옵션 추가                                 
* 할당이 이미 있는 경우 서브넷으로의 역할 할당을 건너뛸 수 있도록 변경                

### <a name="appservice"></a>AppService

* 외부 리소스 그룹에 스토리지 계정을 사용하여 함수 앱을 만들지 못하도록 하는 버그가 수정됨
* Zip 배포 충돌을 해결

### <a name="batchai"></a>BatchAI

* 자동 스토리지 계정 만들기가 &quot;리소스 *그룹*&quot;을 지정하도록 로거 출력 변경        

### <a name="container"></a>컨테이너

* 보안 환경 변수 전달을 위해 컨테이너에 `--secure-environment-variables` 추가      

### <a name="iot"></a>IoT

* [호환성이 손상되는 변경] 사용되지 않는 명령을 제거하여 iot 확장으로 이동
* `azure-devices.net` 도메인을 가정하지 않도록 요소를 업데이트

### <a name="iot-central"></a>Iot Central

* IoT Central 모듈의 초기 릴리스

### <a name="keyvault"></a>KeyVault


* 스토리지 계정 및 sas 정의를 관리하는 것에 대한 명령이 추가됨
* 네트워크 규칙에 대한 명령이 추가됨                                                           
* 비밀, 키 및 인증서 작업에 `--id` 매개 변수를 추가
* KV mgmt 다중 api 버전에 대한 지원 추가
* KV 데이터 평면 다중 api 버전에 대한 지원 추가

### <a name="relay"></a>릴레이

* 초기 릴리스

### <a name="sql"></a>Sql

* `sql failover-group` 명령이 추가됨

### <a name="storage"></a>스토리지

* [호환성이 손상되는 변경]`--location` 매개 변수를 요구하도록 `storage account show-usage`를 변경하여 지역에 따라 나열됨
* `--resource-group` 매개 변수가 `storage account` 명령에 대해 선택 사항이 되도록 변경
* 단일 집계된 메시지에 대한 일괄 처리 명령에서 개별 오류에 대한 '전제 조건 실패’ 경고를 제거
* `[blob|file] delete-batch` 명령을 변경하여 더 이상 null 배열을 출력하지 않도록 함
* 컨테이너 url에서 sas 토큰을 읽도록 `blob [download|upload|delete-batch]` 명령 변경

### <a name="vm"></a>VM

* 사용 편의성을 위해 일반 필터를 `vm list-skus`에 추가

## <a name="july-31-2018"></a>2018년 7월 31일

Version 2.0.43

### <a name="acr"></a>ACR

* `--with-secure-properties` 플래그를 `acr build-task show` 명령에 추가
* `acr build-task update-build` 명령이 추가됨

### <a name="acs"></a>ACS

* `az aks browse`를 종료할 때 [Ctrl + C]를 눌러 return 0(성공)을 반환하도록 변경

### <a name="batch"></a>Batch

* cloudshell에서 AAD 토큰을 보여줄 때의 버그가 수정됨

### <a name="container"></a>컨테이너

* 집합 구독에서 이름 또는ID에 대한 `--log-analytics-workspace-key` 요구 사항이 제거됨

### <a name="network"></a>네트워크

* Azure Stack에 대해 2017-03-09-profile에 dns 지원 추가 

### <a name="resource"></a>리소스

* `group deployment create`에 `--rollback-on-error`를 추가하여 오류 시 성공한 배포를 실행하도록 함
* `group deployment create`를 사용하여 `--parameters {}`에서 오류가 발생하는 문제를 해결

### <a name="role"></a>역할

* 스택 프로필 2017-03-09-profile에 대한 지원 추가
* `app update`에 다한 제네릭 업데이트 매개 변수가 올바르게 작동하지 않는 문제 해결

### <a name="search"></a>검색

* Azure Search 서비스에 대한 명령 추가

### <a name="service-bus"></a>Service Bus

* Service Bus 표준에서 프리미엄으로 네임 스페이스를 마이그레이션하는 추가 마이그레이션 명령 그룹이 추가됨
* Service Bus 큐 및 구독에 새로운 선택적 속성이 추가됨
  *  `queue` 내 `--enable-batched-operations` 및 `--enable-dead-lettering-on-message-expiration`
  *  `subscriptions` 내 `--dead-letter-on-filter-exceptions`

### <a name="storage"></a>스토리지

* 단일 연결을 사용하는 대용량 파일 다운로드에 대한 지원 추가
* 리소스 누락으로 종료 코드 3으로 실패할 때 누락되었던 `show` 명령 변환

### <a name="vm"></a>VM

* 구독 별로 가용성 집합을 리스팅하도록 지원 추가
* `StandardSSD_LRS`에 대한 지원이 추가됨
* VM 확장 집합 생성 시 애플리케이션 보안 그룹에 대한 지원이 추가됨
* [호환성이 손상되는 변경]`[vm|vmss] create`, `[vm|vmss] identity assign`, 및 `[vm|vmss] identity remove`를 사전 형식으로 사용자 할당 ID를 출력하도록 변경

## <a name="july-18-2018"></a>2018년 7월 18일

버전 2.0.42

### <a name="core"></a>핵심

* WSL bash 창에서 브라우저 기반 로그인에 대한 지원 추가
* 모든 일반 업데이트 명령에 `--force-string` 플래그 추가
* [호환성이 손상되는 변경] '표시' 명령이 리소스 누락 시 오류 메시지를 기록하고 종료 코드 3과 함께 실패하도록 변경됨

### <a name="acr"></a>ACR

* [호환성이 손상되는 변경] '--no-push'를 'acr 빌드' 명령에서 순수 플래그로 업데이트
* `show` 및 `update` 명령이 `acr repository` 그룹 아래 추가됨
* 세부 정보를 표시 하기 위해 `--detail` 플래그를 `show-manifests` 및 `show-tags`에 대해 추가
* `--image` 매개 변수를 이미지로 빌드 세부 사항이나 로그를 얻을 수 있도록 지원하기 위해 추가

### <a name="acs"></a>ACS

* `--max-pods`가 5보다 작은 경우 오류가 발생하도록 `az aks create`을 변경

### <a name="appservice"></a>AppService

* PremiumV2 sku에 대한 지원 추가

### <a name="batch"></a>Batch

* 클라우드 셸 모드에서 토큰 자격 증명을 사용할 때의 버그 수정
* JSON 입력을 대/소문자를 구분하지 않도록 변경

### <a name="batch-ai"></a>Batch AI

* `az batchai job exec` 명령 수정됨

### <a name="container"></a>컨테이너

* 비 dockerhub 레지스트리의 사용자 이름 및 암호에 대한 요구 사항을 제거
* yaml 파일에서 컨테이너 그룹을 만들 때 발생하는 오류 수정

### <a name="network"></a>네트워크

* `network nic [create|update|delete]`에 `--no-wait` 지원이 추가됨 
* `network nic wait`가 추가됨
* `network vnet [subnet|peering] list`에 대한 `--ids` 인수가 사용되지 않음
* `network nsg rule list` 출력의 기본 보안 규칙을 포함하도록 `--include-default` 플래그를 추가  

### <a name="resource"></a>리소스

* `group deployment delete`에 `--no-wait` 지원이 추가됨
* `deployment delete`에 `--no-wait` 지원이 추가됨
* `deployment wait` 명령이 추가됨
* 구독 수준 `az deployment` 명령이 프로필 2017-03-09-profile에 잘못 표시되는 문제가 해결됨

### <a name="sql"></a>SQL

* `sql db copy` 및 `sql db replica create` 명령에 탄력적 풀 이름을 지정할 때 ‘제공된 리소스 그룹의 이름이 ... URL 내의 이름과 일치하지 않습니다’ 오류가 해결됨
* `az configure --defaults sql-server=<name>`를 실행하여 기본 SQL Server 구성 허용
* `sql server`, `sql server firewall-rule`, `sql list-usages`, `sql show-usage` 명령에 테이블 포맷터를 구현함

### <a name="storage"></a>스토리지

* 페이지 Blob에 대해 채워질 `storage blob show` 출력에 `pageRanges` 속성을 추가

### <a name="vm"></a>VM

* [호환성이 손상되는 변경]`vmss create`가 `Standard_DS1_v2`를 기본 인스턴스 크기로 사용하도록 변경
* `vm extension [set|delete]` 및 `vmss extension [set|delete]`에 대한 `--no-wait` 지원 추가
* `vm extension wait`가 추가됨

## <a name="july-3-2018"></a>2018년 7월 3일

버전 2.0.41

### <a name="aks"></a>AKS

* 구독 ID를 사용하도록 모니터링 변경

## <a name="july-3-2018"></a>2018년 7월 3일

버전 2.0.40

### <a name="core"></a>핵심

* 대화형 로그인에 대한 새 권한 부여 코드 흐름을 추가

### <a name="acr"></a>ACR

* 빌드 상태 폴링 추가
* 대/소문자 열거형 값에 대한 지원 추가
* `show-manifests`에 `--top` 및 `--orderby` 매개 변수 추가

### <a name="acs"></a>ACS

* [호환성이 손상되는 변경]Kubernetes 역할 기반 액세스 제어를 기본값으로 활성화합니다.
* `--disable-rbac` 인수를 추가 그리고 `--enable-rbac`가 기본값이므로 이제 사용되지 않음
* `aks browse` 명령 옵션이 업데이트됨. `--listen-port` 지원이 추가됨
* `aks install-connector` 명령에 대한 기본 helm 차트 패키지 업데이트 virtual-kubelet-for-aks-latest.tgz 사용
* 기존 클러스터 업데이트에 `aks enable-addons`과 `aks disable-addons` 명령 추가

### <a name="appservice"></a>AppService

* `webapp identity remove`를 통해 ID를 사용하지 않도록 하는 것에 대한 지원 추가
* `preview` 태그의 ID 기능 제거

### <a name="backup"></a>Backup

* 모듈 정의 업데이트

### <a name="batchai"></a>BatchAI

* `batchai cluster node list`, `batchai job node list` 명령에 대한 테이블 출력이 수정됨

### <a name="cloud"></a>클라우드

* `acr login` 서버 접미사를 클라우드 구성에 추가

### <a name="container"></a>컨테이너

* `container create`의 기본값을 장기 실행 작업으로 변경
* Log Analytics 매개 변수를 `--log-analytics-workspace` 및 `--log-analytics-workspace-key`에 추가
* `--protocol` 매개 변수를 사용할 네트워크 프로토콜을 지정하도록 추가

### <a name="extension"></a>내선 번호

* CLI 버전과 호환되는 확장명만 표시하도록 `extension list-available` 변경

### <a name="network"></a>네트워크

* 레코드 형식이 대/소문자를 구분하는 문제 수정([#6602](https://github.com/Azure/azure-cli/issues/6602))

### <a name="rdbms"></a>Rdbms

* `[postgres|myql] server vnet-rule` 명령이 추가됨

### <a name="resource"></a>리소스

* 새 작업 그룹 `deployment` 추가

### <a name="vm"></a>VM

* 시스템 할당 ID를 제거하기 위한 지원 추가

## <a name="june-25-2018"></a>2018년 6월 25일

버전 2.0.39

### <a name="cli"></a>CLI

* 확장 설치 문제를 해결하기 위해 MSI 설치 관리자에서 파일 잘라내기 업데이트

## <a name="june-19-2018"></a>2018년 6월 19일

Version 2.0.38

### <a name="core"></a>핵심

* 대부분의 명령으로 `--subscription`에 대한 전역 지원 추가

### <a name="acr"></a>ACR

* `azure-storage-blob`이 종속성으로 추가됨
* `acr build-task create`가 코어 2개를 사용하도록 기본 CPU 구성이 변경됨

### <a name="acs"></a>ACS

* `aks use-dev-spaces` 명령 옵션이 업데이트됨. `--update` 지원이 추가됨
* `$HOME/.kube/config` 안의 사용자 컨텍스트를 대체하지 않도록 `aks get-credentials --admin` 변경
* 읽기 전용 `nodeResourceGroup` 속성을 관리되는 클러스터에서 공개
* `acs browse` 명령 오류 수정
* `aks install-connector`, `aks upgrade-connector` 및 `aks remove-connector`에 대해 `--connector-name`을 옵션으로 설정
* `aks install-connector`에 대해 새 Azure 컨테이너 인스턴스 영역 추가
* helm 릴리스 이름과 `aks install-connector` 노드 이름에 정규화된 위치 추가

### <a name="appservice"></a>AppService

* Urllib의 최신 버전에 대한 지원 추가
* `functionapp create`가 외부 리소스 그룹 제공 앱 서비스 계획을 사용하도록 지원 추가

### <a name="batch"></a>Batch

* `azure-batch-extensions` 종속성 제거

### <a name="batch-ai"></a>Batch AI

* 작업 영역에 대한 지원 추가. 그룹 클러스터, 파일 서버 및 그룹 내 실험에 작업 영역 허용, 리소스의 수에 대한 제한을 제거
* 실험에 대한 지원 추가. 실험을 컬렉션의 그룹 작업에 허용, 생성된 작업의 수에 대한 제한 제거
* Docker 컨테이너에서 실행 중인 작업에 대해 `/dev/shm`을 구성하는 지원 추가
* `batchai cluster node exec` 및 `batchai job node exec` 명령이 추가됨. 이 명령은 노드에서 직접 모든 명령을 실행하도록 허용하고 포트 포워드를 위한 기능을 제공합니다.
* `--ids`에 대한 지원이 `batchai` 명령에 추가됨
* [호환성이 손상되는 변경] 모든 클러스터 및 파일 서버는 작업 영역에서 만들어야 합니다
* [호환성이 손상되는 변경] 실험 아래에 작업을 만들어야 합니다
* [호환성이 손상되는 변경]`cluster create`, `job create` 명령에서 `--nfs-resource-group`제거. 다른 작업 영역/리소스 그룹에 속한 NFS를 탑재하려면 `--nfs` 옵션을 통해 파일 서버의 ARM ID를 제공
* [호환성이 손상되는 변경]`job create` 명령에서 `--cluster-resource-group`제거. 다른 작업 영역/리소스 그룹에 속한 클러스터 상의 작업을 제출하려면 `--cluster` 옵션을 통해 클러스터의 ARM ID를 제공
* [호환성이 손상되는 변경]`location` 특성을 작업, 클러스터 및 파일 서버에서 제거. 이제 이 위치는 작업 영역의 특성입니다.
* [호환성이 손상되는 변경]`job create`, `cluster create`, `file-server create` 명령에서 `--location`제거
* [호환성이 손상되는 변경] 보다 일관된 인터페이스를 위해 간단한 옵션의 이름을 변경:
  - [`--config`, `-c`]을 [`--config-file`, `-f`]로 이름 바꿈
  - [`--cluster`, `-r`]을 [`--cluster`, `-c`]로 이름 바꿈
  - [`--cluster`, `-n`]을 [`--cluster`, `-c`]로 이름 바꿈
  - [`--job`, `-n`]을 [`--job`, `-j`]로 이름 바꿈

### <a name="maps"></a>지도

* [호환성이 손상되는 변경] 대화형 프롬프트 또는 `--accept-tos` 플래그로 서비스 약관을 수락하도록 `maps account create` 변경

### <a name="network"></a>네트워크

* `network lb probe create` [#6571](https://github.com/Azure/azure-cli/issues/6571)에 `https` 지원 추가
* `--endpoint-status`가 대/소문자를 구분하는 문제 해결 [#6502](https://github.com/Azure/azure-cli/issues/6502)

### <a name="reservations"></a>예약

* [호환성이 손상되는 변경] 필수 매개 변수 `ReservedResourceType`을 `reservations catalog show`에 추가
* `Location` 매개 변수가 `reservations catalog show`에 추가됨
* [호환성이 손상되는 변경]`ReservationProperties`에서 `kind`제거
* [호환성이 손상되는 변경]`Catalog` 내에서 `capabilities`에서 `sku_properties`로 이름이 변경됨
* [호환성이 손상되는 변경]`Catalog`에서 `size`, `tier` 속성 제거
* `InstanceFlexibility` 매개 변수가 `reservations reservation update`에 추가됨

### <a name="role"></a>역할

* 오류 처리 개선

### <a name="sql"></a>SQL

* 구독에 사용할 수 없는 위치에 대해 `az sql db list-editions` 실행 시 발생하는 혼란스러운 오류 수정

### <a name="storage"></a>스토리지

* `storage blob download`에 대한 출력 테이블을 가독성을 높이도록 변경

### <a name="vm"></a>VM

* `vm create` 내 네트워킹 지원 가속화에 대한 구체화 vm 크기 확인 향상
* 기본 vm 크기가 `Standard_D1_v2`에서 `Standard_DS1_v2`로 전환된다는, `vmss create`에 대한 경고 추가
* 구성이 변경되지 않은 경우에도 확장을 업데이트하도록 `--force-update`를 `[vm|vmss] extension set`에 추가

## <a name="june-13-2018"></a>2018년 6월 13일

Version 2.0.37

### <a name="core"></a>핵심

* 개선된 대화형 원격 분석

## <a name="june-13-2018"></a>2018년 6월 13일

Version 2.0.36

### <a name="aks"></a>AKS

* 고급 네트워킹 옵션이 `aks create`에 추가됨
* 인수를  `aks create`에 추가하여 모니터링 및 HTTP 라우팅을 활성화
* `--no-ssh-key` 인수를 `aks create`에 추가
* `--enable-rbac` 인수를 `aks create`에 추가
* [미리 보기] Azure Active Directory 인증에 대한 지원이 `aks create`에 추가됨

### <a name="appservice"></a>AppService

* 호환되지 않는 urllib 버전 관련 문제 해결

## <a name="june-5-2018"></a>2018년 6월 5일

Version 2.0.35

### <a name="interactive"></a>대화형

* 대화형 모드의 종속성에 제한 추가

## <a name="june-5-2018"></a>2018년 6월 5일

버전 2.0.34

### <a name="core"></a>핵심

* 상호 테넌트 리소스 참조에 대한 지원이 추가됨
* 원격 분석 업로드 신뢰성이 향상됨

### <a name="acr"></a>ACR

* 원격 원본 위치로 VSTS 지원이 추가됨
* `acr import` 명령이 추가됨

### <a name="aks"></a>AKS

* 보다 안전한 파일 시스템 권한으로 kube 구성 파일을 만들기 위해 `aks get-credentials`변경함

### <a name="batch"></a>Batch

* 풀 목록 표 서식수정 버그가 수정됨 [[문제 #4378](https://github.com/Azure/azure-cli/issues/4378)]

### <a name="iot"></a>IOT

* 기본 계층 IoT Hub 생성을 위한 지원이 추가됨

### <a name="network"></a>네트워크

* 향상됨 `network vnet peering`

### <a name="policy-insights"></a>Policy Insights

* 초기 릴리스

### <a name="arm"></a>ARM

* `account management-group` 명령이 추가됨

### <a name="sql"></a>SQL

* 새로운 추가된 관리되는 인스턴스 명령:
  * `sql mi create`
  * `sql mi show`
  * `sql mi list`
  * `sql mi update`
  * `sql mi delete`
* 관리형 새 데이터베이스 명령이 추가됨
  * `sql midb create`
  * `sql midb show`
  * `sql midb list`
  * `sql midb restore`
  * `sql midb delete`

### <a name="storage"></a>스토리지

* 파일 확장명에서 유추할 수 있도록 json 및 javascript를 추가 mimetypes으로 추가함

### <a name="vm"></a>VM

* 열을 고정시켜 사용하고 `Tier` 및 `Size`가 제거될 예정이라는 경고를 추가하도록 `vm list-skus` 변경
* `--accelerated-networking` 옵션을 `vm create`에 추가
* `identity create`에 `--tags` 추가

## <a name="may-22-2018"></a>2018년 5월 22일

버전 2.0.33

### <a name="core"></a>핵심

* 파일 이름에 `@` 확장을 위한 지원이 추가됨

### <a name="acs"></a>ACS

* 새 Dev-Space 명령 `aks use-dev-spaces` 및 `aks remove-dev-spaces` 추가
* 도움말 메시지 내 오류 해결

### <a name="appservice"></a>AppService

* 일반 업데이트 명령이 향상됨
* `webapp deployment source config-zip`에 대한 비동기 지원이 추가됨

### <a name="container"></a>컨테이너

* 컨테이너 그룹을 yaml 형식으로 내보내기 위한 지원이 추가됨
* Yaml 파일을 사용하여 컨테이너 그룹 만들기 / 업데이트하기에 대한 지원 추가

### <a name="extension"></a>내선 번호

* 확장 제거가 향상됨

### <a name="interactive"></a>대화형

* 완료 시 파서를 음소거하도록 로깅을 변경함
* 잘못된 도움말 캐시의 처리가 향상됨

### <a name="keyvault"></a>KeyVault

* 클라우드 셸 또는 id를 가진 Vm에서 실행 하도록 keyvault 명령을 수정함

### <a name="network"></a>네트워크

* `network watcher show-topology`이 vnet 및/또는 서브넷 이름[#6326](https://github.com/Azure/azure-cli/issues/6326)으로 작동하지 않는 문제 해결
* `network watcher` 명령 결과 실제로 [#6264](https://github.com/Azure/azure-cli/issues/6264)인 영역에 대해 Network Watcher가 사용 가능하지 않다는 문제 해결

### <a name="sql"></a>SQL

* [호환성이 손상되는 변경]`db` 및 `dw` 명령으로 리턴되는 응답 개체 변경 :
    * `serviceLevelObjective` 속성을 `currentServiceObjectiveName`로 이름을 바꿈
    * `currentServiceObjectiveId` 및 `requestedServiceObjectiveId` 속성 제거
    * `maxSizeBytes` 속성을 문자열 대신 정수값으로 변경
* [호환성이 손상되는 변경]`db` 및 `dw`를 읽기 전용 속성으로 변경
    * `requestedServiceObjectiveName`.  업데이트하려면, `--service-objective` 매개 변수를 사용하거나 `sku.name` 속성 설정
    * `edition`. 업데이트하려면, `--edition` 매개 변수를 사용하거나 `sku.tier` 속성 설정
    * `elasticPoolName`. 업데이트하려면, `--elastic-pool` 매개 변수를 사용하거나 `elasticPoolId` 속성 설정
* [호환성이 손상되는 변경] 다음 `elastic-pool` 속성을 읽기 전용으로 변경
    * `edition`. 업데이트하려면 `--edition` 매개 변수를 사용
    * `dtu`. 업데이트하려면 `--capacity` 매개 변수를 사용
    *  `databaseDtuMin`. 업데이트하려면 `--db-min-capacity` 매개 변수를 사용
    *  `databaseDtuMax`. 업데이트하려면 `--db-max-capacity` 매개 변수를 사용
* `db`,`dw`,`elastic-pool` 명령에 `--family`, `--capacity` 매개 변수 추가합니다.
* `elastic-pool` 명령에 `db`, `dw` 테이블 포맷터를 추가합니다.

### <a name="storage"></a>스토리지

* `--account-name` 인수에 완료자 추가
* `storage entity query`의 문제가 해결됨

### <a name="vm"></a>VM

* [호환성이 손상되는 변경]`vm create`에서 `--write-accelerator`제거됨. 동일한 지원을 `vm update` 또는 `vm disk attach`를 통해 액세스할 수 있음
* `[vm|vmss] extension`에서 일치하는 확장 이미지 수정
* 부팅 로그를 캡처하기 위해 `vm create`에 `--boot-diagnostics-storage` 추가
* `[vm|vmss] update`에 `--license-type` 추가

## <a name="may-7-2018"></a>2018년 5월 7일

버전 2.0.32

### <a name="core"></a>핵심

* 인증서를 사용하여 서비스 사용자 계정에서 비밀을 검색할 때 처리되지 않는 예외가 수정됨
* 위치 인수에 대한 제한된 지원이 추가됨
* `--query`가 `--ids`와 함께 사용될 수 없는 문제가 해결됨 [#5591](https://github.com/Azure/azure-cli/issues/5591)
* `--ids`를 사용하는 경우 명령의 파이핑 시나리오가 개선됨 쿼리가 지정된 `-o tsv` 또는 쿼리가 지정되지 않은 `-o json`을 지원
* 사용자의 명령에 오타가 있는 경우 오류에 대한 명령 제안이 추가됨
* 사용자가 `az ''`를 입력하는 경우 오류가 개선됨
* 명령 모듈 및 확장에 대한 사용자 지정 리소스 유형 지원이 추가됨

### <a name="acr"></a>ACR

* ACR Build 명령이 추가됨
* 리소스를 찾을 수 없음 오류 메시지가 개선됨
* 리소스 생성 성능 및 오류 처리가 개선됨
* 비표준 콘솔 및 WSL에서 acr 로그인이 개선됨
* 리포지토리 명령 오류 메시지가 개선됨
* 테이블 열 및 순서 지정 업데이트

### <a name="acs"></a>ACS

* `az aks`가 미리 보기 서비스라는 경고가 추가됨
* `--aci-resource-group`이 지정되지 않은 경우 `aks install-connector`의 권한 문제가 해결됨

### <a name="ams"></a>AMS

* 최초 릴리스 - Azure Media Services 리소스 관리

### <a name="appservice"></a>App Service

* `--slot`이 제공되는 경우 `webapp delete` 버그가 수정됨
* `webapp auth update`에서 `--runtime-version`이 제거됨
* min\_tls\_version 및 https2.0에 대한 지원이 추가됨
* 멀티 컨테이너 지원이 추가됨

### <a name="batch-ai"></a>Batch AI

* 클러스터의 구성 파일에 구성된 VM 우선 순위를 존중하도록 `batchai create cluster` 변경

### <a name="cognitive-services"></a>Cognitive Services

* `cognitiveservices account create` [#5603](https://github.com/Azure/azure-cli/issues/5603)에 대한 예제의 오타가 수정됨

### <a name="consumption"></a>Consumption

* 예산 API에 대한 새로운 명령이 추가됨

### <a name="container"></a>컨테이너

* 레지스트리 서버가 이미지 이름에 포함될 때 `container create`에 대한 `--registry-server` 요구 사항이 제거됨

### <a name="cosmos-db"></a>Cosmos DB

* Azure CLI용 VNET 지원 소개 - Cosmos DB

### <a name="dms"></a>DMS

* 최초 릴리스 - Azure SQL 마이그레이션 시나리오에 대한 SQL 지원 추가

### <a name="extension"></a>내선 번호

* 확장 메타데이터가 표시되지 않는 버그가 수정됨

### <a name="interactive"></a>대화형

* 대화형 completer가 위치 인수로 작동하도록 허용
* 사용자가 '\'을 입력하면 사용자 친화적인 출력 표시
* 도움이 없는 매개 변수 완성이 수정됨
* 명령 그룹에 대한 설명이 수정됨

### <a name="lab"></a>랩

* knack 전환으로부터 회귀가 수정됨

### <a name="network"></a>네트워크

* [호환성이 손상되는 변경] 다음 항목에서 `--ids` 매개 변수가 제거됨
  * `express-route auth list`
  * `express-route peering list`
  * `nic ip-config list`
  * `nsg rule list`
  * `route-filter rule list`
  * `route-table route list`
  * `traffic-manager endpoint list`

### <a name="profile"></a>프로필

* `disk create` 원본 감지가 수정됨
* [호환성이 손상되는 변경]`--msi-port` 및 `--identity-port`가 더 이상 사용되지 않아서 제거됨
* `account get-access-token` 짧은 요약의 오타가 수정됨

### <a name="redis"></a>Redis

* `redis patch-schedule patch-schedule show`는 사용되지 않고 `redis patch-schedule show`가 사용됨
* `redis list-all`이 사용되지 않음 이 기능은 `redis list`에 포함됨
* `redis import-method`는 사용되지 않고 `redis import`가 사용됨
* 다양한 명령에 `--ids` 지원이 추가됨

### <a name="role"></a>역할

* [호환성이 손상되는 변경] 사용되지 않는 `ad sp reset-credentials`가 제거됨

### <a name="storage"></a>스토리지

* 소스 sas 및 계정 키가 지정되지 않은 경우 Blob 복사본의 소스에 대상 sas-token이 적용되도록 허용
* Blob 업로드 및 다운로드에 대한 --socket-timeout이 노출
* 경로 구분 기호로 시작하는 BLOB 이름을 상대 경로로 처리
* 시작 쿼리 문자가 ?인 `storage blob copy --source-sas` 허용
* key=values 목록을 수락하도록 `storage entity query --marker`가 수정됨

### <a name="vm"></a>VM

* 관리되지 않는 Blob URI에 대한 잘못된 검색 논리가 수정됨
* 사용자가 제공한 서비스 사용자가 없는 디스크 암호화 지원이 추가됨
* [호환성이 손상되는 변경] MSI 지원에 VM 'ManagedIdentityExtension' 사용 금지
* `vmss`에 대한 제거 정책이 추가됨
* [호환성이 손상되는 변경] 다음 항목에서 `--ids`가 제거됨
  * `vm extension list`
  * `vm secret list`
  * `vm unmanaged-disk list`
  * `vmss nic list`
* Write Accelerator 지원이 추가됨
* `vmss perform-maintenance`가 추가됨
* VM의 OS 유형을 안정적으로 감지하도록 `vm diagnostics set`가 수정됨
* 요청된 크기가 현재 설정과 다른지 확인하고 변경 시에만 업데이트하도록 `vm resize` 변경


## <a name="april-10-2018"></a>2018년 4월 10일

버전 2.0.31

### <a name="acr"></a>ACR

* Wincred 대체(fallback)의 향상된 오류 처리

### <a name="acs"></a>ACS

* SPN이 5년 동안 유효하도록 만든 aks 변경

### <a name="appservice"></a>App Service

* [호환성이 손상되는 변경]: Removed `assign-identity`
* 존재하지 않는 webapp 계획에 대한 확인할 수 없는 예외가 수정됨

### <a name="batchai"></a>BatchAI

* 2018-03-01 API에 대한 지원이 추가됨

  - 작업 수준 탑재
  - 비밀 값을 가진 환경 변수
  - 성능 카운터 설정
  - 작업 특정 직선 세그먼트 보고
  - 목록 파일 api의 하위 폴더 지원
  - 사용 및 제한 보고
  - NFS 서버에 대한 캐싱 유형을 지정하도록 허용
  - 사용자 지정 이미지 지원
  - pyTorch 툴킷 지원 추가

* 작업 완료를 기다리고 작업 종료 코드를 보고할 수 있도록 하는 `job wait` 명령 추가
* 현재 Batch AI 리소스 사용을 나열하고 서로 다른 지역에 대해 제한하는 `usage show` 명령 추가
* 국가별 클라우드가 지원됨
* 구성 파일 외에도 파일 시스템을 작업 수준에 탑재하기 위한 작업 명령줄 인수 추가
* 클러스터를 사용자 지정하는 더 많은 옵션 추가 - vm 우선 순위, 서브넷, 자동 크기 조정 클러스터에 대한 초기 노드 수, 사용자 지정 이미지 지정
* Batch AI 관리 NFS에 대한 캐싱 유형을 지정하는 명령줄 옵션 추가
* 구성 파일에 파일 시스템 탑재를 간소화합니다. 이제 Azure File Share 및 Azure Blob Container에 대한 자격 증명을 생략 할 수 있습니다 - CLI는 명령줄 매개 변수를 통해 제공되거나 환경 변수를 통해 지정된 스토리지 계정 키를 사용하여 누락된 자격 증명을 채우거나 Azure Storage에서 키를 쿼리합니다.(스토리지 계정이 현재 구독에 속한 경우)
* 이제 작업 파일 스트림 명령은 작업이 완료될 때 자동 완료합니다.(성공, 실패, 종료 또는 삭제)
* `show` 작업에 대한 `table` 출력이 향상되었습니다.
* 클러스터 생성을 위해 `--use-auto-storage` 옵션이 추가되었습니다. 이 옵션을 사용하면 보다 쉽게 스토리지 계정을 관리하고 Azure File Share 및 Azure Blob Containers를 클러스터에 탑재할 수 있습니다.
* `--generate-ssh-keys` 옵션을 `cluster create` 및 `file-server create`에 추가
* 명령줄을 통해 노드 설정 작업을 제공하는 기능 추가
* [호환성이 손상되는 변경]`job stream-file` 및 `job list-files` 명령을 `job file`로 이동함
* [호환성이 손상되는 변경]`cluster create` 명령과 호환되도록 `file-server create` 명령에서 `--admin-user-name`을 `--user-name`로 이름을 변경함

### <a name="billing"></a>결제

* 등록 계정 명령 추가

### <a name="consumption"></a>Consumption

* `marketplace` 명령이 추가됨
* [호환성이 손상되는 변경]`reservations summaries`에서 `reservation summary`로 이름이 변경됨
* [호환성이 손상되는 변경]`reservations details`에서 `reservation detail`로 이름이 변경됨
* [호환성이 손상되는 변경]`reservation` 명령에 대한 `--reservation-order-id`과 `--reservation-id` 짧은 옵션이 제거됨
* [호환성이 손상되는 변경]`reservation summary` 명령에 대한 `--grain` 짧은 옵션이 제거됨
* [호환성이 손상되는 변경]`pricesheet` 명령에 대한 `--include-meter-details` 짧은 옵션이 제거됨

### <a name="container"></a>컨테이너

* Git 리포지토리 볼륨 탑재 매개 변수 `--gitrepo-url` `--gitrepo-dir` `--gitrepo-revision` 및 `--gitrepo-mount-path` 추가
* [#5926](https://github.com/Azure/azure-cli/issues/5926) 수정됨: --컨테이너-이름이 지정될 때 `az container exec` 실패

### <a name="extension"></a>내선 번호

* 배포 확인 메시지를 디버그 수준으로 변경

### <a name="interactive"></a>대화형

* 인식할 수 없는 명령이 있으면 완료를 중지하도록 변경함
* 명령 하위 트리를 만들기 전과 후에 이벤트 후크 추가
* `--ids` 매개 변수에 대한 완료 추가

### <a name="network"></a>네트워크

* [#5936](https://github.com/Azure/azure-cli/issues/5936) 수정됨: `application-gateway create` 태그는 bet 설정할 수 없습니다.
* `application-gateway http-settings [create|update]`에 대한 인증 인증서를 첨부하기 위해 인수 `--auth-certs`이 추가되었습니다. [#4910](https://github.com/Azure/azure-cli/issues/4910)
* DDoS 보호 계획을 만들기 위해 `ddos-protection` 명령이 추가되었습니다.
* VNet을 DDoS 보호 계획에 연결하기 위해 `--ddos-protection-plan`에 대한 지원을 `vnet [create|update]`에 추가함
* `network route-table [create|update]`에서 `--disable-bgp-route-propagation` 플래그 문제 해결
* `network lb [create|update]`에 대해 더미 인수 `--public-ip-address-type` 및 `--subnet-type`가 제거됨
* `network dns zone [import|export]` 및 `network dns record-set txt add-record`에 대한 RFC 1035 이스케이프 시퀀스를 가진 TXT 레코드에 대한 지원이 추가됨

### <a name="profile"></a>프로필

* `account list`에 있는 Azure Classic 계정에 대한 지원이 추가됨
* [호환성이 손상되는 변경]`--msi` & `--msi-port` 인수가 제거됨

### <a name="rdbms"></a>RDBMS

* `georestore` 명령이 추가됨
* `create` 명령에서 스토리지 크기 제한이 제거됨

### <a name="resource"></a>리소스

* `--metadata`에 대한 지원이 `policy definition create`에 추가됨
* `--metadata`, `--set`, `--add`, `--remove`에 대한 지원이 `policy definition update`에 추가됨

### <a name="sql"></a>SQL

* `sql elastic-pool op list` 및 `sql elastic-pool op cancel`가 추가됨

### <a name="storage"></a>스토리지

* 잘못된 형식의 연결 문자열에 대한 오류 메시지가 향상됨

### <a name="vm"></a>VM

* 플랫폼 장애 도메인 수를 `vmss create`로 구성하는 지원이 추가됨
* 영역, 대형 또는 단일 배치 그룹 비활성화 스케일 집합에 대해 `vmss create`을 기본값인 표준 LB로 변경함
* [호환성이 손상되는 변경]: Removed `vm assign-identity`, `vm remove-identity and `vm format-secret`
* 공용-IP SKU에 대한 지원이 `vm create`에 추가됨
* 명령이 자격 증명 모음 ID를 확인할 수 없는 시나리오를 지원하기 위해 `--keyvault` 및 `--resource-group` 인수가 `vm secret format`에 추가되었습니다. [#5718](https://github.com/Azure/azure-cli/issues/5718)
* 리소스 그룹의 위치에 영역 지원이 없는 경우 `[vm|vmss create]`에 대한 오류가 개선됨


## <a name="march-27-2018"></a>2018년 3월 27일

버전 2.0.30

### <a name="core"></a>핵심

* 도움말에서 미리 보기로 표시된 확장에 대한 메시지 표시

### <a name="acs"></a>ACS

* Cloud Shell에서 `aks install-cli`에 대한 SSL 인증서 확인 오류 수정

### <a name="appservice"></a>App Service

* `webapp update`에 HTTPS만 지원 추가됨
* `az webapp identity [assign|show]` 및 `az functionapp identity [assign|show]`에 대한 슬롯 지원이 추가됨

### <a name="backup"></a>Backup

* 새 명령 `az backup protection isenabled-for-vm`이 추가되었습니다. 이 명령을 사용하여 구독의 자격 증명 모음에 의해 VM을 백업했는지 확인할 수 있습니다.
* 다음 명령의 `--resource-group` 및 `--vault-name` 매개 변수에 대해 Azure 개체 ID를 사용할 수 있습니다.
  * `backup container show`
  * `backup item set-policy`
  * `backup item show`
  * `backup job show`
  * `backup job stop`
  * `backup job wait`
  * `backup policy delete`
  * `backup policy get-default-for-vm`
  * `backup policy list-associated-items`
  * `backup policy set`
  * `backup policy show`
  * `backup protection backup-now`
  * `backup protection disable`
  * `backup protection enable-for-vm`
  * `backup recoverypoint show`
  * `backup restore files mount-rp`
  * `backup restore files unmount-rp`
  * `backup restore restore-disks`
  * `backup vault delete`
  * `backup vault show`
* `backup ... show` 명령의 출력 형식을 허용하도록 `--name` 매개 변수가 변경되었습니다.

### <a name="container"></a>컨테이너

* `container exec` 명령이 추가되었습니다. 실행 중인 컨테이너 그룹에 대해 컨테이너에서 명령을 실행합니다.
* 컨테이너 그룹 생성 및 업데이트에 관한 테이블 출력 허용

### <a name="extension"></a>내선 번호

* 확장이 미리 보기에 있는 경우 `extension add`에 대한 메시지가 추가됨
* `--show-details`로 전체 확장 데이터를 표시하도록 `extension list-available`이 변경됨
* [호환성이 손상되는 변경] 기본적으로 단순화된 확장 데이터를 표시하도록 `extension list-available`로 변경됨

### <a name="interactive"></a>대화형

* 명령 테이블 로딩이 완료되는 즉시 활성화로 변경 완료
* `--style` 매개 변수를 사용하여 버그 수정됨
* 누락된 경우 명령 테이블 덤프 후 대화형 렉서가 인스턴스화됨
* 완성자 지원 향상됨

### <a name="lab"></a>랩

* `create environment` 명령을 사용하여 버그 수정됨

### <a name="monitor"></a>모니터

* `metrics list` [#5785](https://github.com/Azure/azure-cli/issues/5785)에 `--top`, `--orderby` 및 `--namespace`에 대한 지원 추가
* [#4529](https://github.com/Azure/azure-cli/issues/5785) 수정됨: `metrics list` 검색을 위해 공백으로 구분된 메트릭 목록을 허용함
* `metrics list-definitions` [#5785](https://github.com/Azure/azure-cli/issues/5785)에 `--namespace` 지원 추가

### <a name="network"></a>네트워크

* 프라이빗 DNS 영역에 대한 지원 추가됨

### <a name="profile"></a>프로필

* `--identity-port` 및 `--msi-port`에 대한 경고가 `login`에 추가됨

### <a name="rdbms"></a>RDBMS

* 비즈니스 모델 GA API 버전 2017-12-01 추가됨

### <a name="resource"></a>리소스

* [호환성이 손상되는 변경]: Changed `provider operation [list|show]` to not require `--api-version`

### <a name="role"></a>역할

* 필수 액세스 구성 및 네이티브 클라이언트에 대한 지원이 `az ad app create`에 추가됨
* 개체 확인 시 1000개 미만의 ID를 반환하도록 `rbac` 명령이 변경됨
* 자격 증명 관리 명령 `ad sp credential [reset|list|delete]` 추가됨
* [호환성이 손상되는 변경]`az role assignment [list|show]` 출력에서 '속성' 제거됨
* `dataActions` 및 `notDataActions` 권한에 대한 지원이 `role definition`에 추가됨

### <a name="storage"></a>스토리지

* 크기가 195GB에서 200GB 사이인 파일을 업로드할 때 발생하는 문제 수정됨
* [#4049](https://github.com/Azure/azure-cli/issues/4049) 수정됨: 조건 매개 변수를 무시하는 BLOB 업로드 추가에 따른 문제

### <a name="vm"></a>VM

* 100개 이상의 인스턴스가 있는 세트의 향후 호환성이 손상되는 변경에 대한 경고가 `vmss create`에 추가됨
* `vm [snapshot|image]`에 영역 복원 지원 추가됨
* 향상된 암호화 상태를 보고하도록 디스크 인스턴스 보기 변경됨
* [호환성이 손상되는 변경] 더 이상 출력을 반환하지 않도록 `vm extension delete` 변경됨

## <a name="march-13-2018"></a>2018년 3월 13일

버전 2.0.29

### <a name="acr"></a>ACR

* `repository delete`에 `--image` 매개 변수에 대한 지원 추가
* `repository delete` 명령의 `--manifest` 및 `--tag` 매개 변수 사용되지 않음
* 데이터를 삭제하지 않고 태그를 제거하는 `repository untag` 명령 추가

### <a name="acs"></a>ACS

* 기존 커넥터를 업그레이드하는 `aks upgrade-connector` 명령 추가
* 보다 읽기 쉬운 블록 스타일 YAML을 사용하는 `kubectl` 구성 파일 변경

### <a name="advisor"></a>Advisor

* [호환성이 손상되는 변경]`advisor configuration get`에서 `advisor configuration list`로 이름이 변경됨
* [호환성이 손상되는 변경]`advisor configuration set`에서 `advisor configuration update`로 이름이 변경됨
* [호환성이 손상되는 변경]`advisor recommendation generate` 제거됨
* `--refresh` 매개 변수가 `advisor recommendation list`에 추가됨
* `advisor recommendation show` 명령이 추가됨

### <a name="appservice"></a>App Service

* `[webapp|functionapp] assign-identity` 사용되지 않음
* `webapp identity [assign|show]` 및 `functionapp identity [assign|show]` 관리 ID 명령 추가

### <a name="eventhubs"></a>Event Hubs

* 초기 릴리스

### <a name="extension"></a>내선 번호

* 사용된 배포판이 패키지 원본 파일에 저장된 것과 다른 경우 오류가 발생할 수 있으므로 사용자에게 경고하도록 검사 추가

### <a name="interactive"></a>대화형

* [#5625](https://github.com/Azure/azure-cli/issues/5625) 수정됨: 여러 세션 간에 기록 유지
* [#3016](https://github.com/Azure/azure-cli/issues/3016) 수정됨: 범위에 속하지만 남겨지지 않는 기록
* [#5688](https://github.com/Azure/azure-cli/issues/5688) 수정됨: 명령 테이블 로딩에 예외가 발생하는 경우 완료가 표시되지 않음
* 장기 실행 작업의 진행률 표시기 해결

### <a name="monitor"></a>모니터

* `monitor autoscale-settings` 명령 사용되지 않음
* `monitor autoscale` 명령이 추가됨
* `monitor autoscale profile` 명령이 추가됨
* `monitor autoscale rule` 명령이 추가됨

### <a name="network"></a>네트워크

* [호환성이 손상되는 변경]`route-filter rule create`에서 `--tags` 매개 변수 제거됨
* 다음 명령의 일부 잘못된 기본값 제거:
  * `network express-route update`
  * `network nsg rule update`
  * `network public-ip update`
  * `traffic-manager profile update`
  * `network vnet-gateway update`
* `network watcher connection-monitor` 명령 추가
* `network watcher show-topology`에 `--vnet` 및 `--subnet` 매개 변수 추가

### <a name="profile"></a>프로필

* `az login`의 `--msi` 매개 변수 사용되지 않음
* `--msi`을 대체하는 `az login`의 `--identity` 매개 변수 추가

### <a name="rdbms"></a>RDBMS

* [미리 보기] API 2017-12-01-미리 보기를 사용하도록 변경

### <a name="service-bus"></a>Service Bus

* 초기 릴리스

### <a name="storage"></a>스토리지

* [#4971](https://github.com/Azure/azure-cli/issues/4971) 수정됨: `storage blob copy`가 이제 다른 Azure 클라우드도 지원
* [#5286](https://github.com/Azure/azure-cli/issues/5286) 수정됨: 배치 명령`storage blob [delete-batch|download-batch|upload-batch]`이 더 이상 사전 조건 실패 시 오류를 일으키지 않음

### <a name="vm"></a>VM

* 관리되지 않는 데이터 디스크를 추가하고 캐싱을 구성하는 `[vm|vmss] create`에 대한 지원 추가
* `[vm|vmss] assign-identity` 및 `[vm|vmss] remove-identity` 사용되지 않음
* 사용되지 않는 명령을 바꾸는 `vm identity [assign|remove|show]` 및 `vmss identity [assign|remove|show]` 명령 추가
* `vmss create`의 기본 우선 순위를 None으로 변경

## <a name="february-27-2018"></a>2018년 2월 27일

버전 2.0.28

### <a name="core"></a>핵심

* [#5184](https://github.com/Azure/azure-cli/issues/5184) 수정됨: Homebrew 설치 문제
* 사용자 지정 키를 사용하는 확장 원격 분석에 대한 지원 추가
* `--debug`에 대한 HTTP 로깅 추가

### <a name="acs"></a>ACS

* 기본적으로 `aks install-connector`에 `virtual-kubelet-for-aks` Helm 차트를 사용하도록 변경
* 문제 해결됨: 서비스 주체에 ACI 컨테이너 그룹 문제를 만들 권한이 불충분함
* `aks install-connector`에 `--aci-container-group`, `--location` 및 `--image-tag` 매개 변수 추가
* `aks get-versions`에서 사용 중단 공지 제거

### <a name="appservice"></a>App Service

* 새 SDK 버전에 대한 업데이트(azure-mgmt-web 0.35.0)
* [#5538](https://github.com/Azure/azure-cli/issues/5538): 잘못된 SKU로 보고된 `Free` 해결

### <a name="cognitive-services"></a>Cognitive Services

* 새 Cognitive Services 계정을 만들 때 '알림' 업데이트

### <a name="consumption"></a>Consumption

* 가격표 API의 새 명령 추가
* 기존 사용량 세부 정보 및 예약 세부 정보 형식 업데이트

### <a name="container"></a>컨테이너

* ACI에서 암호를 사용하기 위해 `container create`에 `--secrets` 및 `--secrets-mount-path` 인수 추가

### <a name="network"></a>네트워크

* [#5559](https://github.com/Azure/azure-cli/issues/5559) 수정됨: `network vnet-gateway vpn-client generate`에 클라이언트 누락됨

### <a name="resource"></a>리소스

* 실패 시 부분 템플릿 및 오류를 표시하도록 `group deployment export` 변경

### <a name="role"></a>역할

* 서비스 주체 역할의 감사를 허용하도록 `role assignment list-changelogs` 추가

### <a name="sql"></a>SQL

* 생성 및 업데이트 시 데이터베이스 및 탄력적 풀에 대한 영역 중복 지원 추가

### <a name="storage"></a>스토리지

* `storage blob [upload-batch|download-batch]`에 대상-경로/접두사를 지정하도록 설정

### <a name="vm"></a>VM

* 단일 VMSS 인스턴스에서 디스크를 연결/분리하는 지원 추가


## <a name="february-13-2018"></a>2018년 2월 13일

버전 2.0.27

### <a name="core"></a>핵심

* MSI 로그인 시 구독 ID 및 이름에 대한 키 인증 변경

### <a name="acs"></a>ACS

* [호환성이 손상되는 변경] 정확성을 위해 `aks get-versions`에서 `aks get-upgrades`로 이름이 변경됨
* `aks create`에 사용 가능한 Kubernetes 버전 표시를 위한 `aks get-versions` 변경
* 서버가 Kubernetes 버전을 선택할 수 있도록 `aks create` 기본값 변경
* AKS로 생성되는 서비스 주체를 나타내는 도움말 메시지 업데이트
* "Standard\_D1\_v2"에서 "Standard\_DS1\_v2"로 `aks create`의 기본 노드 크기 변경
* `az aks browse`의 대시보드 포드를 찾을 때 안정성 향상
* Kubernetes 구성 파일을 로드할 때 유니코드 오류 처리를 위한 `aks get-credentials` 수정
* `$PATH`에서 `kubectl`을(를) 가져올 때 도움이 되도록 `az aks install-cli`에 메시지 추가

### <a name="appservice"></a>App Service

* Null 참조로 인한 `webapp [backup|restore]` 실패 문제 해결
* `az configure --defaults appserviceplan=my-asp`을(를) 통한 기본 App Service 계획에 대한 지원 추가

### <a name="cdn"></a>CDN

* `cdn custom-domain [enable-https|disable-https]` 명령이 추가됨

### <a name="container"></a>컨테이너

* 스트리밍 로그에 대해 `az container logs`에 `--follow` 옵션 추가
* 로컬 표준 출력 및 오류 스트림을 컨테이너 그룹의 컨테이너에 첨부하는 `container attach` 명령 추가

### <a name="cosmosdb"></a>CosmosDB

* 기능 설정 지원 추가

### <a name="extension"></a>내선 번호

* `az extension [add|update]` 명령에 대한 `--pip-proxy` 매개 변수 지원 추가
* `az extension [add|update]` 명령에 대한 `--pip-extra-index-urls` 인수 지원 추가

### <a name="feedback-command"></a>피드백 명령

* 원격 분석 데이터에 대한 확장 정보 추가

### <a name="interactive"></a>대화형

* Cloud Shell에서 대화형 모드를 사용할 때 사용자에게 로그인 프롬프트가 표시되는 문제 해결
* 누락된 매개 변수 완성 기능의 재발 문제 해결

### <a name="iot"></a>IoT

* 성공 시 `iot dps access policy [create|update]`가 '찾을 수 없음' 오류를 반환하는 문제가 수정됨
* 성공 시 `iot dps linked-hub [create|update]`가 '찾을 수 없음' 오류를 반환하는 문제가 수정됨
* `iot dps access policy [create|update]` 및 `iot dps linked-hub [create|update]`에 대한 `--no-wait` 지원 추가
* 파티션 수를 지정할 수 있도록 `iot hub create` 변경

### <a name="monitor"></a>모니터

* `az monitor log-profiles create` 명령 수정됨

### <a name="network"></a>네트워크

* 다음 명령에 대한 `--tags` 옵션 수정
  * `network public-ip create`
  * `network lb create`
  * `network local-gateway create`
  * `network nic create`
  * `network vnet-gateway create`
  * `network vpn-connection create`

### <a name="profile"></a>프로필

* 대화형 모드에서 `az login` 지원

### <a name="resource"></a>리소스

* `feature show` 다시 추가

### <a name="role"></a>역할

* `--available-to-other-tenants` 인수를 `ad app update`에 추가

### <a name="sql"></a>SQL

* `sql server dns-alias` 명령이 추가됨
* `sql db rename`가 추가됨
* 모든 SQL 명령에 대한 `--ids` 인수 지원 추가

### <a name="storage"></a>스토리지

* 일시 삭제를 지원하는 `storage blob service-properties delete-policy` 및 `storage blob undelete` 명령 추가

### <a name="vm"></a>VM

* 가상 머신 암호화가 완전히 초기화되지 않을 수 있는 충돌 문제 해결
* MSI 지원에 대한 주체 ID 출력 추가
* 고정 `vm boot-diagnostics get-boot-log`


## <a name="january-31-2018"></a>2018년 1월 31일

버전 2.0.26

### <a name="core"></a>핵심

* MSI 컨텍스트에서 기본 토큰 검색 지원 추가
* Windows cmd.exe에서 LRO 완료 후 폴링 표시기 문자열 제거
* 구성된 기본값 사용이 INFO 수준 항목으로 변경되었을 때 표시되는 경고가 추가되었습니다. `--verbose`을(를) 사용하여 확인
* 대기 명령에 대한 진행률 표시기 추가

### <a name="acs"></a>ACS

* `--disable-browser` 인수 설명 추가
* `--vm-size` 인수에 대한 탭 완성 기능 개선

### <a name="appservice"></a>App Service

* 고정 `webapp log [tail|download]`
* Webapps 및 함수에 대한 `kind` 확인 제거

### <a name="cdn"></a>CDN

* `cdn custom-domain create`의 클라이언트 누락 문제 해결

### <a name="cosmosdb"></a>CosmosDB

* 장애 조치(Failover) 정책에 대한 매개 변수 설명 수정

### <a name="interactive"></a>대화형

* 명령 옵션 완성이 더 이상 표시되지 않는 문제 해결

### <a name="network"></a>네트워크

* `application-gateway create`에 `--cert-password` 보호 추가
* `--sku`이(가) 기본값을 잘못 적용하는 `application-gateway update` 문제 해결
* `vpn-connection create`에 `--shared-key` 및 `--authorization-key` 보호 추가
* `asg create`의 클라이언트 누락 문제 해결
* `dns zone export`에 내보낸 이름에 대한 `--file-name / -f` 매개변수 추가
* `dns zone export`의 다음 문제 해결:
  * 긴 TXT 레코드가 잘못 내보내지는 문제 해결
  * 따옴표 붙은 TXT 레코드가 이스케이프 처리 없이 잘못 내보내지는 문제 해결
* `dns zone import`에서 특정 레코드를 두 번 가져오는 문제 해결
* `vnet-gateway root-cert` 및 `vnet-gateway revoked-cert` 명령 복원

### <a name="profile"></a>프로필

* ID가 있는 가상 머신 내에서 작동하도록 `get-access-token` 수정

### <a name="resource"></a>리소스

* 템플릿 'type' 필드에 대문자 값이 포함되었을 때 경고가 잘못 표시되는 `deployment [create|validate]` 버그 수정

### <a name="storage"></a>스토리지

* 스토리지 V1 계정을 스토리지 V2로 마이그레이션할 때의 문제 해결
* 모든 upload/download 명령에 대한 진행률 보고 추가
* `storage account check-name`에서 "-n" 인수 옵션을 방해하는 버그 수정
* `blob [list|show]`에 대한 테이블 출력에 '스냅샷' 열 추가
* Ints로 구문 분석되어야 하는 여러 매개 변수의 버그 수정

### <a name="vm"></a>VM

* 추가 비용이 있는 이미지에서 가상 머신을 만들 수 있게 허용하는 `vm image accept-terms` 명령 추가
* 서명되지 않은 인증서를 사용해서 프록시로 명령을 실행할 수 있도록 `[vm|vmss create]` 수정
* [미리 보기] VMSS에 "낮음" 우선 순위 지원 추가
* `[vm|vmss] create`에 `--admin-password` 보호 추가


## <a name="january-17-2018"></a>2018년 1월 17일

버전 2.0.25

### <a name="acr"></a>ACR

* Windows 자격 증명 오류에 acr 로그인 대체 추가됨
* 레지스트리 로그를 사용하도록 설정됨

### <a name="acs"></a>ACS

* `get-credentials` 명령 수정됨
* SPN 역할 요구 사항 제거됨

### <a name="appservice"></a>App Service

* `hosting_environment_profile`가 Null인 `config ssl upload`을 사용해 버그 수정됨
* 사용자 지정 URL에 대한 지원이 `browse`에 추가됨
* `log tail`에 대한 슬롯 지원 수정됨

### <a name="backup"></a>Backup

* `backup item list`의 `--container-name` 옵션이 선택할 수 있도록 변경됨
* `backup restore restore-disks`에 스토리지 계정 옵션 추가됨
* `backup protection enable-for-vm`의 위치 확인이 대/소문자 구분하지 않도록 수정됨
* 잘못된 컨테이너 이름 때문에 명령이 실패한 문제 해결됨
* 기본값으로 ‘상태’ 포함하도록 `backup item list` 변경됨

### <a name="batch"></a>Batch

* 인증 세부정보 반환하도록 `batch login` 변경됨

### <a name="cloud"></a>클라우드

* 클라우드에서 `--profile`을 설정할 때 엔드포인트를 요구하지 않도록 변경됨

### <a name="consumption"></a>Consumption

* 새 예약 명령 `consumption reservations summaries`과 `consumption reservations details` 추가됨

### <a name="event-grid"></a>Event Grid

* [호환성이 손상되는 변경]`az eventgrid topic event-subscription` 명령이 `eventgrid event-subscription`으로 이동됨
* [호환성이 손상되는 변경]`az eventgrid resource event-subscription` 명령이 `eventgrid event-subscription`으로 이동됨
* [호환성이 손상되는 변경]`eventgrid event-subscription show-endpoint-url` 명령 제거됨 대신 `eventgrid event-subscription show --include-full-endpoint-url`을 사용하세요.
* 명령 `eventgrid topic update` 추가됨
* 명령 `eventgrid event-subscription update` 추가됨
* `eventgrid topic` 명령에 대한 `--ids` 매개 변수 추가됨
* 토픽 이름에 대한 탭 완성 지원 추가됨

### <a name="interactive"></a>대화형

* 대화형 모드가 Python 2.x와 작동하지 않는 문제 해결됨
* 시작할 때 오류 수정됨
* 대화형 모드에서 실행되지 않는 일부 명령 문제 해결됨

### <a name="iot"></a>IoT

* 디바이스 프로비전 서비스에 대한 지원 추가됨
* 명령 및 명령 도움말의 사용 중단 메시지 추가됨
* 사용자에게 IoT 확장을 알려주는 IoT 검사 추가됨

### <a name="monitor"></a>모니터

* 다중 진단 설정 지원이 추가됐습니다. `--name` 매개 변수가 이제 `az monitor diagnostic-settings create`를 위해 필요합니다
* 진단 설정 범주를 가져오기 위해 명령 `monitor diagnostic-settings categories` 추가됨

### <a name="network"></a>네트워크

* `vnet-gateway update`을 사용해 활성-대기 모드를 변경하려고 할 때의 문제 해결됨
* HTTP2에 대한 지원이 `application-gateway [create|update]`에 추가됨

### <a name="profile"></a>프로필

* 사용자 할당 ID를 사용하여 로그인에 대한 지원 추가됨

### <a name="role"></a>역할

* 그래프 쿼리를 사용하지 않기 위해 `--assignee-object-id` 인수가 `role assignment create`에 추가됨

### <a name="service-fabric"></a>Service Fabric

* 클러스터를 만들 때 유효성 검사 응답에 자세한 오류 추가됨
* 여러 명령을 사용하여 누락된 클라이언트 문제 수정됨

### <a name="vm"></a>VM

* [미리 보기] `vmss`에 대한 영역 간 지원
* [호환성이 손상되는 변경] 단일 영역 `vmss` 기본값이 "표준" 부하 분산 장치로 변경됨
* [호환성이 손상되는 변경] EMSI에 대해 `externalIdentities`을 `userAssignedIdentities`로 변경됨
* [미리 보기] OS 디스크 교체에 대한 지원 추가됨
* 다른 구독에서 VM 이미지를 사용하기 위한 지원 추가됨
* `--plan-name`, `--plan-product`, `--plan-promotion-code`, `--plan-publisher` 인수를 `[vm|vmss] create`에 추가
* `[vm|vmss] create`을 사용하여 오류 문제 해결됨
* `vm image list --all`에 의해 발생하는 과도한 리소스 사용 문제 해결됨

## <a name="december-19-2017"></a>2017년 12월 19일

버전 2.0.23

* 사용자 할당 ID를 사용하여 로그인에 대한 지원 추가됨

### <a name="container"></a>컨테이너

* 컨테이너 로그에 대한 매개 변수의 잘못된 순서 수정됨

### <a name="network"></a>네트워크

* `--disable-bgp-route-propagation` 인수를 `route-table [create|update]`에 추가
* `--ip-tags` 인수를 `public-ip [create|update]`에 추가

### <a name="storage"></a>스토리지

* storage V2에 대한 지원 추가됨

### <a name="vm"></a>VM

* [미리 보기] VM 및 VMSS의 사용자 할당 ID에 대한 지원 추가됨


## <a name="december-5-2017"></a>2017년 12월 5일

버전 2.0.22

* `az component` 명령이 제거되었습니다. 대신 `az extension`을 사용하세요.

### <a name="core"></a>핵심
* `AZURE_US_GOV_CLOUD` AAD 권한 엔드포인트가 login.microsoftonline.com에서 login.microsoftonline.us로 수정됨
* 원격 분석이 지속적으로 다시 전송되는 문제가 해결됨

### <a name="acs"></a>ACS

* `aks install-connector` 및 `aks remove-connector` 명령이 추가됨
* `acs create`에 대한 오류 보고가 개선됨
* 정규화된 경로 없이 `aks get-credentials -f`가 사용되는 문제가 해결됨

### <a name="advisor"></a>Advisor

* 초기 릴리스

### <a name="appservice"></a>App Service

* `webapp config ssl upload`를 사용한 인증서 이름 생성 문제가 해결됨
* `webapp [list|show]` 및 `functionapp [list|show]`가 올바른 앱을 표시하도록 수정됨
* `WEBSITE_NODE_DEFAULT_VERSION`에 대한 기본값이 추가됨

### <a name="consumption"></a>Consumption

* API 버전 2017-11-30에 대한 지원이 추가됨

### <a name="container"></a>컨테이너

* 기본 포트 회귀가 수정됨

### <a name="monitor"></a>모니터

* 메트릭 명령에 다차원 지원이 추가됨

### <a name="resource"></a>리소스

* `--include-response-body` 인수를 `resource show`에 추가

### <a name="role"></a>역할

* `role assignment list`에 "클래식" 관리자에 대한 기본 할당 표시가 추가됨
* 덮어 쓰지 않고 자격 증명을 추가하기 위한 지원 기능이 `ad sp reset-credentials`에 추가됨
* `ad sp create-for-rbac`에 대한 오류 보고가 개선됨

### <a name="sql"></a>SQL

* `sql db list-usages` 및 `sql db show-usage` 명령이 추가됨
* `sql server conn-policy show` 및 `sql server conn-policy update` 명령이 추가됨

### <a name="vm"></a>VM

* `az vm list-skus`에 영역 정보가 추가됨


## <a name="november-14-2017"></a>2017년 11월 14일

버전 2.0.21

### <a name="acr"></a>ACR

* 복제 영역에서 웹후크를 만들기 위한 지원 추가됨


### <a name="acs"></a>ACS

* AKS에서 "에이전트"의 모든 단어 표현을 "노드"로 변경
* `acs create`에 대한 사용되지 않음 `--orchestrator-release` 옵션
* AKS의 기본 VM 크기를 `Standard_D1_v2`로 변경됨
* Windows에서 `az aks browse` 수정됨
* Windows에서 `az aks get-credentials` 수정됨

### <a name="appservice"></a>App Service

* WebApps 및 함수 앱을 위해 배포 원본 `config-zip` 추가
* `--docker-container-logging` 옵션을 `az webapp log config`에 추가
* `az webapp log config`의 매개 변수`--web-server-logging`에서 `storage` 옵션 제거됨
* `deployment user set`에 대한 오류 메시지 향상됨
* Linux 함수 앱을 만들기 위한 지원 추가
* 고정 `list-locations`

### <a name="batch"></a>Batch

* 리소스 ID가 `--image` 플래그와 함께 사용된 경우 풀 만들기 명령의 버그 수정됨

### <a name="batchai"></a>Batchai

* `file-server create` 명령에서 VM 크기를 제공할 때 `--vm-size`에 대한 짧은 옵션 `-s` 추가
* 스토리지 계정 이름 및 키 인수를 `cluster create` 매개 변수에 추가됨
* `job list-files` 및 `job stream-file` 설명서 수정됨
* `job create` 명령에서 클러스터 이름을 제공할 때 `--cluster-name`에 대한 짧은 옵션 `-r` 추가

### <a name="cloud"></a>클라우드

* 필요한 엔드포인트가 누락된 클라우드를 등록하지 못하도록 `cloud [register|update]` 변경

### <a name="container"></a>컨테이너

* 다중 포트를 열기 위한 지원 추가
* 컨테이너 그룹 다시 시작 정책 추가
* Azure 파일 공유를 볼륨으로 마운트하기 위한 지원 추가
* 업데이트된 도우미 docs

### <a name="data-lake-analytics"></a>Data Lake Analytics

* 보다 간결한 정보를 반환하기 위해 `[job|account] list` 변경

### <a name="data-lake-store"></a>Data Lake Store

* 보다 간결한 정보를 반환하기 위해 `account list` 변경

### <a name="extension"></a>내선 번호

* 공식 Microsoft 확장 목록을 나열하기 위해 `extension list-available` 추가
* 이름별로 확장 설치하기 위해 `--name`을 `extension [add|update]`에 추가

### <a name="iot"></a>IoT

* 인증 기관(CA) 및 인증서 체인에 대한 지원 추가

### <a name="monitor"></a>모니터

* `activity-log alert` 명령이 추가됨

### <a name="network"></a>네트워크

* CAA DNS 레코드에 대한 지원 추가
* `traffic-manager profile update`로 엔드포인트를 업데이트할 수 없는 문제 해결
* VNET이 만들어지는 방법에 따라 `vnet update --dns-servers`가 작동하지 않는 문제 해결
* `dns zone import`가 상대 DNS 이름을 잘못 가져오는 문제 해결

### <a name="reservations"></a>예약

* 초기 미리 보기 릴리스

### <a name="resource"></a>리소스

* 리소스 ID에 대한 지원을 `--resource` 매개 변수 및 리소스 수준 잠금 기능에 추가

### <a name="sql"></a>SQL

* `--ignore-missing-vnet-service-endpoint` 매개 변수가 `sql server vnet-rule [create|update]`에 추가됨

### <a name="storage"></a>스토리지

* 기본값으로 SKU `Standard_RAGRS`를 사용하기 위해 `storage account create` 변경
* 비 ASCII 문자를 포함하는 파일/Blob 이름을 처리하는 경우 버그 수정됨
* `--source-uri`를 `storage [blob|file] copy start-batch`와 함께 사용하지 못하는 버그 수정
* glob에 명령 추가 및 `storage [blob|file] delete-batch`를 사용하여 여러 개체 삭제
* `storage metrics update`로 메트릭을 사용할 때 문제 해결
* `storage blob upload-batch`를 사용하는 경우 200GB 이상의 파일 사용 시 문제 해결
* `storage account [create|update]`에 의해 `--bypass` 및 `--default-action`이 무시되는 문제 해결

### <a name="vm"></a>VM

* `Basic` 크기 계층을 사용하지 못하게 하는 `vmss create`의 버그 수정
* 청구 정보가 있는 사용자 지정 이미지를 위해 `--plan` 인수를 `[vm|vmss] create`에 추가
* `vm secret `[add|remove|list]` 명령 추가
* `vm format-secret`에서 `vm secret format`로 이름이 변경됨
* `--encrypt format` 인수를 `vm encryption enable`에 추가

## <a name="october-24-2017"></a>2017년 10월 24일

버전 2.0.20

### <a name="core"></a>핵심

* `MGMT_STORAGE` API 버전 `2016-01-01`을 사용하기 위해 `2017-03-09-profile` 업데이트함

### <a name="acr"></a>ACR

* `2017-10-01` API 버전을 가리키도록 리소스 관리를 업데이트함
* 'Bring Your Own Storage' SKU를 클래식으로 변경함
* 레지스트리 SKU를 Basic, Standard 및 Premium으로 이름을 변경함

### <a name="acs"></a>ACS

* [미리 보기] `az aks` 명령이 추가됨
* Kubernetes `get-credentials`가 수정됨

### <a name="appservice"></a>App Service

* 다운로드한 `webapp` 로그가 유효하지 않은 문제가 해결

### <a name="component"></a>구성 요소

* 모든 설치 관리자에 대한 명확한 지원 중단 메시지 및 확인 프롬프트가 추가됨

### <a name="monitor"></a>모니터

* `action-group` 명령이 추가됨

### <a name="resource"></a>리소스

* `group export`의 최신 msrest 종속성과의 비호환성이 수정됨
* 기본 제공 정책 정의 및 정책 집합 정의로 작동하도록 `policy assignment create` 수정

### <a name="vm"></a>VM

* `--accelerated-networking` 인수를 `vmss create`에 추가


## <a name="october-9-2017"></a>2017년 10월 9일

버전 2.0.19

### <a name="core"></a>핵심

* Azure Stack에 후행 슬래시로 ADFS 기관 URL의 처리가 추가됨

### <a name="appservice"></a>App Service

* 새 명령 `webapp update`로 일반 업데이트 추가됨

### <a name="batch"></a>Batch

* 일괄 처리 SDK 4.0.0으로 업데이트됨
* publish:offer:sku:version에 추가로 ARM 이미지 참조를 지원하는 VirtualMachineConfiguration의 `--image` 옵션이 업데이트됨
* 일괄 처리 확장 명령을 위해 새 CLI 확장 모델에 대한 지원이 추가됨
* 구성 요소 모델에서 일괄 처리 지원이 제거됨

### <a name="batchai"></a>Batchai

* 일괄 처리 AI 모듈의 초기 릴리스

### <a name="keyvault"></a>Keyvault

* Azure Stack에서 ADFS를 사용 시 Key Vault 인증 문제가 수정되었습니다. [(#4448)](https://github.com/Azure/azure-cli/issues/4448)

### <a name="network"></a>네트워크

* 빈 주소 풀을 감안하여 `application-gateway address-pool create`의 `--server` 인수가 선택적으로 변경됨
* 최신 기능을 지원하기 위해 `traffic-manager`가 업데이트됨

### <a name="resource"></a>리소스

* 리소스 그룹 이름에 대한 `--resource-group/-g` 옵션 지원이 `group`에 추가됨
* 구독 수준 잠금으로 작동하는 `account lock`에 대한 명령이 추가됨
* 그룹 수준 잠금으로 작동하는 `group lock`에 대한 명령이 추가됨
* 리소스 수준 잠금으로 작동하는 `resource lock`에 대한 명령이 추가됨

### <a name="sql"></a>Sql

* SQL TDE(투명한 데이터 암호화) 및 Bring Your Own Key를 사용하는 TDE에 대한 지원이 추가됨
* 삭제된 데이터베이스를 찾아 복구하는 기능을 허용하는 `db list-deleted` 명령 및 `db restore --deleted-time` 매개 변수가 추가됨
* 진행 중인 작업을 데이터베이스에 나열하고 취소하는 기능을 허용하는 `db op list` 및 `db op cancel` 추가됨

### <a name="storage"></a>스토리지

* 파일 공유 스냅샷에 대한 지원 추가됨

### <a name="vm"></a>VM

* `-d` 사용으로 누락된 개인 IP 주소에서 충돌이 발생하는 `vm show`에서 버그 수정됨
* [미리 보기] 롤링 업그레이드에 대한 지원이 `vmss create`에 추가됨
* `vm encryption enable`을 사용하여 암호화 설정 업데이트에 대한 지원이 추가됨
* `--os-disk-size-gb` 매개 변수가 `vm create`에 추가됨
* Windows에 대한 `--license-type` 매개 변수가 `vmss create`에 추가됨


## <a name="september-22-2017"></a>2017년 9월 22일

버전 2.0.18

### <a name="resource"></a>리소스

* 기본 제공 정책 정의를 표시하기 위한 지원 추가
* 정책 정의를 만들기 위한 지원 모드 매개 변수 추가
* UI 정의 및 템플릿에 대한 지원을 `managedapp definition create`에 추가
* [호환성이 손상되는 변경]`managedapp` 리소스 종류가 `appliances`에서 `applications`로 그리고 `applianceDefinitions`에서 `applicationDefinitions`로 변경됨

### <a name="network"></a>네트워크

* 가용성 영역에 대한 지원을 `network lb` 및 `network public-ip` 하위 명령에 추가
* IPv6 Microsoft 피어링에 대한 지원을 `express-route`에 추가
* `asg` 애플리케이션 보안 그룹 명령 추가
* `--application-security-groups` 인수를 `nic [create|ip-config create|ip-config update]`에 추가
* `--source-asgs` 및 `--destination-asgs` 인수를 `nsg rule [create|update]`에 추가
* `--ddos-protection` 및 `--vm-protection` 인수를 `vnet [create|update]`에 추가
* `network [vnet-gateway|vpn-client|show-url]` 명령이 추가됨

### <a name="storage"></a>스토리지

* SDK를 업데이트한 후 `storage account network-rule` 명령이 실패하는 경우 문제 해결

### <a name="eventgrid"></a>Event Grid

* 최신 API 버전 "2017-09-15-preview"를 사용하기 위해 업데이트된 Azure Event Grid Python SDK

### <a name="sql"></a>SQL

* 선택할 수 있도록 `sql server list` 인수를 `--resource-group`으로 변경되었습니다. 지정하지 않으면 구독의 모든 sql 서버가 반환됨
* `--no-wait` 매개 변수를 `db [create|copy|restore|update|replica create|create|update]` 및 `dw [create|update]`에 추가

### <a name="keyvault"></a>Keyvault

* 프록시 뒤에서부터 Keyvault 명령에 대한 지원 추가

### <a name="vm"></a>VM

* 가용성 영역에 대한 지원을 `[vm|vmss|disk] create`에 추가
* `--app-gateway ID`와 `vmss create`를 함께 사용하는 경우 오류를 일으키는 문제 해결
* `--asgs` 인수를 `vm create`에 추가
* `vm run-command`를 사용하여 VM에서 명령을 실행하기 위한 지원 추가
* [미리 보기] `vmss encryption`을 사용하여 VMSS 디스크 암호화에 대한 지원 추가
* `vm perform-maintenance`를 사용하여 VM에서 유지 관리를 수행하기 위한 지원 추가

### <a name="acs"></a>ACS

* [미리 보기] `--orchestrator-release` 인수를 ACS 미리 보기 영역에 대한 `acs create`에 추가

### <a name="appservice"></a>App Service

* `webapp auth [update|show]`를 사용하여 인증 설정을 업데이트하고 표시하기 위한 기능 추가

### <a name="backup"></a>Backup

* 미리 보기 릴리스


## <a name="september-11-2017"></a>2017년 9월 11일

버전 2.0.17

### <a name="core"></a>핵심

* 원격 분석에서 고유한 상관 관계 ID를 설정하는 명령 모듈 설정됨
* 원격 분석이 진단 모드로 설정된 경우 JSON 덤프 문제 수정됨

### <a name="acs"></a>ACS

* `acs list-locations` 명령이 추가됨
* 예상된 기본값 함께 `ssh-key-file`을 제공함

### <a name="appservice"></a>App Service

* 활성 서비스 계획이 아닌 다른 리소스 그룹에서 웹앱을 만드는 기능 추가됨

### <a name="cdn"></a>CDN

* `cdn custom-domain create`에 대한 'CustomDomain is not interable' 버그 수정됨

### <a name="extension"></a>내선 번호

* 초기 릴리스

### <a name="keyvault"></a>Keyvault

* 사용 권한이 `keyvault set-policy`에서 대/소문자를 구분하는 문제 수정됨

### <a name="network"></a>네트워크

* `vnet list-private-access-services`에서 `vnet list-endpoint-services`로 이름이 변경됨
* `--private-access-services` 인수 이름을 `vnet subnet create/update`의 `--service-endpoints`로 변경
* 여러 IP 범위 및 포트 범위에 대한 지원이 `nsg rule create/update`에 추가됨
* SKU에 대한 지원이 `lb create`에 추가됨
* SKU에 대한 지원이 `public-ip create`에 추가됨

### <a name="resource"></a>리소스

* `policy definition create` 및 `policy definition update`에 있는 리소스 정책 매개 변수 정의를 전달하도록 허용
* `policy assignment create`의 매개 변수 값을 전달하도록 허용
* 모든 매개 변수에 JSON 또는 파일을 전달하도록 허용
* 증가된 API 버전

### <a name="sql"></a>SQL

* `sql server vnet-rule` 명령이 추가됨

### <a name="vm"></a>VM

* 수정됨: `--scope`이(가) 제공되지 않을 경우 액세스 권한을 할당하지 않음
* 수정됨: 포털과 동일한 확장명을 사용함
* `[vm|vmss] create` 출력에서 `subscription`이 제거됨
* 수정됨: `[vm|vmss] create` 스토리지 SKU가 이미지를 포함한 데이터 디스크에 적용되지 않음
* 수정됨: `vm format-secret --secrets`가 줄 바꿈으로 구분된 ID를 허용하지 않음

## <a name="august-31-2017"></a>2017년 8월 31일

버전 2.0.16

### <a name="keyvault"></a>Keyvault

* `secret download`로 인코딩한 비밀을 자동으로 확인하려는 경우 버그 수정됨

### <a name="sf"></a>Sf

* Service Fabric CLI(sfctl)에서 모든 명령이 사용되지 않음

### <a name="storage"></a>스토리지

* 스토리지 계정을 NetworkACLs 기능을 지원하지 않는 지역에 만들지 못하는 문제 수정됨
* 콘텐츠 형식 및 콘텐츠 인코딩을 모두 지정하지 않은 경우 Blob 및 파일을 업로드하는 동안 콘텐츠 형식 및 콘텐츠 인코딩을 결정함

## <a name="august-28-2017"></a>2017년 8월 28일

버전 2.0.15

### <a name="cli"></a>CLI

* `--version`에 법적 정보가 추가됨

### <a name="acs"></a>ACS

* 미리 보기 영역 수정됨
* `dns_name_prefix`의 기본 형식이 올바르게 지정됨
* acs 명령 출력이 최적화됨

### <a name="appservice"></a>App Service

* [호환성이 손상되는 변경]`az webapp config appsettings [delete|set]` 출력의 불일치가 수정됨
* `az webapp config container set --docker-custom-image-name`에 대해 새로운 `-i` 별칭이 추가됨
* `az webapp log show`가 공개됨
* `az webapp delete`에서 앱 서비스 계획, 통계 또는 DNS 등록을 유지하는 새 인수가 공개됨
* 수정됨: 슬롯 설정을 올바르게 검색함

### <a name="iot"></a>IoT

* #3934 수정됨: 정책을 새로 만들어도 더 이상 기존 정책이 지워지지 않음

### <a name="network"></a>네트워크

* [호환성이 손상되는 변경]`vnet list-private-access-services`에서 `vnet list-endpoint-services`로 이름이 변경됨
* [호환성이 손상되는 변경]`vnet subnet [create|update]`에 대한 옵션 이름이 `--private-access-services`에서 `--service-endpoints`로 변경됨
* 여러 IP 및 포트 범위에 대한 지원이 `nsg rule [create|update]`에 추가됨
* SKU에 대한 지원이 `lb create`에 추가됨
* SKU에 대한 지원이 `public-ip create`에 추가됨

### <a name="profile"></a>프로필

* 가상 머신의 ID를 사용하여 로그인하는 `--msi` 및 `--msi-port`가 공개됨

### <a name="service-fabric"></a>Service Fabric

* 미리 보기 릴리스
* 명령에 대한 레지스트리 사용자/암호 규칙이 간소화됨
* 매개 변수를 전달한 후에도 사용자에게 암호를 묻는 메시지가 수정됨
* 빈 `registry_cred`에 대한 지원이 추가됨

### <a name="storage"></a>스토리지

* 설정 Blob 계층이 사용됨
* 서비스 터널링을 지원하는 `--bypass` 및 `--default-action` 인수가 `storage account [create|update]`에 추가됨
* VNET 규칙 및 IP 기반 규칙을 추가하는 명령이 `storage account network-rule`에 추가됨
* 고객 관리 키에 의한 서비스 암호화가 사용됨
* [호환성이 손상되는 변경]`az storage account create and az storage account update` 명령에 대한 옵션 이름이 `--encryption`에서 `--encryption-services`로 변경됨
* #4220: `az storage account update encryption` - 구문 불일치가 수정됨

### <a name="vm"></a>VM

* `--instance-id *`를 사용할 때 `vmss get-instance-view`에 대해 잘못된 추가 정보가 표시되는 문제가 해결되었습니다.
* `--lb-sku`에 대한 지원이 `vmss create`에 추가됨
* `[vm|vmss] create`에 대한 관리자 이름 차단 목록에서 사람의 이름이 제거됨
* 이미지에서 계획 정보를 추출할 수 없는 경우 `[vm|vmss] create`에서 오류를 throw하는 문제가 해결되었습니다.
* 내부 LB가 있는 VMMS 확장 집합을 만들 때 발생하는 크래시가 해결되었습니다.
* `--no-wait` 인수가 `vm availability-set create`와 함께 작동하지 않는 문제가 해결되었습니다.


## <a name="august-15-2017"></a>2017년 8월 15일

버전 2.0.14

### <a name="acs"></a>ACS

* Kubernetes에 대한 sshMaster0 포트 번호가 수정됨

### <a name="appservice"></a>App Service

* 새 Git 기반 Linux 웹앱을 만들 때 발생하는 예외가 해결되었습니다.

### <a name="event-grid"></a>Event Grid

* SDK 종속성이 추가됨

## <a name="august-11-2017"></a>2017년 8월 11일

버전 2.0.13

### <a name="acs"></a>ACS

* 더 많은 미리 보기 지역이 추가됨

### <a name="batch"></a>Batch

* Batch SDK 3.1.0 및 Batch Management SDK 4.1.0으로 업데이트됨
* 작업의 태스크 수를 표시하는 새 명령이 추가됨
* 리소스 파일 SAS URL 처리에서 발견된 버그가 수정됨
* 배치 계정 엔드포인트에서 이제 선택적 'https://' 접두사를 지원합니다.
* 100개 이상의 태스크를 포함한 목록이 작업에 추가되도록 지원합니다.
* 확장 명령 모듈 로드에 대한 디버그 로깅이 추가됨

### <a name="component"></a>구성 요소

* 'az component' 명령에 사용 중단 경고가 추가됨

### <a name="container"></a>컨테이너

* `create`: 환경 변수에서 등호가 허용되지 않던 문제가 해결됨


### <a name="data-lake-store"></a>Data Lake Store

* 진행률 컨트롤이 사용됨

### <a name="event-grid"></a>Event Grid

* 초기 릴리스

### <a name="network"></a>네트워크

* `lb`: 생략했을 때 특정 자식 리소스 이름이 올바르게 확인되지 않던 문제가 해결됨
* `application-gateway {subresource} delete`: `--no-wait`를 적용할 수 없던 문제가 해결됨
* `application-gateway http-settings update`: `--connection-draining-timeout`를 해제할 수 없던 문제가 해결됨
* `az network vpn-connection ipsec-policy add`에서 예기치 않은 `sa_data_size_kilobyes` 키워드 인수 오류가 발생하는 문제가 해결되었습니다.

### <a name="profile"></a>프로필

* `account list`: 서버에서 최신 구독을 동기화하기 위해 `--refresh`가 추가됨

### <a name="storage"></a>스토리지

* 시스템에 할당된 ID를 통한 스토리지 계정 업데이트가 사용됨

### <a name="vm"></a>VM

* `availability-set`: 변환 시 장애 도메인 수가 공개됨
* `list-skus` 명령이 공개됨
* 역할 할당을 만들지 않고 ID를 할당하도록 지원합니다.
* 데이터 디스크 연결 시 스토리지 SKU를 적용합니다.
* 관리 디스크를 사용할 때 기본 OS 디스크 이름 및 스토리지 SKU가 제거됩니다.


## <a name="july-28-2017"></a>2017년 7월 28일

버전 2.0.12

* 컨테이너 명령이 추가됨
* 청구 및 소비 모듈이 추가됨

```text
azure-cli (2.0.12)

acr (2.0.9)
acs (2.0.11)
appservice (0.1.11)
batch (3.0.3)
billing (0.1.3)
cdn (0.0.6)
cloud (2.0.7)
cognitiveservices (0.1.6)
command-modules-nspkg (2.0.1)
component (2.0.6)
configure (2.0.10)
consumption (0.1.3)
container (0.1.7)
core (2.0.12)
cosmosdb (0.1.11)
dla (0.0.10)
dls (0.0.11)
feedback (2.0.6)
find (0.2.6)
interactive (0.3.7)
iot (0.1.10)
keyvault (2.0.8)
lab (0.0.9)
monitor (0.0.8)
network (2.0.11)
nspkg (3.0.1)
profile (2.0.9)
rdbms (0.0.5)
redis (0.2.7)
resource (2.0.11)
role (2.0.9)
sf (1.0.5)
sql (2.0.8)
storage (2.0.11)
vm (2.0.11)
```

### <a name="core"></a>핵심

* 인증서가 있는 서비스 사용자에 대한 SDK 인증 정보가 출력됨
* 배포 진행률 예외가 해결됨
* 현재 클라우드의 ARM 엔드포인트를 사용하여 구독 클라이언트를 만들 수 있음
* clouds.config 파일의 동시 처리가 향상됨(#3636)
* 각 명령 실행에 대한 클라이언트 요청 ID를 새로 고침
* 오른쪽 SDK 프로필을 사용하여 구독 클라이언트를 만들 수 있음(#3635)
* 템플릿 배포에 대한 진행률이 보고됨(#3510)
* jmespath 쿼리를 통한 테이블 출력 필드 선택에 대한 지원이 추가됨(#3581)
* 향상된 구문 분석 인수 차단 및 제스처를 사용한 기록 추가 (#3434)
* 오른쪽 SDK 프로필을 사용하여 구독 클라이언트를 만들 수 있음
* 기존의 모든 기록 파일을 최신 폴더로 이동
* VM/VMSS 만들기에 대한 멱등성 문제가 해결됨(#3586)
* 명령 경로에서 더 이상 대/소문자를 구분하지 않음
* 특정 부울 형식 매개 변수에서 더 이상 대/소문자를 구분하지 않음
* 프리미엄 서버(예: Azure Stack)에서 ADFS에 대한 로그인 지원
* clouds.config에 대한 동시 쓰기 문제가 해결됨(#3255)

### <a name="acr"></a>ACR

* 관리되는 레지스트리에 대한 `show-usage` 명령이 추가됨
* 관리되는 레지스트리에 대한 SKU 업데이트가 지원됨
* 관리되는 SKU에 관리되는 레지스트리가 추가됨
* acr webhook 명령 모듈에 관리되는 레지스트리에 대한 웹후크가 추가됨
* acr login 명령에 AAD 인증이 추가됨
* Docker 리포지토리, 매니페스트 및 태그에 대한 delete 명령이 추가됨

### <a name="acs"></a>ACS

* API 버전 2017-07-01에 대한 지원

### <a name="appservice"></a>App Service

* Linux 웹앱을 나열하면 아무 것도 반환하지 않는 버그가 수정되었습니다.
* ACR에서 자격 증명을 검색하도록 지원합니다.
* `appservice web` 아래의 모든 명령을 제거합니다.
* 명령 출력에서 Docker 레지스트리 암호를 마스킹합니다(#3656).
* macOS에서 기본 브라우저가 오류 없이 사용되는지 확인합니다(#3623).
* `webapp log tail` 및 `webapp log download` 지원이 향상되었습니다(#3624).
* 정적 라우팅을 구성하는 `traffic-routing` 명령이 공개되었습니다(#3566).
* 원본 제어 구성에 안정성 수정이 추가되었습니다(#3245).
* Windows 웹앱에 대한 `webapp config update`에서 지원되지 않는 `--node-version` 인수가 제거되었습니다. 대신 `webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=...`을 사용합니다.

### <a name="batch"></a>Batch

* 풀에서 우선 순위가 낮은 VM을 지원하는 Batch SDK 3.0.0으로 업데이트됨
* `pool create`에 대한 옵션 이름이 `--target-dedicated`에서 `--target-dedicated-nodes`로 변경됨
* `pool create`에 대한 옵션으로 `--target-low-priority-nodes` 및 `--application-licenses`가 추가됨

### <a name="cdn"></a>CDN

* `--profile-name`에서 지정된 프로필이 존재하지 않을 때 `cdn endpoint list`에 대한 더 나은 오류 메시지가 제공됨

### <a name="cloud"></a>클라우드

* 클라우드 메타데이터 엔드포인트의 API 버전이 YYYY-MM-DD 형식으로 변경됨
* 갤러리 엔드포인트가 필요하지 않음
* ARM 리소스 관리자 엔드포인트를 사용한 클라우드 등록이 지원됨
* 현재 클라우드를 선택하는 동안에 `cloud set`에서 프로필을 선택하는 옵션이 제공됨
* `endpoint_vm_image_alias_doc`가 공개됨

### <a name="cosmosdb"></a>CosmosDB

* 사용자 지정 파티션 키를 사용하여 컬렉션을 만들 수 있도록 수정되었습니다.
* 컬렉션 기본 TTL에 대한 지원이 추가됨

### <a name="data-lake-analytics"></a>Data Lake Analytics

* `dla account compute-policy` 제목 아래에 컴퓨팅 정책을 관리하는 명령이 추가됨
* `dla job pipeline show`가 추가됨
* `dla job recurrence list`가 추가됨

### <a name="data-lake-store"></a>Data Lake Store

* `dls account update`에 사용자 관리 키 자격 증명 모음 키 회전에 대한 지원이 추가되었습니다.
* 기본 Data Lake Store 파일 시스템 SDK 버전을 업데이트하여 성능 문제가 해결되었습니다.
* `dls enable-key-vault` 명령이 추가되었습니다. 이 명령은 Data Lake Store 계정의 데이터를 암호화하기 위해 사용자가 제공한 Key Vault를 사용하도록 설정하려고 시도합니다

### <a name="interactive"></a>대화형

* 캐시된 명령을 사용하여 시작 시간이 향상됨
* 테스트 검사가 향상됨
* 다음 명령에 삽입하는 '?' 제스처가 향상됨
* 2017-03-09-profile-preview 프로필을 사용하여 대화형 오류가 수정됨(#3587)
* 대화형 모드에 대한 매개 변수로 `--version`이 허용됨(#3645)
* 유효성 검사 완료 시 오류를 throw하는 대화형 모드가 중지됨(#3570)
* 템플릿 배포에 대한 진행률이 보고됨(#3510)
* `--progress` 플래그가 추가됨
* 완료 시 `--debug` 및 `--verbose`가 제거됨
* 완료 시 `interactive`가 제거됨(#3324)

### <a name="iot"></a>IoT

* 정책을 만들 때 기존 정책을 더 이상 지우지 않도록 수정됨 (#3934)

### <a name="key-vault"></a>주요 자격 증명 모음

* 키 자격 증명 모음 복구 기능에 추가된 명령:
  * `keyvault` 하위 명령: `purge`, `recover`, `keyvault list-deleted`
  * `keyvault secret` 하위 명령: `backup`, `restore`, `purge`, `recover`, `list-deleted`
  * `keyvault certificate` 하위 명령: `purge`, `recover`, `list-deleted`
  * `keyvault key` 하위 명령: `purge`, `recover`, `list-deleted`
* 서비스 사용자 키 자격 증명 모음 통합이 추가됨(#3133)
* 키 자격 증명 모음 데이터 평면이 0.3.2로 업데이트됨 (#3307)

### <a name="lab"></a>랩

* `az lab vm claim`을 통한 랩의 모든 VM 요청 작업에 대한 지원이 추가됨
* `az lab vm list` 및 `az lab vm show`에 대한 테이블 출력 포맷터가 추가됨

### <a name="monitor"></a>모니터

* `monitor autoscale-settings get-parameters-template` 명령으로 템플릿 파일이 수정됨(#3349)
* `monitor alert-rule-incidents list`에서 `monitor alert list-incidents`로 이름이 변경됨
* `monitor alert-rule-incidents show`에서 `monitor alert show-incident`로 이름이 변경됨
* `monitor metric-defintions list`에서 `monitor metrics list-definitions`로 이름이 변경됨
* `monitor alert-rules`에서 `monitor alert`로 이름이 변경됨
* `monitor alert create`에 대한 변경 내용:
  * `condition` 및 `action` 하위 명령에는 JSON이 더 이상 허용되지 않습니다.
  * 다양한 매개 변수를 추가하여 규칙 작성 프로세스가 간소화되었습니다.
  * `location`은 더 이상 필수가 아닙니다.
  * 대상에 대한 이름 및 ID 지원이 추가되었습니다.
  * `--alert-rule-resource-name`이 제거되었습니다.
  * 이름이 `is-enabled`에서 `enabled`로 변경되었으며, 더 이상 필수가 아닙니다.
  * `description`은 제공된 조건에 따라 기본적으로 설정됩니다.
  *  새 형식을 명확히 하는 데 도움이 되는 예제가 추가되었습니다.
* `monitor metric` 명령에 대해 이름 또는 ID가 지원됨
* `monitor alert rule update`에 편의 인수 및 예제가 추가됨

### <a name="network"></a>네트워크

* `list-private-access-services` 명령이 추가됨
* `vnet subnet create` 및 `vnet subnet update`에 `--private-access-services` 인수가 추가됨
* `application-gateway redirect-config create`가 실패하는 문제가 해결되었습니다.
* `--no-wait`가 포함된 `application-gateway redirect-config update`가 작동하지 않는 문제가 해결되었습니다.
* `application-gateway address-pool create` 및 `application-gateway address-pool update`에 `--servers` 인수를 사용하는 버그가 수정되었습니다.
* `application-gateway redirect-config` 명령이 추가됨
* `application-gateway ssl-policy`에 추가된 명령: `list-options`, `predefined list`, `predefined show`
* `application-gateway ssl-policy set`에 추가된 인수: `--name`, `--cipher-suites`, `--min-protocol-version`
* `application-gateway http-settings create` 및 `application-gateway http-settings update`에 추가된 인수: `--host-name-from-backend-pool`, `--affinity-cookie-name`, `--enable-probe`, `--path`
* `application-gateway url-path-map create` 및 `application-gateway url-path-map update`에 추가된 인수: `--default-redirect-config`, `--redirect-config`
* `application-gateway url-path-map rule create`에 추가된 인수: `--redirect-config`
* `--no-wait`에 대한 지원이 `application-gateway url-path-map rule delete`에 추가됨
* `application-gateway probe create` 및 `application-gateway probe update`에 추가된 인수: `--host-name-from-http-settings`, `--min-servers`, `--match-body`, `--match-status-codes`
* `application-gateway rule create` 및 `application-gateway rule update`에 추가된 인수: `--redirect-config`
* `--accelerated-networking`에 대한 지원이 `nic create` 및 `nic update`에 추가됨
* `nic create`에서 `--internal-dns-name-suffix` 인수가 제거됨
* `--dns-servers`에 대한 지원이 `nic update` 및 `nic create`에 추가됨: --DNS 서버에 대한 지원 추가
* `local-gateway create`에서 `--local-address-prefixes`를 무시하는 버그가 수정되었습니다.
* `--dns-servers`에 대한 지원이 `vnet update`에 추가됨
* `express-route peering create`를 사용하여 경로를 필터링하지 않고 피어링을 만드는 버그가 수정되었습니다.
* `--provider` 및 `--bandwidth` 인수가 `express-route update`에서 작동하지 않는 버그가 수정되었습니다.
* `network watcher show-topology` 기본값 논리가 있는 버그가 수정되었습니다.
* `network list-usages`에 대한 출력 형식 지정이 향상됨
* 하나만 있는 경우 `application-gateway http-listener create`에 대해 기본 프런트 엔드 IP가 사용됨
* 하나만 있는 경우 `application-gateway rule create`에 대해 기본 주소 풀, HTTP 설정 및 HTTP 수신기가 사용됨
* 하나만 있는 경우 `lb rule create`에 대해 기본 프런트 엔드 IP 및 백 엔드 풀이 사용됨
* 하나만 있는 경우 `lb inbound-nat-rule create`에 대해 기본 프런트 엔드 IP가 사용됨

### <a name="profile"></a>프로필

* 관리 ID를 사용한 VM 내부 로그인이 지원됨
* `account show`에 대한 SDK 인증 파일 형식 출력이 지원됨
* '--expanded-view'를 사용할 때 사용 중단 경고가 표시됨
* 원시 AAD 토큰을 제공하는 `get-access-token` 명령이 추가됨
* 연결된 구독이 없는 사용자 계정을 사용한 로그인이 지원됨

### <a name="rdbms"></a>RDBMS

* 구독을 통해 서버를 나열하도록 지원합니다(#3417).
* `% server_type` 누락으로 인해 처리되지 않는 `%s`가 해결되었습니다(#3393).
* 고정 문서 원본 맵이 수정되고 CI 확인 작업이 추가되었습니다(#3361).
* MySQL 및 PostgreSQL 도움말이 수정되었습니다(#3369)

### <a name="resource"></a>리소스

* `group deployment create`의 누락된 매개 변수에 대한 프롬프트가 향상됨
* `--parameters KEY=VALUE` 구문 분석이 향상됨
* `@<file>` 구문을 사용하여 `group deployment create` 매개 변수 파일을 더 이상 인식하지 못하는 문제가 해결되었습니다.
* `resource` 및 `managedapp` 명령에 대해 `--ids` 인수가 지원됩니다.
* 일부 구문 분석 및 오류 메시지가 수정되었습니다(#3584).
* `lock` 명령에 대한 `--resource-type` 구문 분석에서 `<resource-namespace>`과 `<resource-type>`을 허용하도록 수정되었습니다.
* 템플릿 링크 템플릿에 대한 매개 변수 검사가 추가되었습니다(#3629).
* `KEY=VALUE` 구문을 사용하여 배포 매개 변수를 지정하도록 지원하는 기능이 추가되었습니다.

### <a name="role"></a>역할

* `create-for-rbac`에 대한 SDK 인증 파일 형식 출력이 지원됨
* 서비스 사용자를 삭제할 때 역할 할당 및 관련 AAD 애플리케이션이 정리됨(#3610)
* `app create` 인수 `--start-date` 및 `--end-date` 설명에 시간 형식이 포함됨
* `--expanded-view`를 사용할 때 사용 중단 경고가 표시됨
* `create-for-rbac` 및 `reset-credentials` 명령에 키 자격 증명 모음 통합이 추가됨

### <a name="service-fabric"></a>Service Fabric
* 업로드 시 애플리케이션의 대형 파일을 자르는 문제가 해결되었습니다(#3666).
* Service Fabric 명령에 대한 테스트가 추가됨(#3424)
* 다양한 Service Fabric 명령이 수정됨(#3234)

### <a name="sql"></a>SQL

* 중단된 `sql server create` `--identity` 매개 변수가 제거됨
* `sql server create` 및 `sql server update` 명령 출력에서 암호 값이 제거됨
* `sql db list-editions` 및 `sql elastic-pool list-editions` 명령이 추가됨

### <a name="storage"></a>스토리지

* `storage blob list`, `storage container list` 및 `storage share list` 명령에서 `--marker` 옵션이 제거됨(#3745)
* HTTPS 전용 스토리지 계정 만들기 사용
* 스토리지 메트릭, 로깅 및 CORS 명령이 업데이트됨(#3495)
* CORS add(#3638)(#3362)의 예외 메시지가 수정됨
* download batch 명령 dryrun(시험 실행) 모드에서 생성기가 목록으로 변환됨(#3592)
* Blob download batch dryrun 문제가 해결됨(#3640)(#3592)

### <a name="vm"></a>VM

* NSG 구성이 지원됨
* DNS 서버가 올바르게 구성되지 않는 버그가 수정되었습니다.
* 관리되는 서비스 ID가 지원됨
* 기존 부하 분산 장치가 있는 `cmss create`에 `--backend-pool-name`이 필요했던 문제가 수정됨
* `vm image create` lun을 사용하여 만든 데이터 디스크가 0으로 시작됨


## <a name="may-10-2017"></a>2017년 5월 10일

버전 2.0.6

* documentdb가 cosmosdb로 바뀜
* rdbms(mysql, postgres) 추가
* Data Lake Analytics 및 Data Lake Store 모듈 포함
* Cognitive Services 모듈 포함
* Service Fabric 모듈 포함
* 대화형 모듈(az-shell 이름 바꾸기) 포함
* CDN 명령에 대한 지원 추가
* 컨테이너 모듈 제거
* 'az --version'에 대한 바로 가기로 'az -v' 추가([#2926](https://github.com/Azure/azure-cli/issues/2926))
* 패키지 로드 및 명령 실행의 성능 개선([#2819](https://github.com/Azure/azure-cli/issues/2819))

```text
azure-cli (2.0.6)

acr (2.0.4)
acs (2.0.6)
appservice (0.1.6)
batch (2.0.4)
cdn (0.0.2)
cloud (2.0.2)
cognitiveservices (0.1.2)
command-modules-nspkg (2.0.0)
component (2.0.4)
configure (2.0.6)
core (2.0.6)
cosmosdb (0.1.6)
dla (0.0.6)
dls (0.0.6)
feedback (2.0.2)
find (0.2.2)
interactive (0.3.1)
iot (0.1.5)
keyvault (2.0.4)
lab (0.0.4)
monitor (0.0.4)
network (2.0.6)
nspkg (3.0.0)
profile (2.0.4)
rdbms (0.0.1)
redis (0.2.3)
resource (2.0.6)
role (2.0.4)
sf (1.0.1)
sql (2.0.3)
storage (2.0.6)
vm (2.0.6)
```

### <a name="core"></a>핵심

* core: 등록되지 않은 공급자에 의한 예외를 캡처하고 자동으로 등록
* perf: 프로세스가 종료될 때까지 adal 토큰 캐시를 메모리에 유지([#2603](https://github.com/Azure/azure-cli/issues/2603))
* hex fingerprint -o tsv에 의해 반환된 바이트 수정([#3053](https://github.com/Azure/azure-cli/issues/3053))
* 향상된 Key Vault 인증서 다운로드 및 AAD SP 통합([#3003](https://github.com/Azure/azure-cli/issues/3003))
* ‘az —version’에 Python 위치 추가([#2986](https://github.com/Azure/azure-cli/issues/2986))
* login: 구독이 없을 때 로그인 지원([#2929](https://github.com/Azure/azure-cli/issues/2929))
* core: 서비스 주체를 사용하여 두 번 로그인할 때 오류 수정([#2800](https://github.com/Azure/azure-cli/issues/2800))
* core: accessTokens.json의 파일 경로가 env var을 통해 구성 가능하도록 허용([#2605](https://github.com/Azure/azure-cli/issues/2605))
* core: 구성된 기본값이 선택 인수에 적용되도록 허용([#2703](https://github.com/Azure/azure-cli/issues/2703))
* core: 성능 향상
* core: 사용자 지정 CA 인증서 - REQUESTS_CA_BUNDLE 환경 변수 설정 지원
* core: 클라우드 구성 - '관리' 엔드포인트가 설정되지 않은 경우 '리소스 관리자' 엔드포인트 사용

### <a name="acs"></a>ACS

* 마스터 및 에이전트 수를 문자열이 아닌 정수로 수정
* 비동기 작성에 대해 'az acs create --no-wait' 및 'az acs wait' 노출
* 시험 실행 유효성 검사에 대해 'az acs create --validate' 노출
* 규모 명령에 대한 PUT 호출 전에 Windows 프로필 제거([#2755](https://github.com/Azure/azure-cli/issues/2755))

### <a name="appservice"></a>AppService

* functionapp: 전체 함수 앱 지원 추가(create, show, list, delete, hostname, ssl 등 포함)
* "appservice web source-control config"에 연속 배달 옵션으로 팀 서비스(vsts) 추가
* "az appservice web"을 대신할 "az webapp" 만들기(이전 버전과의 호환성을 위해 "az appservice web"을 두 릴리스에서 유지함)
* 웹앱 만들기 시 배포 및 “런타임 스택”을 구성하기 위한 인수 노출
* "webapp list-runtimes" 노출
* 연결 문자열 구성 지원([#2647](https://github.com/Azure/azure-cli/issues/2647))
* 미리 보기를 사용한 슬롯 전환 지원
* AppService 서비스 명령의 폴란드어 오류([#2948](https://github.com/Azure/azure-cli/issues/2948))
* 인증서 작업에 대해 App Service 계획의 리소스 그룹 사용([#2750](https://github.com/Azure/azure-cli/issues/2750))

### <a name="cosmosdb"></a>CosmosDB

* documentdb 모듈을 cosmosdb로 이름 바꾸기
* Documentdb 데이터 평면에 API: 데이터베이스 및 컬렉션 관리에 대한 지원이 추가됨
* 데이터베이스 계정에 대해 자동 장애 조치(Failover)를 사용하도록 설정하기 위한 지원이 추가됨
* 새 일관성 정책 ConsistentPrefix에 대한 지원이 추가됨

### <a name="data-lake-analytics"></a>Data Lake Analytics

* 작업 목록의 결과 및 상태에 대한 필터링 시 오류가 throw되는 버그 수정
* 새 카탈로그 항목 형식: 패키지에 대한 지원을 추가합니다. `az dla catalog package`를 통해 액세스
* 데이터베이스 내에서 다음 카탈로그 항목을 나열할 수 있음(스키마 사양 필요 없음):

  * 테이블
  * 테이블 반환 함수
  * 보기
  * 테이블 통계. 테이블 이름을 지정하지 않고 스키마와 함께 나열될 수도 있음

### <a name="data-lake-store"></a>Data Lake Store

* 기본 파일 시스템 SDK 버전을 업데이트하여 서버 쪽 제한 시나리오 처리에 보다 나은 지원 제공
* 패키지 로드 및 명령 실행의 성능 개선([#2819](https://github.com/Azure/azure-cli/issues/2819))
* 액세스 표시에 대한 도움말 누락. 추가됩니다. ([#2743](https://github.com/Azure/azure-cli/issues/2743))

### <a name="find"></a>찾기

* 검색 결과 개선 및 검색 인덱스의 버전 관리 허용

### <a name="keyvault"></a>KeyVault

* BC:`az keyvault certificate download` 문자열 또는 이진 파일의 -e를 PEM 또는 DER로 변경하여 옵션을 보다 잘 나타냅니다.
* BC: --expires 및 --not-before 매개 변수는 서비스에서 지원되지 않으므로 `keyvault certificate create`에서 삭제
* `keyvault certificate create`에 --validity 매개 변수를 추가하여 --policy의 값을 선택적으로 재정의합니다.
* 'expires' 및 'not_before'가 노출되지만 'validity_in_months'는 노출되지 않는 `keyvault certificate get-default-policy`의 문제를 수정합니다.
* pem 및 pfx 가져오기에 대한 KeyVault 수정([#2754](https://github.com/Azure/azure-cli/issues/2754))

### <a name="lab"></a>랩

* 랩 환경에 대한 create, show, delete 및 list 명령 추가
* 랩에서 ARM 템플릿을 보기 위해 show 및 list 명령 추가
* `az lab vm list`에 --environment 플래그를 추가하여 랩의 환경별로 VM 필터링
* 편의성 명령 `az lab formula export-artifacts`를 추가하여 랩 수식 내에 아티팩트 스캐폴드를 내보냄
* 랩 내에서 비밀을 관리하는 명령을 추가

### <a name="monitor"></a>모니터

* 버그 수정: JSON 문자열을 사용하도록 `az alert-rules create`의 `--actions` 모델링([#3009](https://github.com/Azure/azure-cli/issues/3009))
* 버그 수정 - 진단 설정 만들기가 show 명령의 로그/메트릭을 수락하지 않음([#2913](https://github.com/Azure/azure-cli/issues/2913))

### <a name="network"></a>네트워크

* `network watcher test-connectivity` 명령 추가
* `network watcher packet-capture create`의 `--filters` 매개 변수 지원 추가
* Application Gateway 연결 드레이닝에 대한 지원 추가
* Application Gateway WAF 규칙 집합 구성에 대한 지원 추가
* ExpressRoute 경로 필터 및 규칙에 대한 지원 추가
* TrafficManager 지리적 라우팅에 대한 지원 추가
* VPN 연결 정책 기반 트래픽 선택기에 대한 지원 추가
* VPN 연결 IPSec 정책에 대한 지원 추가
* `--no-wait` 또는 `--validate` 매개 변수를 사용할 때 `vpn-connection create` 관련 버그 수정
* 활성-활성 VNet 게이트웨이에 대한 지원 추가
* `network vpn-connection list/show` 명령의 출력에서 null 값 제거
* BC: `vpn-connection create`의 출력에서 버그 수정
* 'vpn-connection create'의 '--key-length' 인수가 제대로 구문 분석되지 않는 버그 수정
* `dns zone import`에서 레코드를 제대로 가져오지 않는 버그 수정
* `traffic-manager endpoint update`가 작동하지 않는 버그를 수정
* 'network watcher' 미리 보기 명령 추가

### <a name="profile"></a>프로필

* 구독이 없을 때 로그인 지원([#2560](https://github.com/Azure/azure-cli/issues/2560))
* az account set --subscription에서 짧은 매개 변수 이름 지원 ([#2980](https://github.com/Azure/azure-cli/issues/2980))

### <a name="redis"></a>Redis

* redis cache에 대해 규모를 조정하는 기능도 추가하는 update 명령 추가
* 'update-settings' 명령은 더 이상 사용하지 않음

### <a name="resource"></a>리소스

* managedapp 및 managedapp definition 명령 추가([#2985](https://github.com/Azure/azure-cli/issues/2985))
* 'provider operation' 명령 지원([#2908](https://github.com/Azure/azure-cli/issues/2908))
* 일반 리소스 만들기 지원([#2606](https://github.com/Azure/azure-cli/issues/2606))
* 리소스 구문 분석 및 API 버전 조회를 수정합니다. ([#2781](https://github.com/Azure/azure-cli/issues/2781))
* az lock update에 대한 문서를 추가합니다. ([#2702](https://github.com/Azure/azure-cli/issues/2702))
* 존재하지 않는 그룹의 리소스를 나열하려고 할 때 오류가 발생합니다. ([#2769](https://github.com/Azure/azure-cli/issues/2769))
* [Compute] VMSS 및 VM 가용성 집합 업데이트 문제를 해결합니다. ([#2773](https://github.com/Azure/azure-cli/issues/2773))
* parent-resource-path가 None인 경우 잠금 만들기 및 삭제 수정([#2742](https://github.com/Azure/azure-cli/issues/2742))

### <a name="role"></a>역할

* create-for-rbac: SP의 종료 날짜가 인증서의 만료 날짜를 초과하지 않도록 보장([#2989](https://github.com/Azure/azure-cli/issues/2989))
* RBAC: 'ad group'에 대한 모든 지원 추가([#2016](https://github.com/Azure/azure-cli/issues/2016))
* role: 역할 정의 업데이트에 대한 문제 수정([#2745](https://github.com/Azure/azure-cli/issues/2745))
* create-for-rbac: 사용자 제공 암호가 선택되도록 보장

### <a name="sql"></a>SQL

* az sql server list-usages 및 az sql db list-usages 명령이 추가됨
* SQL - 리소스 공급자에 직접 연결하는 기능([#2832](https://github.com/Azure/azure-cli/issues/2832))

### <a name="storage"></a>스토리지

* `storage account create`의 리소스 그룹 위치에 대한 기본 위치
* 증분 blob 복사에 대한 지원 추가
* 큰 블록 blob 업로드에 대한 지원 추가
* 업로드할 파일이 200GB보다 큰 경우 블록 크기를 100MB로 변경

### <a name="vm"></a>VM

* avail-set: UD&FD 도메인 수를 옵션으로 지정

  참고: 소버린 클라우드의 VM 명령 다음을 비롯한 관리 디스크 관련 기능을 피하십시오.
  1. az disk/snapshot/image
  2. az vm/vmss disk
  3. "az vm/vmss create" 내에 "—use-unmanaged-disk"를 사용하여 관리되는 디스크를 피합니다. 다른 명령은 작동합니다.
* vm/vmss: ssh 키 쌍을 생성할 때 경고 텍스트 개선
* vm/vmss: 계획 정보가 필요한 marketplace 이미지에서 만들기 지원([#1209](https://github.com/Azure/azure-cli/issues/1209))


## <a name="april-3-2017"></a>2017년 4월 3일

버전 2.0.2

이 릴리스에서는 ACR, Batch, KeyVault 및 SQL 구성 요소가 릴리스되었습니다.

```text
azure-cli (2.0.2)

acr (2.0.0)
acs (2.0.2)
appservice (0.1.2)
batch (2.0.0)
cloud (2.0.0)
component (2.0.0)
configure (2.0.2)
container (0.1.2)
core (2.0.2)
documentdb (0.1.2)
feedback (2.0.0)
find (0.0.1b1)
iot (0.1.2)
keyvault (2.0.0)
lab (0.0.1)
monitor (0.0.1)
network (2.0.2)
nspkg (2.0.0)
profile (2.0.2)
redis (0.1.1b3)
resource (2.0.2)
role (2.0.1)
sql (2.0.0)
storage (2.0.2)
vm (2.0.2)
```

### <a name="core"></a>핵심

* 기본 목록에 acr, 랩, 모니터 및 찾기 모듈 추가
* 로그인: 잘못된 테넌트 건너뛰기([#2634](https://github.com/Azure/azure-cli/pull/2634))
* 로그인: 상태가 "사용"인 구독에 기본 구독 설정([#2575](https://github.com/Azure/azure-cli/pull/2575))
* 더 많은 명령에 wait 명령 및 --no-wait 지원 추가([#2524](https://github.com/Azure/azure-cli/pull/2524))
* 코어: 인증서가 있는 서비스 주체를 사용하여 로그인 지원([#2457](https://github.com/Azure/azure-cli/pull/2457))
* 누락된 템플릿 매개 변수를 요청하는 프롬프트 추가. ([#2364](https://github.com/Azure/azure-cli/pull/2364))
* 기본 리소스 그룹, 기본 웹, 기본 vm 등과 같은 공통 인수에 대한 기본값 설정 지원
* 특정 테넌트에 대한 로그인 지원

### <a name="acs"></a>ACS

* [ACS] 기본 ACS 클러스터 구성 지원 추가([#2554](https://github.com/Azure/azure-cli/pull/2554))
* ssh 키 암호 프롬프트 지원 추가. ([#2044](https://github.com/Azure/azure-cli/pull/2044))
* windows 클러스터 지원 추가. ([#2211](https://github.com/Azure/azure-cli/pull/2211))
* 소유자 역할에서 참가자 역할로 전환. ([#2321](https://github.com/Azure/azure-cli/pull/2321))

### <a name="appservice"></a>AppService

* appservice: DNS A 레코드에 사용된 외부 ip 주소 가져오기 지원([#2627](https://github.com/Azure/azure-cli/pull/2627))
* appservice: 와일드카드 인증서 바인딩 지원([#2625](https://github.com/Azure/azure-cli/pull/2625))
* appservice: 게시 프로필 나열 지원([#2504](https://github.com/Azure/azure-cli/pull/2504))
* AppService - 구성 후 원본 제어 동기화 트리거([#2326](https://github.com/Azure/azure-cli/pull/2326))

### <a name="datalake"></a>DataLake

* Data Lake Analytics 모듈의 초기 릴리스
* Data Lake Store 모듈의 초기 릴리스

### <a name="docuemntdb"></a>DocuemntDB

* DocumentDB: 문자열 목록에 대한 지원 추가([#2580](https://github.com/Azure/azure-cli/pull/2580))

### <a name="vm"></a>VM

* [Compute] 가상 머신 확장 집합 만들기에 AppGateway 지원 추가([#2570](https://github.com/Azure/azure-cli/pull/2570))
* [VM/VMSS] 향상된 디스크 캐싱 지원([#2522](https://github.com/Azure/azure-cli/pull/2522))
* VM/VMSS: 포털에서 사용하는 자격 증명 유효성 검사 논리 통합([#2537](https://github.com/Azure/azure-cli/pull/2537))
* wait 명령 및 --no-wait 지원 추가([#2524](https://github.com/Azure/azure-cli/pull/2524))
* 가상 머신 확장 집합: vm의 인스턴스 보기를 나열하는 * 지원([#2467](https://github.com/Azure/azure-cli/pull/2467))
* 추가 - VM 및 가상 머신 확장 집합의 암호([#2212}(<https://github.com/Azure/azure-cli/pull/2212>))
* 특수 VHD로 VM 만들기 허용([#2256](https://github.com/Azure/azure-cli/pull/2256))

## <a name="february-27-2017"></a>2017년 2월 27일

버전 2.0.0

이 Azure CLI 2.0 릴리스는 최초의 "일반 공급" 릴리스입니다. 일반 공급은 다음 명령 모듈에 적용됩니다.
- 컨테이너 서비스(acs)
- Compute(Resource Manager, VM, 가상 머신 확장 집합, Managed Disks 포함)
- 네트워킹
- 스토리지

이러한 명령 모듈은 프로덕션 환경에서 사용할 수 있으며 표준 Microsoft SLA에서 지원됩니다. Microsoft 지원 부서 또는 [github 문제 목록](https://github.com/azure/azure-cli/issues/)에서 직접 문제를 열 수 있습니다. [azure-cli 태그를 사용하여 StackOverflow](http://stackoverflow.com/questions/tagged/azure-cli)에 대한 질문을 하거나 [azfeedback@microsoft.com](mailto:azfeedback@microsoft.com)에 있는 제품 팀에 문의할 수 있습니다. `az feedback` 명령을 사용하여 명령줄에서 피드백을 제공할 수 있습니다.

이러한 모듈의 명령은 안정적이므로 이 Azure CLI 버전의 이후 릴리스에서 구문이 변경되지는 않을 것입니다.

CLI 버전을 확인하려면 `az --version`을 사용합니다. 출력은 CLI 자체 버전(이 릴리스에서는 2.0.0), 개별 명령 모듈, 현재 사용 중인 Python 및 GCC CLI의 버전을 나열합니다.

```text
azure-cli (2.0.0)

acs (2.0.0)
appservice (0.1.1b5)
batch (0.1.1b4)
cloud (2.0.0)
component (2.0.0)
configure (2.0.0)
container (0.1.1b4)
core (2.0.0)
documentdb (0.1.1b2)
feedback (2.0.0)
iot (0.1.1b3)
keyvault (0.1.1b5)
network (2.0.0)
nspkg (2.0.0)
profile (2.0.0)
redis (0.1.1b3)
resource (2.0.0)
role (2.0.0)
sql (0.1.1b5)
storage (2.0.0)
vm (2.0.0)

Python (Darwin) 2.7.10 (default, Jul 30 2016, 19:40:32)
[GCC 4.2.1 Compatible Apple LLVM 8.0.0 (clang-800.0.34)]
```

> [!Note]
> 명령 모듈 중 일부에는 "b*n*" 또는 "rc*n*" 접미사가 있습니다. 이러한 명령 모듈은 아직 미리 보기이며 나중에 일반 공급될 것입니다.

또한 야간 미리 보기 CLI 빌드가 있습니다. 자세한 내용은 [야간 빌드 받기](https://github.com/Azure/azure-cli#nightly-builds) 및 [개발자 설치 및 코드 기여](https://github.com/Azure/azure-cli#developer-setup)에 대한 지침을 참조하세요.

다음과 같은 방법으로 야간 미리 보기 빌드의 문제를 보고할 수 있습니다.
- [github 문제 목록](https://github.com/azure/azure-cli/issues/)에서 문제 보고
- 제품팀([azfeedback@microsoft.com](mailto:azfeedback@microsoft.com))에 문의
- `az feedback` 명령을 사용하여 명령줄에서 피드백 제공
