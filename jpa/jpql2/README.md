## JPQL2

### 경로표현식
 - . 을 찍어 객체 그래프를 탐색하는 것
 ```java
 select m.username // 상태필드
 from Member m
 join m.team t // 단일 값 연관 필드(ManyToOne, OneToOne 대상이 엔티티)
 join m.orders o // 컬렉션 값 연관필드(OneToMany, ManyToMany, 대상이 컬렉션)
 where t.name = 'A'
 ```
 - 단일 값 연관 경로
    - 묵시적 Inner Join
    - 계속 탐색 가능(m.team.name, m.team.user)

 - 컬렉션 값 연관 경로
    - 묵시적 Inner Join
    - 추가 탐색 불가능(일반적)
    - From절에서 명시적 조인을 통해 별칭을 얻으면 별칭을 통해 추가 탐색 가능
    ```java
    select m.username from Team t join t.members m
    ```
 - 중요! 이렇게 쓰지말고 명시적으로 필요한 JOIN을 사용하자   

### FETCH 조인
 - SQL 조인 X
 - JPQL 성능 최적화를 위해 제공하는 기능
 - 연관된 엔티티나 컬렉션을 SQL 한번에 함께 조회하는 기능
 - [LEFT [OUTER] | INNER] JOIN FETCH 조인경로

 - 단일 값 페치조인
```java
// 회원을 조회하면서 연관된 팀도 함께 조회(SQL 한번에)
// 회원1, 팀A(SQL)
// 회원2, 팀A(영속성 캐시)
// 회원3, 팀B(SQL)
select m 
from Member 
join fetch m.team
// SQL에서는
SELECT M.*, T.* 
FROM MEMBER M
INNER JOIN TEAM T
ON M.TEAM_ID = T.ID
``` 

 - 컬렉션 값 페치조인
```java
select t
from Team t 
join fetch t.members
where t.name = 'A'
```

 - 페치조인과 distinct
    - JPQL의 DISTINCT 2가지 기능
    1. SQL에 distinct를 추가
    2. 애플리케이션에서 엔티티 중복제거
```java
// 1. SQL의 distinct : A팀 회원1, A팀 회원2 --> 중복제거 실패
// 2. 엔티티 중복제거 : 두번째 A팀에 대한 중복 제거(A팀 한개 엔티티로 공통사용)
select distinct t
from Team t
join fetch t.members
where t.name = 'A'
```

 - 페치조인과 일반조인의 차이
    - 일반조인은 연관된 엔티티 정보를 조회하지 않음(join key값만 비교)
    - 페치조인은 연관된 엔티티 정보도 조회
