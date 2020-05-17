---
title: Java를 사용하여 가상 머신 확장 집합 관리 | Microsoft Docs
description: Java용 Azure SDK를 사용하여 Azure 가상 머신 확장 집합을 관리하는 샘플 코드
author: rloutlaw
ms.assetid: b55923b7-d60a-460d-b77c-af5fac67f1cc
ms.topic: article
ms.date: 3/30/2017
ms.reviewer: asirveda
ms.openlocfilehash: 36fdd387653462e56ce2c93553e51da68e9d6d95
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82105014"
---
# <a name="manage-azure-virtual-machine-scale-sets-from-your-java-applications"></a>Java 애플리케이션에서 Azure 가상 머신 확장 세트 관리

[이 샘플](https://github.com/Azure-Samples/compute-java-manage-virtual-machine-scale-sets)에서는 [Java 관리 라이브러리](https://github.com/Azure/azure-sdk-for-java)를 사용하여 [가상 머신 확장 집합](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview)을 만듭니다. 

## <a name="run-the-sample"></a>샘플 실행

[인증 파일](https://docs.microsoft.com/azure/java/java-sdk-azure-authenticate#mgmt-file)을 만들고 컴퓨터에서 파일의 전체 경로가 포함된 `AZURE_AUTH_LOCATION` 환경 변수를 설정합니다. 다음을 실행합니다.

```
git clone https://github.com/Azure-Samples/compute-java-manage-virtual-machine-scale-sets.git
cd compute-java-manage-virtual-machine-scale-sets
mvn clean compile exec:java
```

[GitHub에서 전체 샘플 코드(영문)](https://github.com/Azure-Samples/compute-java-manage-virtual-machine-scale-sets/blob/master/src/main/java/com/microsoft/azure/management/compute/samples/ManageVirtualMachineScaleSet.java)를 봅니다.

## <a name="authenticate-with-azure"></a>Azure를 사용하여 인증

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="create-a-virtual-network-for-the-scale-set"></a>확장 집합에 대한 가상 네트워크 만들기

```java
Network network = azure.networks().define(vnetName)
                    .withRegion(region)
                    .withNewResourceGroup(rgName)
                    .withAddressSpace("172.16.0.0/16")
                    .defineSubnet("Front-end")
                    .withAddressPrefix("172.16.1.0/24")
                    .attach()
                    .create();
```

확장 집합 정의를 만들기 전에 가상 네트워크 및 부하 분산 장치를 설정합니다. 이러한 리소스는 확장 집합에서 초기 구성에 사용합니다.

## <a name="create-a-load-balancer-to-distribute-load-across-the-scale-set"></a>부하 분산 장치를 만들어 확장 집합 간 부하 분산

```java
LoadBalancer loadBalancer1 = azure.loadBalancers().define(loadBalancerName1)
                    .withRegion(region)
                    .withExistingResourceGroup(rgName)
                    // assign a public IP address to the load balancer
                    .definePublicFrontend(frontendName)
                        .withExistingPublicIPAddress(publicIPAddress)
                        .attach()
                    // Add two backend address pools
                    .defineBackend(backendPoolName1)
                        .attach()
                    .defineBackend(backendPoolName2)
                        .attach()
                    // Add two health probes on 80 and 443
                    .defineHttpProbe(httpProbe)
                        .withRequestPath("/")
                        .withPort(80)
                        .attach()
                    .defineHttpProbe(httpsProbe)
                        .withRequestPath("/")
                        .withPort(443)
                        .attach()

                    // balance HTTP and HTTPs traffic
                    .defineLoadBalancingRule(httpLoadBalancingRule)
                        .withProtocol(TransportProtocol.TCP)
                        .withFrontend(frontendName)
                        .withFrontendPort(80)
                        .withProbe(httpProbe)
                        .withBackend(backendPoolName1)
                        .attach()
                    .defineLoadBalancingRule(httpsLoadBalancingRule)
                        .withProtocol(TransportProtocol.TCP)
                        .withFrontend(frontendName)
                        .withFrontendPort(443)
                        .withProbe(httpsProbe)
                        .withBackend(backendPoolName2)
                        .attach()

                    // Add NAT definitions to enable SSH and telnet to the VMs 
                    .defineInboundNatPool(natPool50XXto22)
                        .withProtocol(TransportProtocol.TCP)
                        .withFrontend(frontendName)
                        .withFrontendPortRange(5000, 5099)
                        .withBackendPort(22)
                        .attach()
                    .defineInboundNatPool(natPool60XXto23)
                        .withProtocol(TransportProtocol.TCP)
                        .withFrontend(frontendName)
                        .withFrontendPortRange(6000, 6099)
                        .withBackendPort(23)
                        .attach()
                    .create();
```

 부하 분산 장치는 두 개의 백 엔드 네트워크 주소 풀을 정의합니다. 하나는 HTTP(`backendPoolName1`)에서 부하를 분산하고, 다른 하나는 HTTPS(`backendPoolName2`)에서 부하를 분산합니다.  `defineHttpProbe()` 메서드는 부하 분산 장치에 대한 [상태 프로브 엔드포인트](/azure/load-balancer/load-balancer-custom-probe-overview)를 설정합니다. NAT 규칙은 텔넷 및 SSH 액세스를 위해 확장 집합 가상 머신에서 22와 23 포트를 노출합니다.

## <a name="create-a-scale-set"></a>확장 집합 만들기
 
```java
 // Create a virtual machine scale set with three virtual machines
 // And, install Apache Web servers on them
VirtualMachineScaleSet virtualMachineScaleSet = azure.virtualMachineScaleSets()
       .define(vmssName)
                .withRegion(region)
                .withExistingResourceGroup(rgName)
                .withSku(VirtualMachineScaleSetSkuTypes.STANDARD_D3_V2)
                .withExistingPrimaryNetworkSubnet(network, "Front-end")
                .withExistingPrimaryInternetFacingLoadBalancer(loadBalancer1)
                .withPrimaryInternetFacingLoadBalancerBackends(backendPoolName1, backendPoolName2)
                .withPrimaryInternetFacingLoadBalancerInboundNatPools(natPool50XXto22, natPool60XXto23)
                .withoutPrimaryInternalLoadBalancer()
                .withPopularLinuxImage(KnownLinuxVirtualMachineImage.UBUNTU_SERVER_16_04_LTS)
                .withRootUsername(userName)
                .withSsh(sshKey)
                .withNewDataDisk(100)
                .withNewDataDisk(100, 1, CachingTypes.READ_WRITE)
                .withNewDataDisk(100, 2, 
                     CachingTypes.READ_WRITE, StorageAccountTypes.STANDARD_LRS)
                .withCapacity(3)
                // Use a VM extension to install Apache Web servers
                .defineNewExtension("CustomScriptForLinux")
                    .withPublisher("Microsoft.OSTCExtensions")
                    .withType("CustomScriptForLinux")
                    .withVersion("1.4")
                    .withMinorVersionAutoUpgrade()
                    .withPublicSetting("fileUris", fileUris)
                    .withPublicSetting("commandToExecute", installCommand)
                    .attach()
                .create();
```

이전 단계에서 만든 가상 네트워크 정의와 부하 분산 장치 정의를 사용하여 세 개의 Linux 인스턴스(`withCapacity(3)`) 및 각각 세 개의 100GB 데이터 디스크가 있는 확장 집합을 만듭니다. `defineNewExtension()` 메서드 체인은 각 VM에 Apache 웹 서버를 설치합니다.

## <a name="list-virtual-machine-scale-set-network-interfaces"></a>가상 머신 확장 집합 네트워크 인터페이스 나열

```java
// List network interfaces on the scale set and iterate through them
PagedList<VirtualMachineScaleSetNetworkInterface> vmssNics = 
     virtualMachineScaleSet.listNetworkInterfaces();
for (VirtualMachineScaleSetNetworkInterface vmssNic : vmssNics) {
    System.out.println(vmssNic.id());
}
```

## <a name="get-ssh-connection-strings-for-each-scale-set-virtual-machine"></a>각 확장 집합 가상 머신에 대한 SSH 연결 문자열 가져오기

```java
for (VirtualMachineScaleSetVM instance : virtualMachineScaleSet.virtualMachines().list()) {
    System.out.println("Scale set virtual machine instance #" + instance.instanceId());
    System.out.println(instance.id());
    PagedList<VirtualMachineScaleSetNetworkInterface> networkInterfaces = 
         instance.listNetworkInterfaces();
    // Pick the first NIC on the instance and use its primary IP address
    VirtualMachineScaleSetNetworkInterface networkInterface = networkInterfaces.get(0);
    for (VirtualMachineScaleSetNicIPConfiguration ipConfig : networkInterface.ipConfigurations().values()) {
        if (ipConfig.isPrimary()) {
            List<LoadBalancerInboundNatRule> natRules = ipConfig.listAssociatedLoadBalancerInboundNatRules();
            for (LoadBalancerInboundNatRule natRule : natRules) {
                // find rule matching the inbound SSH port on the backend for the IP address
                // and return the SSH connection string to that port on the load balancer
                if (natRule.backendPort() == 22) {
                    System.out.println("SSH connection string: " + userName + 
                        "@" + publicIPAddress.fqdn() + ":" + natRule.frontendPort());
                break;
                }
            }
            break;
        }
    }
}
```

이전에 만든 NAT 풀에서 가상 머신의 SSH 및 텔넷 포트(각각 22 및 23)를 부하 분산 장치의 포트에 매핑했습니다. 이 코드는 각 가상 머신에 대한 SSH 연결 문자열을 작성합니다.

## <a name="stop-the-virtual-machine-scale-set"></a>가상 머신 확장 집합 중지

```java
// stop (not deallocate) all scale set instances
virtualMachineScaleSet.powerOff();
```

중지된 가상 머신은 예약된 리소스를 계속 사용합니다. `deallocate()`를 사용하여 가상 머신에서 운영 체제를 중지하고 해당 컴퓨팅 리소스를 해제합니다.

## <a name="deallocate-the-virtual-machine-scale-set"></a>가상 머신 확장 집합 할당 해제

```java
// deallocate the virtual machine scale set
virtualMachineScaleSet.deallocate();
```

Deallocate()는 가상 머신에서 운영 체제를 종료하고 확장 집합 인스턴스에서 사용하는 컴퓨팅 및 네트워크 리소스(예: IP 주소)를 해제합니다. 가상 머신에 연결된 모든 디스크(OS 포함)에 대해 스토리지 요금이 계속 발생합니다.

## <a name="start-a-virtual-machine-scale-set"></a>가상 머신 확장 집합 시작

```java
// start a deallocated or stopped virtual machine scale set
virtualMachineScaleSet.start();
```

## <a name="update-the-number-of-virtual-machines-instances-in-the-scale-set"></a>확장 집합의 가상 머신 인스턴스 수 업데이트
```java
// increase the number of virtual machine scale set instances from three to six
virtualMachineScaleSet.update()
                    .withCapacity(6)
                    .apply();
```

`withCapacity()`를 사용하여 확장 집합의 가상 머신 수를 조정하고, `withSku()`를 사용하여 각 가상 머신의 용량을 조정합니다.

## <a name="sample-explanation"></a>샘플 설명

[샘플 코드](https://github.com/Azure-Samples/compute-java-manage-virtual-machine-scale-sets/blob/master/src/main/java/com/microsoft/azure/management/compute/samples/ManageVirtualMachineScaleSet.java)에서는 먼저 통신하도록 설정된 확장 집합에 대한 가상 네트워크와 부하 분산 장치를 만들어 가상 머신 간에 트래픽을 분산합니다. `azure.virtualMachineScaleSets().define()...create()` 메서드 체인은 Apache 웹 서버를 실행하는 세 개의 Linux 인스턴스가 있는 확장 집합을 만듭니다.    
   
| 샘플에 사용되는 클래스 | 메모
|-------|-------|
| [VirtualMachineScaleSet](/java/api/com.microsoft.azure.management.compute.virtualmachinescaleset) | 확장 집합의 모든 가상 머신을 쿼리, 시작, 중지, 업데이트 및 삭제합니다.
| [VirtualMachineScaleSetVM](/java/api/com.microsoft.azure.management.compute.virtualmachinescalesetvm) | `virtualMachineScaleSet.virtualMachines().get()` 또는 `list()`에서 검색되고, 이를 사용하여 확장 집합의 가상 머신을 쿼리, 시작, 중지, 구성 및 삭제할 수 있습니다.
| [VirtualMachineScaleSetNetworkInterface](/java/api/com.microsoft.azure.management.network.virtualmachinescalesetnetworkinterface) | `virtualMachineScaleSet.listNetworkInterfaces()`에서 반환되고, 확장 집합의 가상 머신에 있는 네트워크 인터페이스에 대한 읽기 전용 표현입니다.
| [VirtualMachineScaleSetSkuTypes](/java/api/com.microsoft.azure.management.compute.virtualmachinescalesetskutypes) | 확장 집합 멤버에서 소비할 수 있는 리소스의 크기를 정의하는 데 사용되는 [가상 머신 확장 집합 계층](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)을 설정하는 데 사용되는 정적 필드 클래스입니다.
| [VirtualMachineScaleSetNicIpConfiguration](/java/api/com.microsoft.azure.management.network.virtualmachinescalesetnicipconfiguration) | 확장 집합 가상 머신에서 네트워크 인터페이스와 연결된 IP 구성을 쿼리하는 데 사용됩니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next-steps](includes/java-next-steps.md)]