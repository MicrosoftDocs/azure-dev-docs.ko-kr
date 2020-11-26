---
title: Java용 Azure 관리 라이브러리를 사용하여 개발
description: Azure에서 클라우드 리소스를 관리하기 위해 Java용 Java 관리 라이브러리를 사용하기 위한 패턴 및 개념입니다.
keywords: Azure, Java, SDK, API, Maven, Gradle, 인증, Active Directory, 서비스 사용자
author: bmitchell287
ms.author: brendm
ms.date: 04/16/2017
ms.topic: article
ms.service: multiple
ms.assetid: f452468b-7aae-4944-abad-0b1aaf19170d
ms.custom: seo-java-july2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: ae2c97e8febc60f8dd949ecf15facd06ae7c63ef
ms.sourcegitcommit: 4dac39849ba2e48034ecc91ef578d11aab796e58
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94983932"
---
# <a name="patterns-and-best-practices-for-development-with-the-azure-libraries-for-java"></a>Java용 Azure 라이브러리를 통한 개발 패턴 및 모범 사례 

이 문서에서는 프로젝트에서 Java용 Azure 라이브러리를 사용할 때의 여러 패턴과 모범 사례를 나열합니다. 이러한 패턴 및 지침을 통해 개발하면 코드 유지 관리 작업을 줄이고 향후 업데이트에서 더 간편하게 다른 리소스를 관리 라이브러리에 추가하거나 구성할 수 있습니다.

## <a name="build-resources-through-a-fluent-interface"></a>흐름 인터페이스를 통해 리소스 빌드

흐름 인터페이스는 개체의 특성을 올바르게 구성하는 메서드 체인을 사용하여 개체를 만드는 패턴입니다. 예를 들어 Azure Storage 계정을 새로 만드는 경우 다음과 같습니다.

```java
StorageAccount storage = azure.storageAccounts().define(storageAccountName)
    .withRegion(region)
    .withNewResourceGroup(resourceGroup)
    .create();
```

메서드 체인을 진행할 때 IDE에서 다음과 같은 흐름 대화를 호출하는 메서드를 제안합니다.   

![흐름 체인을 통한 IntelliJ 명령 완료 작업을 보여 주는 GIF](media/intellij-fluent-method-chain.gif)

정의되는 Azure 리소스가 인식되는 한 IDE에서 제안하는 메서드를 연결합니다. 체인에 필요한 메서드가 누락되면 IDE에서 오류와 함께 강조 표시합니다.

## <a name="resource-collections"></a>리소스 컬렉션

관리 라이브러리에는 리소스를 만들고 업데이트하는 최상위 수준의 `com.microsoft.azure.management.Azure` 개체를 통한 단일 진입점이 있습니다. `Azure` 개체에 정의된 리소스 컬렉션 메서드를 사용하여 사용할 리소스 종류를 선택합니다. 예를 들어 SQL Database의 경우 다음과 같습니다.

```java
SqlServer sqlServer = azure.sqlServers().define(sqlServerName)
    .withRegion(Region.US_EAST)
    .withNewResourceGroup(rgName)
    .withAdministratorLogin(administratorLogin)
    .withAdministratorPassword(administratorPassword)
    .create();
```

## <a name="lists-and-iterations"></a>목록 및 반복

각 리소스 컬렉션에는 현재 구독에 있는 해당 리소스의 모든 인스턴스를 반환하는 `list()` 메서드가 있습니다. 예를 들어 `azure.sqlServers().list()`는 구독의 모든 SQL 데이터베이스를 반환합니다.

