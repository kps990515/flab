## 10/03 설계 전 지식

### REST란
- 정의
    - URI를 통해 자원을 명시하고 
    - HTTP Method를 통해 해당자원에 대한 CRUD를 적용
    - Client에게 자원의 상태에 대한 정보를 JSON, XML로 전달
- 의미
    - 자원(resource)의 표현(representation)
        - DB의 학생정보가 자원일때 students를 자원의 표현으로 정한다(www.xxx/students)
    - 상태정보 전달
        - 데이터가 요청되어지는 시점에 자원의 상태 전달
        - Json, xml 등으로 전달   
- 장점
    1. HTTP 프로토콜 인프라를 사용해서 REST를 위한 인프라 구축 필요 X
    2. HTTP 프로토콜을 사용하는 모든 플랫폼에서 사용 가능
    3. REST API 메시지가 의도하는 바를 쉽게 파악 가능     
- 단점 : 표준이 없음

- 필요이유 : 멀티 클라이언트 및 플랫폼에 대한 서비스 자원 지원 아키텍쳐 
- 특징
    1. 서버 클라이언트 구조
    2. Stateless : 통신이 끝나면 상태를 유지하지 않음
        - Client의 Context(쿠키)를 Server에 저장하지 않는다
        - Server는 각각의 요청을 별개의 것으로 인식, 처리(처리방식 일관성, 서비스 자유도)
    3. 캐시 사용 가능
    4. 계층화 
        - Client는 Rest API SERVER만 호출
        - SERVER는 다중 계층으로 구성(로드밸런싱, 암호화, 인증, 게이트웨이 등)
    5. 인터페이스 일관성 : 다양한 플랫폼에서 사용가능    

- REST API : REST기반으로 서비스 API 구현한 것
- RESTFUL : REST API를 제공하는 웹서비스

