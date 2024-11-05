## JPQL

### 기본
 - 데이터베이스 테이블이 아닌 JPA 엔티티와 그 필드를 대상으로 작성
 - 객체지향 쿼리
 - SQL을 추상화해서 특정 DB에 의존적이지 않음
 - 결국 SQL로 변환되서 실행
 - 단점 : 컴파일 타임에 오류를 확인할 수 없음
```java
String jpql = "SELECT m FROM Member m WHERE m.name = :name";
List<Member> members = em.createQuery(jpql, Member.class)
                         .setParameter("name", "John")
                         .getResultList();
```

### QueryDSL
 - 자바로 JPQL 작성가능
 - JPQL 빌더 역할
 - 컴파일 시점에 문법오류 탐색 가능
 - 동적쿼리 작성 편함
```java
QMember member = QMember.member; // Q클래스는 QueryDSL이 엔티티 클래스를 바탕으로 자동 생성하는 클래스

List<Member> members = new JPAQuery<>(em)
                           .select(member)
                           .from(member)
                           .where(member.name.eq("John"))
                           .fetch();
```

### Native SQL
 - SQL을 직접사용하는 기능
 - JPQL로 해결할수없는 특정 데이터베이스에 의존적인 기능(connect by, 힌트 등)

### JPQL 문법
 - 엔티티와 속성은 대소문자 구분(Member, age)
 - JPQL 키워드는 대소문자 구분 x(WHERE, where)
 - 엔티티이름 사용
 - 별칭은 필수(as 생략가능)
 - TypeQuery : 반환타입이 명확할때 사용
 ```java
 TypedQuery<Member> query = em.createQuery("SELECT m FROM Member m", Member.class);
 ```
 - Query : 반환타입이 명확하지 않을 때 사용
 ```java
 Query query = em.createQuery("SELECT m.username, m.age from Member m");
 ```
 - query.getResultList() : 반환값이 여러개일때(결과값 없으면 빈 리스트 반환)
 - query.getSingleResult() : 반환값이 하나일때(결과값이 둘 이상, 없으면 무조건 에러)

 - 파라미터 바인딩
 ```java
 SELECT m FROM Member m where m.username=:username 
 query.setParameter("username", usernameParam);
 ```

### 프로젝션
 - Select절에 조회할 대상을 지정하는 것 
 - 대상 : 엔티티, 임베디드 타입, 스칼라타입(숫자, 문자 등 기본데이터형)

 ```java 
 SELECT m FROM MEMBER m // 엔티티 -> 영속성 컨텍스트로 관리됨
 SELECT m.address FROM MEMBER m // 임베디드 타입
 SELECT m.username, m.age FROM MEMBER m //스칼라 타입
 ```

 - 여러값 조회 방법(m.username, m.age)
    1. Query타입 조회 
    ```java
    Query query = em.createQuery("SELECT m.username, m.age FROM MEMBER m")
    ```
    2. Object[] 타입 조회
    ```java
    List resultList = em.createQuery("SELECT m.username, m.age FROM MEMBER m").getResultList();
    Object o = resultList.get(0);
    Object[] result = (Object[]) o; // object[0] : username, object[1] : age
    ```
    3. new명령어로 조회
    ```java
    // MemberDTO 클래스를 만들고 jpql.MemberDTO()로 생성자 호출
    List<MemberDTO> resultListem.createQuery("SELECT new jpql.MemberDTO(m.username, m.age) FROM MEMBER m", MemberDTO.class).getResultList();
    ```

### 페이징    
 - JPA는 페이징을 두 API로 추상화
    1. setFirstResult(int startPosition) : 조회시작위치(0부터 시작)
    2. setMaxResults(int maxResult) : 조회활 데이터 수
 ```java
 List<Member> result = em.createQuery("SELECT m FROM MEMBER m order by m.age", Member.class)
                        .setFirstResult(0)
                        .setMaxResults(10)
                        .getResultList();
 ```   

### 조인
1. 내부조인
```java
select m from Member m inner join m.team t
```
2. 외부조인
```java
select m from Member m left join m.team t
```
3. 세타조인
```java
select m from Member m, Team t where m.username = t.name
```

- 조인 대상 필터링(on)
```java
select m, t from Member m left join m.team t on t.name='A'
```
### 서브쿼리
 - From절에서 서브쿼리 사용 불가능(Join으로 풀어야함)
```java
select m from Member m
where m.age > (select avg(m2.age) from Member m2)

select m from Member m
where (select count(o) from Order o where m = o.member)
```
- [NOT] EXISTS : 서브쿼리에 결과가 존재하면 참
    - {ALL | ANY | SOME} (subquery) : ALL 모두참, ANY/SOME 같은의미 하나라도 만족하면 참
    ```java
    // 전체 상품 각각 재고보다 주문량이 많은 주문(주문량 100 > 사과 10, 배 20, 딸기 50)
    select o from Order o
    where o.orderAmount > ALL(select p.stockAmount from Product p)

    // 어떤 팀이든 팀에 소속된 회원
    select m from Member m
    where m.team = ANY(select t from Team t)
    ```
- [NOT] IN (subquery) : 서브쿼리의 결과 중 하나라도 같은 것이 있으면 참    

### JPQL 타입 표현
 - 문자 : 'HELLO', 'She''s'
 - 숫자 : 10L, 10D, 10F
 - Boolean: TRUE, FALSE
 - ENUM : jpabook.MemberType.ADMIN(패키지명 포함)
 - 엔티티 : TYPE(m) = MEMBER

### 조건식
 - coalesce : 하나씩 조회해서 null이 아니면 반환
 ```java
 //m.username이 null이면 '이름없는 회원'으로 반환
 select coalesce(m.username, '이름없는 회원') from Member m 
 ```
 - nullif : 두값이 같으면 null반환 아니면 첫번째 값 반환 
 ```java
 // 사용자이름이 '관리자'이면 null 반환 아니면 m.username 반환
 select nullif(m.username, '관리자') from Member m
 ```

### JPQL함수
 - CONCAT, SUBSTRING, TRIM, LENGTH...
 - LOCATE : 위치찾기
 ```JAVA
 select locate('de','abcdefg') from Member m
 ```
 - SIZE, INDEX
 ```java
 // 컬렉션 크기 
 select size(t.members) from Team t
 ```

 - 사용자 정의함수 : SQL Dialect를 상속받아 새로운 클래스 생성 > registerFunction 한 뒤 dialect변경
 ```java
 select function('group_concat', i) from Member m
 ```
