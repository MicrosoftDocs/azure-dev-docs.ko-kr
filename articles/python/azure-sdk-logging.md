---
title: Python용 Azure 라이브러리에서 로깅 구성
description: Azure 라이브러리는 라이브러리 또는 작업별로 구성되는 표준 Python 로깅 모듈을 사용합니다.
ms.date: 02/01/2021
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: f42941ec54876fec5854a0a82cee1cbcf30506ce
ms.sourcegitcommit: b09d3aa79113af04a245b05cec2f810e43062152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99476449"
---
# <a name="configure-logging-in-the-azure-libraries-for-python"></a>Python용 Azure 라이브러리에서 로깅 구성

[azure.core 페이지](azure-sdk-library-package-index.md#libraries-using-azurecore)를 기반으로 하는 Python용 Azure 라이브러리는 표준 Python [logging](https://docs.python.org/3/library/logging.html) 라이브러리를 사용하여 로깅 출력을 제공합니다.

로깅을 사용하는 일반적인 프로세스는 다음과 같습니다.

1. 원하는 라이브러리에 대한 로깅 개체를 가져오고, 로깅 수준을 설정합니다.
1. 로깅 스트림에 대한 처리기를 등록합니다.
1. HTTP 정보를 포함하려면 클라이언트 개체 생성자, 자격 증명 개체 생성자 또는 특정 메서드에 `logging_enable=True` 매개 변수를 전달합니다.

세부 정보는 이 문서의 나머지 섹션에서 제공합니다.

일반적으로 라이브러리 내에서 로깅을 사용하는 방법을 이해하는 데 가장 적합한 리소스는 [github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python)에서 SDK 소스 코드를 검색하는 것입니다. 다음 섹션에서 제안하는 것처럼 필요할 때 세부 정보를 쉽게 검색할 수 있도록 이 리포지토리를 로컬로 복제하는 것이 좋습니다.

## <a name="set-logging-levels"></a>로깅 수준 설정

```python
import logging

# ...

# Acquire the logger for a library (azure.storage.blob in this example)
logger = logging.getLogger('azure.storage.blob')

# Set the desired logging level
logger.setLevel(logging.DEBUG)
```

- 이 예제에서는 `azure.storage.blob` 라이브러리에 대한 로거를 가져온 다음, 로깅 수준을 `logging.DEBUG`로 설정합니다.

- 언제든지 `logger.setLevel`을 호출하여 다양한 코드 세그먼트에 대한 로깅 수준을 변경할 수 있습니다.

다른 라이브러리에 대한 수준을 설정하려면 `logging.getLogger` 호출에서 해당 라이브러의 이름을 사용합니다. 예를 들어 azure-eventhubs 라이브러리는 `azure.eventhubs`라는 로거를 제공하고, azure-storage-queue 라이브러리는 `azure.storage.queue`라는 로거를 제공하는 것과 같습니다. (SDK 소스 코드는 포함하는 모듈의 이름을 사용하여 로거를 가져오는 `logging.getLogger(__name__)` 문을 자주 사용합니다.)

또한 더 일반적인 네임스페이스를 사용할 수도 있습니다. 예를 들면 다음과 같습니다.

```python
import logging

# Set the logging level for all azure-storage-* libraries
logger = logging.getLogger('azure.storage')
logger.setLevel(logging.INFO)

# Set the logging level for all azure-* libraries
logger = logging.getLogger('azure')
logger.setLevel(logging.ERROR)
```

`logger.isEnabledFor` 메서드를 사용하여 지정된 로깅 수준을 사용하도록 설정되어 있는지 여부를 확인할 수 있습니다.

```python
print(f"Logger enabled for ERROR={logger.isEnabledFor(logging.ERROR)}, " \
    f"WARNING={logger.isEnabledFor(logging.WARNING)}, " \
    f"INFO={logger.isEnabledFor(logging.INFO)}, " \
    f"DEBUG={logger.isEnabledFor(logging.DEBUG)}")
```

로깅 수준은 [표준 로깅 라이브러리 수준](https://docs.python.org/3/library/logging.html#levels)과 동일합니다. 다음 표에서는 Python용 Azure 라이브러리에서 이러한 로깅 수준을 일반적으로 사용하는 방법에 대해 설명합니다.

| 로깅 수준             | 일반적인 용도 |
| ---                       | ---         |
| logging.ERROR             | 애플리케이션에서 복구할 가능성이 없는 오류입니다(예: 메모리 부족). |
| logging.WARNING(기본값) | 함수에서 의도된 작업을 수행하지 못합니다(그러나 REST API 호출 재시도와 같이 해당 함수에서 복구할 수 있는 경우는 제외). 함수는 일반적으로 예외를 발생시킬 때 경고를 기록합니다. 경고 수준에서 자동으로 오류 수준을 사용하도록 설정합니다. |
| logging.INFO              | 함수가 정상적으로 작동하거나 서비스 호출이 취소됩니다. 정보 이벤트에는 일반적으로 요청, 응답 및 헤더가 포함됩니다. 정보 수준에서 자동으로 오류 및 경고 수준을 사용하도록 설정합니다. |
| logging.DEBUG             | 문제 해결에 일반적으로 사용되며 예외에 대한 스택 추적을 포함하는 세부 정보입니다. 디버그 수준에서 자동으로 정보, 경고 및 오류 수준을 사용하도록 설정합니다. 주의: 또한 `logging_enable=True`를 설정하는 경우 디버그 수준에는 헤더의 계정 키 및 기타 자격 증명과 같은 중요한 정보가 포함됩니다. 보안이 손상되지 않도록 이러한 로그를 보호해야 합니다. |
| logging.NOTSET            | 모든 로깅을 사용하지 않도록 설정합니다. |

### <a name="library-specific-logging-level-behavior"></a>라이브러리와 관련된 로깅 수준 동작

각 수준의 정확한 로깅 동작은 해당 라이브러리에 따라 달라집니다. azure.eventhub와 같은 일부 라이브러리는 광범위한 로깅을 수행하지만, 다른 라이브러리는 거의 수행하지 않습니다.

라이브러리에 대한 정확한 로깅을 검사하는 가장 좋은 방법은 다음과 같이 Python용 Azure SDK 소스 코드에서 로깅 수준을 검색하는 것입니다.

1. 리포지토리 폴더에서 *sdk* 폴더로 이동한 다음, 관심 있는 특정 서비스에 대한 폴더로 이동합니다.

1. 해당 폴더에서 다음 문자열 중 하나를 검색합니다.

    - `_LOGGER.error`
    - `_LOGGER.warning`
    - `_LOGGER.info`
    - `_LOGGER.debug`

## <a name="register-a-log-stream-handler"></a>로그 스트림 처리기 등록

로깅 출력을 캡처하려면 코드에서 하나 이상의 로그 스트림 처리기를 등록해야 합니다.

```python
import logging

# Direct logging output to stdout. Without adding a handler,
# no logging output is captured.
handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)
```

이 예제에서는 로그 출력을 stdout으로 보내는 처리기를 등록합니다. Python 설명서의 [loging.handlers](https://docs.python.org/3/library/logging.handlers.html)에서 설명한 대로 다른 유형의 처리기를 사용하거나 표준 [logging.basicConfig](https://docs.python.org/3/library/logging.html#logging.basicConfig) 메서드를 사용할 수 있습니다.

## <a name="enable-http-logging-for-a-client-object-or-operation"></a>클라이언트 개체 또는 작업에 대한 HTTP 로깅 사용

기본적으로 Azure 라이브러리 내의 로깅에는 HTTP 정보가 포함되지 않습니다. 로그 출력에 HTTP 정보(DEBUG 수준)를 포함하려면 클라이언트 또는 자격 증명 개체 생성자 또는 특정 메서드에 구체적으로 `logging_enable=True`를 전달해야 합니다.

**주의**: HTTP 로깅에는 헤더의 계정 키 및 기타 자격 증명과 같은 중요한 정보가 포함될 수 있습니다. 보안이 손상되지 않도록 이러한 로그를 보호해야 합니다.

### <a name="enable-http-logging-for-a-client-object-debug-level"></a>클라이언트 개체에 대한 HTTP 로깅 사용(DEBUG 수준)

```python
from azure.storage.blob import BlobClient
from azure.identity import DefaultAzureCredential

# Enable HTTP logging on the client object when using DEBUG level
# endpoint is the Blob storage URL.
client = BlobClient(endpoint, DefaultAzureCredential(), logging_enable=True)
```

클라이언트 개체에 대해 HTTP 로깅을 사용하도록 설정하면 해당 개체를 통해 호출되는 모든 작업에 대한 로깅을 사용할 수 있습니다.

### <a name="enable-http-logging-for-a-credential-object-debug-level"></a>자격 증명 개체에 대한 HTTP 로깅 사용(DEBUG 수준)

```python
from azure.storage.blob import BlobClient
from azure.identity import DefaultAzureCredential

# Enable HTTP logging on the credential object when using DEBUG level
credential = DefaultAzureCredential(logging_enable=True)

# endpoint is the Blob storage URL.
client = BlobClient(endpoint, credential)
```

자격 증명 개체에 대해 HTTP 로깅을 사용하도록 설정하면 해당 개체를 통해 호출되는 모든 작업에 대해 로깅이 가능하지만 인증을 포함하지 않는 클라이언트 개체의 작업에 대한 로깅은 실행되지 않습니다.

### <a name="enable-logging-for-an-individual-method-debug-level"></a>개별 메서드에 대한 로깅 사용(DEBUG 수준)

```python
from azure.storage.blob import BlobClient
from azure.identity import DefaultAzureCredential

# endpoint is the Blob storage URL.
client = BlobClient(endpoint, DefaultAzureCredential())

# Enable HTTP logging for only this operation when using DEBUG level
client.create_container("container01", logging_enable=True)
```

## <a name="example-logging-output"></a>로깅 출력 예제

다음 코드는 [예제: 스토리지 계정 사용](azure-sdk-example-storage-use.md)에 나와 있는 코드이며, 추가적으로 DEBUG 및 HTTP 로깅을 사용하도록 설정되어 있습니다(간단히 하기 위해 주석이 생략됨).

```python
import os, sys, logging
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobClient

logger = logging.getLogger('azure.storage.blob')
logger.setLevel(logging.DEBUG)

handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

credential = DefaultAzureCredential()
storage_url = os.environ["AZURE_STORAGE_BLOB_URL"]

blob_client = BlobClient(storage_url, container_name="blob-container-01",
    blob_name="sample-blob.txt", credential=credential)

with open("./sample-source.txt", "rb") as data:
    blob_client.upload_blob(data, logging_enable=True)
```

로깅 출력은 다음과 같습니다.

<pre>
Request URL: 'https://pythonsdkstorage12345.blob.core.windows.net/blob-container-01/sample-blob.txt'
Request method: 'PUT'
Request headers:
    'Content-Type': 'application/octet-stream'
    'Content-Length': '79'
    'x-ms-version': '2019-07-07'
    'x-ms-blob-type': 'BlockBlob'
    'If-None-Match': '*'
    'x-ms-date': 'Mon, 01 Jun 2020 22:54:14 GMT'
    'x-ms-client-request-id': 'd081f88e-a45a-11ea-b9eb-0c5415dfd03a'
    'User-Agent': 'azsdk-python-storage-blob/12.3.1 Python/3.8.3 (Windows-10-10.0.18362-SP0)'
    'Authorization': '*****'
Request body:
b"Hello there, Azure Storage. I'm a friendly file ready to be stored in a blob.\r\n"
Response status: 201
Response headers:
    'Content-Length': '0'
    'Content-MD5': 'kvMIzjEi6O8EqTVnZJNakQ=='
    'Last-Modified': 'Mon, 01 Jun 2020 22:54:14 GMT'
    'ETag': '"0x8D8067EB52FF7BC"'
    'Server': 'Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0'
    'x-ms-request-id': '5df479b1-f01e-00d0-5b67-382916000000'
    'x-ms-client-request-id': 'd081f88e-a45a-11ea-b9eb-0c5415dfd03a'
    'x-ms-version': '2019-07-07'
    'x-ms-content-crc64': 'QmecNePSHnY='
    'x-ms-request-server-encrypted': 'true'
    'Date': 'Mon, 01 Jun 2020 22:54:14 GMT'
Response content:
</pre>
