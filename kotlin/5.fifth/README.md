## Fifth

### Inteface
 - 인터페이스를 구현할 때는 반드시 override 변경자를 붙여야 한다
 - 인터페이스가 프로퍼티를 선언가능, 이런 인터페이스를 구현하는 클래스는 항상 프로퍼티를 오버라이드해야 한다
```kotlin
interface Computer {
  fun prompt(): String
  fun calculateAnswer(): Int
}

class Desktop : Computer {
  override fun prompt() = "Hello!"
  override fun calculateAnswer() = 11
}
```
```kotlin
interface Player {
  val symbol: Char
}

class Food : Player {
  override val symbol = '.'
}
```

 - SAM 인터페이스 : 단일 추상 메서드
```kotlin
// 자바에서의 선언
public interface Runnable {
    void run();
}

// 코틀린에서 사용
val runnable = Runnable {
    println("Running")
}
```

```kotlin
fun interface ZeroArg {
  fun f(): Int
}
// 클래스로 구현하거나
class VerboseZero : ZeroArg {
  override fun f() = 11
}
// 람다로 구현
val samZero = ZeroArg { 11 }
```

### 생성자
 - 주생성자
  1. 파라미터 
  ```korlin
  class MyClass(i: Int)
  ```
  2. init
  ```kotlin
  class Message(text: String) {
    private val content: String
    init {
      counter += 10
    }
  }
  ```

### 부생성자
 - 부생성자는 항상 주생성자를 직접호출, 간접호출해야지만 실행가능
```kotlin
class WithSecondary(i: Int) {
  init {
    trace("Primary: $i")
  }
  constructor(c: Char) : this(c - 'A') { // this로 return하면서 간접호출
    trace("Secondary: '$c'")
  }
  constructor(s: String) : 
    this(s.first()) { // 직접호출
    trace("Secondary: \"$s\"")
  }
}
```

### 상속
 - open class : 부모클래스가 open으로 선언되어있어야만 상속가능
 - 콜론(:)으로 상속
```kotlin
// 이 클래스는 상속될 수 있다
open class Parent
class Child : Parent()

// Child는 열려 있지 않으므로 다음 상속은 실패한다
// class GrandChild : Child()

// 이 클래스는 상속될 수 없다
final class Single

// 'final'을 쓴 선언과 같은 효과를 낸다
class AnotherSingle

// 클래스를 상속할때는 기반클래스 인자목록을 적어야한다
open class SuperClass1(val i: Int)
class SubClass1(i: Int) : SuperClass1(i)
```

### 추상클래스, 인터페이스
 - 인터페이스
    1. 다중 상속
    2. 상태(멤버 변수)를 가질 수 없음
    3. 생성자를 가질 수 없음
    4. default 키워드를 사용하여 인터페이스 내에서 메서드에 대한 기본 구현을 제공
```kotlin
interface MyInterface {
    val property: Int
    fun abstractMethod()

    // 인터페이스에서 기본 구현
    fun defaultMethod() {
        println("Default method implementation")
    }
}
```

 - 추상클래스
    1. 다중 상속이 불가능
    2. 상태(멤버 변수)를 가질 수 있음
    3. 생성자를 가질 수 있음
    4. 추상뿐만이 아닌 일반 클래스, 프로퍼티 가질 수 있음 
```kotlin
abstract class MyAbstractClass(val property: Int) { //프로퍼티, 생성자
  abstract val variable : int
  var name: String = "Unknown" //멤버변수
  
  abstract fun abstractMethod()
  fun concreteMethod() { //일반 메서드
      println("Concrete method")
  }
}
```

### 업캐스트
 - 하위 클래스의 인스턴스를 상위 클래스 타입으로 변환하는 것
```kotlin
open class Animal {
    fun eat() {
        println("Animal is eating")
    }
}

class Dog : Animal() {
    fun bark() {
        println("Dog is barking")
    }
}

fun main() {
    val animal: Animal = Dog() // 업캐스트 발생
    animal.eat() // Animal 클래스의 메서드 호출
    // animal.bark() // 컴파일 에러! Animal 클래스에는 bark() 메서드가 없음

    // 다시 다운캐스트하여 하위 클래스의 메서드 호출
    val dog = animal as Dog
    dog.bark()
}
```

### 다형성
 1. 컴파일 시 다형성, 정적 다형성
```kotlin
fun add(a: Int, b: Int): Int {
    return a + b
}

fun add(a: Double, b: Double): Double {
    return a + b
}
``` 

 2. 런타입 다형성, 동적 다형성
```kotlin
open class Animal {
    open fun sound() {
        println("Animal makes a sound")
    }
}

class Dog : Animal() {
    override fun sound() {
        println("Dog barks")
    }
}

class Cat : Animal() {
    override fun sound() {
        println("Cat meows")
    }
}

fun main() {
    val animal1: Animal = Dog()
    val animal2: Animal = Cat()

    animal1.sound() // Dog barks
    animal2.sound() // Cat meows
}
```

### 합성
 - 두 개 이상의 클래스를 조합하여 새로운 클래스를 만드는 디자인 패턴
 - 합성은 "has-a" 관계를 강조
```kotlin
class Engine {
    fun start() {
        println("Engine starting")
    }
}

class Car(private val engine: Engine) {
    fun start() {
        engine.start()
        println("Car is starting")
    }
}

fun main() {
    val car = Car(Engine())
    car.start()
}
```








