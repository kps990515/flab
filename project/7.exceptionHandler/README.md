# ExceptionHandler


```java
@Slf4j
@RestControllerAdvice
@Order
public class GlobalExceptionHandler {
    @ExceptionHandler(value = Exception.class) // 전역 에러 처리
    public ResponseEntity<Api<Object>> exception(Exception exception){

        log.error("", exception);

        return ResponseEntity
                .status(500)
                .body(
                        Api.ERROR(CommonErrorCode.SERVER_ERROR)
                );
    }
}
```

```java
@Slf4j
@RestControllerAdvice
@Order(Integer.MIN_VALUE)
public class ApiExceptionHandler {

    // ApiException을 처리하는 메서드
    @ExceptionHandler(ApiException.class)
    public ResponseEntity<Api<Object>> apiExceiption(ApiException apiExceiption) {
        // 예외 로깅
        log.error("", apiExceiption);

        // 예외에서 오류 코드 가져오기
        var errorCode = apiExceiption.getBaseErrorCode();

        // 오류 응답 생성 및 반환
        return ResponseEntity
                .status(errorCode.getHttpStatusCode())
                .body(
                        Api.ERROR(errorCode, apiExceiption.getErrorDescription())
                );
    }

    // ConstraintViolationException을 처리하는 메서드
    @ExceptionHandler(ConstraintViolationException.class)
    public ResponseEntity<Api<Object>> handleConstraintViolation(ConstraintViolationException e) {
        // 예외에서 제약 조건 위반 사항을 맵으로 수집
        Map<String, String> errors = e.getConstraintViolations().stream()
                .collect(Collectors.toMap(
                        violation -> violation.getPropertyPath().toString(),
                        ConstraintViolation::getMessage,
                        (existing, replacement) -> existing)); // 중복 키 처리

        // ApiException 생성
        ApiException apiException = new ApiException(UserErrorCode.VALIDATION_ERROR, errors.toString());

        // 오류 응답 생성 및 반환
        return ResponseEntity
                .status(apiException.getBaseErrorCode().getHttpStatusCode())
                .body(Api.ERROR(apiException.getBaseErrorCode(), apiException.getErrorDescription()));
    }

    // MethodArgumentNotValidException을 처리하는 메서드
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Api<Object>> handleMethodArgumentNotValideException(MethodArgumentNotValidException e) {
        // 예외에서 유효성 검사 오류를 맵으로 수집
        Map<String, String> errors = e.getBindingResult().getAllErrors().stream()
                .collect(Collectors.toMap(
                        error -> ((FieldError) error).getField(),
                        error -> error.getDefaultMessage() != null ? error.getDefaultMessage() : "Unknown error",
                        (existing, replacement) -> existing));

        // ApiException 생성
        ApiException apiException = new ApiException(UserErrorCode.VALIDATION_ERROR, errors.toString());

        // 오류 응답 생성 및 반환
        return ResponseEntity
                .status(apiException.getBaseErrorCode().getHttpStatusCode())
                .body(Api.ERROR(apiException.getBaseErrorCode(), apiException.getErrorDescription()));
    }
}

```

```java
public interface BaseApiException {
    BaseErrorCode getBaseErrorCode();
    String getErrorDescription();
}
```

```java
@Getter
public class ApiException extends RuntimeException implements BaseApiException {

    private final BaseErrorCode baseErrorCode;  // 예외에 대한 기본 오류 코드
    private final String errorDescription;      // 오류 설명

    // BaseErrorCode를 매개변수로 받는 생성자
    public ApiException(BaseErrorCode baseErrorCode){
        super(baseErrorCode.getDescription());
        this.baseErrorCode = baseErrorCode;
        this.errorDescription = baseErrorCode.getDescription();
    }

    // BaseErrorCode와 오류 설명을 매개변수로 받는 생성자
    public ApiException(BaseErrorCode baseErrorCode, String errorDescription){
        super(errorDescription);
        this.baseErrorCode = baseErrorCode;
        this.errorDescription = errorDescription;
    }

    // BaseErrorCode와 Throwable을 매개변수로 받는 생성자
    public ApiException(BaseErrorCode baseErrorCode, Throwable tx){
        super(tx);
        this.baseErrorCode = baseErrorCode;
        this.errorDescription = baseErrorCode.getDescription();
    }

    // BaseErrorCode, Throwable, 오류 설명을 매개변수로 받는 생성자
    public ApiException(BaseErrorCode baseErrorCode, Throwable tx, String errorDescription){
        super(tx);
        this.baseErrorCode = baseErrorCode;
        this.errorDescription = errorDescription;
    }
}
```
