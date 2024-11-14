## 05/06

### [CQRS](https://junuuu.tistory.com/891)
 - CRUD에서 R와 CUD를 분리하는 것

 - 적용방법
  1. 단일 DB
   - 데이터베이스를 분리하지 않고 데이터 모델만 쓰기 모델과 읽기 모델로 분리
  2. DB 분리
   - 쓰기 전용과 읽기 전용 데이터베이스를 분리하고 동기화
  3. 이벤트 소싱
   - 데이터에 대한 변경 사항인 이벤트를 영속적으로 저장
   - 이를 통해 명령 및 쿼리 모델을 동시에 유지

 - 장점
  1. 복잡성 감소
  2. 성능 최적화(읽기 전용 API, DB 구성)
  3. 스케일링
  4. 도메인 모델 유연성

### [인덱스](https://spiderwebcoding.tistory.com/6)
 - 인덱스 : 테이블의 동작속도(조회)를 높여주는 자료구조
 - 조회속도는 빨라지지만 UPDATE, INSERT, DELETE의 속도는 저하된다는 단점(Table의 index 색인 정보를 갱신하는 추가적인 비용 소요)

 - 단일 인덱스, 다중컬럼 인덱스
  1. 단일 : name, address를 각각 인덱스 설정 -> name, address 중 빨리 검색되는것으로 진행
  2. 다중 : (name, address)를 묶어서 인덱스 설정 -> 해당 두개 만족하는 값 바로 검색
 
 - 인덱스 설계시 주의사항
  1. 무조건 많이 설정하지 않는다. (한 테이블당 3~5개가 적당 목적에 따라 상이)
  2. 조회시 자주 사용하는 컬럼
  3. 고유한 값 위주로 설계
  4. 카디널리티가 높을 수록 좋다 (= 한 컬럼이 갖고 있는 중복의 정도가 낮을 수록 좋다.)
  5. INDEX 키의 크기는 되도록 작게 설계
  6. PK, JOIN의 연결고리가 되는 컬럼
  7. 단일 인덱스 여러 개 보다 다중 컬럼 INDEX 생성 고려
  8. UPDATE가 빈번하지 않은 컬럼
  9. JOIN시 자주 사용하는 컬럼
  10. INDEX를 생성할 때 가장 효율적인 자료형은 정수형 자료(가변적 데이터는 비효율적)

 - 인덱스를 안타는 경우
  1. where절에 사용안한 경우
  2. 함수사용
  ```sql
    CREATE INDEX idx_birthdate ON employees(birthdate);
    SELECT * FROM employees WHERE YEAR(birthdate) = 1990;
  ```
  3. 데이터 타입 불일치
  ```sql
  CREATE INDEX idx_id ON users(id);
  SELECT * FROM users WHERE id = '123';  -- '123'은 문자열
  ```
  4. 인덱스 카디널리티 낮을때
  5. ORDER BY 복수의 키 사용
  ```sql
  CREATE INDEX idx_name_age ON people(name, age); -- asc로 설정
  SELECT * FROM people WHERE name = 'John' ORDER BY name, age DESC; -- desc로 조회
  ```
  6. 연속하지 않은 컬럼에 대한 ORDER BY
  ```sql
  CREATE INDEX idx_name ON people(name);
  SELECT * FROM people WHERE age = 30 ORDER BY name; -- where절에는 적용 안됨
  ```
  7. GROUP BY와 ORDER BY의 컬럼이 다른 경우
  ```sql
  CREATE INDEX idx_department ON employees(department);
  SELECT department, COUNT(*) FROM employees GROUP BY department ORDER BY name; -- orderby 시 full scan
  ```

### Thread Local
- 프로세스 : 실행 중인 하나의 어플리케이션
- 쓰레드 : 한 프로세스 내에서 동작 되는 실행 흐름

- 멀티스레드 장점 
  1. CPU 사용률 향상 멀티 프로세싱에 비해 Context switch 오버헤드가 일어나지 않는다
  2. 자원을 공유

- 단점
  1. 동기화(Synchronization) : 공유자원 점유시 다른 쓰레드 wait로 느려짐
  2. 교착상태(Deadlock) : 서로 공유자원 요청해서 교착

