## JPA

| **특징**             | **JPQL**                             | **QueryDSL**                        | **Native Query**                         |
|----------------------|--------------------------------------|-------------------------------------|------------------------------------------|
| **쿼리 기준**        | 엔티티와 필드를 기준으로 작성         | 엔티티 기반, 메서드 체인으로 작성    | 테이블과 컬럼을 직접 사용                |
| **SQL 독립성**       | 데이터베이스 독립적                  | 데이터베이스 독립적                 | 데이터베이스에 종속적                    |
| **유형**             | 정적 쿼리 (동적 쿼리 어렵다)         | 동적 쿼리 작성에 강점               | 정적/동적 모두 가능                     |
| **타입 안전성**      | 없음                                 | 있음                                | 없음                                     |
| **복잡한 쿼리**      | 지원 가능하나 가독성이 떨어짐         | 동적, 복잡한 쿼리에 강함            | 복잡한 SQL을 직접 작성 가능             |
| **성능 최적화**      | 기본적 최적화                        | JPA 최적화 지원                     | DB 특화 최적화 가능                     |
| **주요 사용 사례**    | 일반적인 데이터 조회 및 변경          | 동적 쿼리와 타입 안정성 요구 시      | DB 고유 기능 활용 또는 최적화 필요 시    |


### yaml
```yaml
spring:
  jpa:
    show-sql: true
    properties:
      format_sql: true
      dialect: org.hibernate.dialect.MySQLDialect
    hibernate:
      ddl-auto: validate
  datasource:
    url: jdbc:mysql://localhost:3306/book_store?useSSL=false&useUnicode=true&allowPublicKeyRetrieval=true
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: root1234!!
```

### UserRepository
```java
public interface UserRepository extends JpaRepository<UserEntity, Long> {
// JpaRepository를 상속받아 JPA문법 사용가능

    // select * from user where score >= [??] 이거와 같은 JPA 문법
    public List<UserEntity> findAllByScoreGreaterThanEqual(int score);

    // select * from user where score >= ?? and score <= ??
    public List<UserEntity> findAllByScoreGreaterThanEqualAndScoreLessThenEqual(int min, int max);

    //native query
    @Query(
            value = "select * from user as u where u.score >= :min AND u.score <= :max",
            nativeQuery = true
    )
    public List<UserEntity> score(@Param(value = "min") int min, @Param(value = "max") int max);

    //JPQL
    @Query(
            "SELECT u FROM UserEntity u WHERE u.score >= :min AND u.score <= :max"
    )
    public List<UserEntity> score(@Param("min") int min, @Param("max") int max);

    //QUERY DSL
    @Override
    public List<UserEntity> score(int min, int max) {
        QUserEntity user = QUserEntity.userEntity;

        return queryFactory.selectFrom(user)
                .where(user.score.between(min, max))
                .fetch();
    }
}
```

### UserEntity
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
@Entity(name = "user") //테이블이름
public class UserEntity{
    @Id //PK
    @GeneratedValue(strategy = GenerationType.IDENTITY) //DB에 키 생성 위임
    private Long id;
    private String name;
    private int score;
}
```

### UserService
```java
@Service
@RequiredArgsConstructor
public class UserService {

    private final UserRepository userRepository;

    public UserEntity save(UserEntity user){
        return userRepository.save(user);
    }

    public List<UserEntity> findAll(){
        return userRepository.findAll();
    }

    public void delete(UserEntity id){
        userRepository.delete(id);
    }

    public Optional<UserEntity> findById(Long id){
        return userRepository.findById(id);
    }

    public List<UserEntity> filterScore(int score){
        return userRepository.findAllByScoreGreaterThanEqual(score);
    }
}
```
