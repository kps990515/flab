## Filter, Api, Result, Error

### Filter
```java
@Slf4j
@Component
public class LoggerFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        // 체인에 들어가기전에 req, res를 캐시에 담아서 가져옴
        var req = new ContentCachingRequestWrapper((HttpServletRequest) request);
        var res = new ContentCachingResponseWrapper((HttpServletResponse) response);

        chain.doFilter(req, res);
        // 체인에 들어간후

        // 1.request 정보
        var headerNames = req.getHeaderNames();
        var headerValues = new StringBuilder();

        // 1-1. 헤더의 정보들을 하나씩 가져와서 로깅
        headerNames.asIterator().forEachRemaining(headerKey -> {
            var headerValue = req.getHeader(headerKey);
            headerValues
                    .append("[")
                    .append(headerKey)
                    .append(" : ")
                    .append(headerValue)
                    .append("] ");
        });


        // 1-2. requestbody, uri, method 추출
        var requestBody = new String(req.getContentAsByteArray());
        var uri = req.getRequestURI();
        var method = req.getMethod();

        // 1-3. 로그로 남기기
        log.info(">>>>> uri : {}, method : {}, header : {}, body : {}", uri, method, headerValues, requestBody);

        // 2. response 정보
        var responseHeaderValues = new StringBuilder();
        res.getHeaderNames().forEach(headerKey -> {
            var headerValue = res.getHeader(headerKey);
            responseHeaderValues
                    .append("[")
                    .append(headerKey)
                    .append(" : ")
                    .append(headerValue)
                    .append("] ");
        });

        // 2-1. responsebody 내용 로깅
        var responseBody = new String(res.getContentAsByteArray());
        log.info("<<<<< uri : {}, method : {}, header : {}, body : {}", uri, method, responseHeaderValues, responseBody);

        res.copyBodyToResponse(); // 사용한 response다시 넣어주기
    }
}
```

### Controller
```java
@RequiredArgsConstructor
@RestController
@RequestMapping("/api/account")
public class AccountApiController {

    private final AccountRepository accountRepository;

    @GetMapping("/me")
    public Api<Object> save(){
        var response = AccountMeResponse.builder()
                .name("a")
                .email("a@gmail.com")
                .registerdAt(LocalDateTime.now())
                .build();

        //return Api.OK(response);
        return Api.ERROR(UserErrorCode.USER_NOT_FOUND, "사용자 없음");
    }
}
```

### Api 클래스
 - response를 담당
 - result(Result클래스)와 body(T)로 구성
```json
{
    "result" : {
        "resultCode": "200",
        "resultMessage": "OK",
        "resultDescription": "성공" 
    },
    "body": {

    }
}
```

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Api<T> {

    private Result result;

    @Valid
    private T body;

    // 정적메소드로 클래스 인스턴스 없이 호출 가능
    // T data를 받고, Api<T>를 리턴
    // 제네릭 함수
    public static <T> Api<T> OK(T data){
        var api = new Api<T>();
        api.result = Result.OK(); //result
        api.body = data;          //body
        return api;
    }

    public static Api<Object> ERROR(Result result){
        var api = new Api<Object>();
        api.result = Result.ERROR(result);
        return api;
    }

    public static Api<Object> ERROR(ErrorCodeIfs errorCodeIfs){
        var api = new Api<Object>();
        api.result = Result.ERROR(errorCodeIfs);
        return api;
    }

    public static Api<Object> ERROR(ErrorCodeIfs errorCodeIfs, Throwable tx){
        var api = new Api<Object>();
        api.result = Result.ERROR(errorCodeIfs, tx);
        return api;
    }

    public static Api<Object> ERROR(ErrorCodeIfs errorCodeIfs, String description){
        var api = new Api<Object>();
        api.result = Result.ERROR(errorCodeIfs, description);
        return api;
    }
}
```

### Result클래스
- Response에서 Result공통부분 담당
- resultCode, message, description

```json
{
    "result" : {
        "resultCode": "200",
        "resultMessage": "OK",
        "resultDescription": "성공" 
    }
}
```

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class Result {
    private Integer resultCode;
    private String resultMessage;
    private String resultDescription;

    public static Result OK(){
        return Result.builder()
                .resultCode(ErrorCode.OK.getErrorCode())
                .resultMessage(ErrorCode.OK.getDescription())
                .resultDescription("성공")
                .build();
    }

    public static Result ERROR(ErrorCodeIfs errorCodeIfs){
        return Result.builder()
                .resultCode(errorCodeIfs.getErrorCode())
                .resultMessage(errorCodeIfs.getDescription())
                .resultDescription("에러")
                .build();
    }

    public static Result ERROR(ErrorCodeIfs errorCodeIfs, Throwable tx){
        return Result.builder()
                .resultCode(errorCodeIfs.getErrorCode())
                .resultMessage(errorCodeIfs.getDescription())
                .resultDescription(tx.getLocalizedMessage())
                .build();
    }

    public static Result ERROR(ErrorCodeIfs errorCodeIfs, String description){
        return Result.builder()
                .resultCode(errorCodeIfs.getErrorCode())
                .resultMessage(errorCodeIfs.getDescription())
                .resultDescription(description)
                .build();
    }
}
```

### ErrorCodeInterface
- ErrorCode Enum클래스들의 부모

```java
public interface ErrorCodeIfs {
    Integer getHttpStatusCode();
    Integer getErrorCode();
    String getDescription();
}
```

### ErrorCode 클래스
- httpStatusCode, errorCode, description으로 구성

```java
@AllArgsConstructor
@Getter
public enum ErrorCode implements ErrorCodeIfs{
    OK(200, 200, "성공"),
    BAD_REQUEST(HttpStatus.BAD_REQUEST.value(), 400, "잘못된 요청"),
    SERVER_ERROR(HttpStatus.INTERNAL_SERVER_ERROR.value(), 500, "서버 에러"),
    NULL_POINT(HttpStatus.INTERNAL_SERVER_ERROR.value(), 512, "Null Point");
    
    // Enum 인스턴스 내부 값들을 변경 불가능하게 만들기 위해 final선언
    private final Integer httpStatusCode;
    private final Integer errorCode;
    private final String description;
}
```

### UserErrorCode클래스
```java
@AllArgsConstructor
@Getter
public enum UserErrorCode implements ErrorCodeIfs{
    USER_NOT_FOUND(400, 1404, "사용자를 찾을 수 없음");


    private final Integer httpStatusCode;
    private final Integer errorCode;
    private final String description;
}
```
