---
title: 지역 간에 동시에 VM 만들기 | Microsoft Docs
description: Java용 Azure SDK를 사용하여 여러 Azure 지역 간에 동시에 가상 머신을 만드는 샘플 코드
author: rloutlaw
ms.assetid: e5a36699-2d96-4571-84f9-a6af13f3c067
ms.topic: article
ms.date: 03/30/2017
ms.reviewer: asirveda
ms.openlocfilehash: 7e50a18346eb9bd2bee50bf2ea2d65cd94381292
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82104974"
---
# <a name="create-virtual-machines-across-multiple-regions-from-your-java-applications"></a>Java 애플리케이션에서 여러 지역에 가상 머신 만들기

[이 샘플](https://github.com/Azure-Samples/compute-java-create-virtual-machines-across-regions-in-parallel)에서는 [Java용 Azure 관리 라이브러리](https://github.com/Azure/azure-sdk-for-java)를 사용하여 서로 다른 Azure 지역에서 동시에 가상 머신을 만듭니다.

> [!IMPORTANT]
> 샘플에서는 네 개 지역에서 [STANDARD_DS3_V2 크기](/azure/virtual-machines/virtual-machines-windows-sizes)의 Ubuntu 16.04 LTS를 실행하는 총 48개의 VM을 만듭니다. 샘플 코드는 종료하기 전에 이러한 가상 머신을 삭제합니다. 이 샘플을 VM의 기본 개수로 실행하기 전에 반드시 [서비스 제한 및 할당량을 확인](/azure/azure-subscription-service-limits)하세요.

## <a name="run-the-sample"></a>샘플 실행

[인증 파일](https://docs.microsoft.com/azure/java/java-sdk-azure-authenticate#mgmt-file)을 만들고 컴퓨터에서 파일의 전체 경로가 포함된 `AZURE_AUTH_LOCATION` 환경 변수를 설정합니다. 다음을 실행합니다.

```
git clone https://github.com/Azure-Samples/compute-java-create-virtual-machines-across-regions-in-parallel.git
cd compute-java-create-virtual-machines-across-regions-in-parallel
mvn clean compile exec:java
```

[GitHub에서 전체 샘플 코드(영문)](https://github.com/Azure-Samples/compute-java-create-virtual-machines-across-regions-in-parallel/blob/master/src/main/java/com/microsoft/azure/management/compute/samples/CreateVirtualMachinesInParallel.java)를 봅니다.

## <a name="authenticate-with-azure"></a>Azure를 사용하여 인증

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="set-locations-and-counts-for-the-virtual-machines"></a>가상 머신에 대한 위치 및 개수 설정

```java
// use a Map to define how where and how many VMs to create 
Map<Region, Integer> virtualMachinesByLocation = new HashMap<Region, Integer>();

// create 12 virtual machines in four regions
virtualMachinesByLocation.put(Region.US_EAST, 12);
virtualMachinesByLocation.put(Region.US_SOUTH_CENTRAL, 12);
virtualMachinesByLocation.put(Region.US_WEST, 12);
virtualMachinesByLocation.put(Region.US_NORTH_CENTRAL, 12);
```

이 `Map`은 샘플의 뒷부분에서 전 세계 VM 배포를 설정하는 데 사용됩니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기 

```java
// logically associate the resources in the sample into a randomly named resource group
final String rgName = SdkContext.randomResourceName("rgCOPD", 24);
ResourceGroup resourceGroup = azure.resourceGroups().define(rgName)
                .withRegion(Region.US_EAST)
                .create();
```

샘플의 각 리소스는 이 리소스 그룹에서 관리됩니다. 이렇게 하면 나중에 리소스 그룹을 삭제하여 리소스를 쉽게 정리할 수 있습니다.

## <a name="define-the-virtual-machines"></a>가상 머신 정의
```java
// list to store the VirtualMachine definitions
List<Creatable<VirtualMachine>> creatableVirtualMachines = new ArrayList<>();
    
// outer loop: iterate through each region included in the map
for (Map.Entry<Region, Integer> entry : virtualMachinesByLocation.entrySet()) {
    Region region = entry.getKey();
    Integer vmCount = entry.getValue();

    // Define one virtual network Creatable per region for the VMs to share
    String networkName = SdkContext.randomResourceName("vnetCOPD-", 20);
    Creatable<Network> networkCreatable = azure.networks().define(networkName)
           .withRegion(region)
           .withExistingResourceGroup(resourceGroup)
           .withAddressSpace("172.16.0.0/16");

    // Define one storage account Creatable per region for storing VM disks
    String storageAccountName = SdkContext.randomResourceName("stgcopd", 20);
    Creatable<StorageAccount> storageAccountCreatable = azure.storageAccounts()
        .define(storageAccountName)
              .withRegion(region)
              .withExistingResourceGroup(resourceGroup);

    // generate a common prefix for every VM name
    String linuxVMNamePrefix = SdkContext.randomResourceName("vm-", 15);

    // inner loop: iterate once for every VM instance in the region
    for (int i = 1; i <= vmCount; i++) {

        // Create one public IP address Creatable for each VM
        Creatable<PublicIpAddress> publicIpAddressCreatable = azure.publicIpAddresses()
             .define(String.format("%s-%d", linuxVMNamePrefix, i))
             .withRegion(region)
             .withExistingResourceGroup(resourceGroup)
             .withLeafDomainLabel(SdkContext.randomResourceName("pip", 10));

        publicIpCreatableKeys.add(publicIpAddressCreatable.key());

        // Create one virtual machine Creatable 
        Creatable<VirtualMachine> virtualMachineCreatable = azure.virtualMachines()
             .define(String.format("%s-%d", linuxVMNamePrefix, i))
             .withRegion(region)
             .withExistingResourceGroup(resourceGroup)
             .withNewPrimaryNetwork(networkCreatable)
             .withPrimaryPrivateIpAddressDynamic()
             .withNewPrimaryPublicIpAddress(publicIpAddressCreatable)
             .withPopularLinuxImage(KnownLinuxVirtualMachineImage.UBUNTU_SERVER_16_04_LTS)
             .withRootUsername(userName)
             .withSsh(sshKey)
             .withSize(VirtualMachineSizeTypes.STANDARD_DS3_V2)
             .withNewStorageAccount(storageAccountCreatable);
        // add the virtual machine Creatable to the list     
        creatableVirtualMachines.add(virtualMachineCreatable); 
     }
}
```

위의 `for` 외부 루프는 각 지역을 반복하여 해당 지역의 모든 가상 머신에서 사용할 가상 네트워크 Creatable 개체와 스토리지 계정 Creatable 개체를 정의합니다. 관리 라이브러리를 사용할 때 필요한 경우에만 [Creatable 개체](java-sdk-azure-concepts.md#Creatables)를 사용하여 리소스를 만드는 방법에 대해 자세히 알아보세요.

`for` 내부 루프는 가상 컴퓨터에 대한 공용 IP 주소 Creatable 개체를 가져온 다음 이전에 정의한 가상 네트워크, 스토리지 계정 및 공용 IP 주소에 대한 Creatable 개체를 사용하여 가상 컴퓨터 Creatable 개체를 정의합니다.  그런 다음 `creatableVirtualMachines` 목록에 이 VirtualMachine Creatable 개체를 추가합니다.

## <a name="create-the-virtual-machines"></a>가상 머신 만들기

```java
// create all virtual machines defined in the list, return all Creatable objects used
// including networks, public IP addresses, and storage accounts
CreatedResources<VirtualMachine> virtualMachines = azure.virtualMachines().create(creatableVirtualMachines);

// list the IDs of each virtual machine created 
for (VirtualMachine virtualMachine : virtualMachines.values()) {
    System.out.println(virtualMachine.id());
}

// call createdRelatedResource(key) to get the resources used to define the virtual machines. 
// Save the key at the time you define the Creatable to use CreatedResources like this
for (String publicIpCreatableKey : publicIpCreatableKeys) {
    PublicIPAddress pip = 
         (PublicIPAddress) virtualMachines.createdRelatedResource(publicIpCreatableKey);
}
```

`azure.virtualMachines().create(creatableVirtualMachines)` 호출은 `creatableVirtualMachines` 목록에 정의된 모든 가상 머신을 지역 전체에서 동시에 만듭니다.

반환된 `CreatedResources<VirtualMachine>` 개체를 사용하여 반환된 `VirtualMachine` 형식 외에도 `create()` 메서드 중에 Azure 구독에 만들어진 모든 리소스에 액세스합니다. `createdRelatedResources()`에서 반환된 값을 올바른 형식으로 캐스트합니다. 

[라이브러리 개념 문서](java-sdk-azure-concepts.md)에서 `Creatable<T>` 및 `CreatedResources` 사용에 대해 자세히 알아보세요.

## <a name="delete-the-resource-group"></a>리소스 그룹 삭제 

```java
// finally block deletes the resource group before the code exits
// deleting a resource group deletes all resources created in it
finally {
    try {
        System.out.println("Deleting Resource Group: " + rgName);
        azure.resourceGroups().deleteByName(rgName);
        System.out.println("Deleted Resource Group: " + rgName);
    } catch (NullPointerException npe) {
        System.out.println("Did not create any resources in Azure. No clean up is necessary");
    } catch (Exception g) {
        g.printStackTrace();
    }
}
```

이 블록에서는 먼저 샘플에서 만든 리소스를 삭제한 후에 샘플이 종료됩니다.

## <a name="sample-explanation"></a>샘플 설명

[GitHub에서 전체 샘플 코드(영문)](https://github.com/Azure-Samples/compute-java-create-virtual-machines-across-regions-in-parallel)를 봅니다.

이 샘플에서는 `Creatable` 개체를 사용하여 가상 머신을 호스팅하는 각 지역에 대한 가상 네트워크 및 스토리지 계정을 정의합니다. 그런 다음 각 가상 머신의 공용 IP 주소에 대해 `Creatable` 개체가 정의됩니다. 샘플은 이러한 `Creatable` 개체를 사용하여 가상 머신을 정의하고 `virtualMachineCreatable` 목록에 이 VM 정의를 추가합니다.

코드에서 목록에 모든 가상 컴퓨터 정의를 추가하면 `azure.virtualMachines().create(creatableVirtualMachines)`는 Azure에서 각 가상 컴퓨터를 동시에 만듭니다.

그런 다음 샘플 코드는 반환된 CreatedResources 개체에서 만들어진 모든 가상 머신에 대한 IP 주소를 가져와서 가상 머신 간에 부하를 분산할 수 있는 [Traffic Manager](/azure/traffic-manager/traffic-manager-overview)를 만듭니다. 

`finally` 블록에서는 오류가 발생하더라도 Azure 구독에서 리소스를 삭제합니다.

| 샘플에 사용되는 클래스 | 메모
|-------|-------|
| [VirtualMachine](/java/api/com.microsoft.azure.management.compute.virtualmachine) | 속성을 쿼리하고 가상 머신의 상태를 관리합니다. `azure.virtualMachines().list()` 또는 이름별 또는 ID `azure.virtualMachines().getByResourceGroup()` 목록 형식으로 검색됩니다.
| [VirtualMachineSizeTypes](/java/api/com.microsoft.azure.management.compute.virtualmachinesizetypes) | 가상 머신을 정의할 때 `withSize()`에 대한 매개 변수로 사용할 [가상 머신 크기 옵션](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)에 매핑되는 정적 값입니다.
| [PublicIpAddress](/java/api/com.microsoft.azure.management.network.publicipaddress) | `azure.publicIpAddresses().define()`을 통해 각 가상 컴퓨터에 대해 정의되지만 즉시 만들어지지는 않습니다. 각 `Creatable`에 대한 키를 저장하고 나중에 `createdRelatedResource()`를 통해 검색합니다.
| [KnownLinuxVirtualMachineImage](/java/api/com.microsoft.azure.management.compute.knownlinuxvirtualmachineimage) | 가상 컴퓨터를 정의할 때 `withPopularLinuxImage()` 메서드에 대한 매개 변수로 사용되는 Linux 가상 컴퓨터 옵션 집합입니다.
| [Network](/java/api/com.microsoft.azure.management.network.network) | 샘플에서는 `azure.networks().define()`을 통해 각 지역에 대해 하나의 가상 네트워크를 정의합니다. 

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next-steps](includes/java-next-steps.md)]