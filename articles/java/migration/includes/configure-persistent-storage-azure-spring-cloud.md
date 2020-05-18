---
author: yevster
ms.author: yebronsh
ms.date: 4/15/2020
ms.openlocfilehash: 6fdf908458eec428f4e01d1b5f20fcbf4e039dbe
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990225"
---
### <a name="configure-persistent-storage"></a>영구 스토리지 구성

애플리케이션의 일부에서 로컬 파일 시스템을 읽거나 쓰는 경우 로컬 파일 시스템을 바꾸도록 영구 스토리지를 구성해야 합니다. 자세한 내용은 [Azure Spring Cloud에서 영구 스토리지 사용](/azure/spring-cloud/spring-cloud-howto-persistent-storage)을 참조하세요.

임시 파일은 `/tmp` 디렉터리에 써야 합니다. OS 독립성을 위해 `System.getProperty("java.io.tmpdir")`를 사용하여 이 디렉터리를 가져올 수 있습니다. 또한 [`java.nio.Files::createTempFile`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/nio/file/Files.html#createTempFile(java.lang.String,java.lang.String,java.nio.file.attribute.FileAttribute...))을 사용하여 임시 파일을 만들 수도 있습니다.
