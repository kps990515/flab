# argumentResolver
- controller parameter에 User클래스가 있으면
- requestContext에서 UserId 가져오고
- userEntity의 정보를 리턴해줌

```java
@Component
@RequiredArgsConstructor
public class UserSessionResolver implements HandlerMethodArgumentResolver {

    private final UserService userService; // 사용자 서비스로, 사용자 정보와 관련된 기능을 제공합니다.

    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        // Controller에 들어오는 파라미터가 User 타입인지 확인
        return (parameter.getParameterType().equals(User.class));
    }

    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
        // 현재 요청의 RequestAttributes 가져오기
        var requestContext = RequestContextHolder.getRequestAttributes();
        // 요청 범위 내에서 userId 속성 가져오기
        var userId = requestContext != null ? requestContext.getAttribute("userId", RequestAttributes.SCOPE_REQUEST) : null;
        
        // userId가 null인 경우 예외를 발생시킴
        if(userId == null){
            throw new ApiException(CommonErrorCode.NULL_POINT, "userId == null");
        }

        // userId를 사용하여 사용자 엔티티 가져오기
        var userEntity = userService.getUserWithThrow(userId.toString());

        // 사용자 정보를 User 객체로 변환하여 반환
        return ObjectConvertUtil.getInstance().copyVO(userEntity, User.class);
    }
}
```

