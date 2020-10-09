---
title: Azure를 사용하여 클라우드 개발 - Azure란?
description: Microsoft Azure에서 클라우드 애플리케이션을 개발하는 방법에 대한 개요이며, 데이터 센터, 서비스 및 리소스의 관계부터 설명합니다.
ms.date: 10/06/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: f2bf8c0c8b35881c31fcf88c0faf38669cb261b3
ms.sourcegitcommit: 29b161c450479e5d264473482d31e8d3bf29c7c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91764528"
---
# <a name="cloud-development-on-azure"></a>Azure에서 클라우드 개발

Python 개발자이며 Microsoft Azure용 클라우드 애플리케이션을 개발할 준비가 되었습니다. 세 가지 문서로 구성된 이번 시리즈에서는 길고 생산적인 경력을 준비하는 데 도움이 되도록 Azure에서 클라우드를 개발하는 기본 환경에 대해 안내합니다.

## <a name="what-is-azure-data-centers-services-and-resources"></a>Azure란? 데이터 센터, 서비스 및 리소스

Microsoft CEO Satya Nadella는 종종 Azure를 "세계의 컴퓨터"라고 말합니다. 잘 알고 있듯이, 컴퓨터는 운영 체제로 관리되는 하드웨어의 모음이며, 시스템의 컴퓨팅 기능을 얼마든지 많은 작업에 적용할 수 있도록 소프트웨어를 빌드하는 플랫폼을 제공합니다. (그래서 소프트웨어를 설명할 때 "애플리케이션"이라는 단어를 사용하는 것입니다.)

Azure의 경우, 컴퓨터의 하드웨어는 단일 머신이 아니라 [전 세계 수십 곳의 대규모 데이터 센터](https://azure.microsoft.com/global-infrastructure/regions/)에 있는 가상화된 서버 컴퓨터의 방대한 풀입니다. Azure "운영 체제"는 애플리케이션의 필요에 따라 리소스 풀의 다양한 부분을 동적으로 할당되고 할당을 해제하는 *서비스*로 이루어집니다. 각 할당 &mdash;즉, 컴퓨팅 성능(CPU 코어 및 메모리), 스토리지, 데이터베이스, 네트워크 등&mdash;을 *리소스*라고 합니다. 그리고 각각의 개별적인 리소스에는 고유한 *개체 식별자*(GUID)와 고유 URL이 할당됩니다.

![데이터 센터에서 Azure 서비스에 이르는 리소스를 할당하는 Azure의 레이어](media/cloud-development/azure-layers.png)

리소스는 클라우드 애플리케이션의 구성 요소입니다. 따라서 클라우드 개발 프로세스는 애플리케이션의 다양한 부분을 배포할 수 있는 적절한 환경을 만드는 것부터 시작됩니다. 간단히 말해, 가상 머신, 데이터베이스, 스토리지 계정, 컨테이너 레지스트리, 컨테이너 오케스트레이터, 웹 호스트, 가상 네트워크, AI 및 분석 엔진 등과 같은 적절한 대상 리소스를 할당하고 구성&mdash;프로비저닝&mdash;할 때까지는 Azure에 코드나 데이터를 배포할 수 없습니다.

그러면 애플리케이션을 위한 환경을 만드는 프로세스에 관련 서비스와 리소스 유형을 식별한 다음, 해당 리소스를 프로비저닝(이 시점에서 Azure에서 임대가 시작됨)하는 과정이 포함됩니다. 프로비저닝 프로세스는 기본적으로 애플리케이션을 배포할 컴퓨팅 시스템을 구성하는 방법입니다.

원하는 대로 쓸 수 있는 리소스 유형은 가상 머신과 같은 기본 "인프라" 리소스(배포한 소프트웨어에 대한 모든 권한과 책임이 사용자에게 있음)에서 고급 "플랫폼" 서비스(데이터와 애플리케이션 코드에만 신경을 쓰면 되는 고급 관리형 환경이 제공됨)에 이르기까지 수백 가지가 있습니다.

애플리케이션에 적합한 서비스를 찾고 상대적으로 비용을 분산하는 것이 어려울 수 있지만 바로 이 부분이 클라우드 개발의 창의적인 즐거움 중 하나입니다. 다양한 옵션을 이해하려면 [Azure 개발자 가이드](/azure/guides/developer/azure-developer-guide)를 살펴보세요. 여기서는 이러한 모든 서비스와 리소스를 실제로 사용하는 방법을 알아보겠습니다.

> [!NOTE]
> 아마 "IaaS"(infrastructure-as-a-service), "PaaS" (platform-as-a-service) 등의 용어를 많이 봤고 이제 지겨울 수도 있습니다. "as-a-service"라는 부분은 일반적으로 데이터 센터에 물리적으로 직접 액세스할 수 없다는 사실을 반영합니다. 대신 Azure Portal, Azure CLI 또는 Azure REST API와 같은 도구를 사용하여 "인프라" 리소스, "플랫폼" 리소스 등을 프로비저닝할 수 있습니다. 일종의 "서비스"인 Azure는 귀하의 요청을 항상 기다리고 있습니다.
>
> 이 개발자 센터에는 IaaS, PaaS 등의 용어가 별로 없습니다. "as-a-service"는 클라우드에 처음부터 내재되어 있는 개념이기 때문입니다.

> [!NOTE]
> "하이브리드 클라우드"는 개인용 컴퓨터와 Azure와 같은 클라우드 리소스가 있는 데이터 센터를 결합한 것이며, 이전 논의에서 언급하지 않았던 특유한 고려 사항이 있습니다. 또한, 이 문서에서 새로운 애플리케이션 개발 시나리오를 가정합니다. 기존 온-프레미스 애플리케이션 아키텍처를 재구성하고 마이그레이션하는 시나리오는 여기에서 다루지 않습니다.

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [리소스 프로비저닝, 액세스 및 관리 >>>](cloud-development-provisioning.md)
