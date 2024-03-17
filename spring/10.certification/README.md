## 인증

### HTTP Session
 - 웹어플리케이션에 접속 후 일정시간 동안 유지(웹 어플리케이션에 저장)
 - 특징
    1. 사용자 정보 서버측에 저장하고 클라이언트에는 세션ID발급(Stateless 특성때문에)
    2. 쿠키를 사용해 구현
    3. 로그인정보 관리
    4. 서버에서 관리되기 때문에 클라이언트에서 조작 불가, HTTPS통해 암호화

 - 과정
    1. 로그인
    2. 정보 검증 후 Session ID 생성
    3. 세션은 서버에서 관리
    4. Session Id는 쿠키방식으로 클라이언트에게 전달

#### Controller
```java
public class AccountApiController {

    private final UserService userService;

    @PostMapping("/login")
    public void login(@RequestBody LoginRequest loginRequest, HttpSession httpSession){
        userService.login(loginRequest, httpSession);
    }

    // 세션값으로 정보를 가져오는 API
    @GetMapping("/me")
    public UserDTO me(HttpSession httpSession){
        var userObject = httpSession.getAttribute("USER");

        if(userObject != null){
            return (UserDTO) userObject;
        }else{
            return  null;
        }
    }
}
```

#### Service
```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public void login(LoginRequest loginRequest, HttpSession httpSession){
        var id = loginRequest.getId();
        var pw = loginRequest.getPassword();

        var optionalUser = userRepository.findByName(id);

        if(optionalUser.isPresent()){
            var userDTO = optionalUser.get();

            if(userDTO.getPassword().equals(pw)){
                //세션에 정보저장 -> 쿠키로(JSESSIONID) 클라이언트 전달
                httpSession.setAttribute("USER", userDTO);
            }else{
                throw new RuntimeException();
            }
        }else{
            throw new RuntimeException();
        }
    }
}
```