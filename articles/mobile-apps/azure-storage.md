---
title: Azure Storage를 사용하여 매우 안전하고 내구성이 뛰어나고 확장 가능한 앱을 빌드할 수 있는 클라우드 스토리지
description: 클라우드에 대량의 정형 및 비정형 모바일 애플리케이션 데이터를 저장하는 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 7a1171ebe64b0353fc57180adce63ee702431207
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632365"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Azure Storage를 사용한 매우 안전하고 내구성이 뛰어나고 확장 가능한 앱용 클라우드 저장소

최신 애플리케이션을 위한 Microsoft의 클라우드 스토리지 솔루션인 [Azure Storage](https://azure.microsoft.com/services/storage/)는 데이터 개체를 저장하는 확장성이 우수한 개체 저장소, 클라우드용 파일 시스템 서비스, 안정적인 메시지 제공을 위한 메시지 저장소 및 NoSQL 저장소를 제공합니다. Azure Storage는 다음과 같은 장점이 있습니다.

- **내구성 및 고가용성** 중복성을 통해 일시적인 하드웨어 오류가 발생할 경우 데이터를 안전하게 보호합니다. 또한 로컬 재해 또는 자연 재해로 인한 장애를 방지할 수 있도록 데이터 센터 또는 지리적 영역에서 데이터를 복제하도록 선택할 수도 있습니다. 이러한 방식으로 복제된 데이터는 예기치 않은 중단이 발생할 경우 항상 사용 가능한 상태로 유지됩니다.
- **강력한 보안:** Azure Storage에 기록된 모든 데이터는 서비스에 의해 암호화됩니다. Azure Storage는 데이터에 액세스할 수 있는 사용자에 대한 세분화된 제어를 제공합니다.
- **확장성:** 이 서비스는 최신 애플리케이션의 데이터 스토리지 및 성능 요구 사항을 충족하기 위해 확장성이 매우 뛰어나도록 설계되었습니다.
- **관리형 서비스:** 하드웨어 유지 관리, 업데이트 및 중요한 문제를 Azure에서 처리합니다.
- **접근성:** 전 세계 어디서나 HTTP 또는 HTTPS를 통해 데이터에 액세스할 수 있습니다. Microsoft는 완성도 높은 REST API를 제공할 뿐 아니라 .NET, Java, Node.js, Python, PHP, Ruby, Go 등의 다양한 언어로 클라이언트 라이브러리를 제공합니다. 스크립팅은 Azure PowerShell 또는 Azure CLI에서 지원됩니다. Azure Portal 및 Azure Storage Explorer는 데이터 작업을 위한 간편한 시각적 솔루션을 제공합니다.

다음 서비스를 사용하여 모바일 앱에서 클라우드 스토리지를 사용하도록 설정하세요.

## <a name="azure-blob-storage"></a>Azure Blob 스토리지

[Azure Blob 스토리지](https://azure.microsoft.com/services/storage/blobs/)는 클라우드용 개체 스토리지 솔루션을 제공합니다. Blob 스토리지는 텍스트 또는 이진 데이터처럼 특정 데이터 모델이나 정의를 따르지 않는 비정형 데이터를 대량으로 저장하는 데 최적화되었습니다. 클라이언트 라이브러리에서 사용하는 다양한 언어를 지원합니다. Blob 스토리지는 다음을 위해 설계되었습니다.

- 브라우저에 이미지 또는 문서를 직접 제공
- 분산 액세스용 파일 저장
- 비디오 및 오디오 스트림
- 로그 파일에 쓰기
- 백업/복원, 재해 복구 및 보관용 데이터 저장
- 온-프레미스 또는 Azure에 호스트되는 서비스에서 분석할 데이터 저장

### <a name="azure-blob-storage-references"></a>Azure Blob 스토리지 참조

- [Azure Portal](https://portal.azure.com)
- [Azure Blob 스토리지 설명서](/azure/storage/blobs/storage-blobs-introduction)
- [빠른 시작](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [샘플](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage

[Azure Table 스토리지](https://azure.microsoft.com/services/storage/tables/)는 전형 NoSQL 데이터를 클라우드에 저장하는 서비스로, 스키마 없이 디자인된 키 또는 특성 저장소를 제공합니다. Azure Table Storage는 많은 양의 구조화된 데이터를 저장합니다. 이 서비스는 Azure 클라우드 내부 및 외부에서 인증된 호출을 수락하는 NoSQL 데이터 저장소입니다. Azure 테이블은 정형, 비관계형 데이터를 저장하는 데 적합합니다. Table Storage는 일반적으로 다음 용도로 사용됩니다.

- 웹 규모 애플리케이션을 지원할 수 있는 수 테라바이트의 정형 데이터를 저장합니다.
- 복합 조인, 외래 키 또는 저장 프로시저가 필요하지 않고 빠른 액세스를 위해 역정규화할 수 있는 데이터 세트를 저장합니다.
- 클러스터형 인덱스를 사용하여 신속하게 데이터를 쿼리합니다.
- OData 프로토콜 및 LINQ 쿼리와 WCF(Windows Communication Foundation) Data Services .NET 라이브러리를 사용하여 데이터에 액세스합니다.

Table 스토리지를 사용하여 정형, 비관계형 데이터 세트를 대량으로 저장하고 쿼리할 수 있습니다. 수요가 증가하면 테이블이 확장됩니다.

### <a name="azure-table-storage-references"></a>Azure Table 스토리지 참조

- [Azure Portal](https://portal.azure.com)
- [Azure Table 스토리지 설명서](/azure/storage/tables/table-storage-overview)
- [샘플](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [빠른 시작](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-queue-storage"></a>Azure Queue storage

[Azure Queue storage](https://azure.microsoft.com/services/storage/queues/)는 대량의 메시지를 저장하기 위한 서비스입니다. 전 세계 어디서나 인증된 호출을 통해 HTTP 또는 HTTPS를 사용하여 메시지에 액세스할 수 있습니다. 큐 메시지의 크기는 최대 64KB입니다. 큐는 스토리지 계정의 용량 제한에 도달할 때까지 수백만 개의 메시지를 포함할 수 있습니다. 큐는 비동기적으로 처리할 작업의 백로그를 만드는 데 일반적으로 사용됩니다.

###  <a name="azure-queue-storage-references"></a>Azure Queue storage 참조

- [Azure Portal](https://portal.azure.com)
- [Azure Queue storage 설명서](/azure/storage/queues/)
- [빠른 시작](/azure/storage/queues/storage-quickstart-queues-portal)
- [샘플](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
