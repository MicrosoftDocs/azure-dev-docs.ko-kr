---
title: JavaScript용 Azure SDK를 사용하여 로깅
description: JavaScript용 Azure SDK 클라이언트 라이브러리를 사용하여 로깅을 사용하도록 설정하는 방법을 알아봅니다.
ms.topic: article
ms.date: 07/23/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: fb4be05552a5ed1ea239776985f4f35ca6e900fa
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88218843"
---
# <a name="logging-with-the-azure-sdk-for-javascript"></a>JavaScript용 Azure SDK를 사용하여 로깅

JavaScript용 Azure SDK 코어 라이브러리에는 로깅을 애플리케이션에 쉽게 사용하도록 설정할 수 있는 로거 라이브러리가 포함되어 있습니다. 

[NPM 패키지](https://www.npmjs.com/package/@azure/logger) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger)

## <a name="enable-logging"></a>로깅 사용

단일 환경 변수를 사용하여 전체 애플리케이션에서 로깅을 사용하도록 설정하거나, 애플리케이션의 한 부분에 대한 로깅을 동적으로 구성할 수 있습니다. 이 문서에서는 로거 패키지에 대한 주요 개념 및 다음 방법으로 로깅을 사용하도록 설정하는 방법에 대해 설명합니다.

- `AZURE_LOG_LEVEL` 환경 변수를 설정합니다.
- 로거 라이브러리에서 가져온 `setLogLevel`을 호출합니다.
- 특정 로거에 대한 `enable()`을 호출합니다.

> [!NOTE]
> 이 문서는 최신 버전의 Azure SDK를 사용하는 클라이언트 라이브러리에 적용됩니다. 라이브러리가 지원되는지 확인하려면 [Azure SDK 최신 릴리스](https://azure.github.io/azure-sdk/releases/latest/index.html#javascript) 목록을 참조하세요. 애플리케이션에서 이전 버전의 Azure SDK 클라이언트 라이브러리를 사용하는 경우 해당 서비스 설명서의 특정 지침을 참조하세요.

## <a name="log-levels"></a>로그 수준

`@azure/logger` 패키지는 다음과 같이 가장 높은 자세한 정보 표시에서 가장 낮은 자세한 정보 표시로 순서대로 지정된 로그 수준을 지원합니다.

- verbose
- 정보
- warning
- error

프로그래밍 방식 또는 `AZURE_LOG_LEVEL` 환경 변수를 통해 로그 수준이 설정되면 선택한 로그 수준 이하의 로그 수준으로 기록된 모든 로그를 내보냅니다. 예를 들어 로그 수준을 `warning`으로 설정하면 `warning` 또는 `error` 수준의 모든 로그를 내보냅니다.

## <a name="install-the-logger-package"></a>로거 패키지 설치

JavaScript용 Azure SDK 로거 라이브러리는 [npm](https://www.npmjs.com/) 패키지로 제공됩니다. npm을 사용하여 `@azure/logger` 패키지를 설치합니다.

```cmd
npm install @azure/logger
```

## <a name="set-the-logging-environment-variable"></a>로깅 환경 변수 설정

단일 `AZURE_LOG_LEVEL` 환경 변수를 사용하여 애플리케이션 전체에서 로깅을 사용하도록 설정할 수 있습니다. 로그는 stderr로 출력됩니다. 환경 변수를 설정한 후에는 애플리케이션을 다시 시작하여 로그 생성을 시작해야 합니다.

로그 수준을 verbose로 설정하는 bash 예제:

```bash
export AZURE_LOG_LEVEL="verbose"
```

PowerShell을 사용하는 예제:

```powershell
$env:AZURE_LOG_LEVEL="verbose"
```

CMD를 사용하는 예제:

```cmd
set AZURE_LOG_LEVEL="verbose"
```

## <a name="configure-dynamic-logging"></a>동적 로깅 구성

JavaScript용 Azure SDK를 사용하면 필요에 따라 또는 특정 클라이언트 라이브러리에 대해 로깅을 동적으로 사용하도록 설정할 수 있습니다. 이는 사용자 지정 로깅 구현을 일부 애플리케이션 구성 요소에 사용하거나 임시 로그를 디버깅에 사용하려는 개발자에게 적합합니다.

 `@azure/logger`  모듈을 사용하여 코드에서 로그 수준을 설정할 수 있습니다.

```js
import { setLogLevel } from "@azure/logger";

setLogLevel("verbose");
```

특정 로그 채널을 사용하도록 설정하려면 로그를 내보내려는 패키지에서 `logger`를 가져옵니다. 다음 예제에서는 info 로깅만 Event Hubs에 사용하도록 설정합니다.

```js
import { logger } from "@azure/event-hubs";

logger.info.enable();
```

## <a name="redirect-log-output"></a>로그 출력 리디렉션

로그 메시지를 직접 처리하려면 `AzureLogger` 또는 클라이언트 라이브러리에서 가져온 로거에서 `log` 메서드를 다시 할당합니다.

로그 메시지를 stderr로 리디렉션하는 예제:

```js
import { AzureLogger } from "@azure/logger";

AzureLogger.log = msg => console.error(msg);
```

Azure EventHub에서 info 로그 메시지만 리디렉션하는 예제:

```js
import { logger } from "@azure/event-hubs";
logger.info.log = msg => console.error(msg);
```

## <a name="next-steps"></a>다음 단계

- [Azure App Service에서 앱에 대한 진단 로깅 사용](/azure/app-service/troubleshoot-diagnostic-logs)
- [Azure 보안 로깅 및 감사 옵션 검토](/azure/security/fundamentals/log-audit)
- [Azure 플랫폼 로그를 사용하는 방법 알아보기](/azure/azure-monitor/platform/platform-logs-overview)