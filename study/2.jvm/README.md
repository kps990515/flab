# JVM 

## JVM 구조

1. Class Loader Subsystem: JVM에 클래스를 로드하고 링크하는 역할을 합니다. 주요 단계는 로딩, 링크(Linking), 초기화(Initialization)입니다.
    - 로딩(Loading): 클래스를 메모리에 로드.
    - 링크(Linking): 메모리에 로드된 클래스의 참조를 해결.
    - 초기화(Initialization): 클래스의 정적 변수를 초기화하고 정적 블록을 실행.

2. Runtime Data Area: JVM이 실행되는 동안 사용하는 메모리 영역입니다.
    - Method Area: 클래스 레벨의 정보(메서드, 필드, 정적 변수 등)를 저장.
    - Heap Area: 객체 인스턴스가 저장되는 영역.
    - Stack Area: 메서드 호출 시마다 생성되는 프레임이 저장되는 영역. 각 프레임에는 지역 변수, 피연산자 스택, 반환 주소 등이 포함됨.
    - PC Register: 각 스레드마다 생성되며 현재 실행 중인 JVM 명령의 주소를 저장.
    - Native Method Stack: 네이티브 메서드 호출을 위한 스택.

3. Execution Engine: 바이트코드를 실행하는 컴포넌트입니다.
    - Interpreter: 바이트코드를 하나씩 해석하여 실행.
    - Just-In-Time (JIT) Compiler: 빈번하게 사용되는 바이트코드를 네이티브 코드로 컴파일하여 성능을 향상.
    - Garbage Collector: 사용하지 않는 객체를 자동으로 메모리에서 해제.

4. Native Method Interface (JNI): 자바 코드에서 네이티브 코드를 호출하기 위한 인터페이스입니다.

## JVM 메모리구조

1. Method Area
    - 클래스 로더가 로드한 클래스의 구조 정보를 저장하는 영역.
    - 클래스 메타데이터, 메서드, 정적 변수 등이 저장됩니다.

2. Heap Area
    - 모든 객체 인스턴스와 배열이 저장되는 영역.
    - 메모리 관리는 Garbage Collector에 의해 자동으로 처리됩니다.

3. Stack Area
    - 각 스레드마다 별도의 Stack이 존재.
    - 메서드 호출 시마다 프레임이 생성되고, 메서드가 종료되면 프레임이 제거됩니다.
    - 프레임에는 지역 변수, 피연산자 스택, 반환 주소 등이 포함됩니다.

4. PC Register
    - 각 스레드마다 별도의 PC 레지스터가 존재.
    - 현재 실행 중인 JVM 명령의 주소를 저장합니다.

5. Native Method Stack
    - 네이티브 메서드 호출을 위한 스택.

## GC
    - Mark and Sweep: 모든 객체를 마크하고, 사용되지 않는 객체를 스윕(제거)합니다.
    - Generational Garbage Collection: 객체의 생존 기간에 따라 Young Generation, Old Generation으로 나누어 관리합니다.
    - Young Generation: 새로운 객체가 할당되는 영역. 주로 짧은 생명 주기의 객체가 할당됩니다.
    - Old Generation: Young Generation에서 생존한 객체가 이동되는 영역. 긴 생명 주기의 객체가 할당됩니다.
### 종류
1. Serial GC
    - 설명: 단일 스레드를 사용하여 가비지 컬렉션을 수행하는 가장 간단한 GC 알고리즘입니다. Young Generation과 Old Generation 모두 Serial GC를 사용합니다.
    - 장점: 구현이 간단하고, 적은 메모리를 사용하는 작은 애플리케이션에 적합합니다.
    - 단점: 단일 스레드를 사용하므로, 멀티코어 시스템에서 성능이 떨어질 수 있습니다.
2. Parallel GC
    - 설명: 여러 개의 스레드를 사용하여 가비지 컬렉션을 병렬로 수행합니다. Young Generation은 Parallel GC를 사용하고, Old Generation도 병렬로 수집합니다.
    - 장점: 멀티코어 시스템에서 높은 처리량(throughput)을 제공합니다.
    - 단점: GC 동안 애플리케이션이 일시 중지되며, 응답 시간이 길어질 수 있습니다.

3. CMS (Concurrent Mark-Sweep) GC
    - 설명: Old Generation에 대해 부분적으로 병렬로 가비지 컬렉션을 수행합니다. 마킹(marking)과 스위핑(sweeping) 단계가 병렬로 수행되어 애플리케이션의 일시 중지를 줄입니다.
    - 장점: 짧은 일시 중지 시간(pause time)을 제공합니다.
    - 단점: CPU 리소스를 많이 사용하며, 메모리 단편화 문제가 발생할 수 있습니다.

