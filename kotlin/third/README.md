## Third

### 확장함수
 - 기존 클래스에 멤버함수를 추가하는 효과
 - 확장할 대상타입은 수신객체타입이라고 부름
 - fun 수신타입.확장함수(){}

```kotlin
fun String.singleQuote() = "'$this'"
fun String.doubleQuote() = "\"$this\""
```

 - this로 확장함수안을 호출할수 있다
```kotlin
fun String.strangeQuote() =
  this.singleQuote().singleQuote() // "" ""

fun String.tooManyQuotes() =
  doubleQuote().doubleQuote()      // "\"\"  "\"\"
```

### 이름붙은인자, 디폴트인자
 - 함수 호출시 인자에 이름설정 가능
 ```kotlin
 fun color(red: Int, green: Int, blue: Int) =
  "($red, $green, $blue)"

  color(
    red = 76,                 
    green = 89,
    blue = 0
  ) eq "(76, 89, 0)"
  color(52, 34, blue = 0) eq   
    "(52, 34, 0)"
 ```

 - 디폴트인자 : 파라미터의 디폴트값을 함수정의에서 정의하는것
```kotlin
fun color(
  red: Int = 0, // fun color()호출시 0으로 설정
  green: Int = 0, // fun color()호출시 0으로 설정
  blue: Int = 0, // fun color()호출시 0으로 설정
) = "($red, $green, $blue)"

fun main() {
  color(139) eq "(139, 0, 0)"
  color(blue = 139) eq "(0, 0, 139)"
  color(255, 165) eq "(255, 165, 0)"
  color(red = 128, blue = 128) eq "(128, 0, 128)"
}
```

 - joinToString() : 이터레이션 가능한 객체(리스트, Set등)를 String으로 합쳐줌
```kotlin
fun main() {
  val list = listOf(1, 2, 3,)
  list.toString() eq "[1, 2, 3]"
  list.joinToString() eq "1, 2, 3"
  list.joinToString(prefix = "(",
    postfix = ")") eq "(1, 2, 3)"
  list.joinToString(separator = ":") eq
    "1:2:3"
}
``` 

### 오버로딩
```kotlin
class Overloading {
  fun f() = 0
  fun f(n: Int) = n + 2
}

fun main() {
  val o = Overloading()
  o.f() eq 0
  o.f(11) eq 13
}
```

 - 확장함수보다는 멤버함수 우선
```kotlin
class My {
  fun foo() = 0 //멤버함수
}

fun My.foo() = 1 //확장함수

fun My.foo(i: Int) = i + 2 //파라미터가 달라 작동

fun main() {
  My().foo() eq 0
  My().foo(1) eq 3
}
``` 

 - 함수 오버로딩과 디폴트 인자를 동시에 사용하면 가까운거 실행
```kotlin
fun foo(n: Int = 99) = trace("foo-1-$n") // 멀어서 실행안됨

fun foo() {
  foo(14) // 이거 실행
}

fun main() {
  foo()
}
```

