---
title: Java를 사용하여 Azure 가상 네트워크 관리 | Microsoft Docs
description: Java 코드에서 Azure 가상 네트워크를 관리하는 샘플 코드
author: rloutlaw
ms.assetid: 92736911-3df6-46e7-b751-25bb36bf89b9
ms.topic: article
ms.date: 3/30/2017
ms.reviewer: asirveda
ms.custom: devx-track-java
ms.openlocfilehash: 31e20e4ef347bb3b771e818da6e458261537a3f3
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379397"
---
# <a name="create-and-manage-azure-virtual-networks-from-your-java-apps"></a>Java 앱에서 Azure 가상 네트워크 만들기 및 관리

[이 샘플](https://github.com/Azure-Samples/network-java-manage-virtual-network)에서는 제어하는 네트워크 세그먼트에서 Azure 리소스를 격리하는 [가상 네트워크](/azure/virtual-network/virtual-networks-overview)를 만듭니다.

## <a name="run-the-sample"></a>샘플 실행

[인증 파일](https://docs.microsoft.com/azure/java/java-sdk-azure-authenticate#mgmt-file)을 만들고 컴퓨터에서 파일의 전체 경로가 포함된 `AZURE_AUTH_LOCATION` 환경 변수를 설정합니다. 다음을 실행합니다.

```
git clone https://github.com/Azure-Samples/network-java-manage-virtual-network.git
cd network-java-manage-virtual-network
mvn clean compile exec:java
```

[GitHub에서 전체 샘플 코드(영문)](https://github.com/Azure-Samples/network-java-manage-virtual-network/blob/master/src/main/java/com/microsoft/azure/management/network/samples/ManageVirtualNetwork.java)를 봅니다.

## <a name="authenticate-with-azure"></a>Azure를 사용하여 인증

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="create-a-network-security-group-to-block-internet-traffic"></a>인터넷 트래픽을 차단하는 네트워크 보안 그룹 만들기

```java
// this NSG definition block traffic to and from the public Internet
NetworkSecurityGroup backEndSubnetNsg = azure.networkSecurityGroups()
              .define(vnet1BackEndSubnetNsgName)
                    .withRegion(Region.US_EAST)
                    .withNewResourceGroup(rgName)
                    .defineRule("DenyInternetInComing")
                        .denyInbound()
                        .fromAddress("INTERNET")
                        .fromAnyPort()
                        .toAnyAddress()
                        .toAnyPort()
                        .withAnyProtocol()
                        .attach()
                    .defineRule("DenyInternetOutGoing")
                        .denyOutbound()
                        .fromAnyAddress()
                        .fromAnyPort()
                        .toAddress("INTERNET")
                        .toAnyPort()
                        .withAnyProtocol()
                        .attach()
                    .create();
```

이 [네트워크 보안 규칙](/azure/virtual-network/virtual-networks-nsg)은 인바운드 및 아웃바운드 공용 인터넷 트래픽을 차단합니다. 이 네트워크 보안 그룹은 가상 네트워크의 서브넷에 적용될 때까지 영향을 주지 않습니다.

## <a name="create-a-virtual-network-with-two-subnets"></a>두 서브넷이 있는 가상 네트워크 만들기

```java
// create the a virtual network with two subnets
// assign the backend subnet a rule blocking public internet traffic
Network virtualNetwork1 = azure.networks().define(vnetName1)
                    .withRegion(Region.US_EAST)
                    .withExistingResourceGroup(rgName)
                    .withAddressSpace("192.168.0.0/16")
                    .withSubnet(vnet1FrontEndSubnetName, "192.168.1.0/24")
                    .defineSubnet(vnet1BackEndSubnetName)
                        .withAddressPrefix("192.168.2.0/24")
                        .withExistingNetworkSecurityGroup(backEndSubnetNsg)
                        .attach()
                    .create();
```

백 엔드 서브넷에서 네트워크 보안 그룹에 설정된 규칙에 따라 인터넷 액세스를 거부합니다. 프런트 엔드 서브넷에서는 인터넷으로의 아웃바운드 트래픽을 허용하는 [기본 규칙](/azure/virtual-network/virtual-networks-nsg)을 사용합니다.

## <a name="create-a-network-security-group-to-allow-inbound-http-traffic"></a>인바운드 HTTP 트래픽을 허용하는 네트워크 보안 그룹 만들기
```java
// create a rule that allows inbound HTTP and blocks outbound Internet traffic
NetworkSecurityGroup frontEndSubnetNsg = azure.networkSecurityGroups()
               .define(vnet1FrontEndSubnetNsgName)
                    .withRegion(Region.US_EAST)
                    .withExistingResourceGroup(rgName)
                    .defineRule("AllowHttpInComing")
                        .allowInbound()
                        .fromAddress("INTERNET")
                        .fromAnyPort()
                        .toAnyAddress()
                        .toPort(80)
                        .withProtocol(SecurityRuleProtocol.TCP)
                        .attach()
                    .defineRule("DenyInternetOutGoing")
                        .denyOutbound()
                        .fromAnyAddress()
                        .fromAnyPort()
                        .toAddress("INTERNET")
                        .toAnyPort()
                        .withAnyProtocol()
                        .attach()
                    .create();
```

이 네트워크 보안 규칙은 공용 인터넷에서 80 포트의 인바운드 트래픽을 열고 네트워크 내부에서 공용 인터넷으로 보내는 모든 아웃바운드 트래픽을 차단합니다. 

## <a name="update-a-virtual-network"></a>가상 네트워크 업데이트
```java
// update the front end subnet to use the rules in the new network security group
virtualNetwork1.update()
          .updateSubnet(vnet1FrontEndSubnetName)
          .withExistingNetworkSecurityGroup(frontEndSubnetNsg)
          .parent()
          .apply();
```

이전 단계에서 만든 네트워크 보안 규칙을 사용하여 인바운드 HTTP 트래픽을 허용하도록 프런트 엔드 서브넷을 업데이트합니다.

## <a name="create-a-virtual-machine-on-a-subnet"></a>서브넷에 가상 머신 만들기
```java
// attach the new VM to the front end subnet on the virtual network
VirtualMachine frontEndVM = azure.virtualMachines().define(frontEndVmName)
                    .withRegion(Region.US_EAST)
                    .withExistingResourceGroup(rgName)
                    .withExistingPrimaryNetwork(virtualNetwork1) 
                    .withSubnet(vnet1FrontEndSubnetName)
                    .withPrimaryPrivateIpAddressDynamic()
                    .withNewPrimaryPublicIpAddress(publicIpAddressLeafDnsForFrontEndVm)
                    .withPopularLinuxImage(KnownLinuxVirtualMachineImage.UBUNTU_SERVER_16_04_LTS)
                    .withRootUsername(userName)
                    .withSsh(sshKey)
                    .withSize(VirtualMachineSizeTypes.STANDARD_D3_V2)
                    .create();
```

`withExistingPrimaryNetwork()` 및 `withSubnet()`은 이전 단계에서 만든 가상 네트워크에서 프런트 엔드 서브넷을 사용하도록 가상 컴퓨터를 구성합니다.

## <a name="list-virtual-networks-in-a-resource-group"></a>리소스 그룹의 가상 네트워크 나열
```java
// iterate over every virtual network in the resource group 
for (Network virtualNetwork : azure.networks().listByResourceGroup(rgName)) {
    // for each subnet on the virtual network, log the network address prefix 
    for (Map.Entry<String, Subnet> entry : virtualNetwork.subnets().entrySet()) {
        String subnetName = entry.getKey();
        Subnet subnet = entry.getValue();
        System.out.println("Address prefix for subnet " + subnetName + 
             " is " + subnet.addressPrefix());
    }
}
```       

외부 컬렉션을 사용하여 `Network` 개체를 나열하고 검사하거나 이 예제와 같이 중첩된 for-each 루프를 사용하여 각 네트워크의 각 자식 리소스를 반복할 수 있습니다.

## <a name="delete-a-virtual-network"></a>가상 네트워크 삭제
```java
// if you already have the virtual network object it is easiest to delete by ID
azure.networks().deleteById(virtualNetwork1.id());

// Delete by resource group and name if you don't have the VirtualMachine object
azure.networks().deleteByResourceGroup(rgName,vnetName1);
```

가상 네트워크를 제거하면 네트워크의 서브넷이 삭제되지만 서브넷에 적용된 네트워크 보안 그룹 규칙은 삭제되지 않습니다. 이러한 정의는 다른 서브넷에 다시 적용될 수 있습니다.

## <a name="sample-explanation"></a>샘플 설명

이 샘플에서는 두 서브넷과 각 서브넷에 하나의 가상 컴퓨터가 있는 가상 네트워크를 만듭니다. 백 서브넷은 공용 인터넷에서 차단됩니다. 프런트 엔드 연결 서브넷에서 인터넷의 인바운드 HTTP 트래픽을 허용합니다. 가상 네트워크의 두 가상 머신은 모두 기본 네트워크 보안 그룹 규칙을 통해 서로 통신합니다.

| 샘플에 사용되는 클래스 | 메모
|-------|-------|
| [Network](/java/api/com.microsoft.azure.management.network.network) | `azure.networks().define()...create()`에서 만든 가상 네트워크의 로컬 개체 표현입니다. 기존 가상 네트워크를 업데이트하려면 `update()...apply()` 흐름 체인을 사용합니다.
| [서브넷](/java/api/com.microsoft.azure.management.network.subnet) | `withSubnet()`을 사용하여 네트워크를 정의하거나 업데이트할 때 가상 네트워크에 서브넷을 만듭니다. `Network.subnets().get()` 또는 `Network.subnets().entrySet()`에서 서브넷의 개체 표현을 가져옵니다. 이러한 개체에는 서브넷 속성을 쿼리하는 메서드가 있습니다.
| [NetworkSecurityGroup](/java/api/com.microsoft.azure.management.network.networksecuritygroup) | `azure.networkSecurityGroups().define()...create()` 흐름 체인을 사용하여 만든 다음 가상 네트워크에서 서브넷을 업데이트하거나 만들 때 서브넷에 적용합니다. 

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next-steps](includes/java-next-steps.md)]