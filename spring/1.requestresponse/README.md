## 기본적인 요청 응답

### @PathVariable
```java
    @GetMapping(path = "/echo/{message}/age/{age}/is-man/{isMan}")
    public String echo(@PathVariable(name = "message") String msg,
                       @PathVariable int age,
                       @PathVariable boolean isMan){
        System.out.println(msg);
        System.out.println(age);
        System.out.println(isMan);
        return msg;
    }
```

### @RequestParam
```java
// localhost:8080/api/bookVO/category=IT&issuedYear=2023&issuedMonth=08
    @GetMapping(path = "/book")
    public String echo(@RequestParam String category,
                       @RequestParam String issuedYear,
                       @RequestParam(name = "issued-month") String issuedMonth){
        System.out.println(category);
        System.out.println(issuedYear);
        System.out.println(issuedMonth);
        return issuedMonth;
    }
```
```java
    @GetMapping(path = "/bookVO")
    public String echo(BookQueryParam bookQueryParam){
        return bookQueryParam.getIssuedYear();
    }
```

### @RequestBody
```java
    @PostMapping("/post")
    //htmlBody로 들어오는 데이터를 받는 어노테이션
    public void post(@RequestBody BookRequest bookRequest){
        System.out.println(bookRequest);
    }
```

### 어노테이션들
```java
@Data // @getter, @setter, @RequiredArgsContructor, @ToString, @EqualsAndHashCode
@AllArgsConstructor // 모든필드값을 받는 생성자
@NoArgsConstructor // 파라미터없는 기본생성자
@RequiredArgsConstructor // final, @NonNull만 받는 생성자
public class BookQueryParam {
    private String category;
    private String issuedYear;
    private String issuedMonth;
}
```

### @ResponseBody, @Controller
```java
@Controller // html로 리턴 
@RequestMapping("/api")
public class ResponseController {
    @GetMapping("")
    @ResponseBody // @Controller인경우 @ResponseBody붙여야 json리턴가능
    public BookRequest book3(){
        BookRequest bookRequest = new BookRequest();
        bookRequest.setName("ddd");
        bookRequest.setNumber("1");
        return bookRequest;
    }
}
```

### @RestController, ResponseEntity
```java
@RestController // 응답값 무조건 json
@RequestMapping("/api")
public class ResponseController {
    @GetMapping("")
    // Object : 자동으로 200 OK 리턴
    public BookRequest book(){
        BookRequest bookRequest = new BookRequest();
        bookRequest.setName("ddd");
        bookRequest.setNumber("1");
        return bookRequest;
    }

    @GetMapping("")
    // ResponseEntity : Body의 내용을 Object로 설정, status 세팅 가능
    public ResponseEntity<BookRequest> book2(){
        BookRequest bookRequest = new BookRequest();
        bookRequest.setName("ddd");
        bookRequest.setNumber("1");

        return ResponseEntity.status(HttpStatus.OK)
                .header("x-custom", "hi")
                .body(bookRequest);
    }
```
