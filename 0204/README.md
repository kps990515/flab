## 02/04

### [Git Flow 전략](https://sungjk.github.io/2023/02/20/branch-strategy.html)
1. Git Flow 특징
 - 각 용도에 맞게 main(master), develop, feature, release, hotfix 브랜치를 분리해서 사용
 - 명확한 릴리즈 기간과 주기적인 버전이 정해진 프로덕트를 개발하는 환경에 적합
 - 릴리즈 버전 관리를 위한 release 브랜치를 따로 관리하기 때문에, 특정 버전에 대한 유지보수 기간이 길고, 여러 버전을 동시에 관리해야 할 필요가 있을때 유용함
 - 2, 3과 같은 장점 때문에 소규모 팀보다는 규모가 있는 팀에 더 어울림

2. Github Flow
 - Git Flow에서 develop, release, hotfix 브랜치를 제거한 형태
 - 브랜치는 로컬에 commit하고 정기적으로 원격 브랜치에 push
 - 준비완료되었으면 상시 pull request
 - 상시 master배포

3. Trunk-Based Development
 - Trunk 기반 개발은 단일 브랜치를 지향하기 때문에, 오랫동안 지속되는 브랜치를 지양
 - 잦은 기능 수정과 빠른 릴리즈가 필요한 조직에 적합

### [Git merge](https://velog.io/@yujuck/Git-Merge-%EC%A0%84%EB%9E%B5)
1. Merge
    - 장점 : merger된 후에도 브랜치는 남아서 history파악가능
    - 단점 : 가독성 떨어짐

2. Squash and Merge
 - 여러개의 commit을 하나로 합친 후 merge
 - 장점 : merge commit이 남아서 history파악 가능
 - 단점 : merge전 commit기록들 파악 어려움

3. Rebase Merge
 - 브랜치의 베이스를 새로 설정
 - 예시 :  checkout시점이 아닌 merge시점 master브랜치에서 나온것처럼 표시
 - 장점 : merge기록이 남지않아 하나의 브랜치처럼 보임, commit정보알수있음
 - 단점 : merge기록이 없어 언제 되었는지 알수없음

### [Kotlin Null](https://tourspace.tistory.com/114)
1. Null이 될수있는 type
 - type에 ?붙이면 null 가능
```kotlin
fun strLenSafe(s: String?): Int =
    if (s != null) s.length else 0

fun main(args: Array) {
    val x: String? = null
    println(strLenSafe(x))
    println(strLenSafe("abc"))
}
```

2. null safe operator : ?.
 - null이 아닐때만 함수실행, null이면 null반환
```kotlin
fun printAllCaps(s: String?) {
    val allCaps: String? = s?.toUpperCase()
    println(allCaps)
}

fun main(args: Array) {
    printAllCaps("abc")
    printAllCaps(null)
}
```

3. null 예외처리 : ?:
 - null이 아니면 ?: 왼쪽실행, 아니면 오른쪽 실행
```kotlin
 fun getName(str: String?) {
    val name = str ?: "Unknown"
}
```

4. safe cast : as?
 - as? 는 casting을 시도하고, casting이 불가능 하면 null을 반환
 - Person이면 Person대입 아니면 false반환
```kotlin
val otherPerson = o as? Person ?: return false
```

5. 강제 notnull : !!
```kotlin
fun ignoreNulls(s: String?) {
    val sNotNull: String = s!!
    println(sNotNull.length)
}
```

6. not null인경우에만 실행 : let
```kotlin
    var email: String? = "yole@example.com"
    email?.let { sendEmailTo(it) }
    email = null
```

7. 초기화 지연 
 - 코틀린에서 클래스 property 초기화는 생성자안에서만 가능하기에 나중에 하기 어려움

 1. lateinit
 - var만 가능
 - 프로퍼티에 대한 게터 세터 사용 불가
```kotlin
    private lateinit var myService: MyService

    @Before fun setUp() {
        myService = MyService()
    }
```
 2. lazy
 - val 가능
 - 호출시점에 초기화 진행
