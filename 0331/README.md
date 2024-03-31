## 03/31

### [DispatcherServlet](https://mangkyu.tistory.com/18)
 - 정의 : HTTP요청을 적합한 컨트롤러에 위임해 주는 Front Controller
 - Servlet : 클라이언트 요청을 받아 처리하고 결과를 다시 전달해주는 Java 서버 측 프로그램
 - 장점
   - 과거에는 서블릿과 URL 매핑을 위해 web.xml에 모두 등록 필요함
   - dispatcher-servlet은 모든 요청을 핸들링해주고 공통 작업 처리
 - 정적자원 처리 
   1. 정적자원 요청과 애플리케이션 요청 분리
      - /apps의 URL로 접근하면 dispatcher servlet
      - /resource의 URL로 접근하면 정적자원 처리
   2. 애플리케이션 요청 탐색하고 없으면 정적자원 처리

 - 동작순서
 ![img](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbcff5H%2FbtstbdRuSr9%2FpNKnGdMwftSWmiGLHA7yL0%2Fimg.png)
   1. 클라이언트의 요청 들어옴
   2. Hanlder Mapping(@RequestMapping)에서 알맞은 컨트롤러 탐색
      - 요청정보, 처리대상, 메소드를 chain으로 감싸 인터셉터 등과 함께 전달
   3. 요청을 컨트롤러로 위임할 Handler Adapter에 전달
      - 다양한 유형의 핸들러를 통일된 방식으로 처리할 수 있게 해줌
   4. 컨트롤러로 요청 전달
      - ArgumentResolver, ReturnValueHandler 같은 전후 처리를 HandlerAdapter가 해줌
   5. 비지니스 로직 처리
   6. Response Entity로 결과값 전달
   7. Handler Adapter 반환값 처리
   8. 클라이언트에 결과 전달

### [statement,PreparedStatement](https://velog.io/@dingdoooo/JDBC-Statement-PreparedStatement-%EC%9D%B4%EC%9A%A9%ED%95%98%EA%B8%B0)
 - Java에서 데이터베이스 SQL 쿼리를 실행하기 위해 사용하는 인터페이스
 - 차이점 : 캐시사용유무
   - statement : SQL문 실행마다 새로작성하고 DB 전송(쿼리 분석 -> 컴파일 -> 실행)
   - prepared : SQL 미리 컴파일해두고 파라미터만 바꿔서 사용(처음만 3단계 거치고 바로 실행)

#### statement
![img](https://velog.velcdn.com/images%2Fdingdoooo%2Fpost%2F1651cac5-6869-497f-a52d-0e38c93abc7d%2Fimage.png)
 - 정적 쿼리문 실행 가능 : 쿼리문에 값이 미리 입력되어야 사용가능
 - 매번 실행할때마다 SQL문을 DB로 전송함
 - 동일 쿼리 반복사용에 비효율적

#### PreparedStatement
![img](https://velog.velcdn.com/images%2Fdingdoooo%2Fpost%2Fd5b3f4ff-1b6f-4f88-89ea-61c31d49bdb3%2Fimage.png)
 - 미리 컴파일된 SQL 쿼리 사용
 - 실행시점에 파라미터 전달하여 실행
 - 파라미터는 ?로 표시

### [소프트파싱,하드파싱](https://saltyzun.tistory.com/34)
 - parsing : 데이터를 분석하고 그 의미를 이해하는 과정
 - SQL PARSER(Optimizer)
   - SQL문 분석(parsing) + 실행계획 생성(plan)
 - 오라클에서는 모든 SQL문마다 고유한 ID생성해서 변수만 다른경우 성능저하 발생
 - 바인드변수 활용 필수
 ```java
   -- 바인드 변수 없이 쿼리를 작성하는 경우
   SELECT id FROM emp WHERE id = 1;
   SELECT id FROM emp WHERE id = 2;

   -- 바인드 변수를 활용하는 경우
   SELECT id FROM emp WHERE id = :A;
 ```

#### 하드파싱
 - 공유 풀에 실행계획이 없어 실행계획 생성하는 경우
 - 순서
   1. 구문분석
   2. 의미분석(참조 테이블, 컬럼 존재 확인 및 접근 가능 확인)
   3. 최적화
   4. 실행계획 생성

#### 소프트파싱
 - 공유 풀에 존재하는 실행 계획을 찾아 이를 재사용 하는 경우
 - prepared statement와 같이 활용하면 성능 효율화
 - 순서
   1. 구문, 의미분석 생략
   2. 실행계획 재사용

### [파티션,샤딩](https://hudi.blog/db-partitioning-and-sharding/)
 - 파티셔닝 : 큰DB나 테이블을 여러 작은 DB, 테이블로 분할하는 작업
 - 샤딩 : 데이터를 여러 서버에 분산시켜 저장하는 작업

#### 파티셔닝
 - 물리적으로 여러 테이블로 분산하여 저장
 - 사용자는 마치 하나의 테이블에 접근하는 것과 같이 사용
 - 종류
   1. List : 데이터가 해당 List에 포함되는 경우 데이터 분리(지역별: 아시아, 유럽...)
   2. Range : 특정범위로 분리(1분기, 2분기..)
   3. Hash : 해시함수 사용
   4. 수평 : 행을 기준으로 분리
   5. 수직 : 열을 기준으로 분리

#### 샤딩
 - 동일한 스키마를 가지고 있는 여러대의 데이터베이스 서버들에 데이터를 작은 단위로 나누어 분산 저장
 - 수평 파티셔닝의 일종
 - 파티셔닝은 데이터를 동일 컴퓨터에 저장
 - 샤딩은 데이터를 다른 컴퓨터에 분산해 쿼리성능향상 & 부하 분산
 - 애플리케이션 레벨에서 구현하는 것이 일반적
 - 종류
   1. Modular : PK를 모듈러 연산한 결과로 분산(샤딩 추가시 부하발생)
   2. Range :  PK의 범위를 기준으로 분산(활성유저에 따라 부하발생가능)
   3. Directory : 별도의 조회테이블 사용해 분산(조회테이블 사용에 따라 오버헤드 발생)

### [SPOF](https://blog.seongjun.kr/single-point-of-failure/)
 - Single Point of Failure: 단일 장애 지점
 - 정의 : 시스템 구성요소에서, 동작하지 않으면 전체 시스템이 중단되는 지점
 - 예방법
   1. 중복성 도입 : 시스템 구성요소의 백업 마련
   2. 고가용성(HA) 아키텍쳐 : 시스템 중요부분을 여러대의 서버, 장치 분산
   3. 정기적 백업, 재해 복구
   4. 모니터링시스템 구축
   5. 로드밸런싱
