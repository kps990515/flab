## 04/21

### 트랜잭션 설정법
  1. 프로그래밍적 트랜잭션
    - TransactionTemplate(권장)

```java
    public class PointTransaction {

    private PointDao pointDao;
      private TransactionTemplate transactionTemplate;
      
      public void setPointDao(PointDao pointDao) {
        this.pointDao = pointDao;
      }
      
      public void setTransactionManager(PlatformTransactionManager transactionManager) {
        // 1. transactionTemplate 주입
        this.transactionTemplate = new TransactionTemplate(transactionManager);
        // 2. Transaction 속성 설정
        this.transactionTemplate.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRED);
      }
      
      public void invoke() {
        dolnternalTransaction();
      }
      
      public void dolnternalTransaction() throws Exception {
        // 3. Transaction 실행
        transactionTemplate.execute(new TransactionCallbackWithoutResult() { 
            protected void doInTransactionWithoutResult(TransactionStatus status) { 
                try { 
                    pointDao.plusPoint() 
                  } catch (Exeption ex) { 
                    // 4. 문제발생시 rollback
                    status.setRollbackOnly(); 
                  } 
              } 
          });
      }
    }
```
    - PlatformTransactionManager
  2. 선언적 트랜잭션(@Transaction)
    - 트랜잭션 기능이 적용된 프록시 객체가 생성

### @Transactional이 붙은 메소드를 같은 클래스 내의 다른 메소드에서 직접 호출
 - @Transactional 어노테이션이 붙은 메소드는 트랜잭션 관리를 위해 프록시(proxy)를 사용
 - 같은 클래스 내의 다른 메소드에서 직접 호출의 경우 프록시 처리가 안되서 트랜잭션 처리 X
 - 해결법
  1. 자기 자신 주입
  ```java
  @Service
  public class UserService {
    @Autowired
    private UserService self;

    @Transactional
    public boolean addUser(String userName, String password) {
        // implementation details
    }

    public boolean addUsers(List<User> users) {
        for (User user : users) {
            self.addUser(user.getUserName(), user.getPassword());
        }
    }
  }
  ```
  2. 다른클래스로 분리하기

  ### @Transaction은 public함수에만 작동
   - @Transaction은 Spring AOP를 사용하고 AOP는 프록시를 사용
   - AOP는 public에서만 작동
   - 해결방법
    1. 메소드 접근 제어자 변경
    2. 내부 호출 대신 외부 빈 주입 사용(Ex. TransactionHandler)
```java
    @Service
    public class UserService {

        @Autowired
        private TransactionHandler transactionHandler;

        public boolean addUsers(List<User> users) {
            for (User user : users) {
              // 사용자 목록 각각에 대해 addUser 메소드를 트랜잭션으로 감싸 호출
                transactionHandler.runInTransaction(() -> addUser(user.getUsername, user.getPassword));
            }
        }

        private boolean addUser(String username, String password) {
            // TODO call userRepository
        }
    }

    @Service
    public class TransactionHandler {

        @Transactional(propagation = Propagation.REQUIRED)
        //  이미 진행 중인 트랜잭션이 있으면 그 트랜잭션에 참여하고, 없으면 새로운 트랜잭션을 시작
        private <T> T runInTransaction(Supplier<T> supplier) {
            return supplier.get();
        }

        @Transactional(propagation = Propagation.REQUIRES_NEW)
        // 항상 새로운 트랜잭션을 시작
        private <T> T runInNewTransaction(Supplier<T> supplier) {
            return supplier.get();
        }
    }
```

