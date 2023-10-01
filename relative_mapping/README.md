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

