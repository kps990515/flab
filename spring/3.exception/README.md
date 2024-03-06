## ExceptionHandler
 - 정상응답값은 항상 동일한 형태
 - 에러응답값 또한 항상 동일한 형태로 return해주기 위해

### 특정 Class 
```java
    @GetMapping("")
    public void hello(){
        throw new NumberFormatException();
    }

    @ExceptionHandler(value = { NumberFormatException.class })
    // 특정 컨트롤러의 Exception 처리
    public ResponseEntity numberFormatException(NumberFormatException e){
        log.error("NumberFormatException", e);
        return ResponseEntity.ok().build();
    }
```

### 전역 특정 Exception 처리
```java
@RestControllerAdvice //모든 컨트롤러에서 RestApi 에러 감지
// @RestControllerAdvice(basePackages = "com.example....") 패키지단위로 지정가능
// @RestControllerAdvice(basePackagesClasses = { RestApiController.class }}) 클래스단위로 지정가능
@Order(1) // Handler중 적용할 순서
public class RestApiExceptionHandler {

    @ExceptionHandler(value = { IndexOutOfBoundsException.class })
    public ResponseEntity outOfBound(IndexOutOfBoundsException e){
        log.error("IndexOutOfBoundsException", e);
        return ResponseEntity.status(200).build();
    }

    @ExceptionHandler(value = { NoSuchFieldException.class })
    public ResponseEntity<Api> noSuchElement(NoSuchElementException e){
        var response =  Api.builder()
                .resultCode(String.valueOf(HttpStatus.NOT_FOUND.value()))
                .resultMsg(HttpStatus.NOT_FOUND.name())
                .build();

        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(response);
    }
}
```

### 전역 Default Exception처리
```java
@RestControllerAdvice
@Order(2)
public class GlobalExceptionHandler {

    @ExceptionHandler(value = { Exception.class })
    public ResponseEntity<Api> exception(Exception e){
        var response = Api.builder()
                .resultCode(String.valueOf(HttpStatus.INTERNAL_SERVER_ERROR))
                .resultMsg(HttpStatus.INTERNAL_SERVER_ERROR.name())
                .build();

        return ResponseEntity
                .status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body(response);
    }

}
```

### 정상 Response처리
```java
@RestController
@RequestMapping("user")
public class UserApiController {

    private static List<UserResponse> userList = List.of(
            UserResponse.builder()
                    .id("1")
                    .age(10)
                    .name("abc")
                    .build()
            ,
            UserResponse.builder()
                    .id("2")
                    .age(10)
                    .name("def")
                    .build()
    );

    @GetMapping("id/{userId}")
    public Api<UserResponse> getUser(@PathVariable String userId){
        var user = userList.stream()
                .filter(it -> it.getId().equals(userId))
                .findFirst()
                .get();

        Api<UserResponse> response = Api.<UserResponse>builder()
                .resultCode(String.valueOf(HttpStatus.OK.value()))
                .resultMsg(HttpStatus.OK.name())
                .data(user)
                .build();

        return response;
    }
}
```
