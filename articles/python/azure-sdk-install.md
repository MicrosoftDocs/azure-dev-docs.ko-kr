---
title: Python용 Azure SDK 라이브러리 패키지 설치 방법
description: pip를 사용하여 Azure SDK 또는 Python 라이브러리를 설치, 제거 및 확인하는 방법입니다. 특정 버전 및 미리 보기 패키지 설치에 대한 세부 정보가 포함되어 있습니다.
ms.date: 05/26/2020
ms.topic: conceptual
ms.openlocfilehash: f50de734ab1d007c9e5efac8cd6559a2c03d83f5
ms.sourcegitcommit: efab6be74671ea4300162e0b30aa8ac134d3b0a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84256258"
---
# <a name="how-to-install-azure-library-packages-for-python"></a>Python용 Azure 라이브러리 패키지 설치 방법

Python용 Azure SDK는 [Python용 Azure SDK 인덱스 페이지](https://azure.github.io/azure-sdk/releases/latest/all/python.html)에 나열된 여러 개별 라이브러리로만 구성됩니다. `pip install`을 사용하여 프로젝트에 필요한 특정 라이브러리 패키지를 설치합니다.

이러한 라이브러리를 통해 이름에 `-mgmt`가 있는 관리 라이브러리를 사용하여 Azure 서비스에서 리소스를 프로비저닝 및 관리하고 클라이언트 리소스를 사용하여 앱 코드의 해당 리소스와 연결할 수 있습니다.

## <a name="install-the-latest-version-of-a-library"></a>최신 버전의 라이브러리 설치

```cmd
pip install azure-storage-blob
```

```cmd
pip install azure-mgmt-storage
```

`pip install`은 현재 Python 환경에 최신 버전의 라이브러리를 검색합니다.

Linux 시스템에서는 각 사용자에 대해 라이브러리를 별도로 설치해야 합니다. `sudo pip install`을 사용하는 모든 사용자를 위한 라이브러리 설치는 지원되지 않습니다.

## <a name="install-specific-library-versions"></a>특정 라이브러리 버전 설치

```cmd
pip install azure-storage-blob==12.0.0
```

```cmd
pip install azure-mgmt-storage==10.0.0
```

`pip install`을 사용하여 명령줄에 원하는 버전을 지정합니다.

## <a name="install-preview-packages"></a>미리 보기 패키지 설치

```cmd
pip install --pre azure-storage-blob
```

```cmd
pip install --pre azure-mgmt-storage
```

라이브러리의 최신 미리 보기를 설치하려면 명령줄에서 `--pre` 플래그를 포함시킵니다.

Microsoft는 예정된 기능을 지원하는 미리 보기 라이브러리 패키지를 주기적으로 릴리스하며, 라이브러리가 변경될 수 있으므로 프로덕션 프로젝트에서 사용하면 안 된다는 경고가 있습니다.

## <a name="verify-a-library-installation"></a>라이브러리 설치 확인

```cmd
pip show azure-storage-blob
```

```cmd
pip show azure-mgmt-storage
```

`pip show <library>`를 사용하여 라이브러리가 설치되어 있는지 확인합니다. 라이브러리가 설치된 경우 명령에서는 버전 및 기타 요약 정보를 표시합니다. 그렇지 않으면 아무 항목도 표시되지 않습니다.

또한 `pip freeze` 또는 `pip list`를 사용하여 현재 Python 환경에 설치된 모든 라이브러리를 확인할 수도 있습니다.

## <a name="uninstall-a-library"></a>라이브러리 제거

```cmd
pip uninstall azure-storage-blob
```

라이브러리를 제거하려면 `pip uninstall <library>`를 사용합니다.
