## JPA

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

    @Query(
            value = "select * from user as u where u.score >= :min AND u.score <= :max",
            nativeQuery = true
    )
    public List<UserEntity> score(@Param(value = "min") int min,
                                  @Param(value = "max") int max);
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