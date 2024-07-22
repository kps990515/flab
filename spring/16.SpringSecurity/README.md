## SpringSecurity

## 주요기능
1. 인증
2. 권한부여
3. 세션관리
4. 보안설정
5. 보안이벤트 처리

## CSRF(Cross-Site Request Forgery, 사이트 간 요청 위조) 공격의 작동 방식
1. 사용자가 인증된 상태: 사용자가 웹사이트에 로그인하여 세션을 유지하고 있는 동안 공격이 발생합니다.
2. 악의적인 사이트 방문: 사용자가 악의적인 웹사이트 또는 공격자가 조작한 페이지를 방문합니다.
3. 위조된 요청 전송: 악의적인 사이트는 사용자의 인증된 세션을 이용해 사용자의 의도와는 상관없이 원래의 웹사이트로 요청을 전송합니다.
4. 웹사이트가 요청 처리: 웹사이트는 이 요청이 사용자로부터 온 정상적인 요청으로 생각하고 처리합니다.

## CSRF의 예시
1. 사용자가 온라인 뱅킹 사이트에 로그인하여 세션을 유지하고 있습니다.
2. 그 사용자가 동시에 다른 악의적인 사이트를 방문합니다.
3. 악의적인 사이트가 사용자의 인증된 세션을 이용해 사용자의 계좌에서 돈을 이체하는 요청을 보냅니다.
4. 온라인 뱅킹 사이트는 이 요청을 정상적인 요청으로 처리하여 돈을 이체합니다.

## CSRF 방어 방법
1. CSRF 토큰 사용: 요청마다 고유한 CSRF 토큰을 생성하고 이를 검증합니다.
2. 참조 헤더 검사: 요청의 참조 헤더를 확인하여 신뢰할 수 있는 출처에서 온 요청인지 확인합니다.
3. CAPTCHA 사용: 사용자 입력을 통해 사람이 요청을 보냈는지 확인합니다.

### SecurityConfig
```java
@Configuration                                                                                                                            
@EnableWebSecurity // security활성화                                                                                                         
public class SecurityConfig {                                                                                                             
                                                                                                                                          
    private List<String> SWAGGER = List.of(                                                                                               
         "/swagger-ui.html",                                                                                                              
         "/swagger-ui/**",                                                                                                                
         "/v3/api-docs/**"                                                                                                                
    );                                                                                                                                    
                                                                                                                                          
    public SecurityFilterChain filterChain(HttpSecurity httpSecurity) throws Exception{                                                   
        httpSecurity                                                                                                                      
            .csrf(AbstractHttpConfigurer::disable) // CSRF 비활성화의 최신 방법                                                                    
            .authorizeHttpRequests(authorize -> authorize                                                                                 
                .requestMatchers(PathRequest.toStaticResources().atCommonLocations()).permitAll() // 정적 리소스에 대한 모든 요청 허용                  
                .requestMatchers(SWAGGER.toArray(new String[0])).permitAll() //swagger는 인증없이 통과                                           
                .anyRequest().authenticated() // 그 외 모든 요청은 인증 필요                                                                         
            ).formLogin(Customizer.withDefaults());                                                                                       
                                                                                                                                          
        return httpSecurity.build();                                                                                                      
    }                                                                                                                                     
                                                                                                                                          
    @Bean                                                                                                                                 
    public PasswordEncoder passwordEncoder(){ // encoder 인터페이스                                                                                        
        //hash 암호화                                                                                                                        
        return new BCryptPasswordEncoder();                                                                                               
    }                                                                                                                                     
}                                                                                                                                         
```

### AuthorizationService
- UserDetailsService 인터페이스는 Spring Security에서 사용자의 인증 및 권한 부여 정보를 나타내기 위해 사용되는 인터페이스
```java
@RequiredArgsConstructor
@Service
public class AuthorizationService implements UserDetailsService {

    private final StoreUserService storeUserService;
    private final StoreRepository storeRepository;
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {

        var storeUserEntity = storeUserService.getRegisterUser(username);

        var storeEntity = storeRepository.findFirstByIdAndStatusOrderByIdDesc(
                storeUserEntity.get().getStoreId(),
                StoreStatus.REGISTERED
        );

        return storeUserEntity.map(it ->{

                    var userSession = UserSession.builder()
                            .userId(it.getId())
                            .email(it.getEmail())
                            .password(it.getPassword())
                            .status(it.getStatus())
                            .role(it.getRole())
                            .registeredAt(it.getRegisteredAt())
                            .lastLoginAt(it.getLastLoginAt())
                            .unregisteredAt(it.getUnregisteredAt())

                            .storeId(storeEntity.get().getId())
                            .storeName(storeEntity.get().getName())
                            .build();

                    return userSession;
                })
                .orElseThrow(() -> new UsernameNotFoundException(username));
    }
}
```

### UserSession
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class UserSession implements UserDetails {

    // user
    private Long userId;

    private String email;

    private String password;

    private StoreUserStatus status;

    private StoreUserRole role;

    private LocalDateTime registeredAt;

    private LocalDateTime unregisteredAt;

    private LocalDateTime lastLoginAt;

    // store
    private Long storeId;
    private String storeName;


    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return List.of(new SimpleGrantedAuthority(this.role.toString()));
    }

    @Override
    public String getPassword() {
        return this.password;
    }

    @Override
    public String getUsername() {
        return this.email;
    }

    @Override
    public boolean isAccountNonExpired() {
        return this.status == StoreUserStatus.REGISTERED;
    }

    @Override
    public boolean isAccountNonLocked() {
        return this.status == StoreUserStatus.REGISTERED;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return this.status == StoreUserStatus.REGISTERED;
    }

    @Override
    public boolean isEnabled() {
        return true;
    }
}
```

### StoreUserApiController
```java
@RequiredArgsConstructor
@RestController
@RequestMapping("/api/store-user")
public class StoreUserApiController {

    private final StoreUserConverter storeUserConverter;

    @GetMapping("/me")
    public StoreUserResponse me(
            @Parameter(hidden = true) // docs에서 숨김처리
            @AuthenticationPrincipal UserSession userSession // 현재 인증된 사용자의 UserDetails 또는 사용자 정의 객체주입
    ){
        return storeUserConverter.toResponse(userSession);
    }
}
```

