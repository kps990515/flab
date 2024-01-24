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
 ```kotlin
 fun main() {
  var list = listOf('X') // 불변 리스트
  list += 'Y'            // 가변 리스트처럼 보임
  list eq "[X, Y]"
}
 ```


 - val, var 리스트
 ```kotlin
 fun main() {
    // 'val'/'var'에 가변 리스트를 대입하는 경우:
    val list1 = mutableListOf('A') // or 'var'
    list1 += 'A' // 다음 줄과 같다
    list1.plusAssign('A') // list1이 참조하는 객체가 바뀐것이 아닌 내부값이 바꼈기 때문에 가능
 }
 ```

 ```kotlin
    // 'var'에 불변 리스트를 대입하는 경우:
    var list3 = listOf('C')
    list3 += 'C' // 기존 불변리스트와 다른 리스트 생성(아래줄과 동일효과)
    val newList = list3 + 'C' // 기존 불변리스트와 다른 newList 생성
    list3 = newList // list3가 var이기 때문에 새로운 객체인 newList 참조가능
 ```





