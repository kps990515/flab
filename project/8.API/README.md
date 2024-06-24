# API

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Api<T> {

    private Result result; // 결과 상태를 저장하는 필드

    @Valid
    private T body; // 실제 데이터 본문을 저장하는 필드

    // 성공적인 응답을 생성하는 정적 메서드
    public static <T> Api<T> OK(T body){
        var api = new Api<T>();
        api.result = Result.OK();
        api.body = body;
        return api;
    }

    // 오류 응답을 생성하는 정적 메서드 (결과만 포함)
    public static Api<Object> ERROR(Result result){
        var api = new Api<Object>();
        api.result = result;
        return api;
    }

    // 오류 응답을 생성하는 정적 메서드 (기본 오류 코드 포함)
    public static Api<Object> ERROR(BaseErrorCode baseErrorCode){
        var api = new Api<Object>();
        api.result = Result.ERROR(baseErrorCode);
        return api;
    }

    // 오류 응답을 생성하는 정적 메서드 (기본 오류 코드와 예외 포함)
    public static Api<Object> ERROR(BaseErrorCode baseErrorCode, Throwable tx){
        var api = new Api<Object>();
        api.result = Result.ERROR(baseErrorCode, tx);
        return api;
    }

    // 오류 응답을 생성하는 정적 메서드 (기본 오류 코드와 설명 포함)
    public static Api<Object> ERROR(BaseErrorCode baseErrorCode, String description){
        var api = new Api<Object>();
        api.result = Result.ERROR(baseErrorCode, description);
        return api;
    }
}
```

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class Result {
    private Integer resultCode;       // 결과 코드
    private String resultMessage;     // 결과 메시지
    private String resultDescription; // 결과 설명

    // 성공적인 결과를 생성하는 정적 메서드
    public static Result OK(){
        return Result.builder()
                .resultCode(HttpStatus.OK.value()) // HTTP 상태 코드 200
                .resultMessage(CommonErrorCode.OK.getDescription()) // 공통 오류 코드의 설명
                .resultDescription("성공") // 성공 메시지
                .build();
    }

    // 오류 결과를 생성하는 정적 메서드 (기본 오류 코드 포함)
    public static Result ERROR(BaseErrorCode baseErrorCode){
        return Result.builder()
                .resultCode(baseErrorCode.getErrorCode()) // 기본 오류 코드의 오류 코드
                .resultMessage(baseErrorCode.getDescription()) // 기본 오류 코드의 설명
                .resultDescription("에러") // 에러 메시지
                .build();
    }

    // 오류 결과를 생성하는 정적 메서드 (기본 오류 코드와 예외 포함)
    public static Result ERROR(BaseErrorCode baseErrorCode, Throwable tx){
        return Result.builder()
                .resultCode(baseErrorCode.getErrorCode()) // 기본 오류 코드의 오류 코드
                .resultMessage(baseErrorCode.getDescription()) // 기본 오류 코드의 설명
                .resultDescription(tx.getLocalizedMessage()) // 예외의 로컬라이즈된 메시지
                .build();
    }

    // 오류 결과를 생성하는 정적 메서드 (기본 오류 코드와 설명 포함)
    public static Result ERROR(BaseErrorCode baseErrorCode, String description){
        return Result.builder()
                .resultCode(baseErrorCode.getErrorCode()) // 기본 오류 코드의 오류 코드
                .resultMessage(baseErrorCode.getDescription()) // 기본 오류 코드의 설명
                .resultDescription(description) // 커스텀 설명
                .build();
    }
}
```

```java
public interface BaseErrorCode {
    Integer getHttpStatusCode();
    Integer getErrorCode();
    String getDescription();
}
```

```java
@AllArgsConstructor
@Getter
public enum CommonErrorCode implements BaseErrorCode {
    OK(200, 200, "성공"),
    BAD_REQUEST(HttpStatus.BAD_REQUEST.value(), 400, "잘못된 요청"),
    SERVER_ERROR(HttpStatus.INTERNAL_SERVER_ERROR.value(), 500, "서버 에러"),
    NULL_POINT(HttpStatus.INTERNAL_SERVER_ERROR.value(), 512, "Null Point");

    // Enum 인스턴스 내부 값들을 변경 불가능하게 만들기 위해 final선언
    private final Integer httpStatusCode;
    private final Integer errorCode;
    private final String description;
}

@AllArgsConstructor
@Getter
public enum UserErrorCode implements BaseErrorCode {
    USER_NOT_FOUND(400, 1001, "사용자를 찾을 수 없음");

    private final Integer httpStatusCode;
    private final Integer errorCode;
    private final String description;
}
```