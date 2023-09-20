## 9/19 JAVA

### 원시코드, 목적코드
- 원시코드(source code) : 프로그래밍 언어로 작성된 텍스트
- 컴파일러 : 프로그래밍언어를 바이트코드로 변환
- 목적코드(object code) : 컴파일된 파일
- 원시코드 > 컴파일러 > 목적코드

### [컴파일러, 인터프리터](https://velog.io/@jhur98/%EC%BB%B4%ED%8C%8C%EC%9D%BC%EB%9F%ACcompiler%EC%99%80-%EC%9D%B8%ED%84%B0%ED%94%84%EB%A6%AC%ED%84%B0interpreter%EC%9D%98-%EC%B0%A8%EC%9D%B4)
- 컴파일러(compiler)

전체 파일을 스캔하여 한꺼번에 번역한다.
초기 스캔시간이 오래 걸리지만, 한번 실행 파일이 만들어지고 나면 빠르다.
기계어 번역과정에서 더 많은 메모리를 사용한다.(오브젝트 코드를 묶어서 실행파일로 만드는 Linking 작업 필요)
전체 코드를 스캔하는 과정에서 모든 오류를 한꺼번에 출력해주기 때문에 실행전에 오류를 알 수 있다.
대표적인 언어로 C, C++, JAVA 등이 있다.

- 인터프리터(interpreter)

프로그램 실행시 한 번에 한 문장씩 번역한다.(실행중에 컴파일 가능)
한번에 한문장씩 번역후 실행 시키기 때문에 실행 시간이 느리다.
컴파일러와 같은 오브젝트 코드 생성과정이 없기 때문에 메모리 효율이 좋다.
프로그램을 실행시키고 나서 오류를 발견하면 바로 실행을 중지 시킨다. 실행 후에 오류를 알 수 있다.
대표적인 언어로 Python, Ruby, Javascript 등이 있다.

### [자바의 컴파일러, 인터프리터](https://velog.io/@tsi0521/Java%EB%8A%94-%EC%BB%B4%ED%8C%8C%EC%9D%BC%EB%9F%AC%EC%99%80-%EC%9D%B8%ED%84%B0%ED%94%84%EB%A6%AC%ED%84%B0-%EB%91%98-%EB%8B%A4-%EA%B0%80%EC%A7%84%EB%8B%A4)

- 순서
1. .java파일을 java Complier(javac)를 통해 바이트코드로 된 .class 파일로 변환
2. .class파일을 java Interpreter를 통해 어셈블리언어로 변환

- 이유
1. 컴파일러는 OS 및 기계상에서 효율적으로 작동 가능(OS 특성에 맞게 빠르게 bytecode로 변환 가능)
2. 인터프리터는 OS에 종속적이지 않아 어디서나 실행 가능(어떤 OS에서 작성했던지 어디서나 실행가능)
3. 바이트코드는 컴퓨터와 프로그램 사이에 buffer역할을 함으로서 보안적으로 장점

### [JVM의 JIT 컴파일러](https://kotlinworld.com/307#%EC%A-%--%EC%A-%--%--%EC%BB%B-%ED%-C%-C%EC%-D%BC%EA%B-%BC%--%EB%-F%--%EC%A-%--%--%EC%BB%B-%ED%-C%-C%EC%-D%BC%EC%-D%--%--%ED%--%-C%EA%B-%--%EC%A-%--%EA%B-%BC%--%EC%-E%A-%EC%A-%--)

- JIT란 : JVM에서 바이트 코드들을 기계어를 변환해주는 컴파일러(동적 컴파일러의 효율화)
- 사용이유 : JVM에서 컴파일러(정적 컴파일), 인터프리터(동적 컴파일)의 장점을 모두 갖기 위하여
- 특징
 - 기계어로 변환 시 자주사용하는 코드 캐싱 가능
 - 이를 위해 C1컴파일러(바이트코드 기계어 변환), C2컴파일러(변환 및 캐싱) 두개의 컴파일러를 가짐 
 - Level 1~3은 C1, Level 4는 C2

### [JVM](https://asfirstalways.tistory.com/158)

- 역할 
 - JAVA가 OS에 구애받지 않고 실행 가능하도록 작동
 - 메모리관리, GC
 - 스택기반 가상머신

