---
title: Java 11로 전환해야 하는 이유
titleSuffix: Azure
description: Java 8에서 Java 11로 전환할 경우의 이점을 평가 중인 의사 결정권자를 위한 요약 수준의 문서입니다.
author: dsgrieve
manager: maverbur
tags: java
ms.topic: article
ms.date: 11/19/2019
ms.author: dagrieve
ms.openlocfilehash: 8f35aea0447f8681fb4f456dbafe500a608f6420
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85790780"
---
# <a name="reasons-to-move-to-java-11"></a>Java 11로 전환해야 하는 이유

문제는 Java 11로 전환할지 *여부*가 아닌 전환 *시기*입니다. 앞으로 몇 년 후 Java 8은 더 이상 지원되지 않으며 사용자는 Java 11로 전환해야 합니다. Java 11로 전환하면 이점이 있으며 팀에서 최대한 빨리 전환하도록 권장합니다.

Java 8 이후 새 기능이 추가되었고 기능이 향상되었습니다. API에 대한 눈에 띄는 추가 및 수정 사항이 있으며, 시작, 성능 및 메모리 사용을 개선하는 향상된 기능이 있습니다.

## <a name="transitioning-to-java-11"></a>Java 11로 전환

Java 11로 전환할 때 단계별 방식을 사용할 수 있습니다. Java 모듈을 사용하여 Java 11에서 코드를 실행할 필요가 *없습니다*. Java 11은 JDK 8을 사용하여 개발되고 빌드된 코드를 실행하는 데 사용할 수 있습니다.
그러나 주로 사용되지 않는 API, 클래스 로더 및 리플렉션과 관련하여 몇 가지 잠재적인 문제가 있습니다.

