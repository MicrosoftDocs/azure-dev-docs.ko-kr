---
title: Eclipse용 Azure Explorer를 사용하여 스토리지 계정 관리
description: Eclipse용 Azure 탐색기를 사용하여 Azure Storage 계정을 관리하는 방법을 알아봅니다.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 19f529b6d04e73c18648b77b2ac83a31ca1a455f
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86378137"
---
# <a name="manage-storage-accounts-by-using-the-azure-explorer-for-eclipse"></a>Eclipse용 Azure Explorer를 사용하여 스토리지 계정 관리

Eclipse용 Azure 도구 키트의 일부인 Azure Explorer는 Eclipse IDE(통합 개발 환경) 내에서 Azure 계정의 스토리지 계정을 관리하기 위한 사용하기 쉬운 솔루션을 Java 개발자에게 제공합니다.

[!INCLUDE [prerequisites](includes/prerequisites.md)]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="create-a-storage-account-in-eclipse"></a>Eclipse에서 스토리지 계정 만들기

Azure Explorer를 사용하여 스토리지 계정을 만들려면 다음을 수행합니다.

1. [Eclipse용 Azure 도구 키트에 대한 로그인 지침](/azure/developer/java/toolkit-for-eclipse/sign-in-instructions)을 사용하여 Azure 계정에 로그인합니다.

1. **Azure Explorer** 보기에서 **Azure** 노드를 확장하고 **스토리지 계정**을 마우스 오른쪽 단추로 클릭한 후 **스토리지 계정 만들기**를 클릭합니다.

   ![Storage 계정 만들기 명령][CS01]

1. **스토리지 계정 만들기** 대화 상자에서 다음 옵션을 지정합니다.

   ![새 Storage 계정 만들기 대화 상자][CS02]

   * **Name**: 새 스토리지 계정의 이름을 지정합니다.

   * **구독**: 새 스토리지 계정에 사용할 Azure 구독을 지정합니다.

   * **리소스 그룹**: 가상 머신용 리소스 그룹을 지정합니다. 다음 옵션 중 하나를 선택합니다.
      * **새로 만들기**: 새 리소스 그룹을 만들도록 지정합니다.
      * **기존 리소스 사용**: Azure 계정에 연결된 리소스 그룹 목록에서 선택하도록 지정합니다.

   * **지역**: 스토리지 계정을 만들 위치(예: “미국 서부”)를 지정합니다.

   * **계정 종류**: 만들려는 스토리지 계정의 형식을 지정합니다(예: “BLOB 스토리지”). 자세한 내용은 [Azure Storage 계정 정보]를 참조하세요.

   * **성능**: 선택한 게시자에서 사용할 스토리지 계정 제품을 지정합니다(예: “프리미엄”). 자세한 내용은 [Azure Storage 확장성 및 성능 목표]를 참조하세요.

   * **복제**: 스토리지 계정의 복제를 지정합니다(예: “영역 중복”). 자세한 내용은 [Azure Storage 복제]를 참조하세요.

1. 위의 옵션을 모두 지정했으면 **만들기**를 클릭합니다.

## <a name="create-a-storage-container-in-eclipse"></a>Eclipse에서 스토리지 컨테이너 만들기

Azure Explorer를 사용하여 스토리지 컨테이너를 만들려면 다음을 수행합니다.

1. **Azure Explorer** 보기에서 컨테이너를 만들 스토리지 계정을 마우스 오른쪽 단추로 클릭하고 **Blob 컨테이너 만들기**를 클릭합니다.

   ![Blob 컨테이너 만들기 명령][CC01]

1. **Blob 컨테이너 만들기** 대화 상자에서 컨테이너의 이름을 지정하고 **확인**을 클릭합니다. 스토리지 컨테이너 이름 지정에 대한 자세한 내용은 [컨테이너, BLOB 및 메타데이터 이름 지정 및 참조]를 참조하세요.

   ![Blob 컨테이너 만들기 대화 상자][CC02]

## <a name="delete-a-storage-container-in-eclipse"></a>Eclipse에서 스토리지 컨테이너 삭제

Azure Explorer를 사용하여 스토리지 컨테이너를 삭제하려면 다음을 수행합니다.

1. **Azure Explorer** 보기에서 스토리지 컨테이너를 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭합니다.

   ![스토리지 컨테이너 삭제 명령][DC01]

1. 확인 창에서 **확인**을 클릭합니다.

   ![스토리지 컨테이너 삭제 확인 창][DC02]

## <a name="delete-a-storage-account-in-eclipse"></a>Eclipse에서 스토리지 계정 삭제

Azure Explorer를 사용하여 스토리지 계정을 삭제하려면 다음을 수행합니다.

1. **Azure Explorer** 보기에서 스토리지 계정을 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭합니다.

   ![스토리지 계정 삭제 명령][DS01]

1. 확인 창에서 **확인**을 클릭합니다.

   ![스토리지 계정 삭제 확인 창][DS02]

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
[컨테이너, BLOB 및 메타데이터 이름 지정 및 참조]: https://go.microsoft.com/fwlink/?LinkId=255555

[Azure의 Windows 스토리지 계정 크기]: /azure/virtual-machines/virtual-machines-windows-sizes
[Azure의 Linux 스토리지 계정 크기]: /azure/virtual-machines/virtual-machines-linux-sizes
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
