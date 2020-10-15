---
title: IntelliJ용 Azure 탐색기를 사용하여 Redis Cache 관리
description: IntelliJ용 Azure 탐색기를 사용하여 Azure Redis Cache를 관리하는 방법을 알아봅니다.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 2ce0a20ffd33ba2311de73578338005666779a9d
ms.sourcegitcommit: f460914ac5843eb7392869a08e3a80af68ab227b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92010151"
---
# <a name="managing-redis-caches-using-the-azure-explorer-for-intellij"></a>IntelliJ용 Azure 탐색기를 사용하여 Redis Cache 관리

IntelliJ용 Azure 도구 키트의 일부인 Azure 탐색기는 IntelliJ IDE 내에서 Azure 계정의 Redis Cache를 관리하기 위한 사용하기 쉬운 솔루션을 Java 개발자에게 제공합니다.

[!INCLUDE [prerequisites](includes/prerequisites.md)]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="create-a-redis-cache-by-using-intellij"></a>IntelliJ를 사용하여 Redis Cache 만들기

다음 단계에서는 Azure 탐색기를 사용하여 Redis Cache를 만드는 단계를 안내합니다.

1. [IntelliJ용 Azure 도구 키트에 대한 로그인 지침] 문서의 단계를 사용하여 Azure 계정에 로그인합니다.

1. **Azure 탐색기** 도구 창에서 **Azure** 노드를 펼치고 **Redis Caches**를 마우스 오른쪽 단추로 클릭한 다음 **Redis Cache 만들기**를 클릭합니다.

   ![Redis Cache 만들기 메뉴][CR01]

1. **새 Redis Cache** 대화 상자가 표시되면 다음 옵션을 지정합니다.

   ![새 Redis Cache 만들기 대화 상자][CR02]

   a. **DNS 이름**: 새 Redis Cache에 대한 DNS 하위 도메인을 지정합니다. 이 주소는 ".redis.cache.windows.net" 앞에 추가됩니다(예: *wingtiptoys.redis.cache.windows.net*).

   b. **구독**: 새 Redis Cache에 사용할 Azure 구독을 지정합니다.

   다. **리소스 그룹**: Redis Cache에 대한 리소스 그룹을 지정합니다. 다음 옵션 중 하나를 선택해야 합니다. 
      * **새로 만들기**: 새 리소스 그룹을 만들도록 지정합니다. 
      * **기존 리소스 사용**: Azure 계정과 연결된 리소스 그룹 목록에서 선택하도록 지정합니다. 

   d. **위치**: Redis Cache를 만들 위치를 지정합니다(예: *미국 서부*).

   e. **가격 책정 계층**: Redis Cache에서 사용하는 가격 책정 계층을 지정합니다. 이 설정은 클라이언트 연결 수를 결정합니다. 자세한 내용은 [Redis Cache 가격]을 참조하세요.

   f. **비 SSL 포트**: Redis Cache에서 비SSL 연결을 허용하는지 여부를 지정합니다. 기본적으로 SSL 연결만 허용됩니다.

1. 모든 Redis Cache 설정을 지정한 후 **확인**을 클릭합니다.

Redis Cache를 만들면 Azure 탐색기에서 해당 Redis Cache가 표시됩니다.

    ![Redis Cache in Azure Explorer][CR03]

> [!NOTE]
>
> Azure Redis Cache 설정을 구성하는 방법에 대한 자세한 내용은 [Azure Redis Cache 구성 방법]을 참조하세요.
>

## <a name="display-the-properties-for-your-redis-cache-in-intellij"></a>IntelliJ에서 Redis Cache에 대한 속성 표시

1. [Azure 탐색기]에서 Redis Cache를 마우스 오른쪽 단추로 클릭하고 **속성 표시**를 클릭합니다.

   ![Redis Cache에 대한 속성을 표시하는 Azure 탐색기 컨텍스트 메뉴][SP01]

1. Azure 탐색기에서 Redis Cache에 대한 속성을 표시합니다.

   ![Redis Cache 속성][SP02]

## <a name="delete-your-redis-cache-by-using-intellij"></a>IntelliJ를 사용하여 Redis Cache 삭제

1. [Azure 탐색기]에서 Redis Cache를 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭합니다.

   ![Redis Cache를 삭제하는 Azure 탐색기 컨텍스트 메뉴][DE01]

1. Redis Cache를 삭제할지 묻는 메시지가 표시되면 **예**를 클릭합니다.

   ![Redis Cache 삭제 프롬프트][DE02]

## <a name="next-steps"></a>다음 단계

Azure Redis Cache, 구성 설정 및 가격 책정에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Azure Redis 캐시(영문)]
* [Redis Cache 설명서]
* [Redis Cache 가격]
* [Azure Redis Cache 구성 방법]

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

[Redis Cache 가격]: https://azure.microsoft.com/pricing/details/cache/
[Azure Redis 캐시(영문)]: https://azure.microsoft.com/services/cache/
[Redis Cache 설명서]: /azure/redis-cache
[Azure Redis Cache 구성 방법]: /azure/redis-cache/cache-configure
[IntelliJ용 Azure 도구 키트에 대한 로그인 지침]: ./sign-in-instructions.md

<!-- IMG List -->

[CR01]: media/managing-redis-caches-using-azure-explorer/CR01.png
[CR02]: media/managing-redis-caches-using-azure-explorer/CR02.png
[CR03]: media/managing-redis-caches-using-azure-explorer/CR03.png

[SP01]: media/managing-redis-caches-using-azure-explorer/SP01.png
[SP02]: media/managing-redis-caches-using-azure-explorer/SP02.png

[DE01]: media/managing-redis-caches-using-azure-explorer/DE01.png
[DE02]: media/managing-redis-caches-using-azure-explorer/DE02.png
