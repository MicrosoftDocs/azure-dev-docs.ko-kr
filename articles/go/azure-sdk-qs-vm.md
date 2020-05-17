---
title: Go에서 Azure 가상 머신 배포
description: Azure SDK for Go를 사용하여 가상 머신을 배포합니다.
ms.date: 09/05/2018
ms.topic: quickstart
ms.openlocfilehash: d339681fc4eed55046f5a7c8fa45fffc948fa3bc
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "80319770"
---
# <a name="quickstart-deploy-an-azure-virtual-machine-from-a-template-with-the-azure-sdk-for-go"></a>빠른 시작: Azure SDK for Go를 사용하여 템플릿에서 Azure 가상 머신 배포

이 빠른 시작은 Go용 Azure SDK를 사용하여 Azure Resource Manager 템플릿에서 리소스를 배포하는 방법을 보여줍니다. 템플릿은 [Azure 리소스 그룹](/azure/azure-resource-manager/resource-group-overview) 내의 모든 리소스에 대한 스냅샷입니다. 과정을 진행하는 동안 SDK의 기능 및 규칙에 익숙해질 수 있습니다.

이 빠른 시작을 마치면 가상 머신을 실행하고 사용자 이름 및 암호를 사용해서 로그인하게 됩니다.

> [!NOTE]
> Resource Manager 템플릿을 사용하지 않고 Go에서 VM 생성하는 방법을 보려면 SDK로 모든 VM 리소스를 만들고 구성하는 방법을 보여주는 [필수 예제](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/vm.go)가 있습니다. 이 예제에서 템플릿을 사용하면 Azure 서비스 아키텍처에 대한 세부 정보를 너무 많이 알아보지 않고도 SDK 규칙에 중점을 둘 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

Azure CLI의 로컬 설치를 사용할 경우, 이 빠른 시작을 위해서는 CLI 버전 __2.0.28__ 이상이 필요합니다. `az --version`을(를) 실행하여 CLI 설치가 이 요구 사항을 충족하는지 확인하십시오. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="install-the-azure-sdk-for-go"></a>Azure SDK for Go 설치

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-get.md)]

## <a name="create-a-service-principal"></a>서비스 주체 만들기

애플리케이션으로 Azure에 비 대화형으로 로그인하려면 서비스 주체가 필요합니다. 서비스 주체는 고유한 사용자 ID를 만드는 RBAC(역할 기반 액세스 제어)의 일부입니다. CLI를 사용하여 새로운 서비스 주체를 만들려면 다음 명령을 실행합니다.

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth > quickstart.auth
```

환경 변수 `AZURE_AUTH_LOCATION`을 이 파일의 전체 경로가 되도록 설정합니다. 그러면 어떠한 변경을 하거나 서비스 주체로부터 정보를 기록하지 않아도 SDK가 이 파일에서 직접 자격 증명을 찾아서 읽습니다.

## <a name="get-the-code"></a>코드 가져오기

빠른 시작 코드 및 `go get`의 모든 종속성을 가져옵니다.

```bash
go get -u -d github.com/azure-samples/azure-sdk-for-go-samples/quickstarts/deploy-vm/...
```

`AZURE_AUTH_LOCATION` 변수가 적절히 설정되는 경우 소스 코드를 수정할 필요가 없습니다. 프로그램을 실행하면 여기에서 모든 필요한 인증 정보를 로드합니다.

## <a name="running-the-code"></a>코드 실행

`go run` 명령으로 빠른 시작을 실행합니다.

```bash
cd $GOPATH/src/github.com/azure-samples/azure-sdk-for-go-samples/quickstarts/deploy-vm
go run main.go
```

배포가 성공하면 새롭게 생성된 가상 머신에 로그인하기 위한 사용자 이름, IP 주소 및 암호를 제공하는 메시지가 표시됩니다. 이 시스템에 SSH로 로그인하여 시스템이 작동되어 실행 중인지 알아봅니다. 

## <a name="cleaning-up"></a>정리

CLI를 사용해서 리소스 그룹을 삭제하여 이 빠른 시작 중 생성된 리소스를 정리합니다.

```azurecli-interactive
az group delete -n GoVMQuickstart
```

또한 만든 서비스 주체를 삭제합니다. `quickstart.auth` 파일에 `clientId`에 대한 JSON 키가 있습니다. 이 값을 `CLIENT_ID_VALUE` 환경 변수에 복사하고 다음 Azure CLI 명령을 실행합니다.

```azurecli-interactive
az ad sp delete --id ${CLIENT_ID_VALUE}
```

여기서 `quickstart.auth`로부터 `CLIENT_ID_VALUE`에 대한 값을 제공합니다.

> [!WARNING]
> 이 애플리케이션의 서비스 주체를 삭제하지 못하면 Azure Active Directory 테넌트를 활성 상태로 두게 됩니다.
> 서비스 보안 주체의 이름과 암호는 모두 UUID로 생성되지만, 사용하지 않는 서비스 주체와 Azure Active Directory 애플리케이션을 삭제하여 모범 보안 관행을 따르도록 하십시오.

## <a name="code-in-depth"></a>코드 심화 안내

빠른 시작 코드는 변수 및 여러 작은 기능들의 블록으로 세분화됩니다. 여기에서는 이러한 각 블록에 대해 자세히 설명합니다.

### <a name="variables-constants-and-types"></a>변수, 상수 및 형식

빠른 시작은 자체 포함되므로, 전역 상수와 변수를 사용합니다.

```go
const (
    resourceGroupName     = "GoVMQuickstart"
    resourceGroupLocation = "eastus"

    deploymentName = "VMDeployQuickstart"
    templateFile   = "vm-quickstart-template.json"
    parametersFile = "vm-quickstart-params.json"
)

