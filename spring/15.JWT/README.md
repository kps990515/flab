## JWT

## 1. 토큰 생성

### build.gradle
```java
    implementation 'io.jsonwebtoken:jjwt-api:0.11.5'
    runtimeOnly 'io.jsonwebtoken:jjwt-impl:0.11.5'
    runtimeOnly 'io.jsonwebtoken:jjwt-jackson:0.11.5'
```

### BaseTokenGenerator
```java
public interface TokenHelperInterface {
    TokenDto issueAccessToken(Map<String, Object> data);
    TokenDto issueRefreshToken(Map<String, Object> data);
    Map<String, Object> validationTokenWithThrow(String token);
}
```

### TokenDTO
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class TokenDto {
    private String token;
    private LocalDateTime expiredAt;
}
```

### application.yaml
```java
token:
  secret:
    key: SpringBootJWTHelperTokenSecretKeyValue123!@#
  access-token:
    plus-hour: 1
  refresh-token:
    plus-hour: 12
```

### TokenErrorCode
```java
public enum TokenErrorCode implements ErrorCodeIfs{
    INVALID_TOKEN(400, 2000, "유효하지 않은 토큰"),
    EXPIRED_TOKEN(400, 2001, "만료된 토큰"),
    TOKEN_EXCEPTION(400, 2002, "알수없는 토큰 에러");


    private final Integer httpStatusCode;
    private final Integer errorCode;
    private final String description;
}
```

### TokenGenerator
```java
public class JwtTokenHelper implements TokenHelperInterface {

    @Value("${token.secret.key}")
    private String secretKey;

    @Value("${token.access-token.plus-hour}")
    private Long accessTokenPlusHour;

    @Value("${token.refresh-token.plus-hour}")
    private Long refreshTokenPlusHour;

    @Override
    public TokenDto issueAccessToken(Map<String, Object> data) {
        var expiredLocalDateTime = LocalDateTime.now().plusHours(accessTokenPlusHour);
        var expiredAt = Date.from(expiredLocalDateTime.atZone(ZoneId.systemDefault()).toInstant());

        var key = Keys.hmacShaKeyFor(secretKey.getBytes());
        var jwtToken = Jwts.builder()
                .signWith(key, SignatureAlgorithm.HS256)
                .setClaims(data)
                .setExpiration(expiredAt)
                .compact();

        return TokenDto.builder()
                .token(jwtToken)
                .expiredAt(expiredLocalDateTime)
                .build();
    }

    @Override
    public TokenDto issueRefreshToken(Map<String, Object> data) {
        var expiredLocalDateTime = LocalDateTime.now().plusHours(refreshTokenPlusHour);
        var expiredAt = Date.from(expiredLocalDateTime.atZone(ZoneId.systemDefault()).toInstant());

        var key = Keys.hmacShaKeyFor(secretKey.getBytes());
        var jwtToken = Jwts.builder()
                .signWith(key, SignatureAlgorithm.HS256)
                .setClaims(data)
                .setExpiration(expiredAt)
                .compact();

        return TokenDto.builder()
                .token(jwtToken)
                .expiredAt(expiredLocalDateTime)
                .build();
    }

    @Override
    public Map<String, Object> validationTokenWithThrow(String token) {
        var key =Keys.hmacShaKeyFor(secretKey.getBytes());

        var parser = Jwts.parserBuilder()
                .setSigningKey(key)
                .build();

        try{
            var result = parser.parseClaimsJws(token);
            return new HashMap<String, Object>(result.getBody());
        }catch (Exception e){
            if(e instanceof SignatureException){
                throw new ApiException(TokenErrorCode.INVALID_TOKEN, e);
            }else if(e instanceof ExpiredJwtException){
                throw new ApiException(TokenErrorCode.EXPIRED_TOKEN, e);
            }else{
                throw new ApiException(TokenErrorCode.TOKEN_EXCEPTION, e);
            }
        }
    }
}
```

## 토큰발행적용

### TokenResponse
```java
public class TokenResponse {
    private String accessToken;
    private LocalDateTime accessTokenExpiredAt;
    private String refreshToken;
    private LocalDateTime refreshTokenExpiredAt;
}
```

### TokenResponse
```java
public class TokenConverter {
    public TokenResponse toResponse(TokenDto accessToken, TokenDto refreshToken){
        Objects.requireNonNull(accessToken, () -> {throw new ApiException(ErrorCode.NULL_POINT);});
        Objects.requireNonNull(refreshToken, () -> {throw new ApiException(ErrorCode.NULL_POINT);});

        return TokenResponse.builder()
                .accessToken(accessToken.getToken())
                .accessTokenExpiredAt(accessToken.getExpiredAt())
                .refreshToken(refreshToken.getToken())
                .refreshTokenExpiredAt(refreshToken.getExpiredAt())
                .build();

    }
}
```

### TokenBusiness
```java
public class TokenBusines {
    private final TokenService tokenService;
    private final TokenConverter tokenConverter;