```kotlin
class LazyTest {
    init {
        println("init block") // 2
    }

    val subject by lazy { // 6
        println("lazy subject")
        "kotlin" // lazy에 의한 반환값
    }

    fun flow() {
        println("not initialized") // 4
        println("sub one : $subject") // 5 최초 접근 시점에 lazy 블록 먼저 호출 후 현재 println 출력
        println("sub two : $subject") // 7
    }
}

fun main() {
    val test = LazyTest() // 1
    test.flow() // 3
}
``` 

8. nullable type의 확장함수
 - 널 가능한 객체에 확장함수 선언 가능
```kotlin
fun String?.isNullOrBlank(): Boolean =
    this == null || this.isBlank()
```

9. Generic은 기본적으로 Nullable
 - non null을 위해선 uppber bound제한 필요
```kotlin
fun <T: Any> printHashCode(t: T) {
    println(t.hashCode())
}
```

### [함수형 프로그래밍](https://jongminfire.dev/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D%EC%9D%B4%EB%9E%80)
 - 자료처리를 수학적 함수의 계산으로 취급하고 상태와 가변 데이터를 멀리하는 프로그래밍 패러다임
 - 장점
    - 높은 수준의 추상화
    - 함수단위 코드 재사용
    - 불변성 지향
 - 단점
    - 코드 가독성 안좋음
    - 반복문이 for이 아닌 재귀를 사용하여 무한루프가능
    - 순수함수 사용은 쉽지만 조합은 어려움

1. 순수함수
 - 동일한 입력 동일 출력
 - 독립적 실행(주변 영향 없음)
 - 함수 내부에서 인자의 값을 변경하거나 프로그램 상태를 변경하는 Side Effect가 없는 것

2. 비상태, 불변성
 - 데이터는 불변
 - 변경이 필요한 경우 원본이 아닌 복사본 생성 후 사용

3. 선언형 함수
 - 명령형은 어떻게 하는지 중요(for문 -> 배열 요소 뽑아냄 -> 각 요소에 multiply...)
 - 선언형은 무엇을 할지가 중요(map.filter.reduce 어떻게는 내장함수로 생략)

4. 1급객체, 고차함수
 - 함수가 1급 객체
 - 1급객체
    - 변수나 데이터 구조안에 담을 수 있다.
    - 파라미터로 전달 할 수 있다.
    - 반환값(return value)으로 사용할 수 있다.
    - 할당에 사용된 이름과 관계없이 고유한 구별이 가능하다.
    - 동적으로 프로퍼티 할당이 가능하다.
 - 고차함수
    - 함수를 인자로 전달가능
    - 함수의 반환값으로 함수 사용가능

### [코틀린장점](https://jie0025.tistory.com/528)    
1. 보일러플레이트 제거
 - get,set
 ```kotlin
 class Person(name: String) : get,set 없음
 class Person(var name: String) : get,set 있음
 class Person(val name: String) : set 있음

 person.name = "ㅇㅇㅇ" // set 구현안해도됨
 println(person.name) // get 구현안해도됨
 ```

 - null check : ?, ?. 등등
 - exception 처리 : ?: 등

2. NPE
  - ?, ?: 사용

3. 익명클래스
 - 람다, 고차함수로 간단하게 작성가능
```kotlin
button.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        // 클릭시 발생할 이벤트 코드 작성
    }
});

button.setOnClickListener {
    // 클릭시 발생할 이벤트 코드 작성
}
```

4. 타입추론
5. 코루틴으로 비동기성

### [스프링빈 라이프사이클](https://dev-coco.tistory.com/170)
 - 빈 : 스프링 컨테이너가 관리하는 자바 객체
 - 빈 사용이유 : 객체 의존관계 등록시 컨테이너가 빈과 의존성 생성
 - 스프링 IOC컨테이너는 Bean객체를 의존성 관리(생성~소멸 라이프사이클을 책임져줌)
 - 개발자는 로직에만 집중