// Information loaded from the authorization file to identify the client
type clientInfo struct {
    SubscriptionID string
    VMPassword     string
}

var (
    ctx        = context.Background()
    clientData clientInfo
    authorizer autorest.Authorizer
)
```

값은 생성된 리소스의 이름을 제공하도록 선언됩니다. 또한 여기에 지정된 위치는 다른 데이터 센터에서의 배포 동작을 확인할 수 있도록 변경할 수 있습니다. 일부 데이터 센터에는 필요한 리소스가 없을 수도 있습니다.

`clientInfo` 형식은 SDK에서 클라이언트를 설정하고 VM 암호를 설정하기 위해 인증 파일에서 로딩된 정보를 가지고 있습니다.

`templateFile` 및 `parametersFile` 상수는 배포에 필요한 파일을 가리킵니다. `authorizer`는 인증을 위해 Go SDK를 통해 구성되며 `ctx` 변수는 네트워크 작업에 대한 [Go 컨텍스트](https://blog.golang.org/context)입니다.

### <a name="authentication-and-initialization"></a>인증 및 초기화

`init` 함수가 인증을 설정합니다. 인증은 빠른 시작에 포함된 모든 항목들의 사전 조건이므로, 초기화 중에 준비하는 것이 좋습니다. 또한 클라이언트와 VM을 구성하기 위해 인증 파일에서 필요한 일부 정보를 로드합니다.

```go
func init() {
    var err error
    authorizer, err = auth.NewAuthorizerFromFile(azure.PublicCloud.ResourceManagerEndpoint)
    if err != nil {
        log.Fatalf("Failed to get OAuth config: %v", err)
    }

    authInfo, err := readJSON(os.Getenv("AZURE_AUTH_LOCATION"))
    clientData.SubscriptionID = (*authInfo)["subscriptionId"].(string)
    clientData.VMPassword = (*authInfo)["clientSecret"].(string)
}
```

먼저, `AZURE_AUTH_LOCATION`에 있는 파일에서 인증 정보를 로드하기 위해 [auth.NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile)이 호출됩니다. 그 다음, 이 파일을 `readJSON` 함수(여기서는 생략됨)에서 수동으로 로드하여 프로그램의 나머지 부분을 실행하는 데 필요한 두 개의 값(클라이언트의 구독 ID 및 VM의 암호에도 사용되는 서비스 주체의 비밀 정보)을 끌어옵니다.

> [!WARNING]
> 빠른 시작을 간단하게 유지하기 위해 서비스 주체 암호가 재사용됩니다. 프로덕션 환경에서는 Azure 리소스에 대한 액세스 권한을 부여하는 암호를 __절대__ 재사용하지 않도록 하십시오.

### <a name="flow-of-operations-in-main"></a>main()의 작업 흐름

`main` 함수는 작업 흐름만 나타내고 오류 검사만 수행하는 간단한 함수입니다.

```go
func main() {
    group, err := createGroup()
    if err != nil {
        log.Fatalf("failed to create group: %v", err)
    }
    log.Printf("Created group: %v", *group.Name)

    log.Printf("Starting deployment: %s", deploymentName)
    result, err := createDeployment()
    if err != nil {
        log.Fatalf("Failed to deploy: %v", err)
    }
    if result.Name != nil {
        log.Printf("Completed deployment %v: %v", deploymentName, *result.Properties.ProvisioningState)
    } else {
        log.Printf("Completed deployment %v (no data returned to SDK)", deploymentName)
    }
    getLogin()
}
```

코드가 실행하는 단계는 순서대로 다음과 같습니다.

* (`createGroup`)에 배포할 리소스 그룹 만들기
* 이 그룹(`createDeployment`) 내에 배포 만들기
* 배포된 가상 머신(`getLogin`)에 대한 로그인 정보 가져오기 및 표시

### <a name="create-the-resource-group"></a>리소스 그룹 만들기

`createGroup` 함수는 리소스 그룹을 만듭니다. 호출 흐름 및 인수를 보면 서비스 상호 작용이 SDK에 구성된 방식을 알 수 있습니다.

```go
func createGroup() (group resources.Group, err error) {
    groupsClient := resources.NewGroupsClient(clientData.SubscriptionID)
    groupsClient.Authorizer = authorizer

        return groupsClient.CreateOrUpdate(
                ctx,
                resourceGroupName,
                resources.Group{
                        Location: to.StringPtr(resourceGroupLocation)})
}
```

Azure 서비스와의 일반적인 상호 작용 흐름은 다음과 같습니다.

* `service.New*Client()` 메서드를 사용하여 클라이언트를 만듭니다. 여기서 `*`은(는) 상호 작용하려는 `service`의 리소스 유형입니다. 이 함수는 항상 구독 ID를 사용합니다.
* 클라이언트에 대한 권한 부여 방법을 설정해서 원격 API와 상호 작용할 수 있도록 허용합니다.
* 원격 API에 따라 클라이언트에서 메서드 호출을 수행합니다. 서비스 클라이언트 메서드는 일반적으로 리소스 이름 및 메타데이터 개체를 사용합니다.

여기에서 [`to.StringPtr`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) 함수는 형식 변환을 수행하기 위해 사용됩니다. SDK 메서드에 대한 매개 변수는 거의 배타적으로 포인터를 사용하므로, 형식 변환을 쉽게 하기 위해 편리한 메서드가 제공되었습니다. 편리한 변환기의 전체 목록 및 동작을 보려면 [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to) 모듈 설명서를 참조하십시오.

`groupsClient.CreateOrUpdate` 메서드는 리소스 그룹을 나타내는 데이터 형식에 대한 포인터를 반환합니다. 이러한 종류의 직접 반환 값은 동기적으로 수행되어야 하는 단기 실행 작업을 나타냅니다. 다음 섹션에서는 장기 실행 작업의 예 및 이와 상호 작용하는 방법을 배웁니다.

### <a name="perform-the-deployment"></a>배포 수행

리소스 그룹을 만든 다음에는 배포를 실행해야 합니다. 이 코드는 여러 논리 부분을 강조하기 위해 더 작은 섹션으로 구분됩니다.

```go
func createDeployment() (deployment resources.DeploymentExtended, err error) {
    template, err := readJSON(templateFile)
    if err != nil {
        return
    }
    params, err := readJSON(parametersFile)
    if err != nil {
        return
    }
    (*params)["vm_password"] = map[string]string{
        "value": clientData.VMPassword,
    }
        // ...
```

배포 파일은 `readJSON`에서 로드되며, 이에 대한 세부 사항은 생략되어 있습니다. 이 함수는 리소스 배포 호출을 위한 메타 데이터를 생성하는 데 사용되는 형식인 `*map[string]interface{}`을(를) 반환합니다. VM의 암호도 배포 매개 변수에서 수동으로 설정됩니다.

```go
        // ...

    deploymentsClient := resources.NewDeploymentsClient(clientData.SubscriptionID)
    deploymentsClient.Authorizer = authorizer

    deploymentFuture, err := deploymentsClient.CreateOrUpdate(
        ctx,
        resourceGroupName,
        deploymentName,
        resources.Deployment{
            Properties: &resources.DeploymentProperties{
                Template:   template,
                Parameters: params,
                Mode:       resources.Incremental,
            },
        },
    )
    if err != nil {
        return
    }
```

이 코드는 리소스 그룹을 만들 때와 동일한 패턴을 따릅니다. 새로운 클라이언트가 생성되고 Azure에서 인증을 수행할 수 있는 기능이 제공된 다음, 메서드가 호출됩니다.
이 메서드도 리소스 그룹의 해당 메서드와 동일한 이름(`CreateOrUpdate`)을 갖습니다. 이 패턴은 SDK 전체에서 나타납니다.
일반적으로 비슷한 작업을 수행하는 메서드는 동일한 이름을 갖습니다.

가장 큰 차이점은 `deploymentsClient.CreateOrUpdate` 메서드의 반환 값에 있습니다. 이 값은 [미래 디자인 패턴](https://en.wikipedia.org/wiki/Futures_and_promises)을 따르는 [미래](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#Future) 형식입니다. 미래는 Azure에서 완료 시 폴링, 취소 또는 차단할 수 있는 장기 실행 작업을 나타냅니다.

```go
        //...
    err = deploymentFuture.Future.WaitForCompletion(ctx, deploymentsClient.BaseClient.Client)
    if err != nil {
        return
    }
    return deploymentFuture.Result(deploymentsClient)
}
```

이 예에서 수행할 최상의 작업은 이 작업이 완료될 때까지 기다리는 것입니다. 미래 개체를 기다리기 위해서는 [컨텍스트 개체](https://blog.golang.org/context) 및 `Future`를 생성한 클라이언트가 모두 필요합니다. 여기에서 가능한 두 가지 오류 소스는 메서드를 호출하려고 시도할 때 클라이언트 측에서 발생한 오류와 서버에서의 오류 응답입니다. 후자는 `deploymentFuture.Result` 호출 중에 반환됩니다.

### <a name="get-the-assigned-ip-address"></a>할당된 IP 주소 가져오기

새로 만든 가상 머신에서 어떤 작업을 수행하려면 할당된 IP 주소가 필요합니다. IP 주소는 NIC(네트워크 인터페이스 컨트롤러) 리소스에 바인딩된 고유의 개별 Azure 리소스입니다.

```go
func getLogin() {
    params, err := readJSON(parametersFile)
    if err != nil {
        log.Fatalf("Unable to read parameters. Get login information with `az network public-ip list -g %s", resourceGroupName)
    }

    addressClient := network.NewPublicIPAddressesClient(clientData.SubscriptionID)
    addressClient.Authorizer = authorizer
    ipName := (*params)["publicIPAddresses_QuickstartVM_ip_name"].(map[string]interface{})
    ipAddress, err := addressClient.Get(ctx, resourceGroupName, ipName["value"].(string), "")
    if err != nil {
        log.Fatalf("Unable to get IP information. Try using `az network public-ip list -g %s", resourceGroupName)
    }

    vmUser := (*params)["vm_user"].(map[string]interface{})

    log.Printf("Log in with ssh: %s@%s, password: %s",
        vmUser["value"].(string),
        *ipAddress.PublicIPAddressPropertiesFormat.IPAddress,
        clientData.VMPassword)
}
```

이 메서드는 매개 변수 파일에 저장된 정보에 의존합니다. 이 코드는 가상 머신에 직접 쿼리해서 해당 NIC를 가져오고, NIC에 쿼리해서 해당 IP 주소를 가져온 후, IP 리소스를 직접 쿼리할 수 있습니다. 이것은 종속성 및 작업이 길게 늘어진 분석 체인이기 때문에 비용이 높습니다. JSON 정보는 로컬이기 때문에 대신 로드할 수 있습니다.

VM 사용자에 대한 값은 JSON에서도 로드됩니다. 이전에는 VM 암호가 인증 파일에서 로드되었습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 기존 템플릿을 사용해서 Go를 통해 배포했습니다. 그런 다음 SSH를 통해 새롭게 생성된 가상 머신에 연결했습니다.

Go를 사용한 Azure 환경에서 가상 머신을 사용하는 방법에 대해 학습을 계속하려면 [Go용 Azure 컴퓨팅 샘플](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute) 또는 [Go용 Azure 리소스 관리 샘플](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources)을 참조하십시오.

SDK에서 사용 가능한 인증 방법 및 지원되는 인증 유형에 대해 알아보려면 [Azure SDK for Go를 사용한 인증](azure-sdk-authorization.md)을 참조하십시오.