- 실행과정
 1. 프로그램 실행 시 OS로 부터 메모리 할당받음
 2. javac가 .java를 읽어 .class로 변환
 3. Class Loader를 통해 .class파일을 JVM으로 Load(Library도 포함)
 4. Loading된 .class파일을 Execution Eninge(인터프리터)을 통해 해석
 5. 해석된 .class파일을 Runtime Data Area에 배치하고 실행 & GC 수행

- Class Loader(컴파일러 역할 : loading, linking, initialization)
  - .class파일을 Load하고 오브젝트 코드들을 Linking
  - Runtime시 동적으로 클래스 Load(클래스 선언이 아닌 클래스가 처음으로 참조될때 클래스를 로드 & 링크)
  - Runtime Data Area에 .class 파일 Initialization
  - 사용하지 않는 클래스 삭제  

- Execution Engine(Interpreter, JIT Compiler, GC)
  - class를 실행시키는 역할
  - class 파일을 명령어 단위로 읽어서 수행(Interpreter)
  - 자주 사용하는 코드는 Native Code로 변환 & 캐싱(JIT Compiler)
  - GC

- Runtime Data Area : 프로그램을 수행하기 위해 OS에서 할당받은 메모리 공간
  [참고사이트](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EA%B7%B8%EB%A6%BC%EC%9C%BC%EB%A1%9C-%EB%B3%B4%EB%8A%94-%EC%9E%90%EB%B0%94-%EC%BD%94%EB%93%9C%EC%9D%98-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%98%81%EC%97%AD%EC%8A%A4%ED%83%9D-%ED%9E%99)
 1. Thread
  - PC Register : Thread단위로 생성되는 공간, JVM 명령의 주소 가짐
  - JVM 스택영역 : 지역변수, 매개변수, 참조자료형의 주소값 저장
  - Native Method stack : java가 아닌 다른 언어로 구성된 메소드 실행
 
 2. Method Area(Class Area, Static Area)
  - 상수, 멤버 변수, static 변수, 생성자, 메소드들의 이름, 리턴타입, 매개변수, 접근제어자 저장
  - 어느곳에서나 접근가능한 공용영역
  - 해당 영역 데이터는 프로그램 시작~종료까지 계속 존재(메모리 부족현상 조심)

 3. Heap Area(객체 공간)
  - 참조자료형의 객체(인스턴스), 배열 저장
  - 메모리 호출이 끝나도 삭제되지 않고 유지 > 아무런 참조변수가 인스턴스를 참조하지 않을떄 GC
   1. Eden : 처음 객체가 생성되었을때 저장(Young Generation)
   2. Survivor 0/1 : Eden이 차고 Minor GC이후 객체가 살아남으면 이동(Young Generation)
   3. Old : 지속적으로 살아남은 객체저장 Major GC를 통해 한꺼번에 정리(Tenured Generation) 
   4. Permenant : Class, Method의 메타정보

