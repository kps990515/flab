## ValueType

### 기본값타입
 - Entity 타입
   - @Entity로 정의된 엔티티 클래스
   - 데이터가 변해도 식별자(ID)로 추적가능
   - 엔티티는 영속성 컨텍스트에서 관리되며, **상태 변화(추적)**가 가능
 - 값 타입
   - int, String처럼 단순한 값 타입
   - 데이터 변경시 추적불가
   - 종류
      1. 기본값 타입 : 자바기본타입(int, double), 래퍼클래스(Integer, Long), String
      2. 임베디드 타입(@Embaddable) : 복합 값 타입
      3. 컬렉션 값 타입

### 임베디드타입
 - 새로운 값 타입 정의 가능
 - 기본값들을 조합해서 생성(집 주소, 근무기간일수)
 - 임베디드타입안에 Entity 포함 가능
 - @Embeddable : 값을 정의하는곳
 - @Embedded : 값을 사용하는곳
 - @AtrributeOverride : 같은 값 타입이 있을시 별도 정의
 - 장점 : 재사용 가능, 높은 응집도

 ```java
 @Embeddable
 public class period{
   private LocalDateTime startDate;
   private LocalDateTime endDate;
   @Entity
   private Day day; //임베디드타입안에 Entity 포함 가능
 }

 public class Member{
   @Embedded
   private Period nowSectorPeriod;

    @Embedded
    @AttributeOverrides({
        @AttributeOverride(name = "startDate", column = @Column(name = "pastStartDate")),
        @AttributeOverride(name = "endDate", column = @Column(name = "pastEndDate"))
    })
    private Period pastSectorPeriod; // 과거 기간 정보
 }
 ```

### 값타입 공유와 불변객체
 - 하나의 값타입을 여러 객체에서 공유하는 것은 위험
 ```java
 // 공통 Period 객체 생성
 Period period = new Period(LocalDateTime.now(), LocalDateTime.now().plusDays(10));
 
 // MemberA와 MemberB가 같은 period 객체를 공유
 Member memberA = new Member();
 memberA.setPeriod(period);
 
 Member memberB = new Member();
 memberB.setPeriod(period);

 // 만약 memberA의 period를 변경하면...
 memberA.getPeriod().setEndDate(LocalDateTime.now().plusDays(20));
 
 // memberB의 period도 변경됨 (공유된 참조이므로)
 System.out.println(memberB.getPeriod().getEndDate()); // 예상하지 못한 결과가 나올 수 있음
 ```
 - 값 타입의 내용을 복사해서 사용 필요(깊은 복사 필요)
   ```java
   // MemberA와 MemberB가 각각 독립적인 Period 객체를 사용
   memberA.setPeriod(new Period(LocalDateTime.now(), LocalDateTime.now().plusDays(10)));
   memberB.setPeriod(new Period(LocalDateTime.now(), LocalDateTime.now().plusDays(10)));
   
   // 이제 memberA의 period를 변경해도 memberB는 영향을 받지 않음
   memberA.getPeriod().setEndDate(LocalDateTime.now().plusDays(20));
   ```
 - 원천방지를 위해 객체타입은 불변객체로 설계해야함
 - 생성자로만 값을 설정하게하고 setter를 제거
   ```java
   @Embeddable
   public class Period {
   
       private final LocalDateTime startDate;
       private final LocalDateTime endDate;
   
       // 생성자로만 값 설정, setter 없음
       public Period(LocalDateTime startDate, LocalDateTime endDate) {
           this.startDate = startDate;
           this.endDate = endDate;
       }
   
       // getter만 제공
       public LocalDateTime getStartDate() {
           return startDate;
       }
   
       public LocalDateTime getEndDate() {
           return endDate;
       }
   }
   ```

### 값타입의 비교
 - 값타입은 내용이 같으면 같은걸로 봐야함
 - 동일성 비교 : 인스턴스의 참조값 비교(==)
 - 동등성 비교 : 인스턴스의 값을 비교(.equals)
 - 값 타입은 a.equals(b)를 사용해서 동등성 비교하도록 재정의해야함

### 값타입 컬렉션
 - 엔티티에 여러 개의 값 타입을 컬렉션 형태로 포함(Set, List)
 - @ElementCollection : 값 타입 컬렉션임을 명시
 - @CollectionTable 사용 : 컬렉션 데이터가 저장될 테이블의 이름과 join 관계를 설정
 - 값 타입 컬렉션의 제약
   - 복합 PK: 값 타입 컬렉션의 모든 컬럼은 복합 기본 키로 설정되며, NULL이 불가하고 중복이 허용되지 않습니다.
   - CASCADE + 고아객체 제거기능 필수로 가짐
   - 지연 로딩: 값 타입 컬렉션은 기본적으로 지연 로딩(Lazy Loading)을 사용
 - 값 타입 컬렉션의 데이터 변경 방식
   - 값 타입 컬렉션에는 식별자가 없기 때문에 JPA는 컬렉션을 추적할 수 없습니다.
   - 값이 변경될 경우, JPA는 기존의 값 타입 컬렉션 데이터를 모두 삭제하고, 새롭게 컬렉션의 모든 데이터를 다시 삽입
   - 실무에서는 값 타입 대신 Entity로 하고 일대다로 세팅하는 경우도 있음
      ```java
      @Entity
      public class FavoriteFood {
          @Id @GeneratedValue
          private Long id;
      
          @ManyToOne
          @JoinColumn(name = "MEMBER_ID")
          private Member member;
      
          private String foodName;
      }
      ```

- 데이터베이스에 저장되는 방식
  - Member 엔티티와 값 타입 컬렉션 favoriteFoods는 두 개의 테이블에 저장  
```java
@Entity
public class Member {
    
    @Id @GeneratedValue
    private Long id;
    private String name;

    @ElementCollection
    // FAVORITE_FOODS라는 테이블에 저장되고, Member테이블과 member_id로 연결
    @CollectionTable(name="FAVORITE_FOODS", joinColumns = @JoinColumn(name = "MEMBER_ID"))
    // Member테이블에서 food_name이라는 컬럼으로 사용됨
    @Column(name = "FOOD_NAME")
    private Set<String> favoriteFoods = new HashSet<>();
```

