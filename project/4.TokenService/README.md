# TokenService

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class BaseTokenVO {
    private String token;
    private LocalDateTime expiredAt;
}
```

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class TokenResponse {
    private String accessToken;
    private LocalDateTime accessTokenExpiredAt;
    private String refreshToken;
    private LocalDateTime refreshTokenExpiredAt;
}
```

```java
@Mapper(componentModel = "spring", unmappedTargetPolicy = ReportingPolicy.IGNORE)
public interface TokenMapper {
    @Mappings({
            @Mapping(source = "accessToken.token", target = "accessToken"),
            @Mapping(source = "accessToken.expiredAt", target = "accessTokenExpiredAt"),
            @Mapping(source = "refreshToken.token", target = "refreshToken"),
            @Mapping(source = "refreshToken.expiredAt", target = "refreshTokenExpiredAt")
    })
    // source에 있는 컬럼을 target에 매핑
    // ex) accessToken의 token컬럼을 TokenReponse의 accessToken에 매핑
    TokenResponse toResponse(BaseTokenVO accessToken, BaseTokenVO refreshToken);
}
```
- MapStruct 라이브러리를 사용

```java
```java
public interface BaseToken {
    BaseTokenVO issueAccessToken(Map<String, Object> data);
    BaseTokenVO issueRefreshToken(Map<String, Object> data);
    Map<String, Object> validationTokenWithThrow(String token);
}
```

```java
@Service
public class TokenGenerator implements BaseToken {

    @Value("${token.secretKey}")
    private String secretKey;  // JWT 서명에 사용되는 비밀 키

    @Value("${token.accessTokenExpiryHours}")
    private Long accessTokenPlusHour;  // 액세스 토큰의 만료 시간을 설정하는 값

    @Value("${token.refreshTokenExpiryHours}")
    private Long refreshTokenPlusHour;  // 리프레시 토큰의 만료 시간을 설정하는 값

    @Override
    public BaseTokenVO issueAccessToken(Map<String, Object> data) {
        // 현재 시간에 액세스 토큰 만료 시간을 더하여 만료 시간을 설정
        var expiredLocalDateTime = LocalDateTime.now().plusHours(accessTokenPlusHour);
        var expiredAt = Date.from(expiredLocalDateTime.atZone(ZoneId.systemDefault()).toInstant());

        // 비밀 키를 사용하여 HMAC-SHA256 알고리즘으로 서명된 JWT 생성
        var key = Keys.hmacShaKeyFor(secretKey.getBytes()); //Keys.hmacShaKeyFor 함수를 통해 HMAC-SHA 키 객체로 변환
        var jwtToken = Jwts.builder()
                .signWith(key, SignatureAlgorithm.HS256) //JWT 서명을 위해 비밀 키를 사용하여 HMAC-SHA256 알고리즘을 설정
                .setClaims(data) //JWT의 페이로드 부분에 클레임(Claims)을 설정합니다. 클레임은 JWT의 주요 데이터로, 주로 사용자 정보 등을 포함합니다.
                .setExpiration(expiredAt)
                .compact();

        // JWT와 만료 시간을 포함하는 BaseTokenVO 객체 반환
        return BaseTokenVO.builder()
                .token(jwtToken)
                .expiredAt(expiredLocalDateTime)
                .build();
    }

    @Override
    public BaseTokenVO issueRefreshToken(Map<String, Object> data) {
        // 현재 시간에 리프레시 토큰 만료 시간을 더하여 만료 시간을 설정
        var expiredLocalDateTime = LocalDateTime.now().plusHours(refreshTokenPlusHour);
        var expiredAt = Date.from(expiredLocalDateTime.atZone(ZoneId.systemDefault()).toInstant());

        // 비밀 키를 사용하여 HMAC-SHA256 알고리즘으로 서명된 JWT 생성
        var key = Keys.hmacShaKeyFor(secretKey.getBytes());
        var jwtToken = Jwts.builder()
                .signWith(key, SignatureAlgorithm.HS256)
                .setClaims(data)
                .setExpiration(expiredAt)
                .compact();

        // JWT와 만료 시간을 포함하는 BaseTokenVO 객체 반환
        return BaseTokenVO.builder()
                .token(jwtToken)
                .expiredAt(expiredLocalDateTime)
                .build();
    }

    @Override
    public Map<String, Object> validationTokenWithThrow(String token) {
        // 비밀 키를 사용하여 JWT 파서를 초기화
        var key = Keys.hmacShaKeyFor(secretKey.getBytes());

        var parser = Jwts.parserBuilder()
                .setSigningKey(key)
                .build();

        try {
            // 주어진 토큰을 파싱하고 클레임을 추출
            var result = parser.parseClaimsJws(token);
            return new HashMap<>(result.getBody());
        } catch (Exception e) {
            // 파싱 중 예외가 발생하면 예외 타입에 따라 적절한 ApiException을 발생
            if (e instanceof SignatureException) {
                throw new ApiException(TokenErrorCode.INVALID_TOKEN, e);
            } else if (e instanceof ExpiredJwtException) {
                throw new ApiException(TokenErrorCode.EXPIRED_TOKEN, e);
            } else {
                throw new ApiException(TokenErrorCode.TOKEN_EXCEPTION, e);
            }
        }
    }
}
```

```java
@RequiredArgsConstructor
@Service
public class TokenService {
    private final BaseToken baseToken;
    private final TokenMapper tokenMapper;
    private final UserService userService;

    public TokenResponse issueToken(UserLoginRequest request){
        String userId = userService.getUserId(request);
        var accessToken = issuesAccessToken(userId);
        var refreshToekn = issuesRefreshToken(userId);
        return toResponse(accessToken, refreshToekn);
    }

    public BaseTokenVO issuesAccessToken(String userId){
        var data = new HashMap<String, Object>();
        data.put("userId", userId);
        return baseToken.issueAccessToken(data);
    }

    public BaseTokenVO issuesRefreshToken(String userId){
        var data = new HashMap<String, Object>();
        data.put("userId", userId);
        return baseToken.issueRefreshToken(data);
    }

    public String validationToken(String token){
        var map = baseToken.validationTokenWithThrow(token);
        var userId = map.get("userId");

        Objects.requireNonNull(userId, () -> {throw new ApiException(CommonErrorCode.NULL_POINT, "token에 할당된 사용자 없음");});

        return userId.toString();
    }

    public TokenResponse toResponse(BaseTokenVO accessToken, BaseTokenVO refreshToken){
        Objects.requireNonNull(accessToken, () -> {throw new ApiException(CommonErrorCode.NULL_POINT, "no AccessToken");});
        Objects.requireNonNull(refreshToken, () -> {throw new ApiException(CommonErrorCode.NULL_POINT, "no RefreshToken");});

        return tokenMapper.toResponse(accessToken, refreshToken);

    }
}
```

