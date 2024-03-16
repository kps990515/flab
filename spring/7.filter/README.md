## Filter, Interceptor

### Filter
```java
@Component
@Slf4j
public class LoggerFilter implements Filter {
    @Override
    // request, response가 filter chain 통과할때마다 호출됨
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        // 필터 전처리(로깅, 인증체크, 변환 등)
        var req = new ContentCachingRequestWrapper((HttpServletRequest) request);
        var res = new ContentCachingResponseWrapper((HttpServletResponse) response);

        // 필터체인 이동(다음필터 / JSP,Servlet)
        chain.doFilter(req, res);

        // 응답 후처리
        var reqJson = new String(req.getContentAsByteArray());
        log.info(reqJson);
        var resJson = new String(res.getContentAsByteArray());
        log.info(resJson);

        res.copyBodyToResponse(); // resJson에서 읽은 내용을 다시 응답값에 넣어주기
    }
}
```

### Interceptor

#### Configuration
```java
// interceptor 세팅
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Autowired
    private OpenApiInterceptor openApiInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(openApiInterceptor)
                .addPathPatterns("/**"); // 모든주소에 interceptor설정

    }
}
```

#### interceptor
```java
@Slf4j
@Component
public class OpenApiInterceptor implements HandlerInterceptor {
    // filter -> dispatcher Servlet -> Handler Mapping(목적 controller 정해짐)
    // 어떤 컨트롤러로 보낼것인가 어떠한 주소에 매핑할 것인가 조작가능
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // true이면 Controller전달, false이면 전달하지 않는다

        var handlerMethod = (HandlerMethod) handler;

        // 메소드에 해당 어노테이션이 있는가
        var methodLevel = handlerMethod.getMethodAnnotation(OpenApi.class);
        if(methodLevel != null){
            return true;
        }

        // 클래스에 해당 어노테이션 있는가
        var classLevel = handlerMethod.getBeanType().getAnnotation(OpenApi.class);
        if(classLevel != null){
            return true;
        }

        return false;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        HandlerInterceptor.super.afterCompletion(request, response, handler, ex);
    }
}
```

#### controller
```java
@Slf4j
@RestController
@RequestMapping("/api")
public class UserApiController {
    @OpenApi
    @PostMapping("")
    public UserRequest register(@RequestBody UserRequest userRequest){
        return userRequest;
    }

    @GetMapping("")
    public void hello(){

    }
}

```