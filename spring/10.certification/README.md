## 인증

# HTTP Session, HTTP Cookie, Header, JWT 인증 차이점 비교

| **특징**           | **HTTP Session**                     | **HTTP Cookie**                     | **Header**                           | **JWT 인증**                         |
|--------------------|--------------------------------------|-------------------------------------|-------------------------------------|--------------------------------------|
| **저장 위치**       | 서버                                 | 클라이언트                          | 요청/응답의 헤더                    | 클라이언트                          |
| **상태 유지**       | 서버에서 세션 ID로 상태 관리         | 클라이언트에 데이터 저장            | 메타데이터로 정보 전달              | 토큰 자체에 상태 포함                |
| **Stateless 여부**  | Stateful                             | Stateful                            | Stateless                          | Stateless                          |
| **보안**           | 서버가 관리하여 안전                 | 조작 및 탈취 위험                   | 안전 (HTTPS 기반 추천)              | 서명으로 데이터 변조 방지            |
| **확장성**         | 낮음 (서버 자원 소모)                | 낮음 (쿠키 관리 복잡)               | 높음                                | 높음                                |
| **사용 사례**       | 로그인 상태 유지, 사용자 세션 관리    | 사용자 상태 저장 (선호도, 세션 ID)   | 인증 정보, 데이터 전송              | API 인증, 분산 서비스 인증          |


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

### Http Cookie
 - 로컬에 저장하고 서버에 전송하여 사용자 상태정보 유지
 - 키-값 쌍으로 구성
 - 순서
    1. 로그인
    2. 인증되면 사용자 고유ID+토큰 생성
    3. HTTP헤더에 포함하여 클라이언트에 전달
    4. 클라이언트는 로컬에 저장
    5. 서버 요청 시 쿠키를 헤더에 포함하여 전달
    6. 서버 쿠키 검증 후 리턴값 전달

#### Controller
```java
@Slf4j
@RequiredArgsConstructor
@RestController
@RequestMapping("/api")
public class AccountApiController {

    private final UserService userService;
    private final UserRepository userRepository;

    @PostMapping("/login")
    // 성공하면 클라이언트헤더로 cookie값 전달
    public void login(@RequestBody LoginRequest loginRequest, HttpServletResponse httpServletResponse){
        userService.login(loginRequest, httpServletResponse);
    }

    // 쿠키값 받아서 처리
    @GetMapping("/me")
    public UserDTO me(HttpServletRequest httpServletRequest,
                      @CookieValue(name = "autorization-cookie") String authorizationCookie){
        /**
         * 쿠키를 받아오는 방법
         * 1. @CookieValue로 가져온다
         * 2. httpServletRequest.getCookie()
         */
/*        var cookies = httpServletRequest.getCookies();

        if(cookies != null){
            for(Cookie cookie : cookies){
                log.info("key : {}, value : {}", cookie.getName(), cookie.getValue());
            }
        }else{
            return  null;
        }*/
        log.info("cookie: {}", authorizationCookie);
        var userDto = userRepository.findById(authorizationCookie);

        return userDto.get();
    }
}
```

#### Service
```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public void login(LoginRequest loginRequest, HttpServletResponse httpServletResponse){
        var id = loginRequest.getId();
        var pw = loginRequest.getPassword();

        var optionalUser = userRepository.findByName(id);

        if(optionalUser.isPresent()){
            var userDTO = optionalUser.get();

            if(userDTO.getPassword().equals(pw)){
                // cookie에 저장
                var cookie = new Cookie("autorization-cookie", userDTO.getId());
                cookie.setDomain("localhost");
                cookie.setPath("/"); //모든 URL 사용가능
                cookie.setMaxAge(-1); // 유효시간
                cookie.setHttpOnly(true); // 프론트 JS에서 cookie 접근,수정하지못하도록 막는 옵션
                cookie.setSecure(true); // https에서만 사용옵션

                httpServletResponse.addCookie(cookie);
            }else{
                throw new RuntimeException();
            }
        }else{
            throw new RuntimeException();
        }
    }
}
```

### Header를 통한 인증
 - native에서 주로 사용하는 방식
 - oauth, basic, diget를 통한 방식
 - 특정 header에 token을 넣어 인증
```java
return userDto.getUserId(); // 이것만 해주면 됨

//받을때는
@RequestHeader(name = "autorization-cookie") String cookie
```

### JWT TOKEN인증
 - Json Web Token : Json객체를 활용하여 토큰 인증
 - Url, Header, Html과 같은 다양한 방식으로 전달 가능
 - Header, Payload, Signature(header&payload를 비밀키로 생성) 세 부분으로 구성
 - Payload는 클레임(Id, 권한 등) 정보를 포함하며 Json으로 인코딩

 - 장점
    1. 토큰기반이라 서버에서 세션저장소 유지필요 없음
    2. Json이므로 다양한 플랫폼 간 전송 구현 쉬움
    3. Signature을 통한 무결성 보장으로 변조여부 쉽게 판단가능

 - 단점
    1. JWT가 커지면 네트워크 리소스 증가
    2. JWT 발급 이후에는 내부 정보 수정 불가하므로 유효시간 짧게 필요
    3. HTTPS등을 사용해 JWT 보안 확보 필요

#### Service
```java
@Slf4j
@Service
public class JwtService {

    private static String secretKey="abcdfsjdklfsjdlfsdfsdfdsfsdfsd";

   public String create(Map<String, Object> claims, LocalDateTime expireAt){
       var key = Keys.hmacShaKeyFor(secretKey.getBytes());
       var dateExpireAt = Date.from(expireAt.atZone(ZoneId.systemDefault()).toInstant());

       return Jwts.builder()
               .signWith(key, SignatureAlgorithm.HS256)
               .claims(claims)
               .expiration(dateExpireAt)
               .compact();
   }

   public void validation(String token){
       var key = Keys.hmacShaKeyFor(secretKey.getBytes());

       var parser = Jwts.parser()
               .verifyWith(key)
               .build();

       try{
           var result = parser.parseSignedClaims(token);
       }catch (Exception e){
           if(e instanceof SignatureException){
               throw new RuntimeException();
           }else if(e instanceof ExpiredJwtException){
               throw new RuntimeException();
           }else{
                throw new RuntimeException();
           }
       }
   }
}
```

#### Test
```java
	@Test
	void tokenCreate(){
		var claims = new HashMap<String, Object>();
		claims.put("user_id", 1);

		var expiredAt = LocalDateTime.now().plusMinutes(10);

		var jwtToken = jwtService.create(claims, expiredAt);

		System.out.println(jwtToken);
	}

	@Test
	void tokenValidation(){
		var token = "....";
		jwtService.validation(token);
	}
```
