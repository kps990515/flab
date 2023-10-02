## 9/27 JAVA

### [메모리단편화](https://velog.io/@hanhs4544/%EB%A9%94%EB%AA%A8%EB%A6%AC-%EB%8B%A8%ED%8E%B8%ED%99%94Memory-Fragmentation)
- RAM에서 메모리의 공간이 작은 조각으로 나뉘어져 사용가능한 메모리가 충분히 존재하지만 할당(사용)이 불가능한 상태
- 메모리 단편화는 내부 단편화와 외부 단편화로 구분 가능하다.
  - 내부 단편화 : 메모리 세그먼트가 프로세스에서 필요한 양보다 크게 할당되어 메모리 공간이 낭비되는 상황
  - 외부 단편화 : 메모리가 할당 및 해제 작업의 반복으로 작은 메모리가 중간중간 존재하게 된다. 이 때 중간에 생긴 사용하지 않는 메모리가 많이 존재해서 총 메모리 공간은 충분하지만 실제로 할당이 불가한 상황
- 해결법
    1. 압축 : 메모리 공간을 재배치하여 단편화로 인해 분산되어 있는 메모리 공간들을 하나로 합치는 기법
    2. 통합 : 단편화로 인해 분산된 메모리 공간들을 인접해 있는 것끼리 통합시켜 큰 메모리 공간으로 합치는 기법
    3. 페이징 : 가상메모리(HDD,SSD)는 페이지로 나누고 RAM은 프레임단위로 나눠서 매핑테이블로 관리(내부단편화는 존재)
    4. 세그먼테이션 : 가상메모리를 크기가 각기 다른 세그먼트로 분할해서 사용, 테이블로 세그먼트-RAM 주소 관리(외부단편화 존재)

### [float, double, Bigdecimal](https://velog.io/@jerry92/Java-float%EC%99%80-double-%EA%B7%B8%EB%A6%AC%EA%B3%A0-BigDecimal)
- float, double : 이진 부동소수점 연산에 사용되며, 넓은 범위의 수를 빠르고 정밀한 "근사치"  계산을 위해 세심하게 설계
    - 10진 소수를 2진수로 사용하기에 오차 발생
- BigDecimal : 소수를 사용하지 않고 내부적으로 정수*10^x 로 표현하므로 정확한 숫자 표현이 가능(정수, 소수 분리해서 10진수로 처리)
- Heap영역에 저장된 값을 수정할 수 없는 것

### [직렬화](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%A7%81%EB%A0%AC%ED%99%94Serializable-%EC%99%84%EB%B2%BD-%EB%A7%88%EC%8A%A4%ED%84%B0%ED%95%98%EA%B8%B0)
 - Object 또는 Data를 다른 컴퓨터의 자바 시스템에서도 사용 할수 있도록 바이트 스트림(stream of bytes) 형태로 연속전인(serial) 데이터로 변환하는 포맷 변환 기술
 - JSON 대비 직렬화 장점
    - 직렬화는 자바의 고유 기술인 만큼 당연히 자바 시스템에서 개발에 최적화
    - 자바의 광활한 레퍼런스 타입에 대해 제약 없이 외부에 전송 및 내부에서 자동파싱가능(컬렉션, 클래스, 인터페이스 등...)
 - JSON 대비 직렬화 단점
    - JSON 보다 파일 용량 크기가 거의 2배 이상 차이가 난다.
    - 역직렬화를 통해 공격받을 위험 존재

### [Reflection](https://hudi.blog/java-reflection/)
 - 클래스로더를 통해 메모리에 저장된 정보(생성자, 메소드, 클래스 등)에 대한 정보를 가져오는 기능
 - Annotation은 Reflection을 통해 사용 가능
 - Getter, Setter 자동 생성도 Reflection 활용해서 제공
 - 접근제어자와 무관하게 클래스의 필드, 메소드 호출가능
 - 단점 : 런타임시점에 클래스 분석해서 속도가 느림, 타입체크 불가, 객체의 추상화 깨짐

 - 클래스 객체 Reflection
 ```java
 // 아래와 같이 획득한 Class는 public 생성자가 없으므로 JVM이 자동으로 생성해줌

Class<Member> aClass = Member.class; // Class 프로퍼티 통해 획득

Member member1 = new Member();
Class<? extends Member> bClass = member1.getClass(); // getClass 활용

Class<?> cClass = Class.forName("hudi.reflection.Member"); // (3)
 ```

- getXXX(getFields(), getClass...) : 상속받은 클래스와 인터페이스 포함해 모든 public요소 획득
- getDeclaredXXX(getDeclaredFields...) : 상속받은 클래스, 인터페이스 제외하고 클래스에 정의된 요소획득(private, protected... 포함)

- Constructor
```java
Constructor<?> constructor = aClass.getDeclaredConstructor();
// 생성자 가져오기

Object object = constructor.newInstance();
// 이렇게 인스턴스를 생성할 수 있다.

Member member = (Member) constructor.newInstance();
// 타입 캐스팅을 사용해서 위와 같이 받아올 수 있다.
```

-Field
```java
Class<Member> aClass = Member.class;
Member member = new Member("후디", 25);

Field name = aClass.getDeclaredField("name");
name.setAccessible(true);
name.set(member, "Hudi"); // 필드값 변경

System.out.println("member = " + member);
// member = Member{name='Hudi', age=25}
```

