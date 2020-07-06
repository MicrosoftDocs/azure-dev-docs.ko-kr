---
title: Azure Cognitive Search를 사용하는 모바일 애플리케이션 개발용 AI 기반 클라우드 검색 서비스
description: AI 기반 클라우드 검색 서비스를 사용하여 모바일 애플리케이션을 개발할 수 있는 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 34a8a070-0123-8982-8345-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 0857d4732d15ed389b72273156b5b404f29eb862
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632555"
---
# <a name="ai-powered-cloud-service-with-azure-cognitive-search"></a>Azure Cognitive Search를 사용하는 AI 기반 클라우드 서비스

[Azure Cognitive Search](https://azure.microsoft.com/services/search/)는 개발자에게 웹, 모바일 및 엔터프라이즈 애플리케이션의 프라이빗, 이기종 콘텐츠에 대한 풍부한 검색 환경을 추가하기 위한 API 및 도구를 제공하는 SaaS(Search-as-a-Service) 클라우드 솔루션입니다. 사용자 지정 코드는 데이터 수집(인덱싱)을 호출하여 인덱스를 만들고 로드합니다. 다른 쪽에서는 애플리케이션 코드가 쿼리 요청을 발행하고 응답을 처리합니다. 검색 환경은 Azure Cognitive Search의 기능을 사용하여 클라이언트에 정의되며, 서비스에서 만들고, 소유하고, 저장하는 지속형 인덱스에 대한 쿼리를 실행합니다.

## <a name="azure-cognitive-search-features"></a>Azure Cognitive Search 기능

- **자유 형식 텍스트 검색**: 전체 텍스트 검색, 단순 쿼리 구문 및 Lucene 쿼리 구문을 제공합니다.
- **간단한 채점 프로필**: 관련성을 문서 자체의 값에 대한 함수로 모델링합니다.
- **지리적 검색**: 실제 위치에 대한 검색 결과의 근접도에 따라 데이터를 탐색합니다.
- **사용자 환경 기능**: 자동 완성, 검색 제안, 정렬 및 페이징을 제공합니다.
- **인지 검색**: 인덱싱 파이프라인에 적용하여 원시 콘텐츠에서 이미지 및 텍스트 분석을 위한 텍스트 정보를 추출할 수 있습니다.
- **지식 저장소**: 인덱싱 중에 생성된 보강 내용을 저장합니다.
- **데이터 원본**: Azure SQL Database, Azure Cosmos DB 및 Azure Blob 스토리지에 연결되었을 때 주 데이터 저장소에서 검색 가능한 콘텐츠를 추출합니다.
- **데이터 가져오기 마법사**: 인덱서를 구성합니다. 
- **인덱스 디자이너**: 인덱스를 나타냅니다. 
- **검색 탐색기**: 쿼리를 테스트하고 채점 프로필을 구체화합니다.

Azure Cognitive Search [기능](/azure/search/search-what-is-azure-search#feature-descriptions)에 대한 자세한 설명을 확인하세요.

## <a name="azure-cognitive-search-references"></a>Azure Cognitive Search 참조

- [Azure Portal](https://portal.azure.com) 
- [Azure Cognitive Search 설명서](/azure/search/)
- [개발자 가이드](https://azure.microsoft.com/resources/iot-developers-guide/)
- [Azure Cognitive Search를 사용하는 방법](/azure/search/search-what-is-azure-search#how-to-use-azure-cognitive-search)
- [빠른 시작](/azure/search/search-create-service-portal)

 