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
   // 위에 같은 값이 있으므로 컬럼명 재정의
   @AttributeOverrides((@AttributeOverride(name="startDate", column = @Column("pastStartDate"))))
   private Period pastSectorperiod;
 }
 ```

### 값타입 공유와 불변객체
 - 하나의 값타입을 여러 객체에서 공유하는 것은 위험
 - new period -> memberA, memberB set -> period변경 -> A,B 둘다 바뀜
 - 값 타입의 내용을 복사해서 사용 필요(깊은 복사 필요)
 - 원천방지를 위해 객체타입은 불변객체로 설계해야함
 - 생성자로만 값을 설정하게하고 setter를 제거

### 값타입의 비교
 - 값타입은 내용이 같으면 같은걸로 봐야함
 - 동일성 비교 : 인스턴스의 참조값 비교(==)
 - 동등성 비교 : 인스턴스의 값을 비교(.equals)
 - 값 타입은 a.equals(b)를 사용해서 동등성 비교하도록 재정의해야함

### 값타입 컬렉션
 - 값 타입을 하나이상 저장할때 사용
 - @ElementCollection, @CollectionTable 사용
 - DB는 컬렉션을 같은 테이블에 저장할수없기에 별도의 테이블 생성해서 사용
 - 모든 컬럼은 전부 복합PK(nullX, 중복X)
 - CASCADE + 고아객체 제거기능 필수로 가짐
 - 지연로딩
 - 엔티티와 다르게 식별자 개념이 없음
 - 값 변경하면 추적 불가
 - 변경사항 발생시 주인 엔티티와 관련된 모든데이터를 삭제하고 다시 저장(Address하나만 변경해도 Address 다 지워버리고 새로 다 insert)
 - 실무에서는 값 타입 대신 Entity로 하고 일대다로 세팅하는 경우도 있음

 ```java
 @ElementCollection
 @CollectionTable(name="FAVORITE_FOODS", joinColumns = @JoinColumn(name = "MEMBER_ID"))
 @Column(name = "FOOD_NAME")
 private Set<String> favoriteFoods = new HashSet<>();
 ```
