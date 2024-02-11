## Fourth

### 람다
```kotlin
  val list = listOf(1, 2, 3, 4)
  val result = list.map({ n: Int -> "[$n]" })
  result eq listOf("[1]", "[2]", "[3]", "[4]")

  // 타입제거간소화
  val result = list.map({ n -> "[$n]" })

  // 파라미터 하나인 경우 it으로 대체
  val result = list.map({ "[$it]" })
  val result = list.map({ "[${it.toUpperCase()}]" })

  // 파라미터가 람다만 있는 경우 괄호 제거
  val result = list.map { "[${it.toUpperCase()}]" }

  // 람다가 마지막 파라미턴인 경우는 괄호 다음에 쓰기 가능
  val result =   list.joinToString( // 아래랑 같은 로직
                   separator = " ",
                   transform = { "[$it]" }
                 ) 
  val result = list.joinToString(" ") { "[$it]" }
  
  // 파라미터가 둘 이상인 람다
  list.mapIndexed { index, element ->
    "[$index: $element]"
  } eq listOf("[0: a]", "[1: b]", "[2: c]")

  // 특정인자 사용하지 않을 때는 _ 사용
  list.mapIndexed { index, _ ->
    "[$index]"
  } eq listOf("[0]", "[1]", "[2]")
```

### filter
```kotlin
  //filter
  val list = listOf(1, 2, 3, 4)
  val even = list.filter { it % 2 == 0 }
  val greaterThan2 = list.filter { it > 2 }

  // 람다를 변수에 담아 넘기기
  val list = listOf(1, 2, 3, 4)
  val isEven = { e: Int -> e % 2 == 0 }
  list.filter(isEven) eq listOf(2, 4)

  // 밖에 정의된 val 포획해서 넘기기
  val list = listOf(1, 5, 7, 10)
  var sum = 0
  val divider = 5
  list.filter { it % divider == 0 }
    .forEach { sum += it }
  sum eq 15
```

## Collection
 - list 생성법
 ```kotlin
   val list1 = List(10) { it }
   list1 eq "[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]"

  val mutableList1 = MutableList(5, { 10 * (it + 1) })
  mutableList1 eq "[10, 20, 30, 40, 50]"
 ```

 - 컬렉션 함수
 ```kotlin
  val list = listOf(-3, -1, 5, 7, 10)
  
  list.filter { it > 0 } eq listOf(5, 7, 10)
  list.count { it > 0 } eq 3
  
  list.find { it > 0 } eq 5
  list.firstOrNull { it > 0 } eq 5 // 조건에 만족하는 첫번째
  list.lastOrNull { it < 0 } eq -1 // 조건에 만족하는 마지막
  
  list.any { it > 0 } eq true
  list.any { it != 0 } eq true
  
  list.all { it > 0 } eq false
  list.all { it != 0 } eq true
  
  list.none { it > 0 } eq false
  list.none { it == 0 } eq true

  list.filter(isPositive) eq "[5, 7, 10]" // filter에 맞는값 반환
  list.filterNot(isPositive) eq "[-3, -1]" // filter에 맞지않는값 반환
  list.filterNotNull() eq "[1, 2]" // null 이 아닌 값 반환
  
  val (pos, neg) = list.partition { it > 0 } // 맞는값 틀린값 둘다 반환
  pos eq "[5, 7, 10]"
  neg eq "[-3, -1]"

  products.sumByDouble { it.price } eq 7.0 // 타입이 double일때 합계 구하기
  products.minByOrNull { it.price } eq Product("bread", 2.0) // 제일 작은값 구하기

  val list = listOf('a', 'b', 'c', 'X', 'Z')
  list.takeLast(3) eq "[c, X, Z]" // 마지막에서 3번째 인자부터 가져오고 새로운 리스트생성
  list.takeLastWhile { it.isUpperCase() } eq
    "[X, Z]"
  list.drop(1) eq "[b, c, X, Z]" // 처음 1개 인자제거 하고 새로운 리스트 생성
  list.dropWhile { it.isLowerCase() } eq
    "[X, Z]"
 ``` 

