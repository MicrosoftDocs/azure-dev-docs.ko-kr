---
title: 포함 파일 2
description: 포함 파일 2
ms.topic: include
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: 05f3e5fb847e7589e41d041736c986dce0bc5b29
ms.sourcegitcommit: 5541f993c01ce356e1b0eaa8f95aea9051c3c21e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93278700"
---
이 단계에서는 Deno의 기본 제공 웹 서버를 사용하여 간단한 Deno API를 만듭니다. 그런 다음, 애플리케이션을 로컬로 실행합니다.

1. 터미널 또는 명령 프롬프트에서 앱 폴더를 만들 위치로 이동하고 `deno-demo`라는 새 폴더를 만듭니다.

1. `demo.ts`라는 새 파일을 만듭니다.
1. Deno는 URL에서 코드를 직접 실행하는 것을 허용합니다. "Hello World"를 사용하여 모든 요청에 응답하는 HTTP 서버를 작성합니다. 다음 코드를 사용합니다.

    ```typescript
    import { serve } from "https://deno.land/std@0.54.0/http/server.ts"
    const handler = serve({ port: 80 })

    console.log("Serving at 80")

    for await (const req of handler) {
     req.respond({ body: "Hello World!\n" })
    }
    ```

1. 다음 스크립트를 실행하여 앱을 실행합니다.

    ```bash
    deno run --allow-net ./demo.ts
    ```

1. 브라우저에서 `http://localhost:80`을 열어 앱을 테스트합니다. 사이트가 다음과 같이 표시됩니다.

    ![데모 서버 실행](../../media/deploy-azure/deno-hello-world.png)

    `deno run --allow-net https://gist.githubusercontent.com/khaosdoctor/cd2bbb28e682feb8d20a7aba47fc1e17/raw/92de998fd11f2a24ae40bbcb84f5262cfe9389b2/deno-demo.ts`를 입력하여 이 코드를 실행할 수도 있습니다.

1. 터미널에서 **Ctrl**+**C** 를 눌러 서버를 중지합니다.
