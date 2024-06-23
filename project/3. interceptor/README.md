# AuthorizationInterceptor 분석
- Spring의 HandlerInterceptor를 구현하여 HTTP 요청을 가로채고 인증 토큰을 검증하는 역할

[Tokenservice](https://github.com/kps990515/flab/tree/master/project/5.TokenService)

```java
@Slf4j
@RequiredArgsConstructor
@Component
public class AuthorizationInterceptor implements HandlerInterceptor {

    private final TokenService tokenService;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // HTTP 요청이 컨트롤러에 도달하기 전에 실행
        // 반환값이 true이면 요청 처리를 계속 진행하고, false이면 요청 처리를 중단
        log.info("Authorization Interceptor uri : {}", request.getRequestURI());

        // HTTP Option 메소드일때 Pass
        if(HttpMethod.OPTIONS.matches(request.getMethod())){
            return true;
        }

        // js, html, png resource 요청하는 경우 Pass
        if(handler instanceof ResourceHttpRequestHandler){
            return true;
        }

        // Token 검증
        // 1. 요청 헤더에서 authorization-token을 가져와 토큰의 존재 여부를 확인. 토큰이 없으면 ApiException을 발생시킵니다.
        var accessToken = request.getHeader("authorization-token");
        if(StringUtils.isBlank(accessToken)){
            throw new ApiException(TokenErrorCode.TOKEN_NOT_FOUND);
        }

        var userId = tokenService.validationToken(accessToken);

        if(userId != null){
            // RequestContextHolder : 현재 스레드와 관련된 요청 정보를 RequestAttributes 객체에 저장, 요청 파라미터, 세션 데이터, 헤더 정보 등을 조회
            // 범위는 Thread Local : 하나의 쓰레드에서 읽고 쓸 수 있는 지역변수
            var requestContext = Objects.requireNonNull(RequestContextHolder.getRequestAttributes());
            // RequestAttributes.SCOPE_REQUEST : HTTP 요청이 시작되고 응답이 완료될 때까지 유지
            requestContext.setAttribute("userId", userId, RequestAttributes.SCOPE_REQUEST);
            return true;
        }

        throw new ApiException(CommonErrorCode.UNAUTHORIZED);
    }
}
```