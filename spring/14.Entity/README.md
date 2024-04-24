## Entity

### BaseEntity
```java
@MappedSuperclass // 테이블 매핑없이 컬럼만 제공하겠다는 의미
@Data
@SuperBuilder // 부모클래스의 필드 builder를 자식클래스에서 사용가능하게 하는 어노테이션
@NoArgsConstructor
@AllArgsConstructor
public class BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
}
```

### UserStatus
```java
@AllArgsConstructor
public enum UserStatus {
    REGISTERD("등록"),
    UNREGISTERD("해지")
    ;

    private final String descrtipion;
}
```

### UserEntity
```java
@Entity
@Table(name = "user")
@Data
@EqualsAndHashCode(callSuper = true)
@NoArgsConstructor
@AllArgsConstructor
@SuperBuilder
public class UserEntity extends BaseEntity {
    @Column(length = 50, nullable = false)
    private String name;

    @Column(length = 100, nullable = false)
    private String email;

    @Column(length = 100, nullable = false)
    private String password;

    @Column(length = 50, nullable = false)
    @Enumerated(EnumType.STRING)
    private UserStatus status;

    @Column(length = 150, nullable = false)
    private String address;

    private LocalDateTime registerdAt;

    private LocalDateTime unregisterdAt;

    private LocalDateTime lastLoginAt;
}
```

### UserRepository
```java
public interface UserRepository extends JpaRepository<UserEntity, Long> {

    // select * from user where id = ? and status = ? order by id desc limit 1
    Optional<UserEntity> findFirstByIdAndStatusOrderByIdDesc(Long userId, UserStatus status);

    Optional<UserEntity> findFirstByEmailAndPasswordAndStatusOrderByIdDesc(String email, String password, UserStatus status);
}
```

### BusinessAnnotation
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Service
public @interface Business {

    @AliasFor(annotation = Service.class)
    String value() default "";
}
```

### ConverterAnnotation
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Service
public @interface Converter {

    @AliasFor(annotation = Service.class) //@Service의 기능을 모두 상속
    String value() default "";
}
```

### UserConverter
```java
@RequiredArgsConstructor
@Converter
public class UserConverter {

    public UserEntity toEntity(UserRegisterRequest request){

        return Optional.ofNullable(request)
                .map(it -> {
                    //to entity

                    return UserEntity.builder()
                            .name(request.getName())
                            .email(request.getEmail())
                            .password(request.getPassword())
                            .address(request.getAddress())
                            .build();
                })
                .orElseThrow(() -> new ApiException(ErrorCode.NULL_POINT, "UserRegisterRequest Null"));
    }

    public UserResponse toResponse(UserEntity userEntity) {
        return Optional.ofNullable(userEntity)
                .map(it -> {
                    return UserResponse.builder()
                            .id(userEntity.getId())
                            .name(userEntity.getName())
                            .status(userEntity.getStatus())
                            .email(userEntity.getEmail())
                            .address(userEntity.getAddress())
                            .registerdAt(userEntity.getRegisterdAt())
                            .unregisterdAt(userEntity.getUnregisterdAt())
                            .build();
                })
                .orElseThrow(() -> new ApiException(ErrorCode.NULL_POINT, "UserEntity Null"));
    }
}
```

### Controller
```java
@RequiredArgsConstructor
@RestController
@RequestMapping("/open-api/user/")
public class UserOpenApiController {
    private final UserBusiness userBusiness;

    // 사용자 가입
    @PostMapping("/register")
    public Api<UserResponse> register(@Valid @RequestBody Api<UserRegisterRequest> request){
        var response = userBusiness.register(request.getBody());
        return Api.OK(response);
    }

    //로그인
    @PostMapping("/login")
    public Api<UserResponse> login(@Valid @RequestBody Api<UserLoginRequest> request){
        var response = userBusiness.login(request.getBody());
        return Api.OK(response);
    }
}
```

### UserBusiness
```java
@RequiredArgsConstructor
@Business
public class UserBusiness {
    private final UserService userService;
    private final UserConverter userConverter;

    /**
     * 사용자 가입처리 로직
     * 1. reuqest -> entity
     * 2. entity -> save
     * 3. saved Entity -> reponse
     * 4. return response
     */
    public UserResponse register(UserRegisterRequest request){
        var entity = userConverter.toEntity(request);
        var userEntity = userService.register(entity);
        var response = userConverter.toResponse(userEntity);
        return response;

        /*
        return Optional.ofNullable(request)
                .map(userConverter::toEntity)
                .map(userService::register)
                .map(userConverter::toResponse)
                .orElseThrow(() -> new ApiException(ErrorCode.NULL_POINT, "reuqest Null"));
         */
    }

    /**
     * 1. email, password로 사용자체크
     * 2. user Entity 로그인 확인
     * 3. token생성
     * 4. return token
     */
    public UserResponse login(UserLoginRequest request) {
        var userEntity = userService.login(request.getEmail(), request.getPassword());

        // TODO 토큰생성
        return userConverter.toResponse(userEntity);
    }
}
```

### UserService
```java
@RequiredArgsConstructor
@Service
public class UserService {
    private final UserRepository userRepository;

    @Transactional
    public UserEntity register(UserEntity userEntity){
        return Optional.ofNullable(userEntity)
                .map(it -> {
                    userEntity.setStatus(UserStatus.REGISTERD);
                    userEntity.setRegisterdAt(LocalDateTime.now());
                    return userRepository.save(userEntity);
                })
                .orElseThrow(() -> new ApiException(ErrorCode.NULL_POINT, "UserEntity Null"));
    }

    public UserEntity login(String email, String password){
        var entity = getUserWithThrow(email, password);
        return entity;
    }

    public UserEntity getUserWithThrow(String email, String password){
        return userRepository.findFirstByEmailAndPasswordAndStatusOrderByIdDesc(email, password, UserStatus.REGISTERD)
                .orElseThrow(() -> new ApiException(UserErrorCode.USER_NOT_FOUND));
    }
}
```


