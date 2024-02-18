## 02/11

### Sealed Class
 - java,kotlin 차이점
   - 파일 구조: Kotlin에서는 모든 서브클래스가 동일한 파일에 있어야함, Java에서는 하위 클래스가 별도의 파일에 있어도 됨
   - 완전성 검사: Kotlin은 'when' 표현식에서 컴파일 시간 완전성 검사를 제공
   - 추상적 특성
      - Kotlin에서는 when을 통한 검사를 위해 존재, 인스턴스화 할수 없다(추상적)
      - Java에서는 실제 구현 가능(구체적)

#### Kotlin
 - 제한된 하위 클래스 : 상속한 하위 클래스는 반드시 같은 패키지와 모듈 안에 있어야 한다
 - 패턴매칭 : when 식에서 모든 하위 클래스에 대한 처리를 명시적으로 지정할 수 있습니다.
 - 사용이유
   - 제한된 서브 타입 : HTTP 응답에 대한 서브타입을 제한하기 위해 하위 클래스 생성
   - 패턴매칭을 통한 안정성 : when을 통해 패턴을 확인하고 코드안정성 확보
   - 상태 또는 이벤트 종류 클래스 정의 : 캐릭터 상태, 이벤트 종류
   - 비즈니스 로직 모델링 : 주문 상태값 등

```kotlin
sealed class Result {
    data class Success(val data: String) : Result() // 하위 클래스
    data class Error(val message: String) : Result() // 하위 클래스
}

// when 문
fun handleResult(result: Result) {
    when (result) {
        is Result.Success -> println("Success: ${result.data}")
        is Result.Error -> println("Error: ${result.message}")
    }
}

fun main() {
    val successResult = Result.Success("Hello, World!")
    val errorResult = Result.Error("Something went wrong")

    handleResult(successResult) // Success: Hello, World!
    handleResult(errorResult)   // Error: Something went wrong
}
```

#### Java
 - 상속, 구현할 Class를 미리 명시하는 키워드
 - 상속, 구현하는 클래스는 final, non-sealed, sealed 중 하나로 선언
 - non-sealed로 클래스 생성시 미리 명시하지 않아도 상속, 구현 가능
 - 서브클래스를 별도의 파일에 선언 가능(유연성)
```java
public sealed interface CarBrand permits Hyundai, Kia{}

public final class Hyundai implements CarBrand {}
public non-sealed class Kia implements CarBrand {}
```

### filter, interceptor, aop
![img](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F9983FB455BB4E5D30C)

- filter
   - Spring context외부에서 동작
   - 요청과 응답을 정제하는 역할
   - HTTP 요청 로깅, 인증 및 인가, 인코딩 변환 등

- Interceptor
   - Spring context 내부에서 동작
   - Dispatercher, Controller 사이에서 Controller 요청 응답 처리
   - 요청시간 측정, 로깅, 로그인 체크등 역할

- AOP
   - 모든 계층, 구성요소에 적용가능
   - 개발자가 비즈니스 로직에서 로깅, 트랜잭션관리, 보안 등의 관심사를 분리
   - 관심사가 다른 중복코드를 관리

### DB ACID
 - 원자성(Atomicity)
   - 트랜잭션이 데이터베이스에 모두 반영되거나 전혀 반영되지 않아야 함을 의미
   - 트랜잭션 내의 모든 연산이 성공적으로 완료되어야 전체 트랜잭션이 커밋
   - 하나라도 실패하면 전체 트랜잭션이 롤백
 
 - 일관성(Consistency)
   - 트랜잭션이 실행되기 전과 후에 데이터베이스가 일관된 상태를 유지해야 함을 의미
   - 모든 제약 조건, 규칙, 트리거를 준수하여 실행

 - 격리성(Isolation)
   - 동시에 실행되는 트랜잭션이 서로 영향을 주지 않도록 분리되어야 함
   - 격리성이 낮으면 성능이 향상되지만, 동시성 관련 문제가 발생

 - 지속성(Durability)
   - 트랜잭션이 성공적으로 커밋되면, 그 결과가 시스템 고장이 발생하더라도 영구적으로 반영되어야 함을 의미

### 코틀린 스코프 함수
- apply
   - 호출 객체를 반환합니다.
   - this를 사용하여 호출 객체에 접근합니다.
   - 객체의 초기화나 구성에 주로 사용됩니다.
```kotlin
val person = Person().apply {
    name = "John"
    age = 30
}

// 기존에는
val person = Person()
person.name = ""
person.age = 10
```

- run
   - 마지막 표현식의 결과 반환
   - apply와 비슷하지만 객체 초기화 후 마지막 라인 반환
```kotlin
val Person = person.run {
   height = 180
   isTall() // 반환 값
}
```

- with
   - 마지막 표현식의 결과를 반환
```kotlin
val nameLength = with(person) {
    println(name) // "John"
    name.length   // 반환 값
}
```

- let
   - 마지막 표현식의 결과를 반환
   - null check 후 코드 실행, nullable 객체를 다른 타입의 변수로 변환할때 사용
   - it을 사용하여 호출 객체에 접근
```kotlin
var person: Person? = null
var isReserved = person?.let {it: Person -> reserveMovie(it)}
// it이 Person일때만 해당 함수 실행
// Person을 사용해 예매하고 return은 다른값으로 변환
```

- also
    - 호출 객체를 반환
    - 객체에 추가 작업을 하고 객체를 반환할때 사용
```kotlin
val increaseAge() = Person().also {
    person.age = it.age + 1
}
```


