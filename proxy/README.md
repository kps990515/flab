## Proxy

### Proxy
 - em.find() : DB통해 실제 엔티티 객체 조회
 - em.getReference() : 데이터베이스 조회를 지연하는 프록시 엔티티 (껍데기)객체조회
   - 실제 조회는 해당 값을 활용할때 함
 - 프록시 객체의 초기화
   1. getReference했을때는 실제 Entity상속받아 빈 클래스 생성(초기화X)
   2. 실제 값 호출시(getName()) 초기화
   3. 영속성 컨텍스트 통해 DB 조회 
   4. 실제 Entity 생성
   5. 프록시객체러 실제 Entity를 연결해서 값 전달

 - 프록시의 특성
   - 처음 사용할때 한번만 초기화
   - 초기화시 실제 엔티티로 바뀌는것이 아니라 실제 엔티티에 연결
   - 프록시 객체는 원본 엔티티를 상속, 타입체크시 프록시로 나옴(== 사용X, instanceOf 사용)
   - 조회시 영속성 컨텍스트에 이미 엔티티가 있으면 실제 엔티티 반환됨
   - 조회시 이미 연결된 프록시객체가 있는 경우에는 프록시객체로 반환됨
   - 준영속 상태일때는 프록시 초기화하면 에러

 - 프록시 확인
   - 초기화여부확인 : emf.PersistenceUtil.isLoaded(entity)
   - 클래스확인방법 : entity.getClass().getName()
   - 강제초기화 : Hibernate.initialize(entity)

### 즉시로딩, 지연로딩