### Spring
- [정의](https://joychae.tistory.com/27) : 엔터프라이즈용 Java 애플리케이션 개발을 편하게 할 수 있게 해주는 /오픈소스 /경량급 /애플리케이션 프레임워크
    1. 스프링 이전에는 비즈니스 로직 구현을 위한 기술을 따로 공부해야했음, 스프링이 제공하는 기능을 선택하고 기술보다는 로직개발에 집중가능
    2. 어떤 개인 및 기업도 스프링을 사용하여 웹 애플리케이션을 개발을 할 수 있으며, 필요하다면 스프링의 코드를 일부 수정하여 사용하여도 무관
    3. 스프링을 사용함으로써 기존 기술을 사용할 때에 불가피하게 작성해야만 했던 불필요하게 복잡한 코드를 제거하여 코드의 복잡성을 낮출 수 있음
    4. 애플리케이션 프레임워크는 애플리케이션을 개발하는 데에 있어 필요한 모든 업무 분야 및 모든 기술과 관련된 코드들의 뼈대를 제공

- [스프링을 쓸때 장점](https://joychae.tistory.com/27)
    - 설계는 잘하는 사람한테 맡기고 나머지 개발자는 서비스로직 구현에만 집중(IOC를 통해 설계잘한 프레임워크가 관리한다)
    1. POJO기반 구성 : 특정 라이브러리나, 기술에 종속적이지 않고 JAVA코드 객체 구성하는 방식 그대로 객체지향적 사용 가능
    2. DI를 통한 객체관계 구성 
        - 메소드, 객체의 호출이 IOC를 통해 외부에서 진행
        - DI 발생
        - 그로인해 개발자가 POJO개발이 가능
    3. 컴포넌트 스캔과 자동 의존관계 설정
        - @Controller, @Service 등을 통해 스프링 빈 등록, 스프링컨테이너가 관리
        - 생성자 @Autowired를 통해 스프링이 연관된 객체를 찾아서 넣어줌(DI)
        - (MemberController에 @Autowired붙여서 memberService객체를 Controller 생성될때 연결)
    4. AOP : 공통관심사항, 핵심관심사항을 분리해 비즈니스로직구현에만 집중할수있게 한다
    5. 다른 프레임워크의 포용 : 스프링은 전체 구조에 집중했기에 특정영역의 프레임워크와 공존하는 방식으로 사용 가능(WAS, DB 등)
    6. 수많은 라이브러리가 지원되서 확장성이 높음
- 스프링의 특징
    1. IOC(개발자 비즈니스구현에 집중, 변경용이, 의존성 낮아짐)
        - 객체 생성과 의존성 주입을 개발자가 아닌 스프링 프레임워크가 대신하는 원리
        - 라이브러리의 사용은 제어권이 어플리케이션에 있지만, 프레임워크는 어플리케이션의 제어권을 자기가 가져간다
        - Controller, Service 같은 객체들의 동작을 우리가 직접 구현하기는 하지만, 해당 객체들이 어느 시점에 호출될 지는 신경쓰지 않는다. 
        - 오브젝트(빈)의 생성(@Service, @Controller)과 의존 관계 설정(@Autowired), 사용, 제거 등의 작업을 애플리케이션 코드 대신 스프링 컨테이너가 담당
        - Junit의 @BeforEach, @AfterEach등이 제어의 역전(Junit프레임워크가 알아서 해당 어노테이션 구현된 소스 실행)
        - 메소드나 객체의 호출작업을 개발자가 결정하는 것이 아니라, 외부에서 결정되는 것
        - 객체(Controller, Service...)들의 동작은 구현하지만, 생명주기는 스프링이 제어
        - 이전 : 객체 생성 -> 의존성 객체를 내부에서 new로 생성 -> 의존성 객체 메소드 호출
        - IOC : 객체 생성 -> 의존성 객체 주입(제어권을 스프링에게 위임하여 스프링이 만들어놓은 객체를 주입) -> 의존성 객체 메소드 호출
        - 스프링이 모든 의존성 객체를 스프링이 실행될떄 만들고 필요할때 주입하여 Singleton 패턴을 가짐
    2. DI 
        - 객체를 직접 생성하는 게 아니라 외부(IOC 컨테이너)에서 생성한 후 주입(setter) 시켜주는 방식
        - ex: A클래스에서 B,C를 new 생성하는 것이 아닌, 외부에서 new를 하고 A는 setter로 B,C 사용
        - 스프링 서버가 올라 갈 때 애플리케이션 컨텍스트가 @Bean이나 @Service, @Controller 등 어노테이션을 이용하여 등록한 스프링 빈을 생성
        - @Autowired 어노테이션이 붙은 위치에 의존 관계 주입을 수행
        - 모듈 간의 결합도가 낮아지고 유연성이 높아진다.         
        - [의존성 주입 3가지 방법](https://steady-coding.tistory.com/600)
            1. 생성자 주입(권장)
             - 장점 : 의존관계를 모두 주입해야하므로 NPE방지, 불변성보장, 순환참조 방지(컴파일러가 잡아줌)
            ```java
            @Service
            public class BurgerService {

                private BurgerRecipe burgerRecipe;

                @Autowired // 
                public BurgerRecipe(BurgerRecipe burgerRecipe) {
                    this.burgerRecipe = burgerRecipe;
                }
            }
            ```
            2. Setter 주입
             - 장점 : 선택적인 의존성을 사용할 수 있다.
             - 단점 : 주입받지 않은 구현체를 사용하는 메소드에서 NPE가 발생, 순환참조 발생가능
            ```java
            @Service
            public class BurgerService {

                private BurgerRecipe burgerRecipe;

                @Autowired
                public void setBurgerRecipe(BurgerRecipe burgerRecipe) {
                    this.burgerRecipe = burgerRecipe;
                }
            }
            ```
            3. 필드 주입
             - 장점 : 사용간편
             - 단점 : 단일책임위반(여러 의존성주입 가능), 의존성 파악 어려움, 순환참조 위험
            ```java
            @Service
            public class BurgerService {

                @Autowired
                private BurgerRecipe burgerRecipe;
            }
            ```
### [프레임워크 라이브러리](https://webclub.tistory.com/458)
 - 프레임워크
    - 프레임워크는 뼈대나 기반구조를 뜻하고, 제어의 역전 개념이 적용
    - 소프트웨어의 특정 문제를 해결하기 위해서 상호 협력하는 클래스와 인터페이스의 집합
 - 라이브러리
    - 단순 활용가능한 도구들의 집합   
 - 차이점 : 제어 흐름에 대한 주도성이 누구에게/어디에 있는가
     - 프레임워크 : 전체적인 흐름을 스스로가 쥐고 있으며 사용자는 그 안에서 필요한 코드개발
     - 라이브러리 : 라이브러리는 사용자가 전체적인 흐름을 만들며 라이브러리를 가져다 쓰는 것

### [merge](https://im-developer.tistory.com/182)
 - merge :  커밋 이력을 모두 남길 때 사용
    - Fast-Forward : 내 브랜치가 생성된 후 main에 새로운 커밋이 없을때
    - Recursive : main에 새로운 커밋이 있을때
 - squash merge : 모든 커밋 이력이 하나의 커밋으로 합쳐지며 병합(이력 하나로 통합)
 - rebase :  main브랜치의 최신 커밋을 기준으로 base를 새로 설정해 병합(Fast-Foward사용)
