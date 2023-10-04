## JPA_FIRST

### SQL VS JPA
 - SQL의 문제점
    1. 반복적 CRUD 코드
    2. 객체지향 VS 관계형 데이터베이스
        - 객체를 SQL로 변한해서 RDB에 저장(개발자가)
        - 객체에는 상속이 있지만 RDB에는 없음
        - 객체는 참조를 사용해서 객체 조회, RDB는 외래키를 사용해서 다른 테이블 조회(객체를 테이블에 맞추어 모델링)
        - SQL에서는 처음 조회하는 데이터에 연관된 데이터만 탐색(해당 객체에 모든 참조 테이블에 대한 null여부 확신 어려움)
        - 같은 값으로 SQL 조회해서 객체에 넣어도 다른 객체로 인식
        - 객체답게 모델링할수록 SQL 매핑이 계속 늘어남
 - JPA
    - 객체를 자바 컬렉션에 저장하듯이 DB에 저장할수 없을까? 해서 나옴
    - Object Relational Mapping(객체, DB 매핑)
    - 애플리케이션과 JDBC사이에서 동작
    - 생산성, 유지보수, 패러다임불일치 해결(객체 상속관계인경우 하위 자동 crud, 동일 조회건은 동일성보장)
    - 지연/즉시로딩 둘다 지원, 캐싱, 성능최적화(한 transaction crud모아서 처리)

### JPA
 - hibernate.dialect : JPA는 특정 DB에 종속적이지 않기에 해당 부분에 사용할 DB를 명시(H2, Oracle ...)

 - 동작방식
   1. persistence.xml에서 설정정보 읽음
   2. EntityManagerFactory생성(하나만 생성해서 어플리케이션 전체에서 공유)
   3. EntitiyManager 생성(1회성, 쓰레든간 공유X)
   4. Transaction 실행(JPA의 모든 데이터는 트랜잭션 안에서 실행)

 - JPQL
   - JPA는 SQL을 추상화한 JPQL이라는 객체 지향 쿼리 언어 제공
   - JPQL은 테이블이 아닌 엔티티 객체를 대상으로 검색하는 쿼리(SQL은 데이터베이스 테이블을 대상으로 쿼리)
   - SQL을 추상화해서 특정 데이터베이스 SQL에 의존X


```java
    public static void main(String[] args){
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");
        EntityManager em = emf.createEntityManager();
        EntityTransaction tx = em.getTransaction();
        tx.begin();

        try{
            //insert
            Member member = new Member();
            member.setId(1L);
            member.setName("A");
            em.persist(member);

            //update
            Member updateMember = em.find(Member.class, 1L);
            updateMember.setName("B");

            //Delete
            Member deleteMember = em.find(Member.class, 1L);
            em.remove(deleteMember);

            //JPQL
            List<Member> findMembers = em.createQuery("select m from Member as m", Member.class)
                    .setFirstResult(5) // 페이징처리
                    .setMaxResults(10)
                    .getResultList();

            tx.commit();
        }catch (Exception e){
            tx.rollback();
        }finally {
            em.close();
        }
        emf.close();
    }
```
