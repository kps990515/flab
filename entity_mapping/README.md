## Entity Mapping

### Entity Mapping
 - 기본문법
    1. 객체 테이블 매핑 : @Entity, @Table
    2. 필드 컬럼 매핑 : @Column
    3. 기본키 매핑 : @Id
    4. 연관관계 매핑 : @ManyToOne, @JoinColumn

 - @Entity
    - Entity가 붙은 클래스는 JPA과 관리
    - 기본 생성자 필수
    - final클래스, enum, interface inner클래스 사용 금지
    - 저장할 필드에 final 금지

 - @Table : 엔티티와 매핑할 테이블 지정(@Table(name="MBR"))

 - 컬럼
    - @Column : 컬럼매핑
    - @Temporal : 날짜타입매핑(Time, TimeStamp...) LocalDate, LocalDateTime쓸 경우에는 필요없음
    - @Enumerated : enum타입매핑(String, Ordinal이있지만 ordinal사용하지 않기)
    - @Lob : BLOB, CLOB
    - @Transient : 특정필드를 컬럼에 매핑하지 않음

 - @Column
    - name : 필드와 매핑할 컬럼이름
    - insertable, updatable : 등록 변경 가능 여부(기본 TRUE)
    - nullable
    - unique
    - columnDefinition(varchar(100), default 'EMPTY')
    - length
    - precision, scale      

### 스키마 생성
 - DDL을 애플리케이션 실행시점에 자동생성(객체 중심)
 - dialect를 활용해 DB에 맞는 적절한 DDL 생성
 - DDL 생성기능은 DDL을 자동생성할때만 사용되고 JPA 실행로직에는 영향 주지 않는다
 - hiberante.hbm2ddl.auto 옵션 사용시
    - create : 기존테이블 drop 후 재생성(개발단계만)
    - create-drop : 종료시점에 테이블 drop(개발단계만)
    - update : 스키마 변경분만 반영(개발,테스트)
    - validate : 정상매핑되었는지 확인(테스트, 스테이지, 운영)
    - none
 - 제약조건 추가
    - @Column(nullable=false, length=10)
    - @Table(uniqueContraints ...)

### 기본키 매핑
 - 직접할당 : @Id
 - 자동생성 : @GeneratedValue
    - IDENTITY : DB에 위임
        - Auto_increment의 경우는 DB에 insert한 후에 값을 알수있기에 em.persist하는 순간 실제 DB에 저장된다
    - SEQUENCE 
        - @SequenceGenerator필요(테이블마다 다른 시퀀스 주기 위해)
        - DB에 insert한 후에 PK값을 알수있기에 em.persist하는 순간 실제 DB에 저장된다
        - allocationSize 옵션으로 미리 DB에서 PK값을 받아와 WAS에서 PK할당
    - TABLE : 키 생성 전용테이블 만들어 시퀀스 흉내내기(성능이 느림)
    - AUTO : DB에 맞춰 자동생성