4. G1 (Garbage-First) GC
    - 설명: 힙 메모리를 여러 개의 영역(region)으로 나누어 관리합니다. Young Generation과 Old Generation의 가비지 컬렉션을 모두 관리하며, 병렬로 수행됩니다.
    - 장점: 짧고 예측 가능한 일시 중지 시간을 제공합니다. 힙 메모리 단편화 문제를 줄입니다.
    - 단점: CMS보다 약간 더 복잡하며, 초기 튜닝이 필요할 수 있습니다.

5. ZGC (Z Garbage Collector)
    - 설명: 매우 짧은 일시 중지 시간(pause time)을 목표로 설계된 GC입니다. 대규모 힙 메모리를 효율적으로 관리합니다.
    - 장점: 매우 짧은 일시 중지 시간(10ms 미만)을 제공합니다. 큰 힙 메모리에서 효율적으로 작동합니다.
    - 단점: 상대적으로 최신 기술이며, 모든 시스템에서 완전히 지원되지 않을 수 있습니다.

6. Shenandoah GC
    - 설명: OpenJDK에서 제공하는 저지연(Low Pause) 가비지 컬렉터입니다. 병렬로 작업을 수행하여 일시 중지 시간을 줄입니다.
    - 장점: 짧은 일시 중지 시간을 제공하며, 특히 큰 힙 메모리에서 효율적으로 작동합니다.
    - 단점: 다른 GC에 비해 상대적으로 최신 기술이며, 모든 시스템에서 완전히 지원되지 않을 수 있습니다.

## JVM 튜닝

### 필요성
1. 메모리 관리 최적화: 메모리 사용을 최적화하여 애플리케이션의 성능을 향상.
2. GC 성능 개선: 가비지 컬렉션의 빈도를 줄이고, GC 시간 동안 애플리케이션의 일시 중지를 최소화.
3. 응답 시간 개선: 애플리케이션의 응답 시간을 줄여 사용자 경험 향상.
4. 시스템 안정성 향상: 메모리 누수나 메모리 부족 문제를 방지하여 시스템 안정성을 유지.

### 옵션
    - -Xms: 초기 힙 사이즈 설정.
    - -Xmx: 최대 힙 사이즈 설정.
    - -XX:+UseSerialGC: Serial GC 사용.
    - -XX:+UseParallelGC: Parallel GC 사용.
    - -XX:+UseConcMarkSweepGC: Concurrent Mark-Sweep (CMS) GC 사용.
    - -XX:+UseG1GC: G1 GC 사용.

## 기술면접
### Q. JVM의 주요 구성 요소는 무엇인가요?
### A. JVM의 주요 구성 요소로는 Class Loader, Runtime Data Area, Execution Engine, 그리고 Native Method Interface (JNI)가 있습니다. 각 구성 요소의 역할을 설명할 수 있어야 합니다.

### Q.JVM 메모리 구조를 설명해 주세요.
### A. JVM 메모리 구조는 Method Area, Heap Area, Stack Area, PC Register, Native Method Stack으로 나눌 수 있습니다. 각 메모리 영역의 역할과 특성을 설명할 수 있어야 합니다.

### Q.JVM의 메서드 영역(Method Area)과 힙(Heap)의 차이점은 무엇인가요?
### A. Method Area는 클래스 레벨의 정보(메서드, 필드, 정적 변수 등)를 저장하며, 모든 스레드가 공유하는 영역입니다. Heap은 객체 인스턴스가 저장되는 영역으로, 역시 모든 스레드가 공유합니다.

### Q.JVM의 스택(Stack) 영역과 힙(Heap) 영역의 차이점을 설명해 주세요.
### A. Stack 영역은 각 스레드마다 별도로 할당되며, 메서드 호출 시 생성되는 프레임을 저장합니다. Heap 영역은 모든 스레드가 공유하며, 객체 인스턴스와 배열을 저장합니다.

### Q.JVM의 가비지 컬렉션(GC)은 어떻게 동작하나요?
### A. 가비지 컬렉션은 사용되지 않는 객체를 자동으로 메모리에서 해제합니다. JVM의 GC는 Mark and Sweep, Generational GC, G1 GC 등 다양한 알고리즘을 사용하여 메모리를 관리합니다.

