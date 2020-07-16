---
title: Java를 사용하여 Azure 가상 머신 관리 | Microsoft Docs
description: Java용 Azure SDK를 사용하여 Azure 가상 머신을 관리하는 샘플 코드
author: rloutlaw
ms.assetid: 88629aee-6279-433e-a08b-4f8e290446d0
ms.topic: article
ms.date: 3/30/2017
ms.reviewer: asirveda
ms.custom: devx-track-java
ms.openlocfilehash: 4ef0dee190947fc77d65b46a56289185ac60d128
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379357"
---
# <a name="manage-azure-virtual-machines-from-your-java-applications"></a>Java 애플리케이션에서 Azure 가상 머신 관리

[이 샘플](https://github.com/Azure-Samples/compute-java-manage-vm/)에서는 [Java용 Azure 관리 라이브러리](https://github.com/Azure/azure-sdk-for-java)를 사용하여 Azure 가상 머신을 만들고 작동합니다.

## <a name="run-the-sample"></a>샘플 실행

[인증 파일](https://docs.microsoft.com/azure/java/java-sdk-azure-authenticate#mgmt-file)을 만들고 컴퓨터에서 파일의 전체 경로가 포함된 `AZURE_AUTH_LOCATION` 환경 변수를 설정합니다. 다음을 실행합니다.

```
git clone https://github.com/Azure-Samples/compute-java-manage-vm.git
cd compute-java-manage-vm
mvn clean compile exec:java
```

[GitHub에서 전체 샘플 코드(영문)](https://github.com/Azure-Samples/compute-java-manage-vm/blob/master/src/main/java/com/microsoft/azure/management/compute/samples/ManageVirtualMachine.java)를 봅니다.

## <a name="authenticate-with-azure"></a>Azure를 사용하여 인증

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="create-a-windows-virtual-machine"></a>Windows 가상 머신 만들기

```java
// Prepare a data disk for VM
Disk dataDisk = azure.disks().define(SdkContext.randomResourceName("dsk", 30))
            .withRegion(region)
            .withNewResourceGroup(rgName)
            .withData()
            .withSizeInGB(50)
            .create();

// create the windows virtual machine with the data disk            
VirtualMachine windowsVM = azure.virtualMachines().define(windowsVmName)
            .withRegion(region)
            .withNewResourceGroup(rgName)
            .withNewPrimaryNetwork("10.0.0.0/28")
            .withPrimaryPrivateIpAddressDynamic()
            .withoutPrimaryPublicIpAddress()
            .withPopularWindowsImage(KnownWindowsVirtualMachineImage.WINDOWS_SERVER_2012_R2_DATACENTER)
            .withAdminUsername(userName)
            .withAdminPassword(password)
            .withNewDataDisk(10)
            .withNewDataDisk(dataDiskCreatable)
            .withExistingDataDisk(dataDisk)
            .withSize(VirtualMachineSizeTypes.STANDARD_D3_V2)
            .create();
```

이 코드에서는 다음을 수행합니다.   

0. 가상 컴퓨터에서 사용할 `Disk` Creatable을 50GB 크기와 임의의 이름으로 정의합니다.
0. `azure.virtualMachines().define()..create()` 체인을 사용하여 Windows Server 2012 가상 머신을 만듭니다. API에서 가상 머신과 동일한 시간에 이전 단계에서 정의된 `Disk`를 만듭니다. 10GB 데이터 디스크도 `withNewDataDisk(10)`를 통해 가상 머신에 연결됩니다.

[Creatable<T> 개체](java-sdk-azure-concepts.md#Creatables)를 사용하여 리소스의 로컬 표현을 정의하고 다른 Azure 리소스에 필요한 만큼 리소스를 만드는 방법에 대해 자세히 알아보세요.

## <a name="stop-start-and-restart-a-virtual-machine"></a>가상 머신 중지, 시작 및 다시 시작

```java
// look up a virtual machine by its ID and then restart, stop, and start it
azureVM = azure.getVirtualMachine.getById(windowsVM.id());

azureVM.restart();
azureVM.powerOff();
azureVM.start();
```

`powerOff()`는 가상 컴퓨터 운영 체제를 중지하지만 해당 리소스의 할당을 취소하지 않습니다.

## <a name="add-a-virtual-machine-to-an-existing-network"></a>기존 네트워크에 가상 컴퓨터 추가

```java
// Get the virtual network the current virtual machine is using
Network network = windowsVM.getPrimaryNetworkInterface().primaryIPConfiguration().getNetwork();

// Create a Linux VM in the same subnet
VirtualMachine linuxVM = azure.virtualMachines().define(linuxVmName)
           .withRegion(region)
           .withExistingResourceGroup(rgName)
           .withExistingPrimaryNetwork(network)
           .withSubnet("subnet1") // default subnet name when no name specified at creation
           .withPrimaryPrivateIPAddressDynamic()
           .withoutPrimaryPublicIPAddress()
           .withPopularLinuxImage(KnownLinuxVirtualMachineImage.UBUNTU_SERVER_16_04_LTS)
           .withRootUsername(userName)
           .withRootPassword(password)
           .withSize(VirtualMachineSizeTypes.STANDARD_D3_V2)
           .create();
```

`withPopularLinuxImage`를 사용하여 Windows VM 대신 Linux VM을 정의합니다.


## <a name="list-virtual-machines"></a>가상 머신 나열

```java
// get a list of VMs in the same resource group as an existing VM
String resourceGroupName = windowsVM.resourceGroupName();
PagedList<VirtualMachine> resourceGroupVMs = azure.virtualMachines()
        .listByResourceGroup(resourceGroupName); 

// for each vitual machine in the resource group, log their name and plan
for (VirtualMachine virtualMachine : azure.virtualMachines().listByResourceGroup(resourceGroupName)) {
    System.out.println("VM " + virtualMachine.computerName() + 
        " has plan " + virtualMachine.plan());
}
```

`azure.virtualMachines().list()`를 사용하여 구독에 대한 가상 머신을 모두 나열하고, `tags()`에서 반환한 Map을 반복하여 리소스 그룹에서 태그가 지정된 가상 머신 컬렉션을 관리합니다.

## <a name="update-a-virtual-machine"></a>가상 머신 업데이트

```java
// add a 10GB data disk to the virtual machine
windowsVM.update()
     .withNewDataDisk(10)
     .apply();
```

`update()...apply()` 및 `define()...create()`를 통해 가상 컴퓨터를 만들 때 해당 가상 컴퓨터를 구성하는 데 사용한 것과 동일한 메서드를 사용하여 가상 컴퓨터 구성을 업데이트합니다.

## <a name="delete-a-virtual-machine"></a>가상 머신 삭제

```java
// delete by ID if you already are working with the VM object
azure.virtualMachines().deleteById(windowsVM.id());

// delete by resource group and name
azure.virtualMachines().deleteByResourceGroup(rgName,windowsVmName);
```

## <a name="sample-explanation"></a>샘플 설명

[샘플 코드](https://github.com/Azure-Samples/compute-java-manage-vm/blob/master/src/main/java/com/microsoft/azure/management/compute/samples/ManageVirtualMachine.java)에서는 50GB 데이터 디스크가 있는 Windows 가상 머신을 만듭니다. 그런 다음 샘플에서 두 번째 10GB 데이터 디스크를 만들어 이 Windows 가상 머신에 연결합니다.
그런 다음 샘플에서 Windows 가상 머신과 동일한 가상 네트워크에 Linux 가상 머신을 만듭니다.

샘플에서는 두 가상 머신에 대한 정보를 모두 기록하고 완료하기 전에 모두 삭제합니다.

| 샘플에 사용되는 클래스 | 메모
|-------|-------|
| [VirtualMachine](/java/api/com.microsoft.azure.management.compute.virtualmachine) | 속성을 쿼리하고 가상 머신의 상태를 관리합니다. `azure.virtualMachines().list()` 또는 이름별 또는 ID `azure.virtualMachines().getByResourceGroup()` 목록 형식으로 검색됩니다.
| [VirtualMachineSizeTypes](/java/api/com.microsoft.azure.management.compute.virtualmachinesizetypes) | [가상 머신 크기 옵션](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)에 매핑되는 정적 값이 있는 클래스입니다. `withSize()` 메서드에서 VM에 할당된 리소스를 정의하는 데 사용됩니다.
| [디스크](/java/api/com.microsoft.azure.management.compute.disk) | `withData()`를 사용하여 데이터를 저장하거나 디스크를 정의할 때 적절한 `withLinux` 또는 `withWindows` 메서드 사용하여 운영 체제 이미지를 저장할 디스크를 만듭니다. 만들 때(`using withNewDataDisk` 또는 `withExistingDataDisk`) 또는 VirtualMachine 개체의 `update()..apply()`로 만든 후에 가상 머신에 디스크를 연결합니다.
| [DiskSkuTypes](/java/api/com.microsoft.azure.management.compute.diskskutypes) | 표준 또는 [프리미엄](/azure/storage/storage-premium-storage) 스토리지 계획이 있는 디스크를 정의하는 정적 값이 있는 클래스입니다.
| [KnownLinuxVirtualMachineImage](/java/api/com.microsoft.azure.management.compute.knownlinuxvirtualmachineimage) | 가상 머신을 정의할 때 `withPopularLinuxImage()` 메서드와 함께 사용할 Linux 가상 머신 옵션 집합이 있는 클래스입니다.
| [KnownWindowsVirtualMachineImage](/java/api/com.microsoft.azure.management.compute.knownwindowsvirtualmachineimage) | 가상 컴퓨터를 정의할 때 `withPopularWindowsImage()` 메서드와 함께 사용할 Windows 가상 컴퓨터 이미지 옵션 집합이 있는 클래스입니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next-steps](includes/java-next-steps.md)]