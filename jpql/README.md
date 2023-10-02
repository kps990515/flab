## JPQL

### 기본
 - 테이블이 아닌 엔티티 객체를 대상으로 검색하기 위함
 - 객체지향 쿼리
 - SQL을 추상화해서 특정 DB에 의존적이지 않음
 - 결국 SQL로 변환되서 실행

### QueryDSL
 - 자바로 JPQL 작성가능
 - JPQL 빌더 역할
 - 컴파일 시점에 문법오류 탐색 가능
 - 동적쿼리 작성 편함

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