Microsoft Java 엔지니어링 그룹에는 [Java 8에서 Java 11로 전환](./transition-from-java-8-to-java-11.md)하는 방법에 대한 지침이 들어 있습니다. [Java Platform, Standard Edition Oracle JDK 9 마이그레이션 가이드](https://docs.oracle.com/javase/9/migrate/toc.htm) 및 [모듈 시스템 상태: 호환성 및 마이그레이션](http://openjdk.java.net/projects/jigsaw/spec/sotms/#compatibility--migration)은 또 다른 유용한 가이드입니다. 

## <a name="high-level-changes-between-java-8-and-11"></a>Java 8과 11 간의 대략적인 변경 내용

이 섹션에서는 Java 버전 9 \[[1](#ref1)\], 10 \[[2](#ref2)\] 및 11 \[[3](#ref3)\]에서 변경된 사항을 모두 열거하지는 않습니다. 성능, 진단 및 생산성에 영향을 주는 변경 내용을 중점적으로 설명합니다.

### <a name="modules-4"></a>모듈 \[[4](#ref4)\]

모듈은 *classpath*에서 실행되는 대규모 애플리케이션에서 관리하기 어려운 구성 및 캡슐화 문제를 해결합니다. *모듈*은 Java 클래스와 인터페이스 및 관련 리소스의 자동 기술 컬렉션입니다.

모듈을 통해 애플리케이션에 필요한 구성 요소만 포함하는 런타임 구성을 사용자 지정할 수 있습니다. 이 사용자 지정은 메모리 공간을 더 적게 사용하며 애플리케이션이 [jlink](https://docs.oracle.com/en/java/javase/11/tools/jlink.html)를 사용하여 배포용 사용자 지정 런타임에 정적으로 연결될 수 있게 해줍니다. 메모리 공간을 적게 사용하면 특히 마이크로서비스 아키텍처에서 유용할 수 있습니다.

내부적으로 JVM은 모듈을 활용하여 클래스 로딩을 보다 효율적으로 만들 수 있습니다. 그 결과 런타임이 더 작아지고, 더 가벼워져서 더 빠르게 시작할 수 있습니다. 모듈은 클래스에 필요한 구성 요소를 인코딩하기 때문에 JVM에서 애플리케이션 성능을 개선하기 위해 사용하는 최적화 기법의 효과가 더 좋아질 수 있습니다.

프로그래머의 경우 모듈은 모듈이 내보내는 패키지와 필요한 구성 요소를 명시적으로 선언하고 반사적 액세스를 제한하여 강력한 캡슐화를 적용하는 데 도움이 됩니다.
이 캡슐화 수준을 사용하면 애플리케이션을 더 안전하고 쉽게 유지 관리할 수 있습니다.

애플리케이션은 *classpath*를 계속 사용할 수 있으며 Java 11에서 실행하기 위한 필수 요건인 모듈 전환이 필요 없습니다.

### <a name="profiling-and-diagnostics"></a>프로파일링 및 진단

#### <a name="java-flight-recorder-5"></a>Java Flight Recorder \[[5](#ref5)\]

JFR(Java Flight Recorder)은 실행 중인 Java 애플리케이션에서 진단 및 프로파일링 데이터를 수집합니다. JFR은 실행 중인 Java 애플리케이션에 거의 영향을 주지 않습니다. 따라서 수집된 데이터를 JMC(Java Mission Control) 및 기타 도구를 사용하여 분석할 수 있습니다. JFR 및 JMC는 Java 8에서 상용 기능이지만 Java 11에서는 둘 다 오픈 소스입니다.

#### <a name="java-mission-control-6"></a>Java Mission Control \[[6](#ref6)\]

JMC(Java Mission Control)는 JFR(Java Flight Recorder)에서 수집한 데이터를 그래픽으로 표시하고 Java에서는 오픈 소스로 제공됩니다.
11. JMC는 실행 중인 애플리케이션에 대한 일반 정보를 제공할 뿐만 아니라 사용자가 데이터를 드릴다운할 수 있도록 합니다. JFR 및 JMC를 사용하면 메모리 누수, GC 오버헤드, 핫 메서드, 스레드 병목 상태 및 I/O 블로킹과 같은 런타임 문제를 진단할 수 있습니다.

#### <a name="unified-logging-7"></a>통합 로깅 \[[7](#ref7)\]

Java 11에는 JVM의 모든 구성 요소에 대한 일반적인 로깅 시스템이 있습니다.
이 통합 로깅 시스템을 통해 사용자는 무슨 구성 요소를 어느 수준까지 로깅할지 정의할 수 있습니다. 이 세분화된 로깅은 JVM 충돌에 대한 근본 원인 분석을 수행하고 프로덕션 환경에서 성능 문제를 진단하는 데 유용합니다.

#### <a name="low-overhead-heap-profiling-8"></a>오버헤드가 낮은 힙 프로파일링 \[[8](#ref8)\]

Java 힙 할당을 샘플링하는 데 사용할 수 있는 새 API가 JVMTI(Java Virtual Machine Tool Interface)에 추가되었습니다. 이 샘플링은 오버헤드가 적고 지속적으로 사용하도록 설정할 수 있습니다. JFR(Java Flight Recorder)을 사용하여 힙 할당을 모니터링할 수 있지만 JFR의 샘플링 방법은 할당에서만 작동합니다. JFR 구현에서 할당이 누락될 수도 있습니다. 반면 Java 11의 힙 샘플링은 라이브 개체와 데드 개체 모두에 대한 정보를 제공할 수 있습니다.

APM(애플리케이션 성능 모니터링) 공급업체가 이 새로운 기능을 활용하기 시작했으며, Java Engineering Group은 Azure 성능 모니터링 도구를 사용하여 잠재적 사용량을 조사하고 있습니다.

#### <a name="stackwalker-9"></a>StackWalker \[[9](#ref9)\]

현재 스레드의 스택에 대한 스냅샷 가져오기는 로깅할 때 주로 사용됩니다. 문제는 로깅할 스택 추적의 양과 스택 추적의 로깅 여부입니다. 예를 들어 메서드의 특정 예외에 대한 스택 추적만 보려는 경우가 있을 수 있습니다. StackWalker 클래스(Java 9에서 추가됨)는 스택의 스냅샷을 제공하고, 프로그래머가 스택 추적을 사용하는 방법을 세부적으로 제어할 수 있는 메서드를 제공합니다.

### <a name="garbage-collection-10"></a>가비지 수집 \[[10](#ref10)\]

Java 11에서 사용할 수 있는 가비지 수집기는 직렬, 병렬, 가비지 우선 및 엡실론입니다. Java 11의 기본 가비지 수집기는 G1GC(가비지 우선 가비지 수집기)입니다.

나머지 세 수집기는 완전한 설명을 위해 여기에 언급되었습니다. Z 가비지 수집기(ZGC)는 일시 중지 시간을 10ms 미만으로 유지하려고 하는 대기 시간이 짧은 동시 수집기입니다. ZGC는 Java 11에서 실험적 기능으로 사용할 수 있습니다. Shenandoah 수집기는 실행 중인 Java 프로그램과 동시에 더 많은 가비지 수집을 수행하여 GC 일시 중지 시간을 줄이는 일시 중지 시간이 짧은 수집기입니다.
Shenandoah는 Java 12의 실험적 기능이지만 Java 11에 대한 백포트가 있습니다. CMS(Concurrent Mark and Sweep) 수집기는 사용할 수 있지만 Java 9 이후에는 사용되지 않습니다.

JVM은 평균 사용 사례에서 GC를 기본값으로 설정합니다. 이러한 기본값 및 기타 GC 설정은 애플리케이션의 요구 사항에 따라 최적의 처리량 또는 대기 시간에 맞게 조정해야 하는 경우가 많습니다.
GC를 적절히 조정하려면 GC에 대한 심층 지식, [Microsoft Java Engineering Group](mailto:javaplatformgroup@microsoft.com)에서 제공하는 전문 지식이 필요합니다.

#### <a name="g1gc"></a>G1GC

Java 11의 기본 가비지 수집기는 G1GC(G1 가비지 수집기)입니다. G1GC의 목표는 대기 시간과 처리량 간의 균형을 유지하는 것입니다. G1 가비지 수집기는 높은 확률의 일시 중지 시간 목표를 충족하여 높은 처리량을 달성하려고 시도합니다. G1GC는 풀 컬렉션\'(full collection)을 방지하도록 설계되었습니다. 하지만 동시 컬렉션으로 메모리를 빠르게 회수하기 어려운 경우 이를 대체하기 위해 풀 GC(full GC)를 수행합니다. 전체 GC는 젊고 혼합된 컬렉션과 동일한 수의 병렬 작업자 스레드를 사용합니다.

#### <a name="parallel-gc"></a>병렬 GC

병렬 수집기는 Java 8의 기본 수집기입니다. 병렬 GC는 여러 스레드를 사용하여 가비지 수집 속도를 높이는 처리량 수집기입니다.

#### <a name="epsilon-11"></a>엡실론 \[[11](#ref11)\]

엡실론 가비지 수집기는 할당을 처리하지만 메모리를 회수하지는 않습니다. 힙이 소진되면 JVM이 종료됩니다.
엡실론은 수명이 짧은 서비스와 가비지를 사용하지 않는 것으로 알려진 애플리케이션에 유용합니다.

#### <a name="improvements-for-docker-containers-12"></a>Docker 컨테이너의 향상된 기능 \[[12](#ref12)\]

Java 10 이전에 컨테이너에 설정된 메모리 및 CPU 제약 조건은 JVM에서 인식되지 않았습니다. 예를 들어 Java 8에서 JVM은 최대 힙 크기의 기본값을 기본 호스트의 실제 메모리의 ¼로 설정합니다. Java 10부터 JVM은 컨테이너 제어 그룹(cgroup)에 의해 설정된 제약 조건을 사용하여 메모리 및 CPU 제한을 설정합니다(아래 참고 사항 참조).
예를 들어 기본 최대 힙 크기는 컨테이너의 메모리 제한의 ¼입니다(예: -m2G의 경우 500MB).

Docker 컨테이너 사용자가 Java 힙에 사용되는 시스템 메모리 양을 세부적으로 제어할 수 있도록 JVM 옵션도 추가되었습니다.

이 지원은 기본적으로 사용하도록 설정되어 있으며 Linux 기반 플랫폼에서만 사용할 수 있습니다.

> [!NOTE]
> 대부분의 cgroup 사용 작업은 jdk8u191부터 Java 8로 백포팅되었습니다. 추가 향상 기능을 8로 백포팅할 필요는 없습니다.

#### <a name="multi-release-jar-files-13"></a>다중 릴리스 jar 파일 \[[13](#ref13)\]

Java 11에서 클래스 파일의 여러 Java 릴리스별 버전을 포함하는 jar 파일을 만들 수 있습니다. 라이브러리 개발자는 다중 릴리스 jar 파일을 통해 여러 버전의 jar 파일을 제공하지 않고도 여러 버전의 Java를 지원할 수 있습니다. 이러한 라이브러리의 소비자를 위해 다중 릴리스 jar 파일은 특정 jar 파일을 특정 런타임 대상과 일치시켜야 하는 문제를 해결합니다.

## <a name="miscellaneous-performance-improvements"></a>기타 성능 향상

JVM에 대한 다음과 같은 변경 내용은 성능에 직접적인 영향을 줍니다.

-   **JEP 197: Segmented Code Cache**\[[14](#ref14)\] - 코드 캐시를 고유 세그먼트로 나눕니다. 이러한 구분은 JVM 메모리 공간을 보다 효율적으로 제어하고, 컴파일된 메서드의 검색 시간을 단축하고, 코드 캐시의 조각화를 크게 줄이고, 성능을 향상시킵니다.

-   **JEP 254: Compact Strings**\[[15](#ref15)\] - 문자 인코딩에 따라 문자열의 내부 표현을 문자당 2바이트에서 문자당 1~2바이트로 변경합니다. 대부분의 문자열은 ISO-8859-1/라틴어-1 문자를 포함하므로 이 변경 내용으로 인해 문자열을 저장하는 데 필요한 공간이 효율적으로 변경됩니다.

-   **JEP 310: Application Class-Data Sharing**\[[16](#ref16)\] - 클래스-데이터 공유는 보관된 클래스가 런타임 시 메모리에 매핑될 수 있게 하여 시작 시간을 줄여줍니다. 애플리케이션 클래스-데이터 공유는 애플리케이션 클래스를 CDS 보관함에 배치할 수 있도록 하여 클래스-데이터 공유를 확장합니다. 여러 JVM이 동일한 보관 파일을 공유하는 경우 메모리가 저장되고 전체 시스템 응답 시간이 단축됩니다.

-   **JEP 312: Thread-Local Handshakes**\[[17](#ref17)\] - 글로벌 VM 세이프포인트를 수행하지 않고 스레드에 대한 콜백을 실행하여 VM이 글로벌 세이프포인트의 수를 줄여 대기 시간을 단축할 수 있게 해줍니다.

-   **Lazy Allocation of Compiler Threads**\[[18](#ref18)\] - VM은 단계별 컴파일 모드에서 다량의 컴파일러 스레드를 시작합니다.
    이 모드는 CPU가 여러 개 있는 시스템에서 기본값입니다. 이러한 스레드는 사용 가능한 메모리 또는 컴파일 요청 수에 관계없이 생성됩니다. 스레드는 유휴 상태일 때(거의 모든 시간) 메모리를 사용하므로 리소스를 비효율적으로 사용합니다. 이 문제를 해결하기 위해 시작 시 각 유형의 컴파일러 스레드를 하나씩만 시작하도록 구현이 변경되었습니다. 추가 스레드를 시작하고 사용하지 않는 스레드를 종료하는 것은 동적으로 처리됩니다. 

핵심 라이브러리를 다음과 같이 변경하면 새 코드 또는 수정된 코드의 성능에 영향이 있습니다.

-   **JEP 193: Variable Handles**\[[19](#ref19)\] - 표준을 정의한다는 것은 개체 필드 및 배열 요소, 메모리 정렬의 세부적인 제어를 위한 표준 펜스 작업 세트, 참조된 개체의 강력한 연결성을 유지하기 위한 표준 연결성 펜스 작업에 대한 다양한 java.util.concurrent.atomic 및 sun.misc.Unsafe 작업의 동급 요소를 호출하는 것을 의미합니다.

-   **JEP 269: Convenience Factory Methods for Collections**\[[20](#ref20)\] - 소량의 요소를 사용하여 컬렉션 및 맵 인스턴스를 편리하게 만들 수 있게 해주는 라이브러리 API를 정의합니다. 컬렉션 인터페이스에서 간결하고 수정할 수 없는 컬렉션 인스턴스를 만드는 고정 팩터리 메서드입니다. 이러한 인스턴스는 본질적으로 더 효율적입니다. API는 조밀하게 표시되고 래퍼 클래스가 없는 컬렉션을 만듭니다.

-   **JEP 285: Spin-Wait Hints**\[[21](#ref21)\] - Java에서 스핀 루프에 있음을 런타임 시스템에 암시할 수 있게 해주는 API를 제공합니다. 특정 하드웨어 플랫폼은 스레드가 바쁜 대기(busy-wait) 상태라고 소프트웨어가 알려주면 이점을 얻을 수 있습니다.

-   **JEP 321: HTTP Client(Standard)** \[[22](#ref22)\]- HTTP/2 및 WebSocket을 구현하고 레거시 HttpURLConnection API를 대체할 수 있는 새로운 HTTP 클라이언트 API를 제공합니다.

## <a name="references"></a>참조

<a id="ref1">\[1\]</a> Oracle Corporation, \"Java Development Kit 9 Release Notes\"(온라인). 주소: https://www.oracle.com/technetwork/java/javase/9u-relnotes-3704429.html.
(2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref2">\[2\]</a> Oracle Corporation, \"Java Development Kit 10 Release Notes\"(온라인). 주소: https://www.oracle.com/technetwork/java/javase/10u-relnotes-4108739.html.
(2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref3">\[3\]</a> Oracle Corporation, \"Java Development Kit 11 Release Notes\"(온라인). 주소: https://www.oracle.com/technetwork/java/javase/11u-relnotes-5093844.html.
(2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref4">\[4\]</a> Oracle Corporation, \"Project Jigsaw,\" 9월 22일 
2017. (온라인). 주소: http://openjdk.java.net/projects/jigsaw/.
(2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref5">\[5\]</a> Oracle Corporation, \"JEP 328: Flight Recorder,\" 2018년 9월 9일. (온라인). 주소: http://openjdk.java.net/jeps/328. (2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref6">\[6\]</a> Oracle Corporation, \"Mission Control,\" 2019년 4월 25일. (온라인). 주소: https://wiki.openjdk.java.net/display/jmc/Main. (2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref7">\[7\]</a> Oracle Corporation, \"JEP 158: Unified JVM Logging,\" 2019년 2월 14일. (온라인). 주소: http://openjdk.java.net/jeps/158.
(2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref8">\[8\]</a> Oracle Corporation, \"JEP 331: Low-Overhead Heap Profiling,\" 2018년 9월 5일. (온라인). 주소: http://openjdk.java.net/jeps/331. (2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref9">\[9\]</a> Oracle Corporation, \"JEP 259: Stack-Walking API,\" 2017년 7월 18일. (온라인).
주소: http://openjdk.java.net/jeps/259. (2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref10">\[10\]</a> Oracle Corporation, \"JEP 248: Make G1 the Default Garbage Collector,\" 2017년 9월 12일. (온라인). 주소: http://openjdk.java.net/jeps/248. (2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref11">\[11\]</a> Oracle Corporation, \"JEP 318: 엡실론: A No-Op Garbage Collector,\" 2018년 9월 24일.
(온라인). 주소: http://openjdk.java.net/jeps/318. (2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref12">\[12\]</a> Oracle Corporation, \"JDK-8146115: Improve docker container detection and resource configuration usage,\" 2019년 9월 16일.
(온라인). 주소: https://bugs.java.com/bugdatabase/view_bug.do?bug_id=JDK-8146115.
(2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref13">\[13\]</a> Oracle Corporation, \"JEP 238: Multi-Release JAR Files,\" 2017년 6월 22일. (온라인). 주소: http://openjdk.java.net/jeps/238. (2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref14">\[14\]</a> Oracle Corporation, \"JEP 197: Segmented Code Cache,\" 2017년 4월 28일. (온라인).
주소: http://openjdk.java.net/jeps/197. (2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref15">\[15\]</a> Oracle Corporation, \"JEP 254: Compact Strings,\" 2019년 5월 18일. (온라인). 주소: http://openjdk.java.net/jeps/254.
(2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref16">\[16\]</a> Oracle Corporation, \"JEP 310: Application Class-Data Sharing,\" 2018년 8월 17일. (온라인). 주소: https://openjdk.java.net/jeps/310. (2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref17">\[17\]</a> Oracle Corporation, \"JEP 312: Thread-Local Handshakes,\" 2019년 8월 21일.
(온라인). 주소: https://openjdk.java.net/jeps/312. (2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref18">\[18\]</a> Oracle Corporation, \"JDK-8198756: Lazy allocation of compiler threads,\" 2018년 10월 29일. (온라인). 주소: https://bugs.java.com/bugdatabase/view_bug.do?bug_id=8198756.
(2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref19">\[19\]</a> Oracle Corporation, \"JEP 193: Variable Handles,\" 2017년 8월 17일. (온라인). 주소: https://openjdk.java.net/jeps/193. (2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref20">\[20\]</a> Oracle Corporation, \"JEP 269: Convenience Factory Methods for Collections,\" 2017년 6월 26일. (온라인). 주소: https://openjdk.java.net/jeps/269.
(2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref21">\[21\]</a> Oracle Corporation, \"JEP 285: Spin-Wait Hints,\" 2017년 8월 20일. (온라인). 주소: https://openjdk.java.net/jeps/285. (2019년 11월 13일에 액세스 가능 여부 확인).

<a id="ref22">\[22\]</a> Oracle Corporation, \"JEP 321: HTTP Client (Standard),\" 2018년 9월 27일. (온라인).
주소: https://openjdk.java.net/jeps/321. (2019년 11월 13일에 액세스 가능 여부 확인).
