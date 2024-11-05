## Extend_mapping

### 이론
 - RDB에는 상속 X
 - 그나마 유사한건 슈퍼타입 서브타입 관계

 - 설계 방법
   1. 조인전략 : 각각의 독립적 데이터를 두고 PK, FK공유
      - 장점 : 정규화, 외래키 참조 무결성제약조건 활용가능, 저장공간 효율화
      - 단점 : 조회시 조인을 많이 사용, 조회쿼리 복잡, 데이터 저장시 INSERT 2번 사용
```java
@Entity
@Inheritance(strategy = InheritanceType.JOINED)
public abstract class Vehicle {
    @Id @GeneratedValue
    private Long id;
    private String name;
}

@Entity
public class Car extends Vehicle {
    private int seatingCapacity;
}

@Entity
public class Bike extends Vehicle {
    private boolean hasCarrier;
}
```

   2. 단일테이블 전략 : 상위테이블에 모든 하위 데이터 컬럼 넣기(JPA 기본전략)
      - 장점 : 조인이 필요없음, 조회쿼리 단순
      - 단점 : 자식 엔티티 컬럼은 null 가능, 데이터가 많아지면 점점 느려짐

   3. 구현 클래스마다 테이블 전략 : 상위테이블 없이 상위테이블 컬럼을 각 테이블에 넣기
      - 추천X
      
 - 주요 어노테이션
   - @Inheritance(strategy = InheritanceType.XXX) > 부모클래스에 선언
      - JOINED : 조인전략
      - SINGLE_TABLE : 단일테이블전략
      - TABLE_PER_CLASS : 구현 클래스마다 테이블 전략
   - @DiscriminatorColumn(name="DTYPE") : 조인전략 사용시 해당 데이터가 하위 어떤 클래스의 객체인지 > 부모클래스에 선언
   - @DiscriminatorValue("XXX") : 하위 클래스 별칭  > 자식클래스에 선언   

```java
// 상위클래스에
@Entity
@Inheritance(strategy = InheritanceType.JOINED)
@DiscriminatorColumn(name="DTYPE")
public class Item {

}

// 하위클래스에
@Entity
@DiscriminatorValue("M")
public class Movie extends Item{

}
```

### @MappedSuperclass
   - 객체끼리 공통 컬럼 정보가 필요할때 사용(id, createdBy...)
   - 상속관계 매핑X
   - 엔티티, 테이블과 매핑 X
   - 조회, 검색 불가
   - 추상클래스로 사용할 것을 권장

```java
@MappedSuperclass
public abstract class BaseEntity {
​
   private String createdBy;
​
   private LocalDateTime createdDate;
​
   private String lastModifiedBy;
​
   private LocalDateTime lastModifiedDate;
}

@Entity
public class Member extends BaseEntity {
  ...
}
```
