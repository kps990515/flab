## ExceptionHandler, Interceptor 

### GlobalExceptionHandler
```java
@Slf4j
@RestControllerAdvice
@Order
public class GlobalExceptionHandler {
    @ExceptionHandler(value = Exception.class)
    public ResponseEntity<Api<Object>> exception(Exception exception){

        log.error("", exception);

        return ResponseEntity
                .status(500)
                .body(
                        Api.ERROR(ErrorCode.SERVER_ERROR)
                );
    }
}
```

### BaseApi Interface
```java
public interface BaseApi {
    BaseErrorCode getBaseErrorCode();
    String getErrorDescription();
}
```

### ApiException
```java
@Getter
public class ApiException extends RuntimeException implements BaseApi {

    private final BaseErrorCode baseErrorCode;
    private final String errorDescription;

    public ApiException(BaseErrorCode baseErrorCode){
        super(baseErrorCode.getDescription());
        this.baseErrorCode = baseErrorCode;
        this.errorDescription = baseErrorCode.getDescription();
    }

    public ApiException(BaseErrorCode baseErrorCode, String errorDescription){
        super(errorDescription);
        this.baseErrorCode = baseErrorCode;
        this.errorDescription = errorDescription;
    }

    public ApiException(BaseErrorCode baseErrorCode, Throwable tx){
        super(tx);
        this.baseErrorCode = baseErrorCode;
        this.errorDescription = baseErrorCode.getDescription();
    }

    public ApiException(BaseErrorCode baseErrorCode, Throwable tx, String errorDescription){
        super(tx);
        this.baseErrorCode = baseErrorCode;
        this.errorDescription = errorDescription;
    }
}
```

### ApiExceptionHandler
```java
@Slf4j
@RestControllerAdvice
@Order(Integer.MIN_VALUE)
public class ApiExceptionHandler {
    @ExceptionHandler(ApiException.class)
    // ApiException은 RuntimeException을 상속받았기에 모든 Exception가져올 수 있음
    public ResponseEntity<Api<Object>> apiExceiption(ApiException apiExceiption){
        log.error("", apiExceiption);

        var errorCode = apiExceiption.getBaseErrorCode();

        return ResponseEntity
                .status(errorCode.getHttpStatusCode())
                .body(
                        Api.ERROR(errorCode, apiExceiption.getErrorDescription())
                );
    }
}
```

### AutorizationInterceptor
```java
@Slf4j
@RequiredArgsConstructor
@Component
public class AuthorizationInterceptor implements HandlerInterceptor {
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

        // TODO header 검증

        return true;
    }
}
```

### WebConfig
```java
@RequiredArgsConstructor
@Configuration
public class WebConfig implements WebMvcConfigurer {
    private final AuthorizationInterceptor authorizationInterceptor;

    private List<String> OPEN_API = List.of(
            "/open-api/**"
    );

    private List<String> DEFAULT_EXCLUDE = List.of(
            "/",
            "favicon.ico",
            "/error"
    );

    private List<String> SWAGGER = List.of(
            "/swagger-ui.html",
            "/swagger-ui/**",
            "v3/api-docs/**"
    );

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(authorizationInterceptor)
                // 인증 interceptor 타지말아야할애들 설정
                .excludePathPatterns(OPEN_API)
                .excludePathPatterns(DEFAULT_EXCLUDE)
                .excludePathPatterns(SWAGGER);
    }
}
```