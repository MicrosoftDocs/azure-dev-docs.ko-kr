---
title: Python용 Azure SDK 라이브러리 설치
description: pip를 사용하여 Azure SDK 또는 Python 라이브러리를 설치, 제거 및 확인하는 방법입니다. 특정 버전 및 미리 보기 패키지 설치에 대한 세부 정보가 포함되어 있습니다.
ms.date: 05/13/2020
ms.topic: conceptual
ms.openlocfilehash: 302d17211480f9c8793d7be1de20a6ab5dec3c95
ms.sourcegitcommit: 2cdf597e5368a870b0c51b598add91c129f4e0e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2020
ms.locfileid: "83403667"
---
# <a name="install-azure-sdk-for-python-libraries"></a>Python용 Azure SDK 라이브러리 설치

Python용 Azure SDK는 Python 코드에서 Azure와 상호 작용할 수 있는 API를 제공합니다. 모든 현재 라이브러리의 이름은 [Python용 Azure SDK 인덱스 페이지](https://azure.github.io/azure-sdk/releases/latest/all/python.html)에 있습니다.

이름이 `azure-mgmt`로 시작하는 라이브러리는 *관리* 라이브러리이며, [Azure Portal](https://portal.azure.com) 또는 [Azure CLI](/cli/azure/install-azure-cli)를 사용하여 Azure 리소스를 프로비저닝하고 관리하는 데 사용됩니다. 예를 들어 Azure Storage 리소스를 프로비저닝하고 관리하려면 `azure-mgmt-storage` 라이브러리를 사용합니다.

SDK의 다른 모든 라이브러리는 이미 프로비저닝된 리소스로 작업하기 위해 애플리케이션 코드에서 사용하는 *클라이언트* 라이브러리입니다. 예를 들어 애플리케이션 코드에서 Azure Storage Blob으로 작업하려면 `azure-storage-blob` 라이브러리를 사용합니다.

## <a name="install-the-latest-version-of-a-library"></a>최신 버전의 라이브러리 설치

```bash
pip install azure-storage-blob
```

`pip install`은 현재 Python 환경에 최신 버전의 라이브러리를 설치합니다.

Linux 시스템에서 SDK는 `sudo pip install`을 사용하여 모든 사용자를 위한 라이브러리를 설치하는 것을 지원하지 않습니다. 각 사용자는 `pip install`을 별도로 사용해야 합니다.

## <a name="install-specific-library-versions"></a>특정 라이브러리 버전 설치

```bash
pip install azure-storage-blob==12.0.0
```

명령줄에서 `pip install`을 사용하여 버전을 지정합니다.

## <a name="install-preview-packages"></a>미리 보기 패키지 설치

```bash
pip install --pre azure-storage-blob
```

라이브러리의 최신 미리 보기를 설치하려면 명령줄에서 `--pre` 플래그를 포함시킵니다.

Microsoft는 예정된 기능을 지원하는 미리 보기 SDK 라이브러리를 정기적으로 릴리스하며, 라이브러리가 변경될 수 있으므로 프로덕션 프로젝트에서 사용하면 안 된다는 경고가 있습니다.

## <a name="verify-a-library-installation"></a>라이브러리 설치 확인

```bash
pip show azure-storage-blob
```

`pip show <library>`를 사용하여 라이브러리가 설치되어 있는지 확인합니다. 라이브러리가 설치된 경우 명령에서는 버전 및 기타 요약 정보를 표시합니다. 그렇지 않으면 아무 항목도 표시되지 않습니다.

또한 `pip freeze` 또는 `pip list`를 사용하여 현재 Python 환경에 설치된 모든 라이브러리를 확인할 수도 있습니다.

## <a name="uninstall-a-library"></a>라이브러리 제거

```bash
pip uninstall azure-storage-blob
```

라이브러리를 제거하려면 `pip uninstall <library>`를 사용합니다.

## <a name="next-steps"></a>다음 단계

이제 다음 예제 중 하나를 사용하여 수행할 수 있는 일부 코드를 작성하고 실행할 준비가 완료되었습니다.

> [!div class="nextstepaction"]
> [예: 리소스 그룹 만들기 >>>](azure-sdk-example-resource-group.md)
