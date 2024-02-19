## 02/11

### [OAuth](https://blog.naver.com/mds_datasecurity/222182943542)
- OAuth(openAuthorization) 등장배경
앱에 페이스북, 구글 정보를 가져오기위해 ID,PASSWORD를 받아오는것을 위험하기 때문

- OAuth란
다양한 플랫폼(구글, 페이스북 등)의 사용자 데이터 접근 권한을 위임 받는 프로토콜

- 주체
    1. 리소스 소유자 또는 사용자
        - 구글, 페이스북, 나
    2. 인증 서버
        - 리소스 소유자를 인증하고 Client에게 액세스 토큰 발급
    3. 리소스 서버 : 구글, 페이스북 서버
    4. Client(Application) : 우리의 서비스

 - 주요용어
    1. Authentication : 인증, 접근 자격 검증
    2. Authorization : 자원에 접근할 권한 부여(Access token부여)
    3. Access Token : 자원에 접근할때 사용하는 Token
    4. Refresh Token : Access Token만료 시 갱신하는 Token

 - 권한부여 방식
    1. Authorization Code 부여 방식
        - Authorization Code 요청 -> 로그인 -> 발급 -> AccessToken발급 -> 자원 요청
        - 기본으로 쓰이는 방식(간편 로그인)
        - Client가 사용자 대신 특정 자원 접근 요청
        - Refresh Token 사용가능

    ![img](https://postfiles.pstatic.net/MjAyMzAyMDNfMTA5/MDAxNjc1Mzg2ODIyMTk2.p2TqHr3sSdI5wcVsE01Hn95R-LpblY6qk1-TJ8dmz2cg.opYLjtRDwQu1F4-9SwreEr_df0fc-b1bgCZo2iyZRTIg.JPEG.mds_datasecurity/000.jpg?type=w966)

    2. 암묵적 승인 방식
        - 자격증명을 안전하게 저장하기 힘든 클라이언트(ex: JavaScript등의 스크립트 언어를 사용한 브라우저)에게 최적화된 방식
        - Authorization Code없이 바로 Access Token 발급
        - 문제점 : json payload가 아닌 response url 파라미터로 AccessToken이 전달됨
        - Refresh Token 불가

    ![img](https://postfiles.pstatic.net/MjAyMDEyMjNfMjMz/MDAxNjA4NzAyMjQxOTg2.GTTG4UxxFQrWdRcrJHeQqoZODvOsdLwH70iTaJKsGJUg.wRwxtAmdLuOzscjqOjrm5MOaSF3gVJ-Q1wysvprXMTkg.PNG.mds_datasecurity/image.png?type=w966)

    3. 자원 소유자 비밀번호 인증 방식
        - id, password로 AccessToken받는 방식
        - SSO에서 사용(클라이언트, 리소스, 권한서버가 같은 시스템)
        - Refresh Token 사용 가능
    
    ![img](https://postfiles.pstatic.net/MjAyMDEyMjNfMTQ4/MDAxNjA4NzAyMjYwNjY0.9NccTFwC2vvPezXtsufXP5jaltyM4f3T0Szk7Ykqe50g.4XIlCvZQnGvXWvEdHNImR53EvUs72joB3dqsambiwX4g.PNG.mds_datasecurity/image.png?type=w966)

    4. 클라이언트 자격증명 승인 방식 
        - 인증, 리소스 서버에 해당 클라이언트 접근 권한이 있는 경우       
        - Refresh Token사용불가

    ![img](https://postfiles.pstatic.net/MjAyMDEyMjNfMjIx/MDAxNjA4NzAyNDQwMzQ5.86I4OrUs0bHxoJQKBbFcPkLuXxfEf1slgI_fnF40ja8g.9a4JaG7DQf8pGcyuRmEkpSbGdbff6hETz01uGPwiwRog.PNG.mds_datasecurity/image.png?type=w966)

### [캐싱]
 - 메모리 캐싱
    - 가장 빠르지만, 서버 재시작시 소멸
    - Caffeine, Guava 라이브러리 사용
 
 - 분산 캐싱
    - Redis, Memcached 사용
    - 서버간 공통 데이터 공유

 - DB쿼리 캐싱
     - In-Memory Database
     - Query Result Caching
     - Object-Relational Mapping (ORM) Caching

### [스프링변천사]

 - Spring Framework 1.x:
    - 출시년도: 2003년
    - 주요 특징:
        - AOP(Aspect-Oriented Programming) 지원.
        - DAO(Data Access Object) 패턴을 통한 데이터 액세스 지원.
        - JDBC 추상화 제공으로써 데이터베이스 액세스 단순화.

 - Spring Framework 2.x : 어노테이션, MVC의 등장
    - 출시년도: 2006년
    - 주요 변경 사항:
        - Java 5 지원으로 애노테이션 기반의 설정이 가능해짐.
        - IoC(Inversion of Control) 컨테이너의 확장.
        - 스프링 MVC 등장.

 - Spring Framework 3.0: RestFul 도입
    - 출시년도: 2009년
    - 주요 변경 사항:
        - RESTful 웹 서비스 지원 추가.
        - 스프링 Expression Language (SpEL) 도입

 - Spring Framework 3.1: 자바 Configuration 도입
    - 출시년도: 2011년
    - 주요 변경 사항:
        - JavaConfig(Java 기반 설정) 도입.
        - 조건부 설정(Conditional Configuration) 도입.
        - 캐시 추상화 및 지원 추가.

 - Spring Framework 3.2: 비동기, 자바캐싱, websocket 지원
    - 출시년도: 2012년
    - 주요 변경 사항:
        - 비동기 작업을 위한 @Async 애노테이션 도입.
        - JCache(Java Caching API) 지원.
        - WebSocket 지원 추가.

 - Spring Framework 4.0: 람다, 스프링부트 지원
    - 출시년도: 2013년
    - 주요 변경 사항:
        - Java 8 지원 (람다 표현식, 스트림 API 활용 가능).
        - 스프링 부트 1.0 등장(2014.4)

 - Spring Framework 4.3: HTTP/2, 스프링부트와 통합
    - 출시년도: 2016년
    - 주요 변경 사항:
        - 향상된 JCache 지원과 새로운 @Cacheable 애노테이션 기능 추가.
        - HTTP/2 지원.
        - 스프링 부트 1.4와 통합.    

 - Spring Framework 5.x:
    - 출시년도: 2017년
    - 주요 변경 사항:
        - Java 8 이상만 지원.
        - 리액티브 프로그래밍 지원.
        - 새로운 spring-webflux 모듈 도입.
        - Kotlin 지원.
        - 스프링부트 2.0(2018.3)
        - 스프링부트 2.1(2018.10) : Java 11지원
        - 스프링부트 2.2(2019.10) : 지연 초기화, Junit 5
        - 스플이부트 2.7(2022.5) : 네이티브 컴파일

 - Spring Framework 6.x:
    - 출시년도: 2022년
    - 주요 변경 사항:
        - Java 17 이상만 지원.
        - 네이티브 컴파일 지원
        - Jakarta EE 지원
        - AOT(Ahead-of-Time) 컴파일
            - 코드를 실행하기 전 네이티브 기계어로 컴파일 먼저 진행
            - 시작시간 감소, 메모리 사용량 감소, 전체 실행 속도 향상(미리 다 컴파일)      

### [자바 버전]

 - JDK 1.5
    - Generics: 제네릭 타입 도입.
    - 애노테이션(Annotations): 메타데이터 추가를 위한 애노테이션 도입.
    - Enum 타입: 열거형 타입 지원.
    - Autoboxing/Unboxing: 기본 타입과 래퍼 클래스 간 자동 변환 지원.
    - 향상된 for 루프 및 향상된 동기화: 코드 간결성을 위한 향상된 문법 지원.

 - JDK 1.7
    - 다이아몬드 연산자(Diamond Operator): 제네릭에서 타입 추론을 통한 코드 간결성 제공.
    - Try-with-Resources: 자동으로 리소스를 닫아주는 try-with-resources 문 추가.
    - 문자열 스위치: 문자열을 이용한 switch 문 추가. 

 - JDK 1.8(LTS)
    - 람다 표현식: 함수형 프로그래밍 지원을 위한 람다 표현식 도입.
    - 스트림 API: 데이터 처리를 위한 스트림 API 추가.
    - 메서드 참조(Method Reference): 메서드 참조를 통한 코드 간결성 제공.
    - 인터페이스의 디폴트 및 정적 메서드: 인터페이스에 디폴트 및 정적 메서드를 정의할 수 있게 됨.    
    - 날짜 및 시간 API
    - Metaspace 도입(기존 Heap내부 PermGen영역 => Native내부의 Metaspace)

 - Java 9
    - G1 GC 도입   
    - Epsilon GC 도입
 
 - Java 11(LTS)
    - HTTP/2 및 WebSocket을 지원하는 새로운 HTTP 클라이언트
    - Z 가비지 수집기(ZGC) 

 - Java 12
    - Shenandoah GC 도입

 - Java SE 17 (LTS) 
    - Sealed Classes: 클래스와 인터페이스의 사용 범위를 명시적으로 지정할 수 있는 Sealed Classes 도입.
    - switch문 패턴일치 : 값 반환 할당가능, break제거, -> 표현식
    - 외부함수, 메모리 API사용 : 외부 메모리 액세스 및 외부 함수 호출 가능(JVM 외부)

 - Java 21(LTS)
    - 가상 스레드 제공
    - Sequenced Collections 제공
    - switch case null 검사제공

###[Blocking, Non-blocking, Sync, Async](https://jh-7.tistory.com/25)

- 목적 : CPU의 효율적 사용, 성능향상

- 제어권
    - Blocking : 호출된 함수가 작업을 완료할 때까지 호출자를 대기시키는 방식
    - Non-Blocking : 호출된 함수가 즉시 반환되어 호출자가 다른 작업을 계속할 수 있게 하는 방식

- 시간
    - Sync :  작업들이 순차적으로 이루어지는 것을 의미합니다. 한 작업이 끝나야만 다음 작업이 시작
    - Async : 한 작업의 완료 여부와 상관없이 다른 작업이 시작될 수 있음

- Sync-blocking
    - 해당 작업이 완료될 때까지 다른 작업을 수행하지 못함

- Async-Blocking
    - 사용되지 않음

- Sync-NonBlocking
   - 호출된 작업이 즉시 반환
   - 호출자는 반환된 결과를 기반으로 즉시 다음 작업을 진행
   - 호출된 작업이 완료되면 callback후 작업완료
   - 작업 자체는 동기적으로 처리

- Async-NonBlocking
   - 비동기 프로그래밍 모델
   - 호출된 작업이 즉시 반환
   - 작업의 완료 여부와 상관없이 다음 작업으로 넘어감

    ![img](https://haneepark.github.io/images/blocing-nonblocking-sync-async.png)

### [샤딩](https://velog.io/@kyeun95/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-%EC%83%A4%EB%94%A9Sharding%EC%9D%B4%EB%9E%80)
 - 특정 키를 기준으로 데이터를 나누어 저장, DB 서버를 분할하는 방식
 
 - 장점
    - Scale-out가능
    - 장애 발생 시 Shading단위로 발생
    - 스캔 범위가 작아져 속도 향상
 - 단점
    - 프로그래밍 복잡도 증가
    - HotSpot(데이터 쏠림)발생 시 무의미
    - 샤딩 이전으로 원복 어려움

 - 종류
    - 모듈러 샤딩
        - Key를 PK 모듈러 연산 결과로 분리(PK % 3)
        - 데이터 균일분산
        - DB 추가 증설 시 데이터 재정렬 필요
        - 데이터 크기 예상가능한 곳에서 사용
    
    - 레인지 샤딩
        - PK범위를 기준으로 분리
        - 일부DB에 데이터 쏠릴 수 있음 
        - 증설에 재정렬 비용이 없음

    - 디렉토리 샤딩
        - 별도의 키 조회 테이블 사용
        - 샤딩에 사용되는 시스템, 알고리즘 활용가능
        - 동적 증설 가능
        - 조회 테이블 사용으로 오버헤드 발생

### [Orcale RAC](https://12bme.tistory.com/322)
 - 하나의 DB에 여러 instance를 두는 구조
 - instance별 데이터 동기화를 위해 Cache Fusion을 통해 각 Instance LocalDisk 조회 가능


