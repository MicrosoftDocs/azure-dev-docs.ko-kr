---
title: Java용 Azure SDK의 로깅 구성
description: 로깅과 관련된 Java용 Azure SDK의 개념 소개
author: srnagar
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: srnagar
ms.openlocfilehash: bbb9733a21eabf5d13a698a0785908dcd997af45
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528631"
---
# <a name="configure-logging-in-the-azure-sdk-for-java"></a>Java용 Azure SDK의 로깅 구성

이 문서에서는 Java용 Azure SDK를 사용하는 애플리케이션에 로깅을 사용하도록 설정하는 방법에 대한 개요를 제공합니다. Java용 Azure 클라이언트 라이브러리에는 두 가지 로깅 옵션이 있습니다.

* 임시 디버깅용으로 기본 제공되는 로깅 프레임워크
* [SLF4J](https://www.slf4j.org/) 인터페이스를 사용한 로깅 지원

Java 에코시스템에서 잘 알려져 있으며 문서화가 잘 되어 있는 SLF4J를 사용하는 것이 좋습니다. 자세한 내용은 [SLF4J 사용자 설명서](https://www.slf4j.org/manual.html)를 참조하세요.

이 문서는 여러 주요 Java 로깅 프레임워크를 다루는 다른 문서에 대한 링크를 제공합니다. 이러한 다른 문서는 구성 예제를 제공하고, Azure 클라이언트 라이브러리에서 로깅 프레임워크를 사용하는 방법을 설명합니다.

사용하는 로깅 구성에 관계없이 Java용 Azure 클라이언트 라이브러리의 모든 로깅 출력이 azure-core `ClientLogger` 추상화를 통해 라우팅되도록 두 경우 모두 동일한 로그 출력을 사용할 수 있습니다.

이 문서의 나머지 부분에서는 사용 가능한 모든 로깅 옵션의 구성에 대해 자세히 설명합니다.

## <a name="default-logger-for-temporary-debugging"></a>기본 로거(임시 디버깅용)

앞서 언급했듯이, 모든 Azure 클라이언트 라이브러리는 로깅에 SLF4J를 사용하지만 Java용 Azure 클라이언트 라이브러리에는 대체 기본 로거가 기본 제공됩니다. 이 기본 로거는 애플리케이션이 배포되었고 로깅이 필요한 경우에 제공되지만 SLF4J 로거를 포함하여 애플리케이션을 다시 배포할 수는 없습니다. 이 로거를 사용하도록 설정하려면 먼저 SLF4J 로거가 없음을 확인한 다음(이 로거가 우선하므로), `AZURE_LOG_LEVEL` 환경 변수를 설정해야 합니다. 다음 표에서는 이 환경 변수에 허용되는 값을 보여 줍니다.

| 로그 수준              | 허용되는 환경 변수 값     |
|------------------------|-----------------------------------------|
| VERBOSE                | "verbose", "debug"                      |
| INFORMATIONAL          | "info", "information", "informational"  |
| WARNING                | "warn", "warning"                       |
| 오류                  | "err", "error"                          |

환경 변수를 설정한 후에는 환경 변수가 적용될 수 있도록 애플리케이션을 다시 시작합니다. 이 로거는 콘솔에 로깅하며, 롤오버 및 파일에 로깅 같은 SLF4J 구현의 고급 사용자 지정 기능을 제공하지 않습니다. 로깅을 다시 해제하려면 환경 변수를 제거하고 애플리케이션을 다시 시작하면 됩니다.

## <a name="slf4j-logging"></a>SLF4J 로깅

기본적으로 SLF4J 지원 로깅 프레임워크를 사용하여 로깅을 구성해야 합니다. 먼저 관련 SLF4J 로깅 구현을 프로젝트의 종속성으로 포함합니다. 자세한 내용은 SLF4J 사용자 설명서의 [로깅에 대한 프로젝트 종속성 선언](http://www.slf4j.org/manual.html#projectDep)을 참조하세요. 다음으로, 사용자 환경에서 필요에 따라 작동하도록 로거를 구성합니다(예: 로그 수준 설정, 로깅할 클래스와 로깅하지 않을 클래스 구성 등). 일부 예는 아래 링크를 통해 제공되지만, 자세한 내용은 선택한 로깅 프레임워크에 대한 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계

Java용 Azure SDK의 로깅 작동 방식을 살펴보았으므로 이제 아래 링크를 검토하여 SLF4J 및 Java 클라이언트 라이브러리로 작업할 때 가장 많이 사용되는 Java 로깅 프레임워크 중 몇 개를 구성하는 방법에 대한 지침을 살펴보는 것이 좋습니다.

* [java.util.logging](logging-jul.md)
* [Logback](logging-logback.md)
* [Log4J](logging-log4j.md)
