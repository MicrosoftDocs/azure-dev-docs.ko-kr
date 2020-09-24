---
title: Managed Disks
description: 관리 디스크를 생성, 크기 조정 및 업데이트합니다.
ms.topic: conceptual
ms.date: 6/15/2017
ms.custom: devx-track-python
ms.openlocfilehash: b1973276f6cf080006f61ddc3a2805c345591e89
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831529"
---
# <a name="managed-disks"></a>Managed Disks

Azure Managed Disks는 간소화된 디스크 관리, 향상된 확장성, 더 나은 보안 및 크기 조정을 제공합니다. 디스크에 대한 스토리지 계정의 개념을 없앰으로써 고객이 스토리지 계정과 관련된 제한을 걱정하지 않고 크기 조정할 수 있습니다. 이 게시물에서는 Python에서 서비스를 사용하는 방법에 대한 간단한 소개와 참조를 제공합니다.

개발자 관점에서 Azure CLI의 Managed Disks 환경은 다른 플랫폼 간 도구의 CLI 환경에 자연스러운 환경입니다. [Azure Python](https://azure.microsoft.com/develop/python/) SDK와 [azure-mgmt-compute 패키지 0.33.0](https://pypi.python.org/pypi/azure-mgmt-compute)을 사용하여 Managed Disks를 관리할 수 있습니다. 이 [자습서](/python/api/overview/azure/virtualmachines?view=azure-python)를 사용하여 컴퓨팅 클라이언트를 만들 수 있습니다.

## <a name="standalone-managed-disks"></a>독립 실행형 Managed Disks

독립 실행형 Managed Disks는 다양한 방법으로 쉽게 만들 수 있습니다.

### <a name="create-an-empty-managed-disk"></a>빈 Managed Disk 만들기

```python
from azure.mgmt.compute.models import DiskCreateOption

async_creation = compute_client.disks.create_or_update(
    'my_resource_group',
    'my_disk_name',
    {
        'location': 'eastus',
        'disk_size_gb': 20,
        'creation_data': {
            'create_option': DiskCreateOption.empty
        }
    }
)
disk_resource = async_creation.result()
```

### <a name="create-a-managed-disk-from-blob-storage"></a>Blob 스토리지에서 Managed Disk 만들기

```python
from azure.mgmt.compute.models import DiskCreateOption

async_creation = compute_client.disks.create_or_update(
    'my_resource_group',
    'my_disk_name',
    {
        'location': 'eastus',
        'creation_data': {
            'create_option': DiskCreateOption.import_enum,
            'source_uri': 'https://bg09.blob.core.windows.net/vm-images/non-existent.vhd'
        }
    }
)
disk_resource = async_creation.result()
```

### <a name="create-a-managed-disk-image-from-blob-storage"></a>Blob Storage에서 관리 디스크 이미지 만들기

```python
from azure.mgmt.compute.models import DiskCreateOption

async_creation = compute_client.images.create_or_update(
    'my_resource_group',
    'my_image_name',
    {
        'location': 'eastus',
        'storage_profile': {
           'os_disk': {
              'os_type': 'Linux',
              'os_state': "Generalized",
              'blob_uri': 'https://bg09.blob.core.windows.net/vm-images/non-existent.vhd',
              'caching': "ReadWrite",
           }
        }
    }
)
image_resource = async_creation.result()
```

### <a name="create-a-managed-disk-from-your-own-image"></a>사용자 고유의 이미지에서 Managed Disk 만들기

```python
from azure.mgmt.compute.models import DiskCreateOption

# If you don't know the id, do a 'get' like this to obtain it
managed_disk = compute_client.disks.get(self.group_name, 'myImageDisk')
async_creation = compute_client.disks.create_or_update(
    'my_resource_group',
    'my_disk_name',
    {
        'location': 'eastus',
        'creation_data': {
            'create_option': DiskCreateOption.copy,
            'source_resource_id': managed_disk.id
        }
    }
)

disk_resource = async_creation.result()
```

## <a name="virtual-machine-with-managed-disks"></a>Managed Disks가 있는 가상 머신

특정 디스크 이미지에 대한 암시적 관리 디스크가 있는 Virtual Machine을 만들 수 있습니다. 간단히 만들려면 모든 디스크 세부 정보를 지정하지 않고 관리 디스크를 암시적으로 만들면 됩니다. 이 경우 스토리지 계정을 만들고 관리하는 것에 대해 걱정할 필요가 없습니다.

관리 디스크는 Azure의 OS 이미지에서 VM을 만들 때 암시적으로 만들어집니다. ``storage_profile`` 매개 변수에서 ``os_disk``는 이제 선택적 요소이며 Virtual Machine을 만드는 데 필요한 전제 조건으로 스토리지 계정을 만들 필요가 없습니다.

```python
storage_profile = azure.mgmt.compute.models.StorageProfile(
    image_reference = azure.mgmt.compute.models.ImageReference(
        publisher='Canonical',
        offer='UbuntuServer',
        sku='16.04-LTS',
        version='latest'
    )
)
```

이 ``storage_profile`` 매개 변수는 이제 유효합니다. Python(네트워크 등 포함)에서 VM을 만드는 방법에 대한 완전한 예제를 얻으려면 [Python의 VM 자습서](https://github.com/Azure-Samples/virtual-machines-python-manage) 전체를 확인하세요.

사용자 고유의 이미지를 사용하여 ``storage_profile``을 만들 수도 있습니다.

```python
# If you don't know the id, do a 'get' like this to obtain it
image = compute_client.images.get(self.group_name, 'myImageDisk')
storage_profile = azure.mgmt.compute.models.StorageProfile(
    image_reference = azure.mgmt.compute.models.ImageReference(
        id = image.id
    )
)
```

이전에 프로비전된 관리 디스크를 쉽게 연결할 수 있습니다.

```python
vm = compute.virtual_machines.get(
    'my_resource_group',
    'my_vm'
)
managed_disk = compute_client.disks.get('my_resource_group', 'myDisk')
vm.storage_profile.data_disks.append({
    'lun': 12, # You choose the value, depending of what is available for you
    'name': managed_disk.name,
    'create_option': DiskCreateOptionTypes.attach,
    'managed_disk': {
        'id': managed_disk.id
    }
})
async_update = compute_client.virtual_machines.create_or_update(
    'my_resource_group',
    vm.name,
    vm,
)
async_update.wait()
```

## <a name="virtual-machine-scale-sets-with-managed-disks"></a>Managed Disks를 사용하는 가상 머신 확장 집합

Managed Disks 이전에는 필요한 모든 VM에 대한 스토리지 계정을 확장 집합 내에서 수동으로 만든 다음 ``vhd_containers`` list 매개 변수를 사용하여 모든 스토리지 계정 이름을 확장 집합 Rest API에 제공해야 했습니다. 공식적인 전환 가이드는 이 문서의 내용 `<https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md>`에서 사용할 수 있습니다.

이제 관리 디스크를 사용하면 스토리지 계정을 전혀 관리할 필요가 없습니다. 가상 머신 확장 집합 Python SDK에 익숙하다면 ``storage_profile``은 이제 VM을 만드는 데 사용된 것과 똑같을 수 있습니다.

```python
'storage_profile': {
    'image_reference': {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "16.04-LTS",
        "version": "latest"
    }
},
```

전체 샘플은 다음과 같습니다.

```python
naming_infix = "PyTestInfix"
vmss_parameters = {
    'location': self.region,
    "overprovision": True,
    "upgrade_policy": {
        "mode": "Manual"
    },
    'sku': {
        'name': 'Standard_A1',
        'tier': 'Standard',
        'capacity': 5
    },
    'virtual_machine_profile': {
        'storage_profile': {
            'image_reference': {
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "16.04-LTS",
                "version": "latest"
            }
        },
        'os_profile': {
            'computer_name_prefix': naming_infix,
            'admin_username': 'Foo12',
            'admin_password': 'BaR@123!!!!',
        },
        'network_profile': {
            'network_interface_configurations' : [{
                'name': naming_infix + 'nic',
                "primary": True,
                'ip_configurations': [{
                    'name': naming_infix + 'ipconfig',
                    'subnet': {
                        'id': subnet.id
                    }
                }]
            }]
        }
    }
}

# Create VMSS test
result_create = compute_client.virtual_machine_scale_sets.create_or_update(
    'my_resource_group',
    'my_scale_set',
    vmss_parameters,
)
vmss_result = result_create.result()
```

## <a name="other-operations-with-managed-disks"></a>기타 Managed Disks 작업

### <a name="resizing-a-managed-disk"></a>Managed Disk 크기 조정

```python
managed_disk = compute_client.disks.get('my_resource_group', 'myDisk')
managed_disk.disk_size_gb = 25
async_update = self.compute_client.disks.create_or_update(
    'my_resource_group',
    'myDisk',
    managed_disk
)
async_update.wait()
```

### <a name="update-the-storage-account-type-of-the-managed-disks"></a>Managed Disks의 스토리지 계정 유형 업데이트

```python
from azure.mgmt.compute.models import StorageAccountTypes

managed_disk = compute_client.disks.get('my_resource_group', 'myDisk')
managed_disk.account_type = StorageAccountTypes.standard_lrs
async_update = self.compute_client.disks.create_or_update(
    'my_resource_group',
    'myDisk',
    managed_disk
)
async_update.wait()
```

### <a name="create-an-image-from-blob-storage"></a>BLOB Storage에서 이미지 만들기

```python
async_create_image = compute_client.images.create_or_update(
    'my_resource_group',
    'myImage',
    {
        'location': 'westus',
        'storage_profile': {
            'os_disk': {
                'os_type': 'Linux',
                'os_state': "Generalized",
                'blob_uri': 'https://bg09.blob.core.windows.net/vm-images/non-existent.vhd',
                'caching': "ReadWrite",
            }
        }
    }
)
image = async_create_image.result()
```

### <a name="create-a-snapshot-of-a-managed-disk-that-is-currently-attached-to-a-virtual-machine"></a>현재 가상 머신에 연결된 Managed Disk의 스냅샷 만들기

```python
managed_disk = compute_client.disks.get('my_resource_group', 'myDisk')
async_snapshot_creation = self.compute_client.snapshots.create_or_update(
        'my_resource_group',
        'mySnapshot',
        {
            'location': 'westus',
            'creation_data': {
                'create_option': 'Copy',
                'source_uri': managed_disk.id
            }
        }
    )
snapshot = async_snapshot_creation.result()
```