### [AOP](https://velog.io/@ann0905/AOP%EC%99%80-Transactional%EC%9D%98-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC)
 - AOP의 주요 개념
  - Aspect: Advice + PointCut로 AOP의 기본 모듈
  - Advice: Target에 제공할 부가 기능을 담고 있는 모듈
  - Target: Advice이 부가 기능을 제공할 대상 (Advice가 적용될 비즈니스 로직)
  - JointPoint: Advice가 적용될 위치
    - 메서드 진입 지점, 생성자 호출 시점, 필드에서 값을 꺼내올 때 등 다양한 시점에 적용 가능
  - PointCut: Target을 지정하는 정규 표현식

 - 동작방식 : Proxy 방식
  - Target 클래스 혹은 그의 상위 인터페이스를 상속하는 프록시 클래스를 생성하고, 프록시 클래스에서 부가 기능에 관련된 처리
  ```java
  public interface TargetService{
      void logic(); 
  }

  @Service 
  public class TargetServiceImpl implements TargetService{
      @Override 
      public void logic() {// <- 타겟로직
          ...
  }}
  ```

  ```java
  // 프록시 객체
  @Service 
  // 타겟 로직의 클래스 상속으로 Proxy객체 구현
  public class TargetServiceProxy implements TargetService{ 
      // 지금은 구현체를 직접 생성했지만, 외부에서 의존성을 주입 받도록 할 수 있다.
      TargetService targetService = new TargetServiceImpl();
      ...

      @Override 
      public void logic() {
          // Target 호출 이전에 처리해야하는 부가 기능
          
          // Target 호출
      targetService.logic();

          // Target 호출 이후에 처리해야하는 부가 기능
      }
  }
  ```

  ```java
  @Service 
  public class UseService{ 
      // 사용하는 입장에서는 Target 객체를 사용하는 것처럼 Proxy 객체를 사용
      TargetService targetService = new TargetServiceProxy(); 
      ...
      
      public void useLogic() {
          // Target 호출하는 것처럼 부가 기능이 추가된 Proxy를 호출한다.
          targetService.logic();
      }
  }
  ```

 - @Transaction Proxy
  1. private은 트랜잭션 처리 불가능
  2. 트랜잭션은 객체 외부에서 처음 진입하는 메서드를 기준으로 동작
   - A : 생성 메소드, @Transaction적용
   - B : 호출 메소드 @Transaction 미적용
   - C : 호출 메소드 @Transaction 적용
   - C -> A 에러 발생시 롤백
   - B -> A 에러 발생시 롤백 X(트랜잭션없는 B가 실행되었기 때문)
  ```java
  public class TransactionProxy{
      private final TransactonManager manager = TransactionManager.getInstance();
      ...

      public void transactionLogic() {
          try {
              // 트랜잭션 전처리(트랜잭션 시작, autoCommit(false) 등)
        manager.begin();

        // 다음 처리 로직(타겟 비스니스 로직, 다른 부가 기능 처리 등)
        target.logic();
              
        // 트랜잭션 후처리(트랜잭션 커밋 등)
              manager.commit();
          } catch ( Exception e ) {
        // 트랜잭션 오류 발생 시 롤백
              manager.rollback();
          }
      }
  ```

### [GraceFul Shutdown](https://velog.io/@byeongju/SpringBoot%EC%9D%98-Graceful-Shutdown)
 - 시스템이나 서비스가 작업 중인 작업들을 안전하게 완료하고, 자원을 정리한 후에 시스템을 정지하는 과정
 - Graceful shutdown이 진행되면 더이상 요청은 거부
 - 처리 중인 요청이 있다면 마무리하고 server를 종료
 
 - 필요성
  1. 데이터 무결성 보장
  2. 자원의 안정적 해제
  3. 시스템 유지보수

 - 구현방법
 ```java
 server.shutdown=graceful
 spring.lifecycle.timeout-per-shutdown-phase=30s
 ```

 - 종료과정
  1. shutDownGracefully 메소드 작동
  2. doShutdown 메서드에서 connector들을 닫음(새로운 요청 거부)
  3. doShutdown 메서드 내부 while문에서 isActive 메서드를 통해 현재 처리중인 요청이 있는지 확인
  4. timeout-per-shutdown-phase 최대대기시간 옵션 시간 넘으면 종료


### [JPA에서 Entity에 protected 생성자를 만드는 이유](https://velog.io/@jyyoun1022/JPA-JPA%EC%97%90%EC%84%9C-Entity%EC%97%90-protected-%EC%83%9D%EC%84%B1%EC%9E%90%EB%A5%BC-%EB%A7%8C%EB%93%9C%EB%8A%94-%EC%9D%B4%EC%9C%A0)