    public TokenResponse issueToken(UserEntity userEntity){
        return Optional.ofNullable(userEntity)
                .map(BaseEntity::getId)
                .map(userId -> {
                    var accessToken = tokenService.issuesAccessToken(userId);
                    var refreshToekn = tokenService.issuesRefreshToken(userId);
                    return tokenConverter.toResponse(accessToken, refreshToekn);
                })
                .orElseThrow(() -> new ApiException(ErrorCode.NULL_POINT));
    }
}
```

### TokenService
```java
public class TokenService {
    private final TokenHelperInterface tokenHelperInterface;

    public TokenDto issuesAccessToken(Long userId){
        var data = new HashMap<String, Object>();
        data.put("userId", userId);
        return tokenHelperInterface.issueAccessToken(data);
    }

    public TokenDto issuesRefreshToken(Long userId){
        var data = new HashMap<String, Object>();
        data.put("userId", userId);
        return tokenHelperInterface.issueRefreshToken(data);
    }

    public Long validationToken(String token){
        var map = tokenHelperInterface.validationTokenWithThrow(token);
        var userId = map.get("userId");

        Objects.requireNonNull(userId, () -> {throw new ApiException(ErrorCode.NULL_POINT);});

        return Long.parseLong(userId.toString());
    }
}
```

### UserBusiness
```java
public TokenResponse login(UserLoginRequest request) {
    var userEntity = userService.login(request.getEmail(), request.getPassword());
    var tokenResponse = tokenBusines.issueToken(userEntity);

    return tokenResponse;
}
```

### UserController
```java
    //로그인
    @PostMapping("/login")
    public Api<TokenResponse> login(@Valid @RequestBody Api<UserLoginRequest> request){
        var response = userBusiness.login(request.getBody());
        return Api.OK(response);
    }
```

## 어노테이션으로 인증정보 저장

### AutorizationInterceptor
```java
    // TODO header 검증
    var accessToken = request.getHeader("authorization-token");
    if(StringUtils.isBlank(accessToken)){
        throw new ApiException(TokenErrorCode.TOKEN_NOT_FOUND);
    }

    var userId = tokenBusines.validationToken(accessToken);

    if(userId != null){
        // RequestContextHolder : 현재 스레드와 관련된 요청 정보를 RequestAttributes 객체에 저장, 요청 파라미터, 세션 데이터, 헤더 정보 등을 조회
        // 범위는 Thread Local : 하나의 쓰레드에서 읽고 쓸 수 있는 지역변수
        var requestContext = Objects.requireNonNull(RequestContextHolder.getRequestAttributes());
        requestContext.setAttribute("userId", userId, RequestAttributes.SCOPE_REQUEST);
        return true;
    }

    throw new ApiException(ErrorCode.BAD_REQUEST, "인증실패");
```

### UserSessionResolver
```java
@Component
@RequiredArgsConstructor
public class UserSessionResolver implements HandlerMethodArgumentResolver {

    private final UserService userService;

    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        // 지원하는 파라미터, 어노테이션 체크

        // 1. 어노테이션 체크
        var annotations = parameter.hasParameterAnnotation(UserSession.class);
        // 2. 파라미터 체크
        var parameterType = parameter.getParameterType().equals(User.class);

        return (annotations && parameterType);
    }

    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
        // supportsParameter함수 true일시 실행

        // requestContextHolder 찾아오기
        var requestContext = RequestContextHolder.getRequestAttributes();
        var userId = requestContext.getAttribute("userId", RequestAttributes.SCOPE_REQUEST);

        var userEntity = userService.getUserWithThrow(Long.parseLong(userId.toString()));

        // 사용자 정보세팅
        return User.builder()
                .id(userEntity.getId())
                .name(userEntity.getName())
                .status(userEntity.getStatus())
                .password(userEntity.getPassword())
                .email(userEntity.getEmail())
                .address(userEntity.getAddress())
                .registerdAt(userEntity.getRegisterdAt())
                .unregisterdAt(userEntity.getUnregisterdAt())
                .build();
    }
}
```

### Webconfig
```java
    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        resolvers.add(userSessionResolver);
    }
```

### UserSession
```java
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
public @interface UserSession {

}
```

### Controller
```java
    @GetMapping("/me")
    public Api<UserResponse> me(@UserSession User user){
        var response = userBusiness.me(Long.parseLong(user.getId().toString()));
        return Api.OK(response);
    }
```