- Thread Safe : 공유자원 해결(Synchronized 사용)
  1. 세마포어 : 공유된 자원의 데이터를 여러 프로세스가 접근하는 것을 막는 것
  2. 뮤텍스 : 공유된 자원의 데이터를 여러 쓰레드가 접근하는 것을 막는 것

- Thread Local : 오직 한 쓰레드에 의해서 읽고 쓰여질 수 있는 변수

- 필요한 이유(동시성 문제)
  - Spring에서 bean을 등록하게 되면 해당 객체는 단 1개만 만들어져서 모든 Thread가 공유하여 사용
  - 여러 쓰레드가 동시에 같은 인스턴스의 필드 값을 변경하면서 발생
  - 지역변수에서는 발생 X, 읽기만 할때는 발생X
  - 인스턴스 필드, static 값 변경할떄 발생

- 활용 
  1. 사용자 인증 정보 Spring Security 에서 사용자마다 다른 인증 정보를 사용할때 & Session정보
  2. 쓰레드에 안전해야 하는 데이터를 보관 할때

- 주의점 : Thread 의 사용이 끝나는 시점 ThreadLocal을 초기화 필요
  1. WAS(tomcat)은 Thread pool 기반으로 동작
  2. ThreadLocal을 사용할 때 사용 후에 비워주지 않는다면 해당 Thread를 부여받게 되는 다른 사용자가 기존에 세팅된 ThreadLocal의 데이터를 공유

### Callable 인터페이스
- Runnable : 스레드 생성 및 실행
  - run 단일메소드, 파라미터 리턴 없음
```java
public class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("Runnable: Doing work...");
    }
}
```

- Callable : 제네릭 사용해 결과 return가능
```java
public class MyCallable implements Callable<String> {
    @Override
    public String call() throws Exception {
        System.out.println("Callable: Doing work...");
        return "Callable Result";
    }
}
```

- Future 
  - Callable은 잔여 쓰레드가 없어 실행 연기 될 수 있음
  - 미래에 완료된 Callable 반환값을 구하기 위해 사용(Future.get())
```java
public class FutureExample {
    public static void main(String[] args) throws Exception {
        ExecutorService executorService = Executors.newSingleThreadExecutor();

        // Callable 작업 제출
        Future<String> future = executorService.submit(new MyCallable());

        // 작업의 완료 여부를 확인하고 결과를 가져옴
        if (!future.isDone()) {
            System.out.println("Task is not done yet...");
        }

        String result = future.get(); // 작업이 완료될 때까지 대기 후 결과 반환
        System.out.println("Result from Callable: " + result);

        executorService.shutdown();
    }
}
```

- Excecutor : 쓰레드 풀 구현 중 일부
  - 등록된 작업(Runnable)을 실행하기 위한 인터페이스
  - 작업 등록과 작업 실행 중에서 작업 실행만을 책임짐

- ExecutorService 
  - 작업(Runnable, Callable) 등록을 위한 인터페이스
  - Executor를 상속받아서 작업 등록 뿐만 아니라 실행을 위한 책임도 있음
  - 쓰레드 풀은 기본적으로 ExecutorService 인터페이스를 구현
  - 멀티쓰레드의 구현을 위해 사용
  - 메서드
    1. shutdown() : 라이플 사이클 관리
    2. invokeAll() : 비동기 작업을 위한 메서드
      - 주어진 작업들을 동시에 모두 실행하고, 전부 끝나면 각각의 상태와 결과를 갖는 List을 반환
      - 모든 결과가 나올 때까지 대기하는 Blocking 방식
      - 최대 쓰레드 풀의 크기만큼 작업을 동시에 실행
```java
public class ExecutorServiceExample {
    public static void main(String[] args) throws Exception {
        ExecutorService executorService = Executors.newFixedThreadPool(3); // 최대 3개의 스레드 풀 생성

        List<Callable<String>> tasks = new ArrayList<>();
        for (int i = 0; i < 5; i++) {
            int taskNumber = i + 1;
            tasks.add(() -> {
                return "Task " + taskNumber + " result";
            });
        }

        List<Future<String>> results = executorService.invokeAll(tasks); // 모든 작업 실행 후 결과 목록 반환

        for (Future<String> result : results) {
            System.out.println("Result: " + result.get()); // 각 작업의 결과 가져오기 (Blocking)
        }

        executorService.shutdown();
    }
}
```

