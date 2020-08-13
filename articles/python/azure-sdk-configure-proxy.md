---
title: Azure 라이브러리를 사용할 때 프록시 구성
description: HTTP[S]_PROXY 환경 변수를 사용하여 전체 스크립트 또는 앱의 프록시를 정의하거나, 선택적으로 명명된 인수를 클라이언트 생성자 또는 작업 메서드에 사용합니다.
ms.date: 06/16/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 86eaa5b8dc0ddfb529643a5c015938344582e62b
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87982705"
---
# <a name="how-to-configure-proxies-for-the-azure-libraries"></a>Azure 라이브러리에 대한 프록시를 구성하는 방법

프록시 서버 URL은 `http[s]://[username:password@]<ip_address_or_domain>:<port>/` 형식이며 username:password 조합은 선택 사항입니다.

그런 다음, 환경 변수를 사용하여 전역적으로 프록시를 구성하거나, `proxies`라는 인수를 개별 클라이언트 생성자 또는 작업 메서드에 전달하여 프록시를 지정할 수 있습니다.

## <a name="global-configuration"></a>글로벌 구성

스크립트 또는 앱에 대한 프록시를 전역적으로 구성하려면 서버 URL을 사용하여 `HTTP_PROXY` 또는 `HTTPS_PROXY` 환경 변수를 정의합니다. 이러한 변수는 모든 버전의 Azure 라이브러리에서 작동합니다.

`use_env_settings=False` 매개 변수를 클라이언트 개체 생성자 또는 작업 메서드에 전달하면 이러한 환경 변수가 무시됩니다.

### <a name="from-python-code"></a>Python 코드에서

```python
import os
os.environ["HTTP_PROXY"] = "http://10.10.1.10:1180"

# Alternate URL and variable forms:
# os.environ["HTTP_PROXY"] = "http://username:password@10.10.1.10:1180"
# os.environ["HTTPS_PROXY"] = "http://10.10.1.10:1180"
# os.environ["HTTPS_PROXY"] = "http://username:password@10.10.1.10:1180"
```

### <a name="from-the-cli"></a>CLI에서

# <a name="cmd"></a>[cmd](#tab/cmd)

```cmd
rem Non-authenticated HTTP server:
set HTTP_PROXY=http://10.10.1.10:1180

rem Authenticated HTTP server:
set HTTP_PROXY=http://username:password@10.10.1.10:1180

rem Non-authenticated HTTPS server:
set HTTPS_PROXY=http://10.10.1.10:1180

rem Authenticated HTTPS server:
set HTTPS_PROXY=http://username:password@10.10.1.10:1180
```

# <a name="bash"></a>[bash](#tab/bash)

```bash
# Non-authenticated HTTP server:
HTTP_PROXY=http://10.10.1.10:1180

# Authenticated HTTP server:
HTTP_PROXY=http://username:password@10.10.1.10:1180

# Non-authenticated HTTPS server:
HTTPS_PROXY=http://10.10.1.10:1180

# Authenticated HTTPS server:
HTTPS_PROXY=http://username:password@10.10.1.10:1180
```

---

## <a name="per-client-or-per-method-configuration"></a>클라이언트별 또는 메서드별 구성

특정 클라이언트 개체 또는 작업 메서드에 대한 프록시를 구성하려면 `proxies`라는 인수를 사용하여 프록시 서버를 지정합니다.

예를 들어 [예제: Azure 스토리지 사용](azure-sdk-example-storage.md) 문서의 다음 코드는 `BlobClient` 생성자를 사용하여 사용자 자격 증명으로 HTTPS 프록시를 지정합니다. 이 경우 개체는 azure.core 기반의 azure.storage.blob 라이브러리에서 제공됩니다.

```python
# Earlier code omitted for brevity

blob_client = BlobClient(storage_url, container_name="blob-container-01",
    blob_name="sample-blob.txt", credential=credential,
    proxies={ "https": "https://username:password@10.10.1.10:1180" }
)

# Other forms that the proxy URL might take:
# proxies={ "http": "http://10.10.1.10:1180" }
# proxies={ "http": "http://username:password@10.10.1.10:1180" }
# proxies={ "https": "https://10.10.1.10:1180" }
```