### 자바와 C
 - 자바는 생산성에 초점을 뒀고, C/C++은 성능에 초점
 - 절차지향 : 프로그램의 순서와 흐름을 먼저 세우고 필요한 자료구조와 함수들을 설계하는 방식이다.(어떤 기능을 어떤 순서로 처리할 것인가)
 - 객체지향 : 반대로 자료구조와 이를 중심으로 한 모듈들을 먼저 설계한 다음에 이들의 실행순서와 흐름을 짜는 방식이다.(누가 어떤 일을 할 것인가)
 - 객체지향 특징 4가지
    - 추상화 : 클래스를 정의할 때, 불필요한 부분들을 생략하고 객체의 속성 중 중요한 것에만 중점을 두어 개략화 하는 것, 즉 클래스들의 중요하고 공통된 성질들을 추출하여 슈퍼 클래스를 선정하는 개념(추상클래스, 인터페이스)
    - 상속성
    - 다형성 : 오버라이딩, 오버로드
    - 캡슐화 : 관련이 있는 변수와 함수를 하나의 클래스로 묶고 외부에서 쉽게 접근하지 못하도록 은닉

### [TCP UDP](https://dev-coco.tistory.com/144)
 - TCP : 연결지향적 프로토콜(클라이언트, 서버가 연결된 상태에서 데이터 송수신), 신뢰보장형
    1. 연결형 서비스 : 3-way handshaking 과정을 통해 연결을 설정, 4-way handshaking 과정을 통해 연결을 해제
    2. 흐름 제어 : 데이터 처리 속도를 조절하여 수신자의 버퍼 오버플로우를 방지
    3. 혼잡 제어 : 네트워크 내의 패킷 수가 과도하게 증가하지 않도록 방지
    4. 신뢰성 보장
    5. 전이중(양방향 전송), 점대점(각 연결이 2개의 종단점 유지) 방식

    - 3-way handshaking
        1. SYN을 통해 클라이언트 -> 서버 전송
        2. 서버 SYN-Received 상태로 변경되고 SYN + ACK를 클라이언트에게 송신
        3. 클라리언트는 Established상태가 되고 ACK를 서버에게 송신
        4. 서버도 Established 상태로 변경

    - 4-way handshaking
        1. 클라이언트 FIN 서버로 송신 후 FIN-WAIT-1 상태로 변경
        2. 서버 CLOSE-WAIT상태로 변경 후 ACK 송신
        3. 클라이언트 FIN-WAIT-2로 변경
        4. 서버의 종료 프로세스 진행되면 FIN 송신 후 LAST_ACK로 변경
        5. 클라이언트 ACK 서버로 송신하고 TIME-WAIT 상태 변경

### DBCP
 - 웹 컨테이너(WAS)가 실행되면서 DB와 미리 connection(연결)을 해놓은 객체들을 pool에 저장해두었다가.
 - 클라이언트 요청이 오면 connection을 빌려주고, 처리가 끝나면 다시 connection을 반납받아 pool에 저장하는 방식을 말합니다.
 - 사용이유 : 매번 사용자가 요청을 할 때마다 드라이버를 로드하고 커넥션 객체를 생성하여 연결하고 종료하기 때문에 매우 비효율적이기 때문
 - 특징
    - 웹 컨테이너(WAS)가 실행되면서 connection 객체를 미리 pool에 생성
    - HTTP 요청에 따라 pool에서 connection객체를 가져다 쓰고 반환
    - pool에 미리 connection이 생성되어 있기 때문에 connection을 생성하는 데 드는 요정 마다 연결 시간이 소비되지 않는다.
    - 커넥션을 계속해서 재사용하기 때문에 생성되는 커넥션 수를 제한적으로 설정함

### Context Switching
 - 현재 진행하고 있는 Task(Process, Thread)의 상태를 저장하고 다음 진행할 Task의 상태 값을 읽어 적용하는 과정
 - 과정
    1. Task의 대부분 정보는 Register에 저장되고 PCB(Process Control Block)로 관리
    2. 현재 실행하고 있는 Task의 PCB 정보를 저장
    3. 다음 실행할 Task의 PCB 정보를 읽어 Register에 적재하고 CPU가 이전에 진행했던 과정을 연속적으로 수행
 - 코스트
    1. Cache 초기화
    2. Memory Mapping 초기화
    3. Context Switching비용은 Process(전체변경) > Thread(stack만 변경)

### 자바 8
    1. Optional : null처리를 간편하게 하기 위해
    2. interface Default Method : 하위호환성 위해, 인터페이스에 변경이 일어나면 사용하는 곳에서 모두 구현필요
    3. LocalDate, LocalDateTime, LocalTime    
        - Calender, Date클래스의 문제점
            - 불변 객체가 아니라 수정위험이 있다
            - 상수필드 남용(Calender.SECOND)
            - 혼란주는 월 지정(Calendar.OCTOBER) -> 실질저장값은 9
            - 일관성 없는 요일 상수
            - Date, Calender의 역할분담
    4. Parallel Sort :  병렬정렬    
    5. StringJoiner :  순차적으로 나열되는 문자열에 특정문자열 넣을때 사용(ㅇㅇㅇ, ㅠㅠㅠ, ㄹㄹㄹ)
    6. 람다 : 익명클래스 사용시 가독성 향상
        - Functional Interface : 하나의 메소드만 선언되어있는 인터페이스    
    7. Stream : 연속된 정보 처리에 사용    
    8. 메소드참조 : ClassName::NEW

