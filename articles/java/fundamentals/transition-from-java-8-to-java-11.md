---
title: Java 8에서 Java 11로 전환
titleSuffix: Azure
description: Java 8에서 Java 11로의 전환을 관리하는 방법에 대한 가이드입니다.
author: dsgrieve
manager: maverbur
tags: java
ms.service: azure
ms.devlang: java
ms.topic: article
ms.date: 11/19/2019
ms.author: dagrieve
ms.custom: devx-track-java
ms.openlocfilehash: 6aa930bb4ee5c6a5019e33b3d8075765b92f0bfc
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379787"
---
# <a name="transition-from-java-8-to-java-11"></a>Java 8에서 Java 11로 전환

코드를 Java 8에서 Java 11로 전환하는 단일 크기 솔루션은 없습니다.
중요한 애플리케이션의 경우 Java 8에서 Java 11로 전환하는 작업의 규모가 상당히 클 수 있습니다. 잠재적 이슈로는 제거된 API, 사용되지 않는 패키지, 내부 API 사용, 클래스 로더 변경, 가비지 수집 변경 등이 포함됩니다. 

일반적으로 이 방법은 다시 컴파일하지 않고 Java 11에서 실행하거나 먼저 JDK 11을 사용하여 컴파일하는 것입니다. 애플리케이션을 최대한 신속하게 실행하는 것이 목표라면 Java 11에서 실행을 시도하는 것이 가장 좋은 방법인 경우가 자주 있습니다. 라이브러리의 목표는 JDK 11에서 컴파일하고 테스트를 마친 아티팩트를 게시하는 것입니다.

Java 11로 전환할 가치가 충분히 있습니다. Java 8 이후로 새 기능이 추가되고 기능이 향상되었습니다. 이러한 새 기능과 향상된 기능은 시작, 성능, 메모리 사용을 개선하며 컨테이너와의 보다 원활한 통합을 제공합니다. 그리고 개발자 생산성을 개선할 수 있도록 새 API가 추가되고 기존 API가 수정되었습니다. 

