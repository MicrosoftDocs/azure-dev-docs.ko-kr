---
author: yevster
ms.author: yebronsh
ms.date: 1/20/2020
ms.openlocfilehash: 4b5b73eee66c4a5c9eb28b79804e0dc610f639d6
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "81671629"
---
### <a name="inventory-external-resources"></a>인벤토리 외부 리소스

데이터 원본, JMS 메시지 브로커 등과 같은 외부 리소스는 JNDI(Java 명명 및 디렉터리 인터페이스)를 통해 삽입됩니다. 이러한 리소스 중 일부는 마이그레이션 또는 재구성이 필요할 수 있습니다.

#### <a name="inside-your-application"></a>애플리케이션 내부

*META-INF/context.xml* 파일을 검사합니다. `<Context>` 요소 내에서 `<Resource>` 요소를 찾습니다.

#### <a name="on-the-application-servers"></a>애플리케이션 서버에서

*$CATALINA_BASE/conf/context.xml* 및 *$CATALINA_BASE/conf/server.xml* 파일과 *$CATALINA_BASE/conf/[engine-name]/[host-name]* 디렉터리에 있는 *.xml* 파일을 검사합니다.

*context.xml* 파일에서 JNDI 리소스는 최상위 `<Context>` 요소 내의 `<Resource>` 요소로 설명됩니다.

*server.xml* 파일에서 JNDI 리소스는 `<GlobalNamingResources>` 요소 내의 `<Resource>` 요소로 설명됩니다.

#### <a name="datasources"></a>Datasources

데이터 원본은 `type` 특성이 `javax.sql.DataSource`로 설정된 JNDI 리소스입니다. 각 데이터 원본에 대해 다음 정보를 문서화합니다.

* 데이터 원본 이름은 무엇인가요?
* 연결 풀 구성이란?
* JDBC 드라이버 JAR 파일은 어디에서 찾을 수 있나요?

자세한 내용은 Tomcat 설명서의 [JNDI 데이터 원본 구성 방법](https://tomcat.apache.org/tomcat-9.0-doc/jndi-datasource-examples-howto.html)을 참조하세요.

#### <a name="all-other-external-resources"></a>기타 모든 외부 리소스

이 가이드에서 가능한 모든 외부 종속성을 문서화하는 것은 불가능합니다. 마이그레이션 후 애플리케이션의 모든 외부 종속성을 충족할 수 있는지 확인하는 것은 팀의 책임입니다.
