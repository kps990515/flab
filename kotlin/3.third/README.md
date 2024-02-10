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

### when
```kotlin
fun ordinal(i: Int): String =
  when (i) {                           
    1 -> "erste"                       
    3 -> "dritte"
    7 -> "siebte"
    8 -> "achte"
    20 -> "zwanzigste"
    else -> numbers.getValue(i) + "te"
  }
```

```kotlin
  for (input in inputs) {
    when (input) {                  
      "up", "u" -> coordinates.y--  
      "down", "d" -> coordinates.y++
      "left", "l" -> coordinates.x--
      "right", "r" -> {             // 여러동작 수행가능
        trace("Moving right")
        coordinates.x++
      }
      "nowhere" -> {}               // 아무동작 수행안함
      "exit" -> return              // 종료
      else -> trace("bad input: $input")
    }
  }
```

### ENUM
```kotlin
enum class Level {
  Overflow, High, Medium, Low, Empty
}

Level.values() // enum의 값들을 반환
Overflow.Ordinal eq 0 //enum에는 각자의 순번이 있다
```

 - 인스턴스 개수가 고정이고, 클래스본문에 나열되어있다는 것 제외 Class와 동일
 - 멤버함수, 멤버 프로퍼티 정의 가능
```kotlin
enum class Direction(val notation: String) {
  North("N"), South("S"), East("E"), West("W"); // 세미콜론이 꼭 필요함
  val opposite: Direction // 프로퍼티
    get() = when (this) { // 함수
      North -> South
      South -> North
      West -> East
      East -> West
    }
}
``` 

### Data 클래스
 - 데이터저장 특화 클래스

 - toString()안써도 String으로 표현
```kotlin
data class Simple(
  val arg1: String,
  var arg2: Int
)

fun main() {
  val s1 = Simple("Hi", 29)
  val s2 = Simple("Hi", 29)
  s1 eq "Simple(arg1=Hi, arg2=29)"  // toString()없어도 됨
  s1 eq s2 // 다른객체처럼 보이지만 같음
}
``` 

 - 객체 생성 시 내용 같으면 같은것으로 간주
```kotlin
class Person(val name: String)

data class Contact(
  val name: String,
  val number: String
)

fun main() {
  // 아래 둘은 같아 보이지만 그렇지 않다
  Person("Cleo") neq Person("Cleo")
  // 데이터 클래스는 타당한 동등성 검사를 제공한다
  Contact("Miffy", "1-234-567890") eq
    Contact("Miffy", "1-234-567890")
}
``` 

 - copy() : 복사하는 객체의 데이터를 포함하여 새 객체 생성
```kotlin
fun main() {
  val contact = DetailedContact(
    "Miffy",
    "Miller",
    "1-234-567890",
    "1600 Amphitheatre Parkway")
  val newContact = contact.copy( //기존 데이터에서 필요한 부분만 변경해서 생성
    number = "098-765-4321",
    address = "Brandschenkestrasse 110")
  newContact eq DetailedContact(
    "Miffy",
    "Miller",
    "098-765-4321",
    "Brandschenkestrasse 110")
}
```

 - HashMap, HashSet의 키로 사용할수있는 해시함수 생성
```kotlin
fun main() {
  val korvo: Key = Key("Korvo", 19)
  korvo.hashCode() eq -2041757108
  val map = HashMap<Key, String>()
  map[korvo] = "Alien"
  map[korvo] eq "Alien"
  val set = HashSet<Key>()
  set.add(korvo)
  set.contains(korvo) eq true
}
```

### 구조분해 : 여러값 반환
 - Pair클래스 : 두개의 다른 값 반환 가능
```kotlin
fun compute(input: Int): Pair<Int, String> =
  if (input > 5)
    Pair(input * 2, "High")
  else
    Pair(input * 2, "Low")

fun main() {
  compute(7) eq Pair(14, "High")
  compute(4) eq Pair(8, "Low")
  val result = compute(5)
  result.first eq 10
  result.second eq "Low"
}
```

 - val(a,b,c) : 여러값이 들어있는 값 반환 가능
