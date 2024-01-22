## FIRST

### Compiler, Interpreter
 - C, C++, Go, Rust : 기계코드로 컴파일되어 CPU에서 실행
 - Java, Kotlin : bytecode로 컴파일되어 JVM에서 실행(이식성, 컴파일 시점에 오류 확인)

### 언어개요
 - C 
    - 장점 : 다른 프로세서에서도 어셈블리 프로그램을 이식하기 위해 만든 고수준 어셈블리 언어
    - 단점 : 메모리 관리에 따른 메모리 누수

 - C++ : 객체지향 개념 도입, 메모리 관리 도임
 - JAVA : VM, GC 도입
 - Kotlin
    - 가독성
    - 도구(IntelliJ)
    - 다중 패러다임 제공(명령형, 함수형, 객체지향 프로그래밍) (선언형은 추상화된걸 말한다)
    - 다중플랫폼(JVM, 안드로이드, 자바스크립트, 네이티브 바이너리)
    - Null 값 표현 방식(Null 위반을 컴파일 시점에 체크가능)

### var, val
 - var : 가변변수
    - var n: Int = 1
    - var p: Double = 2.2
 - val : 불변변수
 - val의 장점 =
  ### [final의 장점](https://github.com/kps990515/flab/tree/master/920)

### 함수
 - 일반적 함수
```kotlin
    fun multiply(x:int, y:Double): Double {
        println("multiple")
        return x*2
    }
```  
 - 한줄 함수
```kotlin
    fun multiplyByFour(x:Int) = x*4
```  
 - return값 없음
```kotlin
    fun sayGoodbye(): Unit = println("Auf Wiedersehen!")
```  
### if문
```kotlin
    if (1 > 0)
        println("It's true!")
```
 - true, false값 받기
```kotlin
    fun main() {
    val x: Boolean = 1 >= 1
    if (x)
        println("It's true!")
    }
```
 - 한줄로쓰기
```kotlin
    val result = if (num > 100) 4 else 42
``` 

### 문자열 템플릿
 - $ : 식별자 앞에 $를 붙이면 식별자 내용을 String에 넣는다
```kotlin
    val answer = 42
    println("Found $answer!") // Found 42!
```  
 - ${} : 중괄호 안에 식을 넣으면 식의 결과값을 String으로 변환해 삽입
```kotlin
    val condition = true
    println("${if (condition) 'a' else 'b'}") // a 출력
```   

### 숫자
 - 숫자사이에 _ 허용(가독성 위해)
```kotlin
    val million = 1_000_000 // Int를 추론 
    println(million) // 1000000 
```    

### 루프
 - for문 표현방식
```kotlin
    for (1..5)
    for (i in 1..3)
    for (1 until 3)
    for (5 downTo 1)
    for (0..9 step 2)
    for (0 until 10 step 3)
    for (9 downTo 2 step 3)
    for (alphatbet in 'a'..'z')
    repeat(10) // 10번 반복
```  
 - 인덱스 접근가능
```kotlin
    val s = "abc"
    for (i in 0..s.lastIndex) {
        print(s[i] + 1)
    }
``` 
 - 문자열의 문자 접근가능
```kotlin
    for (ch in "Jnskhm ") {
        print(ch + 1)
    }
```  
```kotlin
    fun hasChar(s: String, ch: Char): Boolean {
        for (c in s) {
            if (c == ch) return true
        }
    }

    fun main() {
        println(hasChar("kotlin", 't'))
        println(hasChar("kotlin", 'a'))
    }
``` 

### in
 - 변수에 정해진 값이 
    - 없으면 정해진 범위 루프
    ```kotlin
    val values = 1..3
    for (v in values) {
        println("iteration $v") // 1,2,3
    }
    ```     
    - 있으면 해당 범위안에 값이 있는지 true/flase
    ```kotlin
    val v = 2
    if (v in values)
        println("$v is a member of $values")
    ```   
    ```kotlin
    println('t' in "kotlin")
    println('a' in "kotlin")  
    println("ab" in "aa".."az")
    println("ba" in "aa".."az")
    ```  
 - Double
```kotlin
    fun inFloatRange(n: Double) {
        val r = 1.0..10.0
        // val r = 1.0 ..< 2.0 (코틀린 1.8이상)
        println("$n in $r? ${n in r}")
    }

    fun main() {
        inFloatRange(0.999999)
        inFloatRange(5.0)
        inFloatRange(10.0)
        inFloatRange(10.0000001)
    }
```  

 