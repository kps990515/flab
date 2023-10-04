## Relative Mapping

### 기본
 - 데이터 중심 설계의 문제점
   - 테이블의 연관관계 클래스가 아닌 외래키를 객체에 넣음
   - 객체 그래프 탐색 불가
   

### 단방향
```java
   //Member 클래스 -> Team 1:N 단방향
    @ManyToOne
    @JoinColumn(name = "TEAM_ID")
    private Team team;

    //insert
    Team team = new Team();
    team.setName("TeamA");
    em.persist(team);

    Member member = new Member();
    member.setName("A");
    member.setTeam(team); //객체자체를 넣음
    em.persist(member);

    //조회
    Member findMember = em.find(Member.class, member.getId());
    Team findTeam = findMember.getTeam();

    //수정
    member.setTeam(teamB);
```

### 양방향
 - 테이블은 조인으로 어느테이블이든 다른쪽 테이블을 볼수 있지만(원래 양방향, FK를 보면 알수있음)
 - 객체는 연관관계의 주인을 정해줘야함(원래 단방향)
 - 객체를 양방향으로 참조하려면 단방향 연관관계를 2개 만들어야한다(A에서 B를 가지고 있고 + B에서도 A를 가지고있어야함)
 - 그래서 객체 중 하나에서 외래키를 관리해야한다(주인)

 - 연관관계의 주인
   - 객체의 두관계 중 하나를 주인으로 지정(외래키를 보유한 객체, 1:N에서 N쪽)
   - 연관관계의 주인만이 외래키를 관리(등록, 수정)
   - 주인 아닌쪽은 읽기만 가능
   - 주인은 mappedBy 속성 사용 X
   - mappedBy = "team" : Member클래스에있는 team객체로 매핑한 객체

```java
   @ManyToOne
   @JoinColumn(name = "TEAM_ID")
   private Team team;

   @OneToMany(mappedBy = "team")
   private List<Members> members = new ArrayList<>(); 

   Team findTeam = em.find(Team.class, team.getId());
   int memberSize = findTeam.getMembers().size(); //역방향 조회
``` 

#### 양방향 연관관계 주의점
   - 순수 객체상태를 고려해 양쪽 객체에 값을 설정해주자
   - 편의 메소드 생성을 통해 세팅
   - 양방향 매핑시에 무한루프 조심(toString(), Lombok, Json)
   - Member -> Team 조회 -> Team에 있는 MEmber조회 -> ....
   - 테이블설계시에는 단방향 매핑으로도 충분(양방향은 반대방향으로 조회하는 기능이 추가된것뿐)
   - JPQL에서 역방향으로 탐색할일이 많음(필요할때 추가)

```java
public void setTeam(Team team){
   this.team = team; // Member의 Team에 데이터 세팅하면
   team.getMembers().add(this); // Team의 Member에도 데이터 세팅
}
```

### 일대다 단방향
 - 기본적으로는 다대일이 정석(다쪽에 외래키가 있기때문)
 - 일대다는 외래키가 다쪽에 있지만 일이 연관관계의 주인(Team은 Member를 가지지만 Member는 팀이 없음)
 - 일쪽에서 다쪽의 외래키를 관리하는 특이한 구조(다쪽 외래키를 바꾸는 Update SQL 추가적으로 필요)
 - @JoinColumn 필수, 사용안하면 조인테이블 방식 사용(중간 테이블 생성)

### 일대다 양방향
 - 이런매핑은 공식적으로 X
 - 다쪽에다가 @JoinColumn(name="TEAM_ID", insertable=false, updatable=false) : FK읽기전용으로 만들기
 - Mappedby가 아님
 - 읽기전용 필드를 사용해서 양방향처럼 사용하는 방법

### 일대일
 - 외래키가 있는곳을 주인으로 설정
 - 외래키에 데이터베이스 유니크 제약조건 추가
 - 외래키 없는곳이 주인이 될 수 없음
 - 주테이블에 외래키(Member)
   - 장점 : 주 테이블만 조회해도 대상테이블(Team) 데이터가 있는지 확인가능
   - 단점 : 값이 없으면 외래키에 null 허용
 - 대상테이블에 외래키
   - 장점 : 1:N으로 관계 변경해도 테이블 구조 유지 가능
   - 단점 : 지연로딩으로 해도 즉시 로딩됨(Member가 Team을 가지고 있지 않기에 Member, Team 둘다 조회해야함)  

### 다대다
 - RDB에서는 테이블 2개로 다대다 관계 표현 불가능
 - 연결테이블 추가해서 일대다, 다대일로 풀어내야함
 - 근데 객체는 됨(하지만 지양)
 - 연결테이블이 연결만 하고 끝나지않음(추가적 데이터 주문시간, 주문수량.. 들어올수있음)

```java
@ManyToMany
@JoinTable(name="MEMBER_PRODUCT", joinColumns = @JoinColumn(name="PRODUCT_ID")), inverseJoinColumns = @JoinColumn(name="MEMBER_ID")
private List<Product> products = new ArrayList();

@ManyToMany(mappedBy = "products")
private List<Member> members = new ArrayList<>();
``` 

