## Sixth

### 예외처리
```kotlin
 - 일반적인 예외처리
fun main() {
    try {
        // 예외 발생 가능한 코드
        val result = 10 / 0
    } catch (e: ArithmeticException) {
        // ArithmeticException 예외 처리
        println("Caught ArithmeticException: ${e.message}")
    } catch (e: Exception) {
        // 다른 예외들을 처리
        println("Caught Exception: ${e.message}")
    } finally {
        // 항상 실행되는 블록
        println("Finally block")
    }
}
```

 - try식 : return문으로 원하는 값 전달 가능
```kotlin
fun divide(a: Int, b: Int): Int {
    return try {
        a / b
    } catch (e: ArithmeticException) {
        println("Caught ArithmeticException: ${e.message}")
        -1 // 예외가 발생하면 이 값이 반환됨
    } finally {
        println("Finally block")
        // 이 블록의 코드가 실행된 후에도 반환 값이 유지됨
    }
}
```

### 검사명령
 - require는 주로 함수인자의 조건 확인에 사용
 - check는 주로 함수 내부에서 발생하는 조건 확인에 사용
 - assert는 디버깅에만 작동

 - require : 주어진 조건에 만족하지않으면 예외 발생
 - requireNotNull : Null이 아닌지 검사
```kotlin
fun divide(a: Int, b: Int): Int {
    require(b != 0) { "Denominator should not be zero" }
    requireNotNull(value) { "Value should not be null" }
    return a / b
}

fun main() {
    val result = divide(10, 2)
    println("Result: $result")

    // 아래의 코드는 조건을 만족하지 않아 IllegalArgumentException 발생
    val invalidResult = divide(10, 0)
    println("This line won't be reached")

    // 아래의 코드도 IllegalArgumentException 발생
    val nullValue: int? = null
    divide(10, nullValue);
}
```

 - check : 주어진조건이 false일때 IllegalArgumentException 발생
```kotlin
fun example(value: Int) {
    check(value >= 0) { "Value should be non-negative" }
    // 나머지 코드
}
``` 

 - assert : 주어진 조건이 false일 때 AssertionError 예외를 발생
```kotlin
fun example(value: Int) {
    assert(value >= 0) { "Value should be non-negative" }
    // 나머지 코드
}
```

### Nothing
 - 예외를 던지거나 무한 루프에 빠져 더 이상 진행되지 않을 함수에 사용
 - 아무값 반환 안함
 - 모든 타입의 하위 타입
```kotlin
fun fail(message: String): Nothing {
    throw IllegalArgumentException(message)
}

try {
    val result: Int = fail("Something went wrong")
    // 이 부분은 실행되지 않음
} catch (e: IllegalArgumentException) {
    println("Caught exception: ${e.message}")
}
```

### use
 - AutoCloseable 또는 Closeable 인터페이스를 구현한 자원에 대해 사용
 - 자원을 사용하는 블록을 정의하고, 블록이 끝날 때 자동으로 자원을 닫아줌
```kotlin
fun readFromFile(fileName: String): String {
    BufferedReader(FileReader(fileName)).use { reader ->
        // 파일에서 데이터 읽기
        return reader.readText()
    }
    // 위 블록 끝나면 자동으로 자원해제
}

fun main() {
    val content = readFromFile("example.txt")
    println(content)
}
``` 
 - 기존 방식(try,catch,finally)
```kotlin
fun readFromFileWithFinally(fileName: String): String {
    var reader: BufferedReader? = null

    try {
        reader = BufferedReader(FileReader(fileName))
        // 파일에서 데이터 읽기
        return reader.readText()
    } catch (e: IOException) {
        // 예외 처리
    } finally {
        // finally 블록에서 자원 닫기
        try {
            reader?.close()
        } catch (e: IOException) {
            // 자원 닫기 중에 예외 처리
        }
    }
    return ""
}
```

