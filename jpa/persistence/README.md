## Persistence

### Persistence Context
 - DB 저장하기 전 엔티티를 유지하는 정보(Entity Manager단위로 작동)
 - EntityManager.persist(Entity)
 - EntityManager <-> PersistenceContext 1:1관계

 - 생명주기
    1. new/transient : 새로운상태(new Member, setId())
    2. managed : Persistence Context에 관리되는 상태(em.persist())
    3. detached : 저장되었다가 분리된 상태(em.detach())
    4. removed : 엔티티가 데이터베이스에서 삭제될 예정인 상태

 - 장점
    1. 1차캐시(id, Entity, 스냅샷)
        - 한 트랜잭션안에서만 유효
        - em.persist(member) 후 em.find()하면 1차캐시에서 조회
        - 없으면 DB조회 후 1차캐시에 저장
    2. 동일성보장
        - A = em.find(1), B = em.find(1)일때 a==b의 동일성 보장
    3. 트랜잭션을 지원하는 쓰기 지연
        - tx.commit()할때 SQL실행
    4. 변경감지
        - A.setName("update")와 같이 데이터 수정을 하면 스냅샷 비교 후 DB update
    5. 지연로딩   

 - Flush()
    - Persistence Context의 변경내용을 DB에 반영
    - 트랜잭션단위로 동작
    
    - 순서
        1. 변경감지
        2. 수정된 Entity를 쓰기지연 SQL 저장소에 등록
        3. 쓰기지연 SQL 저장소의 쿼리를 DB에 전송
    
    - 호출하는 방법
        1. em.flush() 직접호출
        2. 트랜잭션 커밋
        3. JPQL 쿼리 실행
    
    - 옵션(em.setFlushMode())
        1. FlushModeType.AUTO : 커밋이나 쿼리실행시 flush
        2. FlushModeType.COMIIT : 커밋할때만 flush

  - Detached상태(Persistence Context와 분리)
    - em.close() 종료
    - em.detach(entity) 특정 Entit만 분리
    , em.clear() 초기화
