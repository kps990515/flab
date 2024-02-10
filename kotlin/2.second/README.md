## SECOND

### 객체지향언어
 - 명사를 객체로 만들고 사용하는 언어

### 함수형 언어
 - 수행할 동작(동사)에 초점을 맞춘 언어

### val, var클래스
 - val house = House() : 새 객체 참조 불가
 - var house = House() : 새 객체 참조 가능
 - house클래스 내부 변수들은 var인 경우 값 변경 가능

### 클래스
  - val wombat = Wombat() : new가 사라짐
```kotlin
class Alien(name: Stirng){
    val greeting = "Poor $name"
}

// 파라미터에 var, val넣으면 클래스밖에서도 호출가능
class AlineTwo(var name: String)

fun main(){
    val alien = Alien("alien")
    println(alien.greeting) // Poor alien
    println(alien.name) // 에러 발생
    
    val alienTwo = AlienTwo("alienTwo")
    println(AlienTwo.name) // Poor alienTwo
}
```

### Override

```kotlin
class Scientist(val name: String) {
  override fun toString(): String {
    return "Scientist('$name')"
  }
}

fun main() {
  val zeep = Scientist("Zeep Xanflorp")
  println(zeep)
}
```

### List
```kotlin
import atomictest.eq

fun main() {
  val list = mutableListOf<Int>() // 가변리스트 생성시 사용
  
  list.add(1)
  list.addAll(listOf(2, 3))
  
  list += 4
  list += listOf(5, 6)
  
  list eq listOf(1, 2, 3, 4, 5, 6)
}
```
 - += 사용
  - 불변리스트에서는 새로운 list생성
  - 가변리스트에서는 내부 값 추가
 ```kotlin
 fun main() {
  var list = listOf('X') // 불변 리스트
  list += 'Y'            // 기존 리스트와 다른 list가 생성 & var list에 대입
  list eq "[X, Y]"
}
 ```


 - val, var 리스트
 ```kotlin
 fun main() {
    // 'val'/'var'에 가변 리스트를 대입하는 경우:
    val list1 = mutableListOf('A') // or 'var'
    list1 += 'A' // 다음 줄과 같다
    list1.plusAssign('A') // 가변리스트에서 +=은 새로운 list가 아닌 내부 값을 추가
 }
 ```

 ```kotlin
    // 'var'에 불변 리스트를 대입하는 경우:
    var list3 = listOf('C')
    list3 += 'C' // 기존 불변리스트와 다른 리스트 생성(아래줄과 동일효과)
    val newList = list3 + 'C' // 기존 불변리스트와 다른 newList 생성
    list3 = newList // list3가 var이기 때문에 새로운 객체인 newList 참조가능
 ```

### args : 가변인자 array

- 1개의 파라미터에만 사용가능
```kotlin
fun v(s: String, vararg d: Double) {}

fun main() {
  v("abc", 1.0, 2.0)
  v("def", 1.0, 2.0, 3.0, 4.0)
  v("ghi", 1.0, 2.0, 3.0, 4.0, 5.0, 6.0)
}
```

- 스프레드연산자(*) : array를 풀어서 전달하고 싶을 때 사용
```kotlin
fun main() {
  val array = intArrayOf(4, 5)
  sum(1, 2, 3, *array, 6) eq 21 // sum(1,2,3,4,5,6) 과 동일

  // sum(1, 2, 3, array, 6) -> 이렇게하면 컴파일 안됨

  val list = listOf(9, 10, 11)
  sum(*list.toIntArray()) eq 30 // 스프레드연산자는 array만 적용되기에 list를 array로 변경필요
}
```

### Set
```kotlin
fun main() {
  val intSet = setOf(1, 1, 2, 3, 9, 9, 4)
  // 중복이 없다
  intSet eq setOf(1, 2, 3, 4, 9)
  
  // 원소 순서는 중요하지 않다
  setOf(1, 2) eq setOf(2, 1)
  
  // 원소인지 검사
  (9 in intSet) eq true
  (99 in intSet) eq false
  
  intSet.contains(9) eq true
  intSet.contains(99) eq false

  // 이 집합이 다른 집합을 포함하는가?
  intSet.containsAll(setOf(1, 9, 2)) eq true
  
  // 합집합
  intSet.union(setOf(3, 4, 5, 6)) eq
    setOf(1, 2, 3, 4, 5, 6, 9)
  
  // 교집합
    intSet intersect setOf(0, 1, 2, 7, 8) eq
    setOf(1, 2)
  
  // 차집합
  intSet subtract setOf(0, 1, 9, 10) eq
    setOf(2, 3, 4)  
  intSet - setOf(0, 1, 9, 10) eq
    setOf(2, 3, 4)
}
```

### Map
 - mapOf : 고정Map 생성
 ```kotlin
   // 키에 해당하는 값을 찾는다
  constants["e"] eq 2.718              // e라는 키값으로 검색
  constants.keys eq setOf("Pi", "e", "phi")
  constants.values eq "[3.141, 2.718, 1.618]"
  
  var s = ""
  // 키-값 쌍을 이터레이션한다
  for (entry in constants) {           // 맵 항목으로 하나씩 조회
    s += "${entry.key}=${entry.value}, "
  }
  s eq "Pi=3.141, e=2.718, phi=1.618,"
  
  s= "" 

  // 이터레이션을 하면서 키와 값을 분리한다
  for ((key, value) in constants)      // 키 값 분리 가능
    s += "$key=$value, "
  s eq "Pi=3.141, e=2.718, phi=1.618,"
 ```
 
 - mutableMapOf : 가변 Map 생성

 - getValue() : 값이 null이면 NoSuchElementException 발생
 ```kotlin
 map.getValue('b') // NoSuchElementException
 ```

 - getOrDefault : Null일때 예외처리
 ```kotlin
 map.getOrDefault('a', 0)
 ```

### 프로퍼티(객체) 접근자 
 
```kotlin
class Data(var i: Int)
 
fun main() {
  val data = Data(10)
  data.i eq 10 // 'i' 프로퍼티를 읽음 
  data.i = 20  // 'i' 프로퍼티에 값을 씀
}
```
 - getter, setter
 ```kotlin
 class Default {
  var i: Int = 0
    get() { //getter
      return field  // getter,setter안에서만 field이름을 사용해 데이터 접근가능
    }
    set(value) { //seter
      field = value
    }
}
 ```

 - private setter, public getter
 ```kotlin
 class Counter {
  var value: Int = 0
    private set // setter를 private으로 선언해 접근못하도록
  fun inc() = value++ // 해당 함수 통해서만 데이터 변경가능
}

fun main(){
  val counter = Counter()
  repeat(10) {
    counter.inc()
  }
  counter.value eq 10
}
 ``` 