```kotlin
fun main() {
  val (value, description) = compute(7)
  value eq 14
  description eq "High"
}
```

 - Data클래스는 자동으로 여러값 반환 가능
```kotlin
data class Computation(
  val data: Int, //Pair, Triple 클래스로 안해도 자동으로 분해해서 반환
  val info: String
)

fun evaluate(input: Int) =
  if (input > 5)
    Computation(input * 2, "High")
      else
    Computation(input * 2, "Low")

fun main() {
  val (value, description) = evaluate(7)
  val (_, description) = evalute(8) // _로 반환 값 생략 가능
  value eq 14
  description eq "High"
}
```

### Null 가능 타입
 - 코틀린은 기본적으로 Null 불가
 - 이름뒤에 ?넣어서 Null 예외적 허용
```kotlin
  val s1 = "abc"            // 널불가
  
  // 컴파일 오류
  // val s2: String = null  // [2]
  
  // 널이 될 수 있는 정의들
  val s3: String? = null    
  val s4: String? = s1     
  
  // 컴파일 오류
  // val s5: String = s4   
  val s6 = s4               
```

 - Map에 괄호를 사용하면 Null 허용 결과 추출가능
```kotlin
  val map = mapOf(0 to "yes", 1 to "no")
  val first: String? = map[0]
  val second: String? = map[2]
  first eq "yes"
  second eq null
```

 - Null 가능 타입 역참조 불가
```kotlin
  val s1: String = "abc"
  val s2: String? = s1
  s1.length eq 3 
  // 컴파일되지 않는다
  s2.length   // Null발생할수있기에 에러
```

### 안전한 호출을 위한 여러 연산자
 - ?. : 안전한 호출
```kotlin
  val s1: String? = "Howdy!"
  s1.echo() // Null발생할수있기에 에러
  s1?.echo() // Null허용으로 작동                
  val s2: String? = null
  s2?.echo() //null을 받기에 수행안함
``` 

 - ?: 왼쪽값이 null이면 오른쪽 작동
```kotlin
  val s1: String? = "abc"
  (s1 ?: "---") eq "abc" // s1반환
  val s2: String? = null
  (s2 ?: "---") eq "---" // --- 반환
```

```kotlin
fun checkLength(s: String?, expected: Int) {
  val length1 =
    if (s != null) s.length else 0 // null 체크해줘서 s.length 사용가능
  val length2 = s?.length ?: 0     // s.length체크해서 null이면 0 반환
}
```

### !! : Null 불가 선언
```kotlin
  val s: String? = "abc"
  s!!.length eq 3
```

### Generic
 - 여러가지 클래스에 적합함
```kotlin
class GenericHolder<T>(             // 제네릭 T선언
  private val value: T
) {
  fun getValue(): T = value
}

fun main() {
  val h1 = GenericHolder(Automobile("Ford"))
  val a: Automobile = h1.getValue() // 변수에 타입정해서 올바른 타입 반환
  val h2 = GenericHolder(1)
  val i: Int = h2.getValue()        // 변수에 타입정해서 올바른 타입 반환
  val h3 = GenericHolder("Chartreuse")
  val s: String = h3.getValue()     // 변수에 타입정해서 올바른 타입 반환
}
```

 - Any는 구체적 타입이 필요할떄 사용불가
```kotlin
class AnyHolder(private val value: Any) {
  fun getValue(): Any = value
}

class Dog {
  fun bark() = "Ruff!"
}

fun main() {
  val holder = AnyHolder(Dog())
  val any = holder.getValue()  
  any.bark()  // 컴파일되지 않음 Dog가 아닌 Any.bark()로 인식
}
```

 - 제네릭함수 : 함수이름앞에 <>붙임
```kotlin
fun <T> identity(arg: T): T = arg

fun main() {
  identity("Yellow") eq "Yellow"
  identity(1) eq 1
  val d: Dog = identity(Dog())
  d.bark() eq "Ruff!"
}
```

 - 확장 프로퍼티
  - 확장 프로퍼티는 커스텀 Getter 필요
```kotlin
val String.indices: IntRange // 확장프로퍼티
  get() = 0 until length // Getter

fun main() {
  "abc".indices eq 0..2
}
```














