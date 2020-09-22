---
title: Eclipse용 Azure Explorer를 사용하여 스토리지 계정 관리
description: Eclipse용 Azure 탐색기를 사용하여 Azure Storage 계정을 관리하는 방법을 알아봅니다.
documentationcenter: java
ms.date: 08/25/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 8c7c39fa80568efa3040d5cbfa18c3b0cfcd43bc
ms.sourcegitcommit: a139e25190960ba89c9e31f861f0996a6067cd6c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534641"
---
# <a name="manage-storage-accounts-by-using-the-azure-explorer-for-eclipse"></a>Eclipse용 Azure Explorer를 사용하여 스토리지 계정 관리

> [!NOTE]
> Azure Explorer의 스토리지 계정 기능은 더 이상 사용되지 않습니다. Azure Portal을 활용하여 스토리지 계정 및 컨테이너를 만들고 관리할 수 있습니다. 스토리지 계정을 관리하는 방법에 대한 빠른 시작은 [Azure Storage](/azure/storage/blobs/storage-quickstart-blobs-portal) 설명서를 참조하세요.

Azure Toolkit for Eclipse의 일부인 Azure Explorer는 Eclipse IDE(통합 개발 환경) 내에서 Azure 계정의 스토리지 계정을 관리하기 위한 사용하기 쉬운 솔루션을 Java 개발자에게 제공합니다.

[!INCLUDE [prerequisites](includes/prerequisites.md)]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

1. [Eclipse용 Azure 도구 키트에 대한 로그인 지침](/azure/developer/java/toolkit-for-eclipse/sign-in-instructions)을 사용하여 Azure 계정에 로그인합니다.

1. **Azure Explorer** 보기에서 **Azure** 노드를 확장하고 **스토리지 계정**을 마우스 오른쪽 단추로 클릭한 후 **스토리지 계정 만들기**를 클릭합니다.

1. **스토리지 계정 만들기** 대화 상자에서 다음 옵션을 지정합니다.

   * **Name**: 새 스토리지 계정의 이름을 지정합니다.

   * **구독**: 새 스토리지 계정에 사용할 Azure 구독을 지정합니다.

   * **리소스 그룹**: 가상 머신용 리소스 그룹을 지정합니다. 다음 옵션 중 하나를 선택합니다.
      * **새로 만들기**: 새 리소스 그룹을 만들도록 지정합니다.
      * **기존 리소스 사용**: Azure 계정에 연결된 리소스 그룹 목록에서 선택하도록 지정합니다.

   * **지역**: 스토리지 계정을 만들 위치(예: “미국 서부”)를 지정합니다.

   * **계정 종류**: 만들려는 스토리지 계정 형식을 지정합니다(예: "범용 v1"). 자세한 내용은 [Azure Storage 계정 정보]를 참조하세요.

   * **성능**: 선택한 게시자에서 사용할 스토리지 계정 제품을 지정합니다(예: "표준"). 자세한 내용은 [Azure Storage 확장성 및 성능 목표]를 참조하세요.

   * **복제**: 스토리지 계정의 복제를 지정합니다(예: "로컬 중복"). 자세한 내용은 [Azure Storage 복제]를 참조하세요.

1. 위의 옵션을 모두 지정했으면 **만들기**를 클릭합니다.

## <a name="create-and-manage-storage-containers"></a>스토리지 컨테이너 만들기 및 관리

스토리지 컨테이너를 만들고 관리하려면 Azure Portal을 방문하거나 프로그래밍 방식으로 리소스를 프로비저닝합니다.

Azure Portal을 사용하여 Azure Storage에 컨테이너를 만들고 해당 컨테이너에서 블록 Blob을 업로드 및 다운로드하는 방법에 대한 단계별 자습서는 [Azure Portal을 사용하여 Blob 업로드, 다운로드 및 나열](/azure/storage/blobs/storage-quickstart-blobs-portal)을 참조하세요.

## <a name="delete-a-storage-account"></a>스토리지 계정 삭제

1. **Azure Explorer** 보기에서 스토리지 계정을 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭합니다.

1. 확인 창에서 **확인**을 클릭합니다.


## <a name="next-steps"></a>다음 단계

Azure Storage 계정, 크기 및 가격 책정에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Microsoft Azure Storage 소개]
* [Azure Storage 계정 정보]
* Azure Storage 계정 크기
  * [Azure의 Windows 스토리지 계정 크기]
  * [Azure의 Linux 스토리지 계정 크기]
* Azure Storage 계정 가격 책정
  * [Windows 스토리지 계정 가격 책정]
  * [Linux 스토리지 계정 가격 책정]

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

[Microsoft Azure Storage 소개]: /azure/storage/common/storage-introduction
[Azure Storage 계정 정보]: /azure/storage/storage-create-storage-account
[Azure Storage 복제]: /azure/storage/storage-redundancy
[Azure Storage 확장성 및 성능 목표]: /azure/storage/storage-scalability-targets
[Naming and referencing containers, blobs, and metadata]: https://go.microsoft.com/fwlink/?LinkId=255555

[Azure의 Windows 스토리지 계정 크기]: https://docs.microsoft.com/azure/virtual-machines/sizes
[Azure의 Linux 스토리지 계정 크기]: https://docs.microsoft.com/azure/virtual-machines/sizes
[Windows 스토리지 계정 가격 책정]: https://azure.microsoft.com/pricing/details/virtual-machines/windows/
[Linux 스토리지 계정 가격 책정]: https://azure.microsoft.com/pricing/details/virtual-machines/linux/

<!-- IMG List -->

[CS01]: media/managing-storage-accounts-using-azure-explorer/CS01.png
[CS02]: media/managing-storage-accounts-using-azure-explorer/CS02.png
[CC01]: media/managing-storage-accounts-using-azure-explorer/CC01.png
[CC02]: media/managing-storage-accounts-using-azure-explorer/CC02.png

[DS01]: media/managing-storage-accounts-using-azure-explorer/DS01.png
[DS02]: media/managing-storage-accounts-using-azure-explorer/DS02.png
[DC01]: media/managing-storage-accounts-using-azure-explorer/DC01.png
[DC02]: media/managing-storage-accounts-using-azure-explorer/DC02.png