### Q.Mark and Sweep 가비지 컬렉션 알고리즘에 대해 설명해 주세요.
### A. Mark and Sweep 알고리즘은 두 단계로 나뉩니다. 첫 번째 단계에서는 모든 객체를 검사하여 사용 중인 객체를 마킹합니다. 두 번째 단계에서는 마킹되지 않은 객체를 메모리에서 제거합니다.

### Q.Generational Garbage Collection이란 무엇인가요?
### A. Generational Garbage Collection은 객체의 생존 기간에 따라 Young Generation과 Old Generation으로 메모리를 나누어 관리합니다. Young Generation에서는 새로운 객체가 할당되며, 대부분의 객체가 여기서 생성되고 빠르게 수집됩니다. Old Generation에서는 Young Generation에서 생존한 객체가 이동되며, 긴 생명 주기의 객체가 할당됩니다.

### Q.CMS GC와 G1 GC의 차이점은 무엇인가요?
### A. CMS (Concurrent Mark-Sweep) GC는 Old Generation에 대해 병렬로 가비지 컬렉션을 수행하여 짧은 일시 중지 시간을 제공합니다. 그러나 메모리 단편화 문제가 발생할 수 있습니다. G1 (Garbage-First) GC는 힙을 여러 개의 영역으로 나누어 관리하며, Young Generation과 Old Generation의 가비지 컬렉션을 모두 관리합니다. G1 GC는 짧고 예측 가능한 일시 중지 시간을 제공하며, 메모리 단편화 문제를 줄입니다.

### Q.Java 9 이후에 도입된 ZGC와 Shenandoah GC에 대해 설명해 주세요.
### A. ZGC는 매우 짧은 일시 중지 시간(10ms 미만)을 목표로 설계된 GC입니다. 대규모 힙 메모리를 효율적으로 관리합니다. Shenandoah GC는 OpenJDK에서 제공하는 저지연(Low Pause) 가비지 컬렉터로, 병렬로 작업을 수행하여 일시 중지 시간을 줄입니다. 두 GC 모두 큰 힙 메모리에서 효율적으로 작동합니다.

### Q.GC 로그를 활성화하고 분석하는 방법에 대해 설명해 주세요.
### A. GC 로그를 활성화하여 GC 활동을 모니터링할 수 있습니다. 예를 들어, -Xlog:gc* 옵션을 사용하여 GC 로그를 활성화할 수 있습니다. GC 로그를 통해 메모리 사용 패턴, GC 빈도, 일시 중지 시간 등을 분석할 수 있습니다.

### Q.JVM 힙 사이즈를 조절하는 방법을 설명해 주세요.
### A. JVM 힙 사이즈는 -Xms와 -Xmx 옵션을 사용하여 조절할 수 있습니다. -Xms는 초기 힙 사이즈를, -Xmx는 최대 힙 사이즈를 설정합니다. 예를 들어, java -Xms512m -Xmx1024m -jar myapp.jar 명령어를 사용하여 초기 힙 사이즈를 512MB, 최대 힙 사이즈를 1024MB로 설정할 수 있습니다.

### Q.JVM 튜닝에서 GC 옵션을 설정하는 방법을 설명해 주세요.
### A. JVM 튜닝을 위해 다양한 GC 옵션을 설정할 수 있습니다. 예를 들어, -XX:+UseG1GC 옵션을 사용하여 G1 GC를 활성화할 수 있습니다. 또한, -XX:NewSize, -XX:MaxNewSize, -XX:SurvivorRatio, -XX:MetaspaceSize, -XX:MaxMetaspaceSize 등의 옵션을 사용하여 JVM 메모리 영역의 크기와 비율을 조절할 수 있습니다.

### Q.JVM 성능 모니터링 도구에 대해 설명해 주세요.
### A. JVM 성능 모니터링 도구로는 JVisualVM과 JConsole이 있습니다. JVisualVM은 GUI 기반의 성능 모니터링 도구로, 메모리 사용량, GC 활동, 스레드 상태 등을 실시간으로 모니터링할 수 있습니다. JConsole은 JMX 기술을 사용하는 성능 모니터링 도구로, 메모리 사용량, 스레드 활동, CPU 사용량 등을 모니터링할 수 있습니다.

### Q.GC 로그를 활성화하고 분석하는 방법을 설명해 주세요.
### A. GC 로그를 활성화하여 GC 활동을 모니터링할 수 있습니다. 예를 들어, -Xlog:gc* 옵션을 사용하여 GC 로그를 활성화할 수 있습니다. GC 로그를 통해 메모리 사용 패턴, GC 빈도, 일시 중지 시간 등을 분석할 수 있습니다.
