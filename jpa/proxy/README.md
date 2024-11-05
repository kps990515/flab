## Proxy

### Proxy
 - em.find() : DB통해 실제 엔티티 객체 조회
 - em.getReference() : 데이터베이스에서 값을 가져오지 않고, 실제 엔티티의 참조만 제공하는 프록시 객체 반환
   - 필요할 때 데이터베이스에 접근하여 데이터를 가져오는 지연 로딩을 사용
   - 엔티티의 일부 필드만 사용할 때 사용
   - 엔티티 간 관계만 설정하고 실제 데이터를 사용하지 않는 경우, getReference()를 사용해 메모리를 절약
     
 - 프록시 객체의 초기화
   1. getReference했을때는 실제 Entity상속받아 빈 클래스 생성(초기화X)
   2. 실제 값 호출시(getName()) 초기화
   3. 영속성 컨텍스트 통해 DB 조회 
   4. 실제 Entity 생성
   5. 프록시객체러 실제 Entity를 연결해서 값 전달

 - 프록시의 특성
   - 처음 사용할때 한번만 초기화(프록시객체는 데이터없이 존재 -> 필드 접근하면 DB조회 -> 이후에는 DB조회값 사용)
   - 초기화시 프록시 객체 자체가 실제 엔티티로 변경되는 것이 아니라, 프록시 객체가 실제 엔티티의 데이터와 연결
   - 프록시 객체는 원본 엔티티를 상속받은 클래스라 ==으로 비교하면 다른 객체로 인식, instance of사용 필요(if (proxy instanceof Member))
   - 조회시 영속성 컨텍스트에 이미 엔티티가 있으면 실제 엔티티 반환됨
   - 조회시 이미 연결된 프록시객체가 있는 경우에는 프록시객체로 반환됨
   - 준영속 상태(Persistence Context에서 관리되지 않는 상태)일때는 프록시 초기화하면 에러

 - 프록시 확인
   - 초기화여부확인 : emf.PersistenceUtil.isLoaded(entity)
   - 클래스확인방법 : entity.getClass().getName()
   - 강제초기화 : Hibernate.initialize(entity)

### 즉시로딩, 지연로딩
 - 지연로딩
```java
// Member와 Team은 연결되어있지만 Member만 조회하고 싶은경우
public class Member{
   @ManyToOne(fetch = FetchType.LAZY) //지연로딩
   @JoinColumn(name = "TEAM_ID")
   private Team team; // 처음에는 프록시객체로 넣어둠
}

Team team = member.getTeam() // 실제 team을 사용하는 시점에 프록시객체를 초기화
```

 - 즉시로딩
```java
public class Member{
   @ManyToOne(fetch = FetchType.EAGER)
   @JoinColumn(name = "TEAM_ID")
   private Team team;
}
```

 - 즉시로딩 주의(지연로딩만 사용하자)
   - 가급적 지연로딩만 사용
   - 즉시로딩 적용하면 예상하지 못한 SQL 발생
   - 즉시로딩은 JPQL에서 N+1 문제 발생
   - @ManyToOne, @OneToOne는 기본이 즉시로딩(LAZY로 변경해야함)
   - @OneToMany, @ManyToMany는 기본이 지연로딩

### 영속성 전이 : CASCADE
 - 특정 엔티티를 영속상태로 만들때 연관된 엔티티도 함께 하고 싶을때
 - 부모 엔티티 저장시 자식엔티티도
 - 영속성 전이는 연관관계랑 관련없음
 - 종류 : ALL, PERSIST, REMOVE, MERGE...

```java
public class Parent{
   @OneToMany(MappedBy = "parent", cascade = CascadeType.ALL)
   private List<Child> childList = new ArrayList<>();
}
```

### 고아객체
 - 부모엔티티와 연관관계가 끊어진 자식 엔티티를 자동으로 삭제
 - orpahnRemoval = true로 설정
 - 참조하는곳이 하나일때만 사용
 - 특정엔티티가 개인소유할때 사용
 - @OneToOne, @OneToMany만 가능

 ```java
public class Parent{
   @OneToMany(MappedBy = "parent", cascade = CascadeType.ALL, orphanRemoval=true)
   private List<Child> childList = new ArrayList<>();
}

 Parent parent1 = em.find(Parent.class, id);
 parent1.getChildren().remove(0) // -> child테이블에서도 삭제
 ```

### 영속성 전이 + 고아객체, 생명주기
 - CascadeType.ALL + orphanRemoval = true
 - 스스로 생명주기 관리하는 엔티티(Parent)에서 em.persist(), em.remove 사용
 - 부모 엔티티를 통해 자식의 생명주기 관리 가능
 - DDD의 Aggregate Root개념 구현 가능