- 의존성 주입과정
 - Bean으로 등록가능한 어노테이션(@Bean, @Controller...), 설정을 읽어 Bean등록
 - 객체 생성
    1. 생성자 주입 : 객체 생성 & 의존관계 주입(동시)
        - 장점 : NPE방지, 의존관계 오류를 컴파일단계에서 검증가능
    ```kotlin
    // 아래와 같이 의존관계 주입이 동시에 되지않는다면 Service의존이 없어 Controller생성불가
    public class Main {    
        public static void main(String[] args) {         
            // CocoController controller = new CocoController(); // 컴파일 에러         
            CocoController controller1 = new CocoController(new CocoService());    
        }}
    ```
    2. Setter, Field 주입 : 객체 생성 후 의존관계 주입
    ```kotlin
    @Controller
    public class CocoController {	    
        private CocoService cocoService;        
        
        @Autowired    
        public void setCocoService(CocoService cocoService) {    	
            this.cocoService = cocoService;    
        }}
    ```

- 라이프사이클
 1. IOC 컨테이너 생성
 2. Bean 생성
 3. 의존관계 주입
 4. 초기화 콜백 메소드 호출
 5. 사용
 6. 소멸전 콜백 메소드 호출
 7. 호출종료

- 콜백방법 : @PostConstruct, @Predestroy
 - 장점
    - 간편성
    - 스프링종속적이지 않음(자바표준지원 기술)
    - 컴포넌트 스캔과 알맞음
 - 단점
    - 외부라이브러리에서 사용 불가
```kotlin
public class ExampleBean {     
    @PostConstruct    
    public void initialize() throws Exception {        
        // 초기화 콜백 (의존관계 주입이 끝나면 호출)    
    }     
    
    @PreDestroy    
    public void close() throws Exception {        
        // 소멸 전 콜백 (메모리 반납, 연결 종료와 같은 과정)    
    }}
```

### [스프링 Bean Scope](https://code-lab1.tistory.com/186)
 - 빈의 스프링이 관리하는 범위
1. Singleton 
 - 기본 스코프, 컨테이너 시작~종료 유지
 - 조회 시 동일 인스턴스 반환
2. ProtoType
 - 빈의 생성, 의존관계주입까지만 관여
 - 조회 시 새로운 인스턴스 생성 후 반환
 - 이후 생성주기 관리는 클라이언트에게 있음(종료콜백 없음)
 - 문제점 
    - 종료가 안되서 Sigleton bean내부에 prototype bean이 있을시 계속 재사용됨
    ```kotlin
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(ClientBean.class, PrototypeBean.class);

        ClientBean clientBean1 = ac.getBean(ClientBean.class);
        int count1 = clientBean1.logic();
        assertThat(count1).isEqualTo(1);

        ClientBean clientBean2 = ac.getBean(ClientBean.class);
        int count2 = clientBean2.logic();
        assertThat(count2).isEqualTo(2); // 1을 기대했지만 prototype이 재사용되어 2 출력


    static class ClientBean{
        private final PrototypeBean prototypeBean;

        @Autowired
        public ClientBean(PrototypeBean prototypeBean) {
            this.prototypeBean = prototypeBean;
        }

        public int logic() {
            prototypeBean.addCount();
            int count = prototypeBean.getCount();
            return count;
        }
    }
    ```
 - 해결법 : Object Provider 사용(Dependency Lookup 제공)
 ```kotlin
    @Autowired
    private ObjectProvider<PrototypeBean> prototypeBeanProvider;

    public int logic() {
        // 항상 새로운 bean 생성
        PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
        prototypeBean.addCount();
        int count = prototypeBean.getCount();
        return count;
    }
 ```

3. 웹 관련 스코프
 - request : HTTP 요청 ~ 출력까지(HTTP요청마다 생성)
 - session : 웹 세션
 - application : 웹 서블릿 컨텍스트






