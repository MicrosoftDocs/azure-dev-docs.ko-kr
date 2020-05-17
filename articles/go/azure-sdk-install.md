---
title: Azure SDK for Go 설치
description: Azure SDK for Go 설치, 공급 및 구성 방법.
ms.date: 03/14/2018
ms.topic: conceptual
ms.openlocfilehash: 55b841101f2307e869a57193ce6d678f298b8e6e
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "80319800"
---
# <a name="install-the-azure-sdk-for-go"></a>Azure SDK for Go 설치

Azure SDK for Go 시작! SDK를 사용하면 Go 애플리케이션에서 Azure 서비스를 관리하고 상호 작용할 수 있습니다.

## <a name="get-the-azure-sdk-for-go"></a>Azure SDK for Go 가져오기

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-get.md)]

일부 Azure 서비스에는 자체적으로 Go SDK가 있고, 코어 Azure SDK Go 패키지에는 포함되어 있지 않습니다. 다음 표에서 고유의 SDK가 포함된 서비스 및 해당 패키지 이름이 리스트되어 있습니다. 이러한 패키지는 모두 미리 보기에 있는 것으로 간주됩니다.

| 서비스 | 패키지 |
|---------|---------|
| Blob Storage | [github.com/Azure/azure-storage-blob-go](https://github.com/Azure/azure-storage-blob-go) |
| File Storage | [github.com/Azure/azure-storage-file-go](https://github.com/Azure/azure-storage-file-go) |
| 스토리지 큐 | [github.com/Azure/azure-storage-queue-go](https://github.com/Azure/azure-storage-queue-go) |
| 이벤트 허브 | [github.com/Azure/azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go) |
| Service Bus | [github.com/Azure/azure-service-bus-go](https://github.com/Azure/azure-service-bus-go) |

## <a name="vendor-the-azure-sdk-for-go"></a>Azure SDK for Go 공급하기

Azure SDK for Go는 [dep](https://github.com/golang/dep)를 통해 공급할 수 있습니다. 안정성을 위해서는 공급 방식이 권장됩니다. 고유한 프로젝트에 `dep`를 사용하려면 `Gopkg.toml`의 `[[constraint]]` 섹션에 `github.com/Azure/azure-sdk-for-go`를 추가합니다. 예를 들어 버전 `14.0.0`에 공급하려면 다음 항목을 추가합니다.

```toml
[[constraint]]
name = "github.com/Azure/azure-sdk-for-go"
version = "14.0.0"
```

## <a name="include-the-azure-sdk-for-go-in-your-project"></a>프로젝트에 Azure SDK for Go 포함하기

Go 코드에서 Azure 서비스를 사용하려면 상호 작용하려는 모든 서비스 및 필요한 `autorest` 모듈을 가져옵니다.
GoDoc에서 제공되는 전체 모듈 목록에서는 [사용 가능한 서비스](https://godoc.org/github.com/Azure/azure-sdk-for-go) 및 [AutoRest 패키지](https://godoc.org/github.com/Azure/go-autorest)를 확인할 수 있습니다. `go-autorest`에서 가장 일반적으로 필요한 패키지는 다음과 같습니다.

| 패키지 | Description |
|---------|-------------|
| [github.com/Azure/go-autorest/autorest][autorest] | 서비스 클라이언트 인증을 처리하기 위한 개체 |
| [github.com/Azure/go-autorest/autorest/azure][autorest/azure] | Azure 서비스와의 상호 작용을 위한 상수 |
| [github.com/Azure/go-autorest/autorest/adal][autorest/adal] | Azure 서비스에 액세스하기 위한 인증 메커니즘 |
| [github.com/Azure/go-autorest/autorest/to][autorest/to] | Azure SDK 데이터 구조를 사용하기 위한 형식 어설션 도우미 |

[autorest]: https://godoc.org/github.com/Azure/go-autorest/autorest
[autorest/azure]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure
[autorest/adal]: https://godoc.org/github.com/Azure/go-autorest/autorest/adal
[autorest/to]: https://godoc.org/github.com/Azure/go-autorest/autorest/to

Go 패키지 및 Azure 서비스는 독립적으로 버전 관리됩니다. 서비스 버전은 `services` 모듈 아래의 모듈 가져오기 경로의 일부입니다. 모듈의 전체 경로는 서비스 이름과 `YYYY-MM-DD` 형식의 버전 그리고 다시 서비스 이름으로 구성됩니다. 예를 들어 컴퓨팅 서비스의 `2017-03-30` 버전을 가져오기 위해서는 다음과 같습니다.

```go
import "github.com/Azure/azure-sdk-for-go/services/compute/mgmt/2017-03-30/compute"
```

개발을 시작할 때 서비스의 최신 버전을 사용하고 일관되게 유지하는 것이 좋습니다.
Go SDK 업데이트가 없더라도 버전이 다르면 서비스 요구 사항이 변경되어 코드가 손상될 수도 있습니다.

서비스에 대해 총체적 스냅샷이 필요한 경우 단일 프로필 버전을 선택할 수도 있습니다. 현재까지 유일하게 잠긴 프로필은 버전 `2017-03-09`이며, 여기에는 최신 서비스 기능이 포함되지 않았을 수 있습니다. 프로필은 `profiles` 모듈 아래에 있으며, 버전이 `YYYY-MM-DD` 형식으로 표시되어 있습니다. 서비스는 해당 프로필 버전 아래에 그룹화되어 있습니다. 예를 들어 `2017-03-09` 프로필에서 Azure 리소스 관리 모듈을 가져오려면:

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/resources/mgmt/resources"
```

> [!WARNING]
> 또한 `preview` 및 `latest` 프로필도 사용할 수 있습니다. 이것들은 사용하지 않는 것이 좋습니다. 이러한 프로필은 롤링 버전이므로, 서비스 동작이 언제든지 변경될 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure SDK for Go 사용을 시작하려면 빠른 시작을 참조하십시오.

* [템플릿에서 가상 머신 배포](azure-sdk-qs-vm.md)
* [Azure Blob SDK for Go를 사용하여 Azure Blob Storage에 개체 전송](/azure/storage/blobs/storage-quickstart-blobs-go?toc=/azure/developer/go/toc.json)
* [Azure Database for PostgreSQL에 연결](/azure/postgresql/connect-go?toc=/azure/developer/go/toc.json)

Go SDK에서 직접 다른 서비스를 시작하려면, 제공되는 샘플 코드 중 일부를 참조하십시오.

* [Azure 서비스를 사용하여 인증](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/internal/iam)
* [SSH 인증으로 새로운 가상 머신 배포](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)
* [Azure Container Instances에 컨테이너 이미지 배포](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)
* [Azure Kubernetes Service에서 클러스터 만들기](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute)
* [Azure Storage 서비스 작업](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage)
* [Azure SDK for Go를 위한 모든 샘플](https://github.com/azure-samples/azure-sdk-for-go-samples)
