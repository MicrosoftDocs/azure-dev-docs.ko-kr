---
author: yevster
ms.author: yebronsh
ms.date: 5/4/2020
ms.openlocfilehash: b15ebf1491dd494701dd5c18e0248e73bdd86f2c
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990205"
---
### <a name="inventory-configuration-sources-and-secrets"></a>인벤토리 구성 원본 및 비밀

#### <a name="inventory-passwords-and-secure-strings"></a>인벤토리 암호 및 보안 문자열

프로덕션 배포의 모든 속성 및 구성 파일과 모든 환경 변수에 비밀 문자열과 암호가 있는지 확인합니다. Spring Cloud 애플리케이션에서 이러한 문자열은 일반적으로 개별 서비스 또는 Spring Cloud Config 리포지토리의 *application.properties* 또는 *application.yml* 파일에서 찾을 수 있습니다.

[!INCLUDE [inventory-certificates-h4](inventory-certificates-h4.md)]

#### <a name="determine-whether-spring-cloud-vault-is-used"></a>Spring Cloud Vault를 사용하는지 여부 확인

Spring Cloud Vault를 사용하여 비밀을 저장하고 액세스하는 경우 지원되는 비밀 저장소(예: HashiCorp Vault 또는 CredHub)를 확인합니다. 그런 다음, 애플리케이션 코드에서 사용하는 모든 비밀을 확인합니다.

#### <a name="locate-the-configuration-server-source"></a>구성 서버 원본 찾기

애플리케이션에서 [Spring Cloud Config 서버](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server)를 사용하는 경우 구성이 저장된 위치를 확인합니다. 이 설정은 일반적으로 *bootstrap.yml* 또는 *bootstrap.properties* 파일, 때로는 *application.yml* 또는 *application.properties* 파일에서 찾을 수 있습니다. 설정은 다음 예제와 같습니다.

```properties
spring.cloud.config.server.git.uri: file://${user.home}/spring-cloud-config-repo
```

git이 Spring Cloud Config의 지원되는 데이터 저장소로 가장 일반적으로 사용되지만, 앞에서 설명한 대로 다른 가능한 백 엔드가 사용 중일 수 있습니다. [관계형 데이터베이스(JDBC)](https://cloud.spring.io/spring-cloud-config/reference/html/#_jdbc_backend), [SVN](https://cloud.spring.io/spring-cloud-config/reference/html/#_version_control_backend_filesystem_use), [로컬 파일 시스템](https://cloud.spring.io/spring-cloud-config/reference/html/#_file_system_backend)과 같은 다른 백 엔드에 대한 자세한 내용은 [Spring Cloud Config 설명서](https://cloud.spring.io/spring-cloud-config/reference/html/#_environment_repository)를 참조하세요.

> [!NOTE]
> 구성 서버 데이터가 온-프레미스(예: GitHub Enterprise)에 저장되는 경우 Git 리포지토리를 통해 Azure Spring Cloud에서 사용할 수 있도록 해야 합니다.