### 멤버참조
 - 프로퍼티 참조
 ```kotlin
 data class Message(
  val sender: String,
  val text: String,
  val isRead: Boolean
)
 val unread = messages.filterNot(Message::isRead)
 ```

 - 함수 참조
 ```kotlin
 fun Message.isImportant(): Boolean =
  text.contains("Salary increase") ||
    attachments.any {
      it.type == "image" &&
        it.name.contains("cat")
    }

 messages.any(Message::isImportant) eq true
 ```

 - 생성자 참조
 ```kotlin
 data class Student(
  val id: Int,
  val name: String
 )

 val names = listOf("Alice", "Bob")

 val students =
    names.mapIndexed { index, name ->
      Student(index, name)
    }
 students eq listOf(Student(0, "Alice"), Student(1, "Bob"))
 names.mapIndexed(::Student) eq students
 ```

 - 확장함수 참조
 ```kotlin
fun Int.times47() = times(47)

class Frog
fun Frog.speak() = "Ribbit!"

fun goInt(n: Int, g: (Int) -> Int) = g(n)

fun goFrog(frog: Frog, g: (Frog) -> String) =
  g(frog)

fun main() {
  goInt(12, Int::times47) eq 564
  goFrog(Frog(), Frog::speak) eq "Ribbit!"
}
```

### 고차함수
 - 변수에 함수 담기 가능
 - (파라미터타입1, ..., 파라미터타입N) -> 반환타입
```kotlin
val isPlus: (Int) -> Boolean = { it > 0 }

fun main() {
  listOf(1, 2, -3).any(isPlus) eq true
}
```

 - any함수
```kotlin
fun <T> List<T>.any(                   
  predicate: (T) -> Boolean // predicate 함수는 T를 받아 Boolean 리턴 
): Boolean {
  for (element in this) {
    if (predicate(element)) 
      return true
  }
  return false
}

fun main() {
  val ints = listOf(1, 2, -3)
  ints.any { it > 0 } eq true // T가 int, boolean 리턴
  val strings = listOf("abc", " ")
  strings.any { it.isBlank() } eq true // T가 string, boolean 리턴
  strings.any(String::isNotBlank) eq   // 멤버참조 전달
    true
}  
```

 - trace구현
```kotlin
fun repeat(
  times: Int,
  action: (Int) -> Unit //여기서 받는건 trace람다
) {
  for (index in 0 until times) {
    action(index) // trace호출
  }
}
fun main() {
  repeat(3) { trace("#$it") }
  trace eq "#0 #1 #2"
}  
```
 - 반환값이 null가능
```kotlin
  var canReturnNull: (Int, Int) -> Int? = {x, y -> null} // 형태예시

  val transform: (String) -> Int? =
    { s: String -> s.toIntOrNull() }
  transform("112") eq 112
  transform("abc") eq null
  val x = listOf("112", "abc")
  x.mapNotNull(transform) eq "[112]"
  x.mapNotNull { it.toIntOrNull() } eq "[112]" // 위랑 같은것
```

 - 함수자체가 null가능
 ```kotlin
 var funOrNull: ((Int, Int) -> Int)? = Null
  // 널 검사를 하지 않으면 컴파일이 되지 않는다
  // mightBeNull("abc")
  if (mightBeNull != null) {
    mightBeNull("abc")
  }
 ```

### 리스트함수
 - zip() : 두 List원소를 하나씩 합치기
```kotlin
  val left = listOf("a", "b", "c", "d")
  val right = listOf("q", "r", "s", "t")
  
  left.zip(right) eq                 
    "[(a, q), (b, r), (c, s), (d, t)]"
  
  left.zip(0..4) eq                  
    "[(a, 0), (b, 1), (c, 2), (d, 3)]"
  
  (10..100).zip(right) eq           
    "[(10, q), (11, r), (12, s), (13, t)]"
``` 

 - zipWithNext() : 인접한 원소끼리 묶기
