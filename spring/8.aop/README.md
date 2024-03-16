## AOP

### 정의
 - 관점 지향 프로그래밍
 - 핵심로직을 건드리지 않고 부가적인 로직을 공통으로 적용 가능
 - Web Layer : Rest API제공, Client중심의 로직
 - Business Layer : 내부 Logic
 - Data Layer : DB와 외부와 연동


```java
@Aspect // AOP적용 클래스
@Component
public class TimerAop {

    // AOP 적용할 위치, 범위 세팅하는 어노테이션(Bean에만 적용가능)
    @Pointcut(value = "within(com.example.filter.controller.UserApiController)")
    // execution : 반환타입, 타입, 메소드, 파라미터 기준으로 지정
    // within : 특정 경로 타입 지정
    // this, target : 특정 타입 객체
    // args : 특정 타입의 파라미터를 갖는 메소드 기준
    // @target, @args, @within, @annotation : 특정 어노테이션 가진 타겟, 파라미터, 경로 등
    public void timerPointCut(){

    }

    /*
     실행순서
     1. around
     2. before
     3. 메소드 실행
     4. afterReturning(afterThrowing)
     5. after
     6. around
     */

    // UserApiController에 AOP가 실행되며
    // 호출전 / 호출후 에 적용하겠다
    @Around(value = "timerPointCut()")
    public void around(ProceedingJoinPoint joinPoint) throws Throwable{
        System.out.println("메소드 실행 이전");

        // 1. parameter 들어오면 가져와서 수정하는 법
        // userRequest를 변환
        Arrays.stream(joinPoint.getArgs()).forEach(
                it -> {
                    if(it instanceof UserRequest){
                        var tempUser = (UserRequest) it;
                        var phoneNumber = tempUser.getPhoneNumber().replace("-", "");
                        tempUser.setPhoneNumber(phoneNumber);
                    }
                }
        );

        // 2. Primitive 타입을 처리하는 법
        var newObjs = List.of(new UserRequest());

        // API 시간측정
        var stopWatch = new StopWatch();
        stopWatch.start();

        joinPoint.proceed(newObjs.toArray()); // 다음 AOP나 타겟 메소드 실행

        stopWatch.stop();
        System.out.println("총 소요 시간 : " + stopWatch.getTotalTimeMillis());

        System.out.println("메소드 실행 후");
    }

    @Before(value = "timerPointCut()")
    public void before(JoinPoint joinPoint){

    }

    @After(value = "timerPointCut()")
    public void after(JoinPoint joinPoint){

    }

    @AfterReturning(value = "timerPointCut()", returning = "result")
    public void afterReturning(JoinPoint joinPoint, Object result){

    }

    @AfterThrowing(value = "timerPointCut()", throwing = "ex")
    public void afterThrowing(JoinPoint joinPoint, Throwable ex){

    }
}
```