- Executors
  - Executor, ExecutorService를 구현한 쓰레드 풀을 손쉽게 생성
  - 쓰레드의 개수 및 종류 설정 가능
```java
public class ExecutorsExample {
    public static void main(String[] args) throws Exception {
        ExecutorService executorService = Executors.newFixedThreadPool(2);

        // Runnable 작업 제출
        executorService.submit(new MyRunnable());

        // Callable 작업 제출
        Future<String> future = executorService.submit(new MyCallable());
        System.out.println("Callable Result: " + future.get());

        executorService.shutdown();
    }
}
```

### 동시성관리 Java
1. volatile 
  - 변수를 메인 메모리에 저장
  - 모든 스레드가 변수의 최신 값을 볼 수 있도록 보장
  - 복합 작업(증가 연산 등)에서는 원자성을 보장하지 않음 -> 여러쓰레드가 하나에 변수에 write할수있기때문
```java
public class SharedObject {
    public volatile int counter = 0;
}
```

2. synchronized
  - 메소드나 코드 블록에 사용되어, 한 시점에 하나의 스레드만 해당 코드 영역을 실행
  - 무거운잠금(OS가 스레드 동기화 관리 - Context Switch오버헤드 큼)
  - 자동으로 Lock이 잠기고 풀림
```java
public synchronized void add(int value) {
    count += value;
}
```

3. Atomic
  - CAS(Compare-And-Swap) 연산
  - 가벼운잠금(CPU의 원자적 연산을 통해 데이터를 동기화 - 빠름, 락프리)
```java
import java.util.concurrent.atomic.AtomicInteger;

AtomicInteger count = new AtomicInteger(0);
count.incrementAndGet(); // 원자적으로 증가
```

4. ReentrantLock
 - 수동으로 lock 관리(lock(), unlock(), isLocked(), tryLock())
 - 공정한 락 제공 : public ReentrantLock(boolean fair)을 줌으로서 가장 오래대기한 스레드에 락을 점유할수있게
 - tryLock()을 통해 락 획득 대기시간 지정 가능
 - synchronized보다 유연하게 잠금을 관리

```java
Lock lock = new ReentrantLock();
try {
    lock.lock();
    // 임계 영역
} finally {
    lock.unlock();
}
```
   
### 멀티스레딩에서 발생할 수 있는 문제
- 멀티스레딩 또는 멀티프로세스 환경에서 자원에 동시에 접근하려고 할 때 발생하는 문제
  1. 데드락 : 서로의 리소스를 기다리면서 무한대기 -> 자원접근순서 정하거나 타임아웃설정으로 해결
  2. Race Condition : 여러 스레드가 동시에 접근하면서 데이터 일관성 꺠지는 문제 -> synchronized, reentrantLock등 사용필요
  3. Starvation : 특정 스레드가 계속해서 자원할당을 못받는 상태 -> ReentrantLock(fair)세팅
  4. LiveLock : 두 스레드가 상대방 작업 기다리면서 무한 실행 반복하는 상태 -> ReentrantLock(fair)세팅

### 동시성 컬렉션
- 멀티스레드 환경에서 데이터 구조에 안전하게 접근할 수 있도록 설계된 데이터 구조
- ConcurrentHashMap, ConcurrentLinkedQueue, CopyOnWriteArrayList

### 오토박싱, 언박싱
- 오토박싱 : int 타입의 값을 Integer 클래스 객체에 할당할 때 자동으로 Integer 객체가 생성
- 언박싱 : Integer 객체에서 int 값으로 자동 변환
- 주의점
  - 추가적인 객체 생성이 일어날 수 있으므로 성능 저하가 발생
  - null 값을 갖는 래퍼 클래스 객체를 언박싱할 때 NullPointerException