```kotlin
  val list = listOf('a', 'b', 'c', 'd')
  
  list.zipWithNext() eq listOf(
    Pair('a', 'b'),
    Pair('b', 'c'),
    Pair('c', 'd'))
```

 - flatten : 리스트들을 하나의 리스트로 평탄화
```kotlin
val nestedList = listOf(listOf(1, 2, 3), listOf(4, 5), listOf(6, 7, 8))
val flattenedList = nestedList.flatten()

println(flattenedList) // 출력: [1, 2, 3, 4, 5, 6, 7, 8]
```

 - flatMap : map()과 flatten()을 한 번에 수행하는 역할
```kotlin
val numbers = listOf(1, 2, 3, 4, 5)

val squaredAndFlattened = numbers.flatMap { listOf(it * it) }

println(squaredAndFlattened) // 출력: [1, 4, 9, 16, 25]
```

 - fold() : 주어진 초기값과 각 요소에 대한 연산을 사용하여 컬렉션의 모든 요소를 처리하고 최종 결과값을 반환
```kotlin
val result = collection.fold(initialValue) { accumulator, element ->
    // initialValue : 연산 시작점
    // accumulator: 현재까지의 누적값
    // element: 현재 처리 중인 요소
    // 결과값은 다음 누적값이 됨
}

val list = listOf(1, 10, 100, 1000)
list.fold(0) { sum, n -> // 0번째 인덱스부터 시작
  sum + n
} eq 1111

```

### Map함수
 - groupBy : 주어진 키값에 따라 그룹화
```kotlin
  fun people(): List<Person> =
  names.zip(ages) { name, age ->
    Person(name, age)
  }

  val map: Map<Int, List<Person>> = people().groupBy(Person::age)
```

 - associateWith : 각 요소에 대해 다른 값을 매핑한 Map을 생성
```kotlin
val numbers = listOf(1, 2, 3, 4, 5)
val squaredMap = numbers.associateWith { it * it }
// {1=1, 2=4, 3=9, 4=16, 5=25}
```

 - associateBy : associateWith의 반대
```kotlin
val numbers = listOf(1, 2, 3, 4, 5)
val squaredMap = numbers.associateBy { it * it }
// {1=1, 4=2, 9=3, 16=4, 25=5}
``` 

 - getOrElse : 특정 인덱스나 키에 해당하는 요소를 가져오는 함수 없으면 기본값을 반환
```kotlin
val list = listOf(1, 2, 3, 4, 5)
val element = list.getOrElse(2) { -1 }
println(element) // 출력: 3

val map = mapOf("a" to 1, "b" to 2, "c" to 3)
val value = map.getOrElse("b") { -1 }
println(value) // 출력: 2
``` 

 - getOrPut() : 없으면 기본값 삽입

### Sequence
 - Stream의 다른말
 - List는 즉시 연산이지만, asSequence()사용하면 지연연산으로 변경
```kotlin
val numbers = listOf(1, 2, 3, 4, 5)
val result = numbers.map { it * 2 }.any { it > 5 }

val numbers = listOf(1, 2, 3, 4, 5)
val result = numbers.asSequence().map { it * 2 }.any { it > 5 }
```

### 꼬리재귀
 - 재귀 호출이 함수의 마지막 단계에서 수행되고 추가 계산 없이 바로 반환
 - 이미 계산한값을 사용하기 때문에 효율적
 - 재귀 : f(9) + f(8) VS 꼬리재귀 : f(9) + 10
```kotlin
// 일반재귀
fun fibonacci(n: Long): Long {
  return when (n) {
    0L -> 0
    1L -> 1
    else ->
      fibonacci(n - 1) + fibonacci(n - 2)
  }
}

// 꼬리 재귀로 구현된 피보나치 함수
tailrec fun fibonacciTailRec(n: Int, a: Long = 0, b: Long = 1): Long {
    if (n == 0) {
        return a
    }
    return fibonacciTailRec(n - 1, b, a + b)
}

fun main() {
    val result = fibonacciTailRec(10)
    println(result) // 출력: 55
}
```