---
title: Java용 Azure SDK 사용
description: Azure 리소스를 프로비저닝, 사용 및 관리하는 경우 생산성을 높이는 데 도움이 되는 Java용 Azure SDK의 특징과 기능을 소개합니다.
author: jonathangiles
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: jogiles
ms.openlocfilehash: 64a13c99f58b109a066acd322aa90f39a74ea0e9
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528630"
---
# <a name="use-the-azure-sdk-for-java"></a>Java용 Azure SDK 사용

Java용 오픈 소스 Azure SDK는 Java 애플리케이션 코드에서 Azure 리소스를 간편하게 프로비저닝하고, 관리하고, 사용할 수 있게 해줍니다.

## <a name="important-details"></a>중요 세부 정보

* Azure 라이브러리는 로컬로 또는 클라우드에서 실행하는 Java 코드에서 Azure 서비스와 통신하는 방법입니다.
* 이 라이브러리는 Java 8 이상을 지원하며, Java 8 기준과 최신 Java '장기 지원' 릴리스 모두에 대한 테스트를 거쳤습니다.
* 이 라이브러리에는 전체 Java 모듈 지원이 포함되어 있으므로 Java 모듈의 요구 사항을 완벽하게 준수하며, 모든 관련 패키지를 내보내서 사용할 수 있습니다.
* Java용 Azure SDK는 특정 Azure 서비스와 관련된 여러 개의 개별 Java 라이브러리로만 구성되어 있습니다. "SDK"에는 다른 도구가 없습니다.
* "관리" 및 "클라이언트" 라이브러리("관리 평면" 및 "데이터 평면" 라이브러리라고도 함)가 별도로 있습니다. 각 세트는 서로 다른 용도로 사용되며 서로 다른 종류의 코드에 사용됩니다. 자세한 내용은 이 문서의 뒷부분에 나오는 다음 섹션을 참조하세요.
  * [클라이언트 라이브러리를 사용하여 Azure 리소스에 연결 및 사용](#connect-to-and-use-azure-resources-with-client-libraries)
  * [관리 라이브러리를 사용하여 Azure 리소스 프로비저닝 및 관리](#provision-and-manage-azure-resources-with-management-libraries)
* 라이브러리에 대한 설명서는 [Java용 Azure 참조](/java/api/overview/azure/)(Azure 서비스로 구성되어 있음) 또는 [Java API 브라우저](/java/api/)(패키지 이름으로 구성되어 있음)에서 찾을 수 있습니다.

## <a name="other-details"></a>기타 세부 정보

* Java용 Azure SDK 라이브러리는 기본 Azure REST API를 기반으로 빌드되기 때문에, 익숙한 Java 패러다임을 통해 해당 API를 사용할 수 있습니다. 하지만 필요할 때는 언제든지 Java 코드에서 직접 REST API를 사용할 수 있습니다.
* [GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-java)에서 Azure 라이브러리용 소스 코드를 찾을 수 있습니다. 오픈 소스 프로젝트이기 때문에 여러분의 기여를 환영합니다!
* 현재 인증 프로토콜, 로깅, 추적, 전송 프로토콜, 버퍼링된 응답 및 재시도와 같은 일반적인 클라우드 패턴을 공유하도록 Java용 Azure SDK 라이브러리를 업데이트하고 있습니다.
  * 이 공유 기능은 [azure-core](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/core/azure-core) 라이브러리에 포함되어 있습니다.
* 라이브러리에 적용되는 지침에 대한 자세한 내용은 [Java Azure SDK 설계 지침](https://azure.github.io/azure-sdk/java_introduction.html)을 참조하세요.

## <a name="connect-to-and-use-azure-resources-with-client-libraries"></a>클라이언트 라이브러리를 사용하여 Azure 리소스에 연결 및 사용

클라이언트(또는 "데이터 평면") 라이브러리는 이미 프로비저닝된 서비스와 상호 작용하는 Java 애플리케이션 코드를 작성하는 데 유용합니다. 클라이언트 라이브러리는 클라이언트 API를 지원하는 서비스를 위해서만 존재합니다. Maven 그룹 ID는 `com.azure`이므로 식별할 수 있습니다.

모든 Azure Java 클라이언트 라이브러리는 클라이언트의 인스턴스를 만드는 작업을 담당하는 Java 작성기 클래스를 제공하는 패턴과 동일한 API 디자인 패턴을 따릅니다. 이 패턴은 클라이언트의 정의 및 인스턴스화를 작업과 분리하므로 클라이언트를 변경할 수 없어 더 쉽게 사용할 수 있습니다. 또는 모든 클라이언트 라이브러리가 몇 가지 중요한 패턴을 따릅니다.

* 동기 API와 비동기 API를 모두 지원하는 클라이언트 라이브러리는 별도의 클래스에서 이러한 API를 제공해야 합니다. 예를 들어 이러한 경우 동기 API용 `KeyVaultClient`와 비동기 API용 `KeyVaultAsyncClient`가 있습니다.

* 동기 API 및 비동기 API 작성 작업을 모두 담당하는 단일 작성기 클래스가 있습니다. 이 작성기의 이름은 동기 클라이언트 클래스와 `Builder`를 포함합니다. 예: `KeyVaultClientBuilder`. 이 작성기에는 `buildClient()` 및 `buildAsyncClient()` 메서드가 있으므로 클라이언트 인스턴스를 적절히 만들 수 있습니다.

이러한 규칙으로 인해 `Client`로 끝나는 모든 클래스는 변경할 수 없으며 Azure 서비스와 상호 작용하는 작업을 제공합니다. `ClientBuilder`로 끝나는 모든 클래스는 특정 클라이언트 유형의 인스턴스를 구성하고 만드는 작업을 제공합니다.

### <a name="client-libraries-example"></a>클라이언트 라이브러리 예

다음 코드 예제는 동기 Key Vault `KeyClient`를 만드는 방법을 보여 줍니다.

```java
KeyClient client = new KeyClientBuilder()
        .endpoint(<your Key Vault URL>)
        .credential(new DefaultAzureCredentialBuilder().build())
        .buildClient();
```

다음 코드 예제는 동기 Key Vault `KeyAsyncClient`를 만드는 방법을 보여 줍니다.

```java
KeyAsyncClient client = new KeyClientBuilder()
        .endpoint(<your Key Vault URL>)
        .credential(new DefaultAzureCredentialBuilder().build())
        .buildAsyncClient();
```

각 클라이언트 라이브러리를 사용하는 방법에 대한 자세한 내용은 [SDK GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-java)에서 라이브러리의 프로젝트 디렉터리에 있는 *README.md* 파일을 참조하세요. [참조 설명서](/java/api) 및 [Azure 샘플](/samples/browse/?products=azure&languages=java)에서 추가 코드 조각을 확인할 수도 있습니다.

## <a name="provision-and-manage-azure-resources-with-management-libraries"></a>관리 라이브러리를 사용하여 Azure 리소스 프로비저닝 및 관리

관리(또는 "관리 평면") 라이브러리는 Java 애플리케이션 코드에서 Azure 리소스를 생성, 프로비저닝 및 관리하는 데 유용합니다. `com.azure.resourcemanager` Maven 그룹 ID에서 이러한 라이브러리를 찾을 수 있습니다. 모든 Azure 서비스에는 해당하는 관리 라이브러리가 있습니다.

관리 라이브러리를 사용하면 구성 및 배포 스크립트를 작성하여 [Azure Portal](https://portal.azure.com/)이나 [Azure CLI](/cli/azure/install-azure-cli)를 통해 수행할 수 있는 동일한 작업을 수행할 수 있습니다.

모든 Azure Java 관리 라이브러리는 `*Manager` 클래스를 서비스 API로 제공합니다(예: Azure 컴퓨팅 서비스용 `ComputeManager` 또는 주요 서비스 집합용 `AzureResourceManager`).

### <a name="management-libraries-example"></a>관리 라이브러리 예

다음 코드 예제는 `ComputeManager`를 만드는 방법을 보여 줍니다.

```java
ComputeManager computeManager = ComputeManager
    .authenticate(
        new DefaultAzureCredentialBuilder().build(),
        new AzureProfile(AzureEnvironment.AZURE));
```

다음 코드 예제는 새로운 가상 머신을 프로비저닝하는 방법을 보여 줍니다.

```java
VirtualMachine virtualMachine = computeManager.virtualMachines()
    .define(<your virtual machine>)
    .withRegion(Region.US_WEST)
    .withExistingResourceGroup(<your resource group>)
    .withNewPrimaryNetwork("10.0.0.0/28")
    .withPrimaryPrivateIPAddressDynamic()
    .withoutPrimaryPublicIPAddress()
    .withPopularLinuxImage(KnownLinuxVirtualMachineImage.UBUNTU_SERVER_18_04_LTS)
    .withRootUsername(<virtual-machine username>)
    .withSsh(<virtual-machine SSH key>)
    .create();
```

다음 코드 예제는 기존 가상 머신을 가져오는 방법을 보여 줍니다.

```java
VirtualMachine virtualMachine = computeManager.virtualMachines()
    .getByResourceGroup(<your resource group>, <your virtual machine>);
```

다음 코드 예제는 가상 머신을 업데이트하고 새 데이터 디스크를 추가하는 방법을 보여 줍니다.

```java
virtualMachine.update()
    .withNewDataDisk(10)
    .apply();
```

각 관리 라이브러리를 사용하는 방법에 대한 자세한 내용은 [SDK GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/resourcemanager#readme)에서 라이브러리의 프로젝트 디렉터리에 있는 *README.md* 파일을 참조하세요. [참조 설명서](/java/api) 및 [Azure 샘플](/samples/browse/?products=azure&languages=java)에서 추가 코드 조각을 확인할 수도 있습니다.

## <a name="get-help-and-connect-with-the-sdk-team"></a>SDK 팀과 교류하고 도움 받기

* [Java용 Azure SDK 설명서](https://azure.github.io/azure-sdk-for-java/)를 참조하세요.
* [Stack Overflow(영문)](https://stackoverflow.com/questions/tagged/azure-sdk-for-java)의 커뮤니티에 질문을 게시합니다.
* [GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-java/issues)에서 SDK에 대한 문제를 엽니다.
* Twitter에서 [@AzureSDK](https://twitter.com/AzureSdk/)를 언급합니다.

## <a name="next-steps"></a>다음 단계

이제 Java용 Azure SDK의 기능을 이해했으므로 라이브러리를 생산적으로 사용할 수 있게 해주는 여러 가지 공통 개념을 자세히 살펴볼 수 있습니다. 다음 문서는 좋은 시작점을 제공합니다.

* [HTTP 클라이언트 및 파이프라인](http-client-pipeline.md)
* [비동기 프로그래밍](async-programming.md)
* [페이지 매김 및 반복](pagination.md)
* [장기 실행 작업](lro.md)
* [프록시 구성](proxying.md)
* [추적 구성](tracing.md)