이 문서에서는 코드를 검사하는 도구를 다룹니다. 발생할 수 있는 이슈와 이슈 해결을 위한 권장 사항도 다룹니다. [Oracle JDK 마이그레이션 가이드](https://docs.oracle.com/en/java/javase/11/migrate/index.html) 같은 다른 가이드도 살펴보아야 합니다. 기존 코드를 [모듈식](http://openjdk.java.net/projects/jigsaw)으로 만드는 방법은 여기서 다루지 않습니다.  


## <a name="the-toolbox"></a>도구 상자

Java 11에는 잠재적인 이슈를 해결하는 데 유용하게 사용할 수 있는 두 가지 도구인 *jdeprscan* 및 *jdeps*가 포함되어 있습니다. 두 도구를 기존 클래스 또는 jar 파일에 대해 실행할 수 있습니다. 다시 컴파일하지 않아도 전환 작업을 평가할 수 있습니다. 

[jdeprscan](https://docs.oracle.com/en/java/javase/11/tools/jdeprscan.html)은 더 이상 사용되지 않거나 제거된 API 사용을 찾습니다.
사용되지 않는 API를 사용하는 것은 차단 문제가 아니지만, 살펴볼 만한 이슈입니다. 업데이트된 jar 파일이 있나요? 더 이상 사용되지 않는 API 사용 문제를 해결하기 위해 이슈를 기록해야 하나요? 제거된 API 사용은 차단 문제이므로 Java 11에서 실행을 시도하기 전에 해결해야 합니다.


[jdeps](https://docs.oracle.com/en/java/javase/11/tools/jdeps.html)는 Java 클래스 종속성 분석기입니다. `--jdk-internals` 옵션과 함께 사용할 경우 *jdeps*는 어떤 클래스가 어떤 내부 API에 종속되는지 알려줍니다. Java 11에서 내부 API를 계속 사용해도 되지만, 사용 방법을 바꾸는 것을 최우선으로 해야 합니다. OpenJDK wiki 페이지 [Java 종속성 분석 도구](https://wiki.openjdk.java.net/display/JDK8/Java+Dependency+Analysis+Tool)에는 일반적으로 사용되는 일부 JDK 내부 API를 교체하는 것이 좋습니다. 

Gradle과 Maven에 모두 사용할 수 있는 *jdeps* 및 *jdeprscan*플러그 인이 있습니다. 빌드 스크립트에 이러한 도구를 추가하는 것이 좋습니다. 

> [!div class="mx-tdBreakAll"]
> |도구|Gradle 플러그 인|Maven 플러그 인|
> |-|-|-|
> |jdeps|[jdeps-gradle-plugin](https://github.com/kordamp/jdeps-gradle-plugin)|[Apache Maven JDeps 플러그 인](https://maven.apache.org/plugins/maven-jdeps-plugin/index.html)|
> |jdeprscan|[jdeprscan-gradle-plugin](https://github.com/kordamp/jdeprscan-gradle-plugin)|[Apache Maven JDeprScan 플러그 인](https://maven.apache.org/plugins/maven-jdeprscan-plugin/index.html)|

Java 컴파일러 *javac* 자체가 도구 상자의 또 다른 도구입니다. *jdeprscan* 및 *jdeps*에서 받는 경고와 오류는 컴파일러에서 옵니다.  *jdeprscan* 및 *jdeps*를 사용하면 타사 라이브러리를 비롯한 기존 jar 및 클래스 파일을 대상으로 이러한 도구를 실행할 수 있다는 장점이 있습니다.

*jdeprscan* 및 *jdeps*는 캡슐화된 API에 액세스하기 위한 리플렉션 사용에 대해 경고할 수 없습니다. 리플렉션 액세스는 런타임에 검사됩니다. 궁극적으로는 명확하게 알 수 있도록 Java 11에서 코드를 실행해야 합니다.

### <a name="using-jdeprscan"></a>jdeprscan 사용

[jdeprscan](https://docs.oracle.com/en/java/javase/11/tools/jdeprscan.html)을 사용하는 가장 쉬운 방법은 기존 빌드에서 jar 파일을 제공하는 것입니다. 컴파일러 출력 디렉터리 또는 개별 클래스 이름과 같은 디렉터리를 지정할 수도 있습니다. `--release 11` 옵션을 사용하면 사용되지 않는 API의 가장 완전한 목록을 얻을 수 있습니다. 더 이상 사용되지 않는 API가 뒤로 가도록 우선 순위를 지정하려면 설정을 다시 `--release 8`로 되돌립니다. Java 8에서 더 이상 사용되지 않는 API는 보다 최근에 사용이 중단된 API보다 일찍 제거될 가능성이 높습니다. 

```console
jdeprscan --release 11 my-application.jar
```

*jdeprscan* 도구는 종속 클래스를 해결하는 데 문제가 있으면 오류 메시지를 생성합니다.
`error: cannot find class org/apache/logging/log4j/Logger`)을 입력합니다. `--class-path`에 종속 클래스를 추가하거나 애플리케이션 클래스 경로를 사용하는 것이 좋지만, 없어도 이 도구는 스캔을 계속합니다.
인수는 *&#8209;&#8209;class&#8209;path*입니다. class-path 인수의 다른 변형은 작동하지 않습니다.

```console
jdeprscan --release 11 --class-path log4j-api-2.13.0.jar my-application.jar
error: cannot find class sun/misc/BASE64Encoder
class com/company/Util uses deprecated method java/lang/Double::<init>(D)V
```
위의 출력을 보면 `com.company.Util` 클래스가 `java.lang.Double` 클래스의 더 이상 사용되지 않는 생성자를 호출하고 있다는 것을 알 수 있습니다. javadoc는 더 이상 사용되지 않는 API 대신 사용할 API를 추천해 줍니다. 제거된 API이므로 어떤 방법으로도 `error: cannot find class sun/misc/BASE64Encoder` 오류가 해결되지 않습니다. Java 8부터 `java.util.Base64`를 사용해야 합니다. 

`jdeprscan --release 11 --list`를 실행하여 Java 8 이후로 더 이상 사용되지 않는 API에 대해 알아보세요. 제거된 API 목록을 가져오려면 `jdeprscan --release 11 --list --for-removal`을 실행합니다.

### <a name="using-jdeps"></a>jdeps 사용

[jdeps](https://docs.oracle.com/en/java/javase/11/tools/jdeps.html)와 `--jdk-internals` 옵션을 사용하여 JDK 내부 API에 대한 종속성을 찾을 수 있습니다. *log4j-core-2.13.0.jar* 파일이 [다중 릴리스 jar 파일](https://docs.oracle.com/en/java/javase/11/docs/specs/jar/jar.html#multi-release-jar-files)이므로, 이 예제에는 `--multi-release 11` 명령줄 옵션이 필요합니다. 이 옵션을 사용하지 않으면 *jdeps*는 다중 릴리스 jar 파일을 찾으면 오류를 표시합니다. 이 옵션은 검사할 클래스 파일 버전을 지정합니다. 

```console
jdeps --jdk-internals --multi-release 11 --class-path log4j-core-2.13.0.jar my-application.jar
Util.class -> JDK removed internal API
Util.class -> jdk.base
Util.class -> jdk.unsupported
   com.company.Util        -> sun.misc.BASE64Encoder        JDK internal API (JDK removed internal API)
   com.company.Util        -> sun.misc.Unsafe               JDK internal API (jdk.unsupported)
   com.company.Util        -> sun.nio.ch.Util               JDK internal API (java.base)

Warning: JDK internal APIs are unsupported and private to JDK implementation that are
subject to be removed or changed incompatibly and could break your application.
Please modify your code to eliminate dependence on any JDK internal APIs.
For the most recent update on JDK internal API replacements, please check:
https://wiki.openjdk.java.net/display/JDK8/Java+Dependency+Analysis+Tool

JDK Internal API                         Suggested Replacement
----------------                         ---------------------
sun.misc.BASE64Encoder                   Use java.util.Base64 @since 1.8
sun.misc.Unsafe                          See http://openjdk.java.net/jeps/260   

```

위의 출력을 보면 JDK 내부 API의 사용을 제거하는 것에 대한 유용한 조언을 얻을 수 있습니다. 가능한 경우 대체 API를 사용하라는 내용이 표시됩니다. 패키지가 캡슐화되는 모듈의 이름은 괄호 안에 표시됩니다. 명시적으로 [캡슐화를 해제](https://docs.oracle.com/javase/9/migrate/toc.htm#JSMIG-GUID-2F61F3A9-0979-46A4-8B49-325BA0EE8B66)해야 하는 경우 모듈 이름에 `--add-exports` 또는 `--add-opens`를 사용할 수 있습니다. 

*sun.misc.BASE64Encoder* 또는 *sun.misc.BASE64Decoder*를 사용하면 Java 11에서 *java.lang.NoClassDefFoundError*가 발생합니다. 이러한 API를 사용하는 코드는 *java.util.Base64*를 사용하도록 수정해야 합니다. 

*jdk.unsupported* 모듈에서 오는 API 사용을 제거하려고 시도해 보세요. 이 모듈의 API는 추천 대체 항목으로 [JEP(JDK Enhancement Proposal) 260](http://openjdk.java.net/jeps/260)을 참조합니다.
간단히 말해서, JEP 260은 대체 API를 사용할 수 있을 때까지 내부 API 사용을 지원한다는 의미입니다. 코드에서 JDK 내부 API를 사용하더라도, 적어도 당분간은 계속 실행됩니다. 일부 내부 API의 대체 항목을 가리키는 JEP 260을 꼭 살펴보세요. 
예를 들어 일부 *sun.misc.Unsafe* API 대신 [변수 핸들](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/invoke/VarHandle.html)을 사용할 수 있습니다. 

*jdeps*는 JDK 내부 사용을 검사하는 일 외에도 다른 일을 수행할 수 있습니다. 종속성을 분석하고 모듈 정보 파일을 생성하는 데 유용한 도구입니다. 자세한 내용은 [설명서](https://docs.oracle.com/en/java/javase/11/tools/jdeps.html)를 살펴보세요.

### <a name="using-javac"></a>javac 사용

JDK 11을 사용하여 컴파일하려면 스크립트, 도구, 테스트 프레임워크 및 포함된 라이브러리를 빌드하도록 업데이트가 필요합니다. *javac*의 `-Xlint:unchecked` 옵션을 사용하여 JDK 내부 API 및 기타 경고 사용에 대한 세부 정보를 가져오세요. `--add-opens` 또는 `--add-reads`를 사용하여 캡슐화된 패키지를 컴파일러에 노출해야 할 수도 있습니다([JEP 261](http://openjdk.java.net/jeps/261) 참조). 

라이브러리는 패키징을 [다중 릴리스 jar 파일](https://docs.oracle.com/en/java/javase/11/docs/specs/jar/jar.html#multi-release-jar-files)로 간주할 수 있습니다. 다중 릴리스 jar 파일을 사용하면 동일한 jar 파일에서 Java 8 및 Java 11 런타임을 모두 지원할 수 있습니다. 하지만 빌드가 더 복잡해집니다. 다중 릴리스 jar 파일을 빌드하는 방법은 이 문서에서 다루지 않습니다. 

## <a name="running-on-java-11"></a>Java 11에서 실행

대부분의 애플리케이션은 수정하지 않고 Java 11에서 실행해야 합니다. 가장 먼저 할 일은 코드를 다시 컴파일하지 않고 Java 11에서 실행해 보는 것입니다. 실행 중에 어떤 경고와 오류가 발생하는지 확인하기 위해 그냥 실행하는 것입니다. 이 방법을 사용하면  
꼭 해야 하는 최소한의 작업에 집중하여 보다 빠르게 Java 11에서 애플리케이션을 실행할 수 있습니다. 

발생하는 대부분의 문제는 코드를 다시 컴파일할 필요 없이 해결할 수 있습니다.
코드에서 이슈를 해결해야 하는 경우 수정하고 JDK 8에서 계속 컴파일합니다. 가능하면 JDK 11로 *컴파일*하기 전에 `java` 버전 11에서 애플리케이션을 *실행*하세요. 

### <a name="check-command-line-options"></a>명령줄 옵션 확인

Java 11에서 실행하기 전에, 명령줄 옵션을 빠르게 검사합니다. 
[제거된 옵션](#unrecognized-options)을 사용하면 JVM(Java Virtual Machine)이 종료됩니다. Java 8부터 GC 로깅 옵션이 완전히 바뀌었기 때문에 GC 로깅 옵션을 사용하는 경우에는 이 검사가 특히 중요합니다. [JaCoLine](https://jacoline.dev/about) 도구는 명령줄 옵션과 관련된 문제를 찾는 데 유용한 도구입니다. 

### <a name="check-third-party-libraries"></a>타사 라이브러리 검사

사용자가 제어할 수 없는 타사 라이브러리는 잠재적 문제의 원인입니다. 타사 라이브러리를 최신 버전으로 미리 업데이트할 수 있습니다. 또는 애플리케이션을 실행하고 그 결과를 본 후 필요한 라이브러리만 업데이트할 수도 있습니다. 모든 라이브러리를 최신 버전으로 업데이트하는 경우 애플리케이션에 오류가 있을 때 근본 원인을 찾기가 더 어려워질 수 있다는 문제가 있습니다. 업데이트된 라이브러리 때문에 오류가 발생했을까요? 아니면 런타임 변경으로 인해 오류가 발생했을까요? 필요한 것만 업데이트하는 경우 문제 해결을 위한 작업을 수차례 반복해야 할 수도 있다는 문제가 있습니다.

변경을 최소화하고 타사 라이브러리를 [별도의 작업](#next-steps)으로 업데이트하는 것이 좋습니다. 타사 라이브러리를 업데이트할 때 대부분은 Java 11과 호환되는 가장 좋은 최신 버전을 원합니다. 현재 사용 중인 버전이 얼마나 오래된 것인가에 따라, 좀 더 신중하게 접근하여 첫 번째 Java 9+ 호환 버전으로 업그레이드하는 것이 좋을 수 있습니다. 

릴리스 정보를 검토하는 것 외에도, *jdeps* 및 *jdeprscan*을 사용하여 jar 파일을 평가할 수 있습니다. 또한 OpenJDK 품질 그룹은 OpenJDK 버전을 대상으로 여러 FOSS(Free Open Source Software) 프로젝트를 테스트한 상태를 보여주는 [Quality Outreach](https://wiki.openjdk.java.net/display/quality/Quality+Outreach) wiki 페이지를 운영하고 있습니다. 

### <a name="explicitly-set-garbage-collection"></a>명시적으로 가비지 수집 설정

병렬 GC(병렬 가비지 수집)는 Java 8의 기본 GC입니다. 애플리케이션에서 기본값을 사용하는 경우 `-XX:+UseParallelGC` 명령줄 옵션을 사용하여 명시적으로 GC를 설정해야 합니다.
Java 9에서는 기본값이 G1GC(가비지 우선 가비지 수집기)로 변경되었습니다. Java 8과 Java 11에서 실행되는 애플리케이션을 공정하게 비교하려면 GC 설정이 동일해야 합니다. GC 설정 실험은 Java 11에서 애플리케이션의 유효성을 검사할 때까지 연기해야 합니다. 

### <a name="explicitly-set-default-options"></a>명시적으로 기본 옵션 설정

핫스폿 VM에서 실행 중인 경우 `-XX:+PrintCommandLineFlags` 명령줄 옵션을 설정하면 VM에서 설정한 옵션의 값, 특히 GC에서 설정한 기본값이 덤프됩니다.
Java 8에서 이 플래그를 사용하여 실행하고, 출력된 옵션은 Java 11에서 실행할 때 사용합니다. 대부분의 경우 기본값은 8~11입니다. 그러나 8의 설정을 사용하면 패리티가 보장됩니다.

`--illegal-access=warn` 명령줄 옵션을 설정하는 것이 좋습니다.
Java 11에서 리플렉션을 사용하여 JDK 내부 API에 액세스하면 [잘못된 리플렉션 액세스 경고](#warning-an-illegal-reflective-access-operation-has-occurred)가 발생합니다.
기본적으로 이 경고는 잘못된 첫 번째 액세스에 대해서만 발생합니다. `--illegal-access=warn`을 설정하면 잘못된 리플렉션 액세스*마다* 경고가 발생합니다. 옵션이 *경고*로 설정된 잘못된 사례를 더 많이 찾을 수 있습니다. 그러나 수많은 중복 경고가 발생합니다.  
애플리케이션이 Java 11에서 실행되면 `--illegal-access=deny`를 설정하여 Java 런타임의 향후 동작을 모방하세요. Java 16부터는 `--illegal-access=deny`가 기본값입니다. 

### <a name="classloader-cautions"></a>ClassLoader 주의 사항

Java 8에서는 시스템 클래스 로더를 `URLClassLoader`에 캐스팅할 수 있습니다. 일반적으로 이 작업은 런타임에 클래스를 클래스 경로에 삽입하려는 애플리케이션과 라이브러리에 의해 수행됩니다. Java 11에서 클래스 로더 계층 구조가 변경되었습니다. 애플리케이션 클래스 로더라고도 하는 시스템 클래스 로더는 이제 내부 클래스입니다. `URLClassLoader`에 캐스팅하면 런타임에 `ClassCastException`이 throw됩니다. Java 11에는 런타임에 클래스 경로를 동적으로 보강하는 API가 없지만 리플렉션을 통해 보강할 수 있으며, 내부 API 사용에 대한 명확한 주의 사항이 있습니다. 

Java 11에서는 부팅 클래스 로더가 핵심 모듈만 로드합니다. Null 부모를 사용하여 클래스 로더를 만들면 일부 플랫폼 클래스를 찾지 못할 수 있습니다. Java 11에서는 이러한 경우에 부모 클래스 로더로 `null` 대신 `ClassLoader.getPlatformClassLoader()`를 전달해야 합니다. 

### <a name="locale-data-changes"></a>로캘 데이터 변경

Java 11에서는 [JEP 252](http://openjdk.java.net/jeps/252)를 사용하여 로캘 데이터의 기본 소스가 유니코드 컨소시엄의 공통 로캘 데이터 리포지토리로 변경되었습니다. 이로 인해 지역화된 서식에 영향이 있을 수 있습니다. 필요한 경우 시스템 속성 `java.locale.providers=COMPAT,SPI`를 설정하여 Java 8 로캘 동작으로 되돌리세요. 

### <a name="potential-issues"></a>잠재적 이슈

다음은 발생할 수 있는 몇 가지 일반적인 이슈입니다. 링크를 따라 이동하여 이러한 이슈에 대해 자세히 알아보세요.

- [인식할 수 없는 VM 옵션](#unrecognized-options)
- [인식할 수 없는 옵션](#unrecognized-options)
- [VM 경고: 옵션 무시](#vm-warnings)
- [VM 경고: 더 이상 사용되지 않는 &lt;*option*&gt; 옵션](#vm-warnings)
- [경고: 잘못된 리플렉션 액세스 작업 발생](#warning-an-illegal-reflective-access-operation-has-occurred)
- [java.lang.reflect.InaccessibleObjectException](#javalangreflectinaccessibleobjectexception)
- [java.lang.NoClassDefFoundError](#javalangnoclassdeffounderror)
- [더 이상 지원되지 않는 -Xbootclasspath/p 옵션](#-xbootclasspathp-is-no-longer-a-supported-option)
- [java.lang.UnsupportedClassVersionError](#unsupportedclassversionerror)

#### <a name="unrecognized-options"></a>인식할 수 없는 옵션

명령줄 옵션이 제거되면 애플리케이션은 문제가 있는 옵션 이름 앞에 `Unrecognized option:` 또는 `Unrecognized VM option`을 출력합니다. 인식할 수 없는 옵션이 있으면 VM이 종료됩니다.
더 이상 사용되지 않지만 제거되지 않는 옵션은 [VM 경고](#vm-warnings)를 생성합니다.

일반적으로 제거된 옵션은 대체 옵션이 없으며 유일한 방법은 명령줄에서 옵션을 제거하는 것입니다. 가비지 수집 로깅 옵션은 예외입니다. GC 로깅은 Java 9에서 [통합 JVM 로깅 프레임워크](http://openjdk.java.net/jeps/158)를 사용하도록 [다시 구현되었습니다](http://openjdk.java.net/jeps/271). Java SE 11 도구 참조의 [JVM 통합 로깅 프레임워크를 사용하여 로깅 사용](https://docs.oracle.com/en/java/javase/11/tools/java.html#GUID-BE93ABDC-999C-4CB5-A88B-1994AAAC74D5) 섹션에서 "표 2-2 레거시 가비지 수집 로깅 플래그를 Xlog 구성에 매핑"을 참조하세요. 

#### <a name="vm-warnings"></a>VM 경고

더 이상 사용되지 않는 옵션을 사용하면 경고가 생성됩니다. 다른 옵션으로 대체되거나 더 이상 유용하지 않은 옵션은 사용이 중단됩니다. [제거된 옵션](#unrecognized-options)과 마찬가지로, 이러한 옵션은 명령줄에서 제거해야 합니다.
`VM Warning: Option <option> was deprecated` 경고는 옵션이 여전히 지원되지만 향후 지원이 제거될 수 있음을 의미합니다. 더 이상 지원되지 않는 옵션은 `VM Warning: Ignoring option` 경고를 생성합니다.
더 이상 지원되지 않는 옵션은 런타임에 아무런 영향을 주지 않습니다.

웹 페이지 [VM 옵션 탐색기](https://chriswhocodes.com/hotspot_option_differences.html)에서는 JDK 7 이후로 Java에 추가되거나 제거된 옵션의 완전한 목록을 제공합니다. 

#### <a name="error-could-not-create-the-java-virtual-machine"></a>오류: Java Virtual Machine을 만들 수 없음

이 오류 메시지는 JVM이 [인식할 수 없는 옵션](#unrecognized-options)을 발견할 때 출력됩니다.

#### <a name="warning-an-illegal-reflective-access-operation-has-occurred"></a>경고: 잘못된 리플렉션 액세스 작업 발생

Java 코드에서 리플렉션을 사용하여 JDK 내부 API에 액세스하는 경우 런타임에서 잘못된 리플렉션 액세스 경고를 생성합니다.

```console
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by my.sample.Main (file:/C:/sample/) to method sun.nio.ch.Util.getTemporaryDirectBuffer(int)
WARNING: Please consider reporting this to the maintainers of com.company.Main
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
```

즉, 모듈이 리플렉션을 통해 액세스되는 패키지를 내보내지 않았다는 뜻입니다. 패키지는 모듈에서 *캡슐화*되며, 기본적으로 내부 API입니다. Java 11에서 앱을 실행하기 위한 첫 번째 방법으로 이 경고를 무시하는 방법이 있습니다.
Java 11 런타임은 레거시 코드가 계속 작동할 수 있도록 리플렉션 액세스를 허용합니다.  

이 경고를 해결하려면 내부 API를 사용하지 않는 업데이트된 코드를 찾습니다. 업데이트된 코드를 사용해도 이슈가 해결되지 않으면 `--add-exports` 또는 `--add-opens` 명령줄 옵션을 사용하여 패키지에 대한 액세스를 열 수 있습니다.
이러한 옵션을 사용하면 내보내지 않은 유형의 모듈을 다른 모듈에서 액세스할 수 있습니다.

[`--add-exports`](https://docs.oracle.com/javase/9/migrate/toc.htm#JSMIG-GUID-2F61F3A9-0979-46A4-8B49-325BA0EE8B66) 옵션을 사용하면 대상 모듈이 *public* 유형의 명명된 원본 모듈 패키지에 액세스할 수 있습니다. 코드에서 `setAccessible(true)`을 사용하여 public이 아닌 멤버와 API에 액세스하는 경우도 있습니다. 이것을 *심층 리플렉션*이라고 합니다. 이 경우 [`--add-opens`](https://docs.oracle.com/javase/9/migrate/toc.htm#JSMIG-GUID-12F945EB-71D6-46AF-8C3D-D354FD0B1781)를 사용하여 패키지의 public이 아닌 멤버에 대한 액세스 권한을 코드에 부여합니다. *--add-exports* 또는 *--add-opens* 중에 무엇을 사용해야 하는지 잘 모르겠으면 *--add-exports*부터 사용해 보세요. 

`--add-exports` 또는 `--add-opens` 옵션은 장기적인 해결책이 아닌 임시 방편으로 생각해야 합니다.
이러한 옵션을 사용하면 모듈 시스템의 캡슐화가 중단되어 JDK 내부 API를 사용할 수 없습니다.  내부 API가 제거 또는 변경되면 애플리케이션이 실패합니다.  Java 16에서는 `--add-opens` 같은 명령줄 옵션을 통해 액세스를 설정한 경우를 제외하고 리플렉션 액세스가 거부됩니다.
향후 동작을 모방하려면 명령줄에서 `--illegal-access=deny`를 설정하세요.

위의 예제에서 경고가 발생한 이유는 `java.base` 모듈에서 `sun.nio.ch` 패키지를 내보내지 않았기 때문입니다. 즉, `java.base` 모듈의 `module-info.java` 파일에 `exports sun.nio.ch;`가 없습니다. 이 문제는 `--add-exports=java.base/sun.nio.ch=ALL-UNNAMED`를 사용하여 해결할 수 있습니다. 모듈에 정의되지 않은 클래스는 암묵적으로 *이름 없는* 모듈인 `ALL-UNNAMED`에 속합니다.

#### <a name="javalangreflectinaccessibleobjectexception"></a>java.lang.reflect.InaccessibleObjectException

이 예외는 캡슐화된 클래스의 필드 또는 메서드에서 `setAccessible(true)`을 호출하려고 시도한다는 의미입니다. [잘못된 리플렉션 액세스 경고](#warning-an-illegal-reflective-access-operation-has-occurred)가 발생할 수도 있습니다. [`--add-opens`](https://docs.oracle.com/javase/9/migrate/toc.htm#JSMIG-GUID-12F945EB-71D6-46AF-8C3D-D354FD0B1781) 옵션을 사용하여 패키지의 public이 아닌 멤버에 대한 액세스 권한을 코드에 부여하세요. 이 예외 메시지는 모듈이 *setAccessible*을 호출하려고 시도하는 모듈에 대해 패키지를 "열지 않는다"는 것을 알려줍니다. 모듈이 "이름 없는 모듈"인 경우 *--add-opens* 옵션에서 대상 모듈로 `UNNAMED-MODULE`을 사용합니다.

```shell
java.lang.reflect.InaccessibleObjectException: Unable to make field private final java.util.ArrayList jdk.internal.loader.URLClassPath.loaders accessible: 
module java.base does not "opens jdk.internal.loader" to unnamed module @6442b0a6

$ java --add-opens=java.base/jdk.internal.loader=UNNAMED-MODULE example.Main
```

#### <a name="javalangnoclassdeffounderror"></a>java.lang.NoClassDefFoundError

*NoClassDefFoundError*는 대부분 분할 패키지 때문에 발생하거나, 제거된 모듈을 참조하여 발생합니다. 

##### <a name="noclassdeffounderror-caused-by-split-packages"></a>분할 패키지 때문에 발생한 NoClassDefFoundError

한 패키지가 여러 라이브러리에서 발견되면 그것이 바로 분할 패키지입니다. 분할 패키지 문제의 증상은 클래스 경로에 있어야 하는 클래스를 찾을 수 없는 것입니다. 

이 이슈는 모듈 경로를 사용하는 경우에만 발생합니다. Java 모듈 시스템은 패키지를 *명명된* 한 모듈로 제한하여 클래스 조회를 최적화합니다. 클래스를 조회할 때 런타임에서는 클래스 경로보다 모듈 경로에 우선 순위를 부여합니다. 패키지가 모듈과 클래스 경로 사이에 분할된 경우 클래스 조회에 모듈만 사용됩니다. 이로 인해 `NoClassDefFound` 오류가 발생할 수 있습니다. 

분할 패키지를 확인하는 쉬운 방법은 모듈 경로와 클래스 경로를 [jdeps](https://docs.oracle.com/en/java/javase/11/tools/jdeps.html)에 삽입하고 애플리케이션 클래스 파일의 경로를 &lt;path&gt;로 사용하는 것입니다. 분할 패키지가 있으면 jdeps에서 `Warning: split package: <package-name> <module-path> <split-path>` 경고를 출력합니다. 

이 이슈는 `--patch-module <module-name>=<path>[,<path>]`를 사용하여 분할 패키지를 명명된 모듈에 추가하는 방법으로 해결할 수 있습니다. 

##### <a name="noclassdeffounderror-caused-by-using-java-ee-or-corba-modules"></a>Java EE 또는 CORBA 모듈을 사용하여 발생한 NoClassDefFoundError

애플리케이션이 Java 8에서 실행되지만 `java.lang.NoClassDefFoundError` 또는 `java.lang.ClassNotFoundException`를 throw하는 경우 애플리케이션이 Java EE 또는 CORBA 모듈의 패키지를 사용 중일 가능성이 높습니다. 두 모듈은 Java 9에서 더 이상 사용되지 않으며 [Java 11에서 제거되었습니다](https://openjdk.java.net/jeps/320). 

이 이슈를 해결하려면 프로젝트에 런타임 종속성을 추가합니다.

> [!div class="mx-tdBreakAll"]
> |제거된 모듈|영향을 받는 패키지|제안된 종속성|
> |-|-|-|
> |JAX-WS(Java API for XML Web Services) |java.xml.ws |[JAX WS RI 런타임](https://mvnrepository.com/artifact/com.sun.xml.ws/jaxws-rt) |
> |JAXB(Java Architecture for XML Binding) |java.xml.bind |[JAXB 런타임](https://mvnrepository.com/artifact/org.glassfish.jaxb/jaxb-runtime)|
> |JAV(JavaBeans Activation Framework) |java.activation |[JavaBeans(TM) Activation Framework](https://mvnrepository.com/artifact/javax.activation/activation) |
> |일반 주석 |java.xml.ws.annotation |[Javax Annotation API](https://mvnrepository.com/artifact/javax.annotation/javax.annotation-api)|
> |CORBA(Common Object Request Broker Architecture) |java.corba | [GlassFish CORBA ORB](https://mvnrepository.com/artifact/org.glassfish.corba/glassfish-corba-orb) |
> |JTA(Java Transaction API) |java.transaction | [Java Transaction API](https://mvnrepository.com/artifact/javax.transaction/jta)|

#### <a name="-xbootclasspathp-is-no-longer-a-supported-option"></a>더 이상 지원되지 않는 -Xbootclasspath/p 옵션

`-Xbootclasspath/p`에 대한 지원이 제거되었습니다. 대신 `--patch-module`를 사용하세요. *--patch-module* 옵션은 [JEP 261](http://openjdk.java.net/jeps/261)에 설명되어 있습니다. "모듈 콘텐츠 패치"라는 섹션을 찾아보세요. *--patch-module*은 *javac* 및 *java*와 함께 사용하여 모듈의 클래스를 재정의하거나 보강할 수 있습니다. 

*--patch-module*이 실제로 하는 일은 모듈 시스템의 클래스 조회에 패치 모듈을 삽입하는 것입니다. 모듈 시스템은 가장 먼저 패치 모듈에서 클래스를 가져옵니다. 이는 Java 8에서 bootclasspath를 접두어로 붙이는 것과 동일한 효과입니다. 

#### <a name="unsupportedclassversionerror"></a>UnsupportedClassVersionError

이 예외는 이전 버전의 Java에서 보다 최신 버전의 Java를 사용하여 컴파일된 코드를 실행하려고 시도한다는 의미입니다. 예를 들어 JDK 13에서 컴파일된 jar 파일을 Java 11에서 실행하려고 시도하는 것입니다. 

| Java 버전 | 클래스 파일 형식 버전 |
|-|-|
| 8  | 52 |
| 9  | 53 |
| 10 | 54 |
| 11 | 55 |
| 12 | 56 |
| 13 | 57 |

## <a name="next-steps"></a>다음 단계

애플리케이션이 Java 11에서 실행되면 라이브러리를 클래스 경로에서 모듈 경로로 이동하는 방안을 고려해 봅니다. 애플리케이션이 종속된 라이브러리의 업데이트된 버전을 찾습니다. 사용 가능한 경우 모듈식 라이브러리를 선택합니다. 애플리케이션에서 모듈을 사용할 계획이 없더라도 모듈 경로를 최대한 많이 사용하세요. 모듈 경로를 사용하면 클래스 경로보다 클래스 로드 성능이 향상됩니다. 
