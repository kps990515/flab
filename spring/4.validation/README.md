## Validation

### Validation DTO
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
public class UserRegisterRequest {
    //@NotNull null불가
    //@NotEmpty null, "" 불가
    //@NotBlank // null, "", " "불가
    private String name;

    private String nickName;

    @Size(min = 1, max = 12)
    @NotBlank
    private String password;

    @NotNull
    @Min(1)
    @Max(100)
    private Integer age;

    @Email
    private String email;

    @PhoneNumber // customize validation
    //@Pattern(regexp = "^\\d{2,3}-\\d{3,4}-\\d{4}\\$")
    private String phoneNumber;

    @FutureOrPresent
    private LocalDateTime registerAt;

    @AssertTrue(message = "name / nickName 하나는 있어야함") // 해당 값이 true이어야만 함
    public boolean nameCheck(){
        return !name.isBlank() || !nickName.isBlank();
    }
}
```

### ResponseEntity Valid
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class Api<T> {
    private String resultCode;

    private String resultMsg;

    @Valid // valid할 곳에 넣어야함
    private T data;

    private Error error;

    @Data
    @NoArgsConstructor
    @AllArgsConstructor
    @Builder
    public static class Error{
        private List<String> errorMessage;
    }
}
```

### Controller Valid
```java
@RestController
@RequestMapping("user")
public class UserApiController {
    @Valid // DTO 어노테이션 기반으로 요청들어올때 검사
    @PostMapping("")
    public UserRegisterRequest register(@RequestBody UserRegisterRequest userRegisterRequest){
        return userRegisterRequest;
    }

    @PostMapping("")
    public Api<UserRegisterRequest> register2(@Valid @RequestBody Api<UserRegisterRequest> userRegisterRequest){

        var body = userRegisterRequest.getData();
        Api<UserRegisterRequest> response = Api.<UserRegisterRequest>builder()
                .resultCode(String.valueOf(HttpStatus.OK.value()))
                .resultMsg(HttpStatus.OK.getReasonPhrase())
                .data(body).build();
        return response;
    }
}
```

### 전역 Validation
```java
@Slf4j
@RestControllerAdvice
public class ValidationExceptionHandler {
    @ExceptionHandler(value = { MethodArgumentNotValidException.class })
    public ResponseEntity<Api> validationException(MethodArgumentNotValidException exception){

        var errorMessageList = exception.getFieldErrors().stream()
                .map( it -> {
                    var format = "%s : { %s } 은 %s";
                    var message = String.format(format, it.getField(), it.getRejectedValue(), it.getDefaultMessage());
                    return message;
                }).toList();

        var error = Api.Error.builder()
                .errorMessage(errorMessageList)
                .build();

        var errorResponse = Api.builder()
                .resultCode(String.valueOf(HttpStatus.BAD_REQUEST.value()))
                .resultMsg(HttpStatus.BAD_REQUEST.getReasonPhrase())
                .error(error)
                .build();

        return ResponseEntity
                .status(HttpStatus.BAD_REQUEST)
                .body(errorResponse);
    }
}
```

### Customize Validation
```java
// @phoneNumber 인터페이스

@Constraint(validatedBy = { PhoneNumberValidator.class })
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface PhoneNumber {
    String message() default "핸드폰 번호 양식이 맞지 않습니다";
    String regexp() default "^\\d{2,3}-\\d{3,4}-\\d{4}\\$";

    Class<?>[] groups() default { };

    Class<? extends Payload>[] payload() default { };
}


public class PhoneNumberValidator implements ConstraintValidator<PhoneNumber, String> {

    private String regexp;

    @Override
    public void initialize(PhoneNumber constraintAnnotation) {
        regexp = constraintAnnotation.regexp();
    }

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        boolean result = Pattern.matches(value, regexp);
        return result;
    }
}
```
