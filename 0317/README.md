## 03/17

### [JPA N+1](https://velog.io/@sweet_sumin/JPA-N1-%EC%9D%B4%EC%8A%88%EB%8A%94-%EB%AC%B4%EC%97%87%EC%9D%B4%EA%B3%A0-%ED%95%B4%EA%B2%B0%EC%B1%85%EC%9D%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80%EC%9A%94)
 - 정의 : 한번 조회할 것을 N개 종류의 데이터를 추가로 조회해서 총 N+1번 DB조회하는 문제
 - 원인
   - 1:N, N:1 연관관계에서 발생
   - 즉시로딩 : 연관관계있는 데이터도 조회하여 바로 N+1발생
   - 지연로딩 : 필요로 인해 연관관계 데이터 조회 시 N+1 발생
 - 예시 : 사용자 테이블(5명), 주문 테이블
   - 사용자 조회(1번) select * from User
   - 사용자와 연관된 주문 데이터 조회(5번) select * from order where userId = ?
   - N+1발생
 - 해결법
   1. fetch Join : 처음부터 연관된 데이터 한번에 조회하는 방식
      - 문제점 : 중복데이터 발생(유저1, 주문1), (유저1, 주문2)... 유저1 계속 쌓임
      - 해결법 : 유저에는 distinct (select distinct u from User u join fetch u.order)
      - 장점 : 한번의 쿼리로 전체 조회 가능
      - 단점 : 페이징처리 불가, 1:N관계가 2개인 엔티티는 사용 불가

### [JPA OSIV](https://hstory0208.tistory.com/entry/SpringJPA-OSIV-%EC%A0%84%EB%9E%B5%EC%9D%B4%EB%9E%80-%EC%96%B8%EC%A0%9C-%EC%82%AC%EC%9A%A9%ED%95%B4%EC%95%BC-%ED%95%A0%EA%B9%8C)
- Open Session In View : 영속성 컨텍스트를 View 영역까지 열어두는 것
- 기본적으로 스프링부트에서는 True

- true인 경우
![img](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdNhmS8%2FbtskiABFOGa%2FkgfDsLBDBvtE2CNyghK17K%2Fimg.png)
   - API 시작~끝까지 영속성 컨텍스트 & DB 커넥션을 유지
   - 장점 : 지연로딩 사용 가능
   - 단점 : API가 오래걸릴 경우 커넥션을 잡고 있어 DB커넥션 부족 발생

- false인 경우
![img](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcMbAvr%2FbtskAFH5ptJ%2FLO3VQqpaNc5kTpIYnifOd1%2Fimg.png)
   - 트랜잭션 종료 시 영속성 컨텍스트 종료, DB 커넥션 반환
   - 장점 : 커넥션 리소스 절약
   - 단점 : 모든 지연로딩을 커넥션안에서만 처리가능, View에서 예기치못한 수정발생할 수 있음

### [@Transactional](https://medium.com/gdgsongdo/transactional-%EB%B0%94%EB%A5%B4%EA%B2%8C-%EC%95%8C%EA%B3%A0-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-7b0105eb5ed6)
 - @Transactional은 메서드나 클래스에 트랜잭션을 적용
 - 어트랜잭션은 데이터베이스 작업이 모두 성공하면 커밋(commit)하고, 하나라도 실패하면 롤백(rollback)해서 데이터의 일관성을 보장하는 기능
 - 작동방식
    - AOP(Aspect-Oriented Programming, 관점 지향 프로그래밍)를 통해 프록시 객체가 생성
    - 프록시가 메서드 호출을 가로채고, 트랜잭션 관리 코드를 삽입하여 작업을 처리
    - 작업이 끝나면 프록시가 커밋(성공) 또는 롤백(실패)을 결정

 - 속성
   1. REQUIRED (기본값): 트랜잭션이 없으면 새 트랜잭션 시작, 이미 있으면 재사용
   2. SUPPORTS: 트랜잭션이 있으면 사용하고, 없다면 트랜잭션 없이 실행
   3. MANDATORY: 트랜잭션이 반드시 있어야 하며, 없다면 예외를 발생
   4. REQUIRES_NEW: 항상 새로운 트랜잭션을 시작, 트랜잭션이 있다면 일시 정지
   5. NOT_SUPPORTED: 트랜잭션을 사용하지 않도록 설정, 트랜잭션이 있다면 일시 정지
   6. NEVER: 트랜잭션을 사용하지 않으며, 트랜잭션이 있을 경우 예외를 발생
   7. NESTED: 기존 트랜잭션과 중첩된 트랜잭션을 생성하고, 없다면 새로 트랜잭션을 생성합니다.

 - 주의점
   - public 사용필요
   - 다른 어노테이션과의 충돌 고려(@Secure 인증하기 전 @Transactional 실행 등)
   - Service 계층에서 주로 사용
   - SimpleJpaRepository클래스는 @Transactional 사용(조회시 @Transactional(readOnly = true)통해 DB락 없이 읽기성능향상)
   - checked Exception 예외처리 : @Transactional(rollbackFor={Exception.class})을 통해 rollback 하게 할 수 있음
   - Kotlin에서는 check, unchecked구분이 없어서 다 롤백됨
      - 해결법 
      ```java
      @Transactional(noRollbackFor = [SomeException::class])
      fun someMethod() {
         // 이 메서드 내에서 SomeException이 발생해도 롤백하지 않음
      }

      @Throws(IOException::class)
      fun readFile(name: String): String {
         return File(name).readText()
      }
      ```

### 분산DB에서의 partial commit
- 정의 : 일부 DB는 commit했으나, 다른 일부는 실패하거나 아직 커밋하지 못한 상태
- 문제점 : 데이터 불일치
- 해결법
   - 2단계 커밋 프로토콜(Two-Phase Commit, 2PC) 
      1. 준비단계 : 모든 노드에게 트랜잭션 커밋 준비를 지시 -> 준비여부 응답
      2. 커밋단계 : 트랜잭션 커밋 지시(하나라도 준비여부 응답없으면 롤백)

### 분산 서버에서의 분산 Lock
- 네트워크를 통해 분산되어 있는 여러 시스템 간 공유 자원에 대한 접근을 동기화하는 메커니즘

### MSA에서 Rollback API
- 필요한 이유 : 서비스 간의 트랜잭션이 실패했을 때 일관성을 유지하고 데이터 무결성을 보호하기 위해

### DB 기본 락
 - 공유 Lock : 읽기 작업에서 사용, 동시읽기는 가능하지만 쓰기 금지
 - 배타적 Lock : 쓰기작업에서 사용, 해당 트랜잭션만 쓰기읽기 가능

### REPEATABLE READ에서 phantom read를 막는법(inno DB)
 - MVCC(Multi-Version Concurrency Control)
   - 읽기 : 데이터를 읽을 때 해당 데이터의 특정 버전을 읽음(스냅샷)
   - 쓰기 : 데이터를 수정할 때, 새로운 데이터 버전을 생성해 이전 버전의 데이터는 여전히 유지(쓰기를 해도 읽기 작업 비차단)

 - Next-Key Lock
   - SELECT * FROM child WHERE id > 100 FOR UPDATE; 쿼리를 날렸을 때 100 보다 큰 첫 번째 레코드에 대해 락
   - Gap Lock + Record Lock
   - 100, 102에는 데이터 O / 101 데이터 X 때 Next-Key Lock을 통해 101 insert를 막아 Phantom Read방지
