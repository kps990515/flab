## 02/25

### [Filter, Interceptor, Argument Resolver](https://velog.io/@dlduq29/Filter-Interceptor-Argument-Resolver)

 - Filter는 서블릿 컨테이너 레벨에서 작동하며 모든 요청에 대한 전처리와 후처리를 담당합니다.
 - Interceptor는 Spring MVC 내에서 작동하며, 컨트롤러 실행 전후의 처리에 좀 더 초점을 맞춥니다.
 - AOP는 애플리케이션의 다양한 부분에 걸쳐 재사용 가능한 공통 로직을 적용하는데 초점

 - Filter
   - Spring context외부 Dispatcher Servlet 전에서 동작
   - 스프링부트부터는 내장 웹서버 지원해서 관리가능
   - 웹 컨테이너에 의해 관리
   - 요청과 응답을 정제하는 역할
   - init(), doFilter(), destry()

 - Interceptor
   - Dispatcher Servlet 컨트롤러 사이에서 요청,응답 가공
   - preHandle() : 컨트롤러 전
   - postHandle() : 컨트롤러 후
   - afterCompletiont() : view렌더링 후

 - Argument Resolver
   - 컨트롤러에 요청에 들어온 값으로부터 원하는 객체를 만들어 내는 작업가능
   - 아규먼트 앞에 특정 어노테이션 확인후 해당 어노테이션에 맞는 작업 실행
   - @PathVariable, @Requestbody등이 속함
   - Interceptor이후에 동작

 - 차이
   - filter는 request, response 조작 가능 / interceptor는 불가
   - filter는 서블릿 영역에서 관리 / interceptor는 스프링 영역에서 관리
   - filter는 스프링과 무관하게 처리하는 작업, 컨트롤러 상관없이 전역처리필요작업
   - interceptor는 controller 요구사항 처리
   - interceptor는 boolean void만 반환 가능
   - Argument Resolver는 interceptor이후에 동작

### [DB isolation Level](https://mangkyu.tistory.com/299)
- 격리수준 : 여러 트랜잭션이 동시에 처리될 때, 특정 트랜잭션이 다른 트랜잭션에서 변경하거나 조회하는 데이터를 볼 수 있게 허용할지 여부

|   |Dirty Read|Non-Repeatable Read|Phantom Read|
|------|--------|--------|--------|
|READ UNCOMMITED|발생|발생|발생|
|READ COMMITTED|없음|발생|발생|
|REPEATABLE READ|없음|없음|발생|
|SERIALIZABLE|없음|없음|없음|

- dirty read : Commit되지 않은 데이터를 타 트랜잭션이 읽는 경우
- non-repeatable read : 같은 조회를 두번했을 때 데이터가 다른경우(타 트랜잭션이 데이터 수정+커밋해서)
- phantom read : 같은 조회를 두번했을 때 데이터가 다른경우(타 트랜잭션이 데이터 삽입+커밋해서)

- SERIALIZABLE 
   - 정의 : - 다른 트랜잭션과의 완전한 격리를 보장(순차 실행)
   - 가장 높은 격리 수준
   - Select에도 Lock걸림

- REPEATABLE READ(MySQL)
   - 정의 : 타 트랜잭션의 데이터 변경은 read에 영향을 미치지 않음
   - 변경 전 데이터 백업(dirty read, non-reapeatable read 방지)
   - 변경은 백업하지만 추가는 백업하지 않음(phantom read는 방지 어려움)
   - 동일한 트랜잭션에서는 read 후 데이터변경되었더라도 변경 전 데이터 read가능


- READ COMMITTED(ORACLE)
   - 정의 : Commit된 데이터 조회 가능
   - phantom read, non-repeatable read 발생

- READ UNCOMITTED
   - 정의 : uncommit된 변경, 추가된 데이터 조회 가능

### Transaction 상태
1. Begin
2. Active : 트랜잭션 실행중
3. Partially Commited: SQL이 실행되고 Commit요청
4. Commited : 트랜잭션 완로
5. failed : Partially Commited의 commit이 실패하면 failed
6. aborted : 트랜잭션 취소상태, 트랜잭션 이전 데이터로 원복

### Mysql Lock의 종류
- 레코드락
   - 테이블 레코드를 잠그는 락
   - Mysql에서는 DML이 발생하면 인덱스의 레코드를 잠금

- 갭락
   - 레코드와 레코드사이의 비어있는 간격을 잠금
   - 실제 레코드를 제외하고 데이터가 추가될 수 있는 인덱스 범위(위아래)에 락
   - 유일성 보장되는 PK, UK 인덱스 작업에서는 갭 락이 사용되지 않는다
```sql
//J로 시작하는 이름 수정시
SELECT * FROM member WHERE last_name LIKE "J%" FOR UPDATE;         // 쓰기 잠금(베타락)
SELECT * FROM member WHERE last_name LIKE "J%" LOCK IN SHARE MODE; // 읽기 잠금(공유락)
```
- Next Key Lock
    - 레코드락 + 갭락

- Auto Increment Lock
    - 동시에 여러개 INSERT 되더라도 순차적으로 증가하는 번호를 제공하기 위해 내부적으로 테이블 수준의 잠금
    - 새로운 데이터 저장하는 쿼리에서만 사용
    - 트랜잭션 실패해도 증가한 숫자는 줄어들지 않음

### [JPA 락](https://hudi.blog/jpa-concurrency-control-optimistic-lock-and-pessimistic-lock/)
- Isolation과 JPA 동시성 제어의 차이
   - JPA : 특정 엔티티에 대한 동시접근 방지
   - Isolation : 트랜잭션 동안에 일관된 데이터 read를 위해 사용

- Optimisitc Lock : 데이터 갱신 시, 충돌이 발생하지 않을 것이라고 낙관적으로 가정(어플리케이션 단)
    - @Version : 엔티티를 조회한 시점의 버전과 수정한 시점의 버전이 일치하지 않으면 예외
 ```sql
@Entity
public class Board {

  @Id
  private String id;
  private String title;

  @Version
  private Integer version;
}
 ```
 - LockModeType
   - NONE : @Version 기본값
      - 용도 : 조회 -> 수정까지 데이터가 변경되지 않았음을 보장
      - 동작 : 수정시점에 version 체크 및 증가
   - OPTIMISTIC : 조회부터 버전 체크
      - 용도 : 트랜잭션 시작~종료까지 데이터 변경되지 않음 보장
      - 동작 : 커밋하는 시점에 버전정보 체크
   - OPTIMISTIC FORCE INCREMENT : 엔티티 논리적 변경 시 버전정보 증가
      - 용도 : 1:N관계에서 N에서 데이터 추가 시 1의 버전 정보 증가(논리적 단위 엔티티묶음 관리)
      - 동작 : 엔티티 물리적 수정 없어도 커밋 시 버전정보 강제 증가

- Pessimistic Lock : DB락 사용
 - LockModeType
   - PESSIMISTIC_WRITE : 기본값
      - 용도 : 데이터베이스에 SELECT ... FOR UPDATE 를 사용하여 배타 락
   - PESSIMISTIC_READ : 읽기만 할때 사용
   - PESSIMISTIC_FORCE_INCREMENT : 유일하게 버전 정보를 사용

### Cardinality
 - 테이블 전체 행에서 특정컬럼이 얼마나 중복되는지 보여주는 지표
 - 중복도가 낮으면 cardinality가 높다
 - 카디널리티 높은 컬럼에 인덱싱하는전략