### [GC](https://aljjabaegi.tistory.com/636)

 - 알고리즘(Mark and Sweep Compact)
  1. GC Root부터 참조되는 객체와 Reachable한 객체를 Marking
  2. UnReachable 객체를 메모리에서 해제(Sweep)
  3. 살아남은 영역 조각모음(Compact)

 - GC Root 대상
  - Heap 영역 내 객체참조가 있는 경우
  - Stack영역의 지역변수, 파라미터에 대한 참조가 있을 경우
  - Method 영역의 static 변수 참조가 있을 경우
  - JNI(Java native interface)에 의해 생성된 객체 참조가 있을 경우

 - 실행과정
  1. Eden영역이 차면 Minor GC 발생 & age-bit 1증가
  2. 살아남은 객체 Service 0영역 이동
  3. Minor GC 실행 후 Service 0 비우고 Service 1로 이동시킴
  4. agebit가 MaxTenuringThreshold 값 초과 시 Old Generation 영역으로 이동
  5. Old Generation 일정 용량되면 Major GC 실행

 - Stop the World
  - GC발생 시 GC를 제외한 모든 Thread 일시정지
  - Major GC시 주로 문제 발생

 - [GC의 종류](https://velog.io/@guswlsapdlf/Java-GC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98)
  1. Serial GC : 싱글코어 GC(Mark and Sweep Compact 사용)
  2. Parallel GC : Java8 Minor GC에서 멀티 스레드로 동작(Mark and Sweep Compact 사용)
  3. Parallel old GC : Major GC에서도 Multi Thread로 동작(Mark and summary Comapct)
  4. CMS(Concurrent Mark Sweep) GC : stop the world로 어플리케이션이 멈추는 현상을 줄이고자 나온 GC 알고리즘이다. 자주 참조되는 객체를 한번에 찾지 않고 4번에 걸쳐 찾는 방식을 사용한다
      - Initial mark : GC의 root가 참조하는 객체만 마킹한다(stop the world 발생)
      - Concurrent mark : 참조하는 객체를 따라가며 지속적으로 마킹한다(stop the world 없이 발생, 마킹하는 스레드 외 다른 스레드들도 작업 가능)
      - Remark : Concurrent mark 과정에서 변경된 사항이 없는지 다시 한 번 마킹을 진행한다(stop the world 발생)
      - Concurrent Sweep : unreachable 객체를 제거한다(stop the world 없이 발생)
      - 단점 : CPU, 메모리 소모가 높고 Compact 미제공에 따라 조각 난 메모리 많은 경우 긴 Stop the World 발생
  5. G1 GC : Java 9의 기본 GC 알고리즘, Heap 영역을 동일한 사이즈의 Region으로 분할, 각 지역에 Eden, Survivor 등의 역할 부여
      - Humongous 영역 신설 : Region 크기의 50% 넘는 큰 객체 할당
      - Avaiable/Unused 영역 신설 : 사용되지 않는 지역
      - G1 Young GC : MinorGC를 거쳐 살아남은 객체들은 Survivor 지역으로 이동된 후 Eden 지역은 Available/Unused로 변경
      - G1 OLD GC
        1. Initial Mark : Old 지역의 객체들이 참조하는 객체를 Survivor 지역에서 찾습니다. Stop-the-World 발생.
        2. Root Region Scan : Initial Mark 과정에서 살아남은 객체가 있는 Survivor 지역을 스캔합니다. Stop-the-World 없이 Application Thread와 동시에 실행됩니다.
        3. Concurrent Mark : 전체 Heap 에서 GC의 대상이 될 지역을 Mark 합니다.
        4. Remark : Concurrent Mark 과정에서 GC의 대상이라고 판단된 지역 메모리에서 해제하고 Available/Unused 로 변경합니다. Stop-the-World 발생.
        5. Clean up : 살아남은 객체가 가장 적은 지역의 미사용 객체를 제거합니다.  Stop-the-World 발생.
        6. Copy : Clean up 과정에서 완전히 비워지지 않은 지역의 살아남은 객체들을 Available/Unused 지역으로 복사하여 Compaction을 수행합니다.  Stop-the-World 발생.
  6. [Z GC](https://d2.naver.com/helloworld/0128759) : JDK 15버전에서사용, 조금 더 큰 메모리에서 효율적으로 GC(8mb ~ 16TB)
      - Z pages라는 G1 GC의 Region과 비슷한 개념 사용
      - page는 2의 배수의 크기로 동적으로 생성, 삭제 가능
      - colored pointer를 사용해 64bit 메모리에서 Mark 진행
        1. Finalizable : finalizer을 통해서만 참조되는 Object로 garbage로 보시면 됩니다
        2. Remapped : 재배치 여부를 판단하는 Mark
        3. Marked 1 : Live Object
        4. Marked 0 : Live Object
      - Load barriers 사용해 STW없이 동시적으로 재배치 진행(Remap mark, Relocation set 활용)
        1. Remap bit 1 이면 참조 주소 리턴
        2. bit 0이고 Relocation Set(garbage가 있는 z page)에 있다면 Remap bit를 1로 변환하고 주소 리턴
        3. Relocation Set에 있다면 참조주소를 새로운 주소로 업데이트하고 주소 리턴
        4. Relocation Set에 없다면 object를 재배치하고 새로운 주소 리턴
      - 실행순서
        1. Pause Mark Start : ZGC의 Root에서 가리키는 객체 Mark 표시 (Live Object) > STW
        2. Concurrent Mark/Remap : 객체의 참조를 탐색하며 모든 객체에 Mark를 표시 한다(필요없는 객체 coloring)
        3. Pause Mark End : 새롭게 들어온 객체들의 대해서 Mark를 표시한다 > STW
        4. Concurrent Pereare for Reloc : 재배치 하려는 z page를 찾아 RelocationSet에 배치한다
        5. Pause Relocate Start : 모든 루트참조의 재배치를 진행하고 업데이트한다 > STW
        6. Concurrent Relocate : 이후 Load barriers를 사용하여 모든 객체를 재배치 및 참조를 수정한다