`listByResourceGroup(String groupname)` 메서드를 사용하여 반환된 List의 범위를 특정 [Azure 리소스 그룹](/azure/azure-resource-manager/resource-group-overview#resource-groups)으로 지정합니다.  

일반 `List<T>`와 마찬가지로 반환된 `PagedList<T>` 컬렉션을 검색하고 반복합니다.

```java
PagedList<VirtualMachine> vms = azure.virtualMachines().list();
for (VirtualMachine vm : vms) {
    System.out.println("Found virtual machine with ID " + vm.id());
}
```   

## <a name="collections-returned-from-queries"></a>쿼리에서 반환되는 컬렉션

관리 라이브러리는 결과의 구조에 따라 쿼리에서 특정 컬렉션 형식을 반환합니다.

- `List<T>`: 순서가 지정되지 않았지만 쉽게 검색하고 반복할 수 있는 데이터입니다.
- `Map<T>`: 고유 키가 있는 키/값 쌍이지만 반드시 고유한 값은 아닙니다. Map의 예로 App Service 웹앱에 대한 앱 설정이 있습니다.
- `Set<T>`: 고유한 키와 값이 있습니다. Set의 예로 고유 식별자(키)와 고유 네트워크 구성(값)이 모두 있는 가상 머신에 연결된 네트워크가 있습니다.

## <a name="actionable-verbs"></a>실행 가능한 동사

이름에 동사가 있는 메서드는 Azure에서 즉시로 작업을 수행합니다. 이러한 메서드는 동기적으로 작동하고 완료될 때까지 현재 스레드의 실행을 차단합니다. 

| 동사   |  샘플 사용 |
|--------|---------------|
| create | `azure.virtualMachines().create(listOfVMCreatables)` |
| apply  | `virtualMachineScaleSet.update().withCapacity(6).apply()` |
| delete | `azure.disks().deleteById(id)` | 
| list   | `azure.sqlServers().list()` | 
| get    | `VirtualMachine vm  = azure.virtualMachines().getByResourceGroup(group, vmName)` |

>[!NOTE]
> `define()` 및 `update()`는 동사이지만 `create()` 또는 `apply()`가 뒤에 나오지 않으면 차단하지 않습니다.
 
이러한 메서드 중 일부의 비동기 버전은 [Reactive eXtension(영문)](https://github.com/ReactiveX/RxJava)을 사용하여 `Async` 접미사와 함께 존재합니다. 

일부 개체에는 Azure에서 리소스의 상태를 변경하는 다른 메서드가 있습니다. 예를 들어 `VirtualMachine`의 `restart()`와 같습니다.

```java
VirtualMachine vmToRestart = azure.getVirtualMachines().getById(id);
vmToRestart.restart();
```
이러한 메서드는 항상 비동기 버전을 가지고 있는 것은 아니며, 완료될 때까지 해당 스레드의 실행을 차단합니다.

<a name="Creatables"></a>

## <a name="lazy-resource-creation"></a>지연 리소스 만들기

Azure 리소스를 만들 때 새 리소스가 아직 존재하지 않는 다른 리소스에 종속되면 문제가 발생합니다. 이 시나리오의 예로, 새 가상 머신을 만들 때 공용 IP 주소를 예약하고 디스크를 설정합니다. 주소 예약 또는 디스크 만들기를 확인하지 않으려는 경우 가상 머신을 만들 때 이러한 리소스가 있는지 확인만 하면 됩니다.

`Creatable<T>` 개체를 사용하면 구독에 Azure 리소스를 만들 때까지 기다리지 않고 코드에서 사용할 수 있도록 이러한 리소스를 정의할 수 있습니다. 관리 라이브러리는 `Creatable<T>` 개체가 필요할 때까지 기다린 후에 이러한 개체를 만듭니다.

`define()` 동사를 통해 Azure 리소스에 대한 `Creatable<T>` 개체를 생성하는 예제는 다음과 같습니다:

```java
Creatable<PublicIPAddress> publicIPAddressCreatable = azure.publicIPAddresses().define(publicIPAddressName)
    .withRegion(Region.US_EAST)
    .withNewResourceGroup(rgName);
```

이 예제에서 `Creatable<PublicIPAddress>`로 정의된 Azure 리소스는 이 코드를 실행할 때는 아직 구독에 존재하지 않습니다.  `publicIPAddressCreatable` 개체를 사용하여 이 IP 주소가 있는 다른 Azure 리소스를 만듭니다. 

```java
Creatable<VirtualMachine> vmCreatable = azure.virtualMachines().define("creatableVM")
    .withNewPrimaryPublicIPAddress(publicIPAddressCreatable)
```

개체를 사용하여 정의된 리소스가 Azure에서 `create()`를 사용하여 빌드될 때 구독에 `Creatable<T>` 리소스가 생성됩니다. IP 주소 및 가상 머신 예제를 계속하는 예제는 다음과 같습니다.

```java
CreatedResources<VirtualMachine> virtualMachine = azure.virtualMachines().create(vmCreatable);
```

`Creatable<T>`를 `create()` 호출에 전달하면 단일 리소스 개체 대신 `CreatedResources` 개체가 반환됩니다.  `CreatedResources<T>` 개체를 사용하면 호출에서 형식화된 리소스 외에도 `create()` 호출로 만든 모든 리소스에 액세스할 수 있습니다. 위의 예제에서 만든 가상 머신에 대해 Azure에서 만든 공용 IP 주소에 액세스하려면 다음을 수행합니다.

```java
PublicIPAddress pip = (PublicIPAddress) virtualMachine.createdRelatedResource(publicIPAddressCreatable.key());
```    

## <a name="exception-handling"></a>예외 처리

관리 라이브러리의 Exception 클래스는 `com.microsoft.rest.RestException`을 확장합니다. 관련된 `try` 문 뒤에 `catch (RestException exception)` 블록이 있는 관리 라이브러리에서 생성된 예외를 catch합니다.

## <a name="logs-and-trace"></a>로그 및 추적

`withLogLevel()`을 사용하여 `Azure` 진입점 개체를 빌드할 때 관리 라이브러리에서 로깅의 양을 구성합니다. 다음과 같은 추적 수준이 있습니다.

| 추적 수준 | 사용되는 로깅 
| ------------ | ---------------
| com.microsoft.rest.LogLevel.NONE | 출력 없음
| com.microsoft.rest.LogLevel.BASIC | 기본 REST 호출, 응답 코드 및 시간에 URL을 기록합니다.
| com.microsoft.rest.LogLevel.BODY | BASIC의 모든 항목 및 REST 호출에 대한 요청/응답 본문
| com.microsoft.rest.LogLevel.HEADERS | BASIC의 모든 항목 및 REST 호출에 대한 요청/응답 헤더
| com.microsoft.rest.LogLevel.BODY_AND_HEADERS | BODY 및 HEADERS 로그 수준 둘 다의 모든 항목

[Log4J 2(영문)](https://logging.apache.org/log4j/2.x/)와 같은 로깅 프레임워크에 출력을 기록해야 하는 경우 [SLF4J 로깅 구현(영문)](https://www.slf4j.org/manual.html)을 바인딩합니다.
