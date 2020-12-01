---
title: Python용 Azure 라이브러리를 통해 Azure Managed Disks 사용
description: Azure SDK를 사용하여 관리 디스크를 만들고, 크기를 조정하고, 업데이트합니다.
ms.topic: conceptual
ms.date: 11/18/2020
ms.custom: devx-track-python
ms.openlocfilehash: fe2378bcb836dbfc52ad1d5d3e88f048d6ef117e
ms.sourcegitcommit: b70a38d46616f5e519d5b9c1a1eaf3fe0ecb9605
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94932417"
---
# <a name="use-azure-managed-disks-with-the-azure-libraries-sdk-for-python"></a>Python용 Azure 라이브러리(SDK)를 통해 Azure Managed Disks 사용

Azure Managed Disks는 스토리지 계정을 통해 직접 작업할 필요 없이 간편한 디스크 관리, 향상된 확장성, 강화된 보안 및 향상된 스케일링 기능을 제공합니다.

[`azure-mgmt-compute`](/python/api/overview/azure/virtualmachines) 라이브러리를 사용하여 Managed Disks를 관리합니다. (`azure-mgmt-compute` 라이브러리로 가상 머신을 프로비저닝하는 예제는 [예 - 가상 머신 프로비저닝](azure-sdk-example-virtual-machines.md)을 참조하세요.)

## <a name="standalone-managed-disks"></a>독립 실행형 Managed Disks

다음 섹션에 설명된 대로 여러 가지 방법으로 독립 실행형 Managed Disks를 만들 수 있습니다.

### <a name="create-an-empty-managed-disk"></a>빈 Managed Disk 만들기

```python
from azure.mgmt.compute.models import DiskCreateOption

poller = compute_client.disks.begin_create_or_update(
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
disk_resource = poller.result()
```

### <a name="create-a-managed-disk-from-blob-storage"></a>Blob 스토리지에서 Managed Disk 만들기

```python
from azure.mgmt.compute.models import DiskCreateOption

poller = compute_client.disks.begin_create_or_update(
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
disk_resource = poller.result()
```

### <a name="create-a-managed-disk-image-from-blob-storage"></a>Blob Storage에서 관리 디스크 이미지 만들기

```python
from azure.mgmt.compute.models import DiskCreateOption

poller = compute_client.images.begin_create_or_update(
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
image_resource = poller.result()
```

### <a name="create-a-managed-disk-from-your-own-image"></a>사용자 고유의 이미지에서 Managed Disk 만들기

```python
from azure.mgmt.compute.models import DiskCreateOption

# If you don't know the id, do a 'get' like this to obtain it
managed_disk = compute_client.disks.get(self.group_name, 'myImageDisk')

poller = compute_client.disks.begin_create_or_update(
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

disk_resource = poller.result()
```

## <a name="virtual-machine-with-managed-disks"></a>Managed Disks가 있는 가상 머신

특정 디스크 이미지에 대한 암시적 Managed Disk가 있는 Virtual Machine을 만들 수 있습니다. 따라서 모든 세부 정보를 일일이 지정할 필요가 없습니다.

관리 디스크는 Azure의 OS 이미지에서 VM을 만들 때 암시적으로 만들어집니다. `storage_profile` 매개 변수에서 `os_disk`는 선택적 요소이며 Virtual Machine을 만들기 위한 전제 조건으로 스토리지 계정을 만들 필요가 없습니다.

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

Python용 Azure 관리 라이브러리를 사용하여 가상 머신을 만드는 방법에 대한 전체 예제는 [예 - 가상 머신 프로비저닝](azure-sdk-example-virtual-machines.md)을 참조하세요.

사용자 고유의 이미지를 사용하여 `storage_profile`을 만들 수도 있습니다.

```python
# If you don't know the id, do a 'get' like this to obtain it
image = compute_client.images.get(self.group_name, 'myImageDisk')

storage_profile = azure.mgmt.compute.models.StorageProfile(
    image_reference = azure.mgmt.compute.models.ImageReference(
        id = image.id
    )
)
```

이전에 프로비저닝된 Managed Disk를 쉽게 연결할 수 있습니다.

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

async_update = compute_client.virtual_machines.begin_create_or_update(
    'my_resource_group',
    vm.name,
    vm,
)
async_update.wait()
```

## <a name="virtual-machine-scale-sets-with-managed-disks"></a>Managed Disks를 사용하는 가상 머신 확장 집합

Managed Disks 이전에는 필요한 모든 VM에 대한 스토리지 계정을 확장 집합 내에서 수동으로 만든 다음 `vhd_containers` list 매개 변수를 사용하여 모든 스토리지 계정 이름을 확장 집합 Rest API에 제공해야 했습니다. (마이그레이션 가이드의 경우 [확장 집합 템플릿을 관리 디스크 확장 집합 템플릿으로 변환](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md)을 참조하세요.)

Azure Managed Disks를 통해 스토리지 계정을 관리할 필요가 없기 때문에 이제 `storage_profile`은 VM 만들기에 사용되는 것과 완전히 같아도 됩니다.

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
result_create = compute_client.virtual_machine_scale_sets.begin_create_or_update(
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

async_update = self.compute_client.disks.begin_create_or_update(
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

async_update = self.compute_client.disks.begin_create_or_update(
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

async_snapshot_creation = self.compute_client.snapshots.begin_create_or_update(
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

## <a name="see-also"></a>참고 항목

- [예 - 가상 머신 프로비저닝](azure-sdk-example-virtual-machines.md)
