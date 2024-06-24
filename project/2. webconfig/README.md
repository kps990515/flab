# WebConfig 클래스 분석

- [AuthorizationInterceptor](https://github.com/kps990515/flab/tree/main/project/3.interceptor)
- [UserSessionResolver](https://github.com/kps990515/flab/tree/master/project/5.argumentResolver)
- [filer, interceptor, argumentresolver](https://github.com/kps990515/flab/tree/main/0225)
```java
@RequiredArgsConstructor
@Configuration
public class WebConfig implements WebMvcConfigurer {
  // Interceptor와 argumentResolver 설정
    private final AuthorizationInterceptor authorizationInterceptor;
    private final UserSessionResolver userSessionResolver;

    private final List<String> OPEN_API = List.of(
            "/v1/users/signup",
            "/v1/users/login",
            "/v1/users/get-token"
    );

    private final List<String> DEFAULT_EXCLUDE = List.of(
            "/",
            "favicon.ico",
            "/error"
    );

    private final List<String> SWAGGER = List.of(
            "/swagger-ui.html",
            "/swagger-ui/**",
            "/v3/api-docs/**"
    );

    @Override
    // interceptor 설정
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(authorizationInterceptor)
                // 인증 interceptor 타지말아야할애들 설정
                .excludePathPatterns(OPEN_API)
                .excludePathPatterns(DEFAULT_EXCLUDE)
                .excludePathPatterns(SWAGGER);
    }

    @Override
    // argumentResolver 설정
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        resolvers.add(userSessionResolver);
    }
}
```

# AuthorizationInterceptor 분석
- Spring의 HandlerInterceptor를 구현하여 HTTP 요청을 가로채고 인증 토큰을 검증하는 역할

```java
@Slf4j
@RequiredArgsConstructor
@Component
public class AuthorizationInterceptor implements HandlerInterceptor {

    private final TokenService tokenService;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        log.info("Authorization Interceptor uri : {}", request.getRequestURI());

        // Web의 경우 Option 메소드일때 Pass
        if(HttpMethod.OPTIONS.matches(request.getMethod())){
            return true;
        }

        // js, html, png resource 요청하는 경우 Pass
        if(handler instanceof ResourceHttpRequestHandler){
            return true;
        }

        // Token 검증
        var accessToken = request.getHeader("authorization-token");
        if(StringUtils.isBlank(accessToken)){
            throw new ApiException(TokenErrorCode.TOKEN_NOT_FOUND);
        }

        var userId = tokenService.validationToken(accessToken);

        if(userId != null){
            // RequestContextHolder : 현재 스레드와 관련된 요청 정보를 RequestAttributes 객체에 저장, 요청 파라미터, 세션 데이터, 헤더 정보 등을 조회
            // 범위는 Thread Local : 하나의 쓰레드에서 읽고 쓸 수 있는 지역변수
            var requestContext = Objects.requireNonNull(RequestContextHolder.getRequestAttributes());
            requestContext.setAttribute("userId", userId, RequestAttributes.SCOPE_REQUEST);
            return true;
        }

        throw new ApiException(CommonErrorCode.UNAUTHORIZED);
    }
}
```