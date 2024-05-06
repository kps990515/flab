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


