# Exception

## 1. 개념

### 필요성
 - 예기치 않은 상황을 관리하고 프로그램이 비정상적으로 종료되지 않도록 도와줍니다.

### 계층 구조
 - Throwable 클래스의 서브클래스
    - Error: 애플리케이션에서 복구할 수 없는 심각한 문제를 나타냅니다. 주로 JVM 레벨에서 발생합니다.
    - Exception: 애플리케이션에서 처리하거나 복구할 수 있는 문제를 나타냅니다. 다시 두 가지로 나뉩니다:
        - Checked Exception: 컴파일 타임에 체크되는 예외.
        - Unchecked Exception (RuntimeException): 런타임에 체크되는 예외.

## 2. Checked, Unchecked
 - Checked 예외
    - 설명: 컴파일 타임에 체크되는 예외로, 반드시 예외 처리가 필요합니다.
    - 예제: IOException, SQLException 등
 - Unchecked 예외 (RuntimeException)
    - 설명: 런타임에 발생하는 예외로, 예외 처리를 강제하지 않습니다.
    - 예제: NullPointerException, ArrayIndexOutOfBoundsException 등

## 3. 예외전환
 - 사용자 정의 예외
 ```java
    class CustomException extends Exception {
        public CustomException(String message) {
            super(message);
        }
    }
 ```

 - 예외전환
 ```java
    public class ExceptionConversionExample {
        public static void main(String[] args) {
            try {
                methodThatThrowsIOException();
            } catch (IOException e) {
                throw new CustomException("Custom message", e);
            }
        }

        public static void methodThatThrowsIOException() throws IOException {
            // 코드가 IOException을 발생시킴
            throw new IOException("IO error");
        }
    }
 ```

## 4. 다중예외처리
```java
try {
    // 예외 발생 가능성이 있는 코드
} catch (IOException | SQLException e) {
    System.out.println("Exception occurred: " + e.getMessage());
}
```

## 5. try-with-resources
 - AutoCloseable을 구현한 자원을 자동으로 닫아줍니다.
```java
try (FileInputStream fis = new FileInputStream("sample.txt")) {
    int data;
    while ((data = fis.read()) != -1) {
        System.out.print((char) data);
    }
} catch (IOException e) {
    System.out.println("Error handling file: " + e.getMessage());
}
```

## 6. 기술면접
### 1. 예외와 에러의 차이점은 무엇인가요?
### 예외(Exception)는 프로그램의 비정상적인 동작을 나타내는 이벤트로, 애플리케이션에서 처리하거나 복구할 수 있습니다. 예외는 주로 애플리케이션 레벨에서 발생하며, 개발자가 예외를 처리하여 프로그램의 정상적인 흐름을 유지할 수 있습니다. 예외는 다시 Checked 예외와 Unchecked 예외로 나뉩니다.
### **에러(Error)**는 애플리케이션에서 복구할 수 없는 심각한 문제를 나타냅니다. 에러는 주로 JVM 레벨에서 발생하며, 시스템 수준의 문제를 포함합니다. 예를 들어, OutOfMemoryError나 StackOverflowError 등이 있습니다. 이러한 에러는 프로그램이 복구할 수 없기 때문에, 보통 처리하지 않고 프로그램을 종료합니다.

### 2. Checked 예외와 Unchecked 예외의 차이점은 무엇인가요?
### Checked 예외는 컴파일 타임에 체크되는 예외로, 반드시 예외 처리가 필요합니다. 예외가 발생할 가능성이 있는 코드를 작성할 때, 이를 처리하기 위해 try-catch 블록을 사용하거나 메서드 선언에 throws 키워드를 사용해야 합니다. 예시로 IOException, SQLException 등이 있습니다.
### Unchecked 예외는 런타임에 발생하는 예외로, 예외 처리가 강제되지 않습니다. 주로 프로그래밍 오류에 의해 발생하며, 개발자가 선택적으로 처리할 수 있습니다. 예시로 NullPointerException, ArrayIndexOutOfBoundsException 등이 있습니다.

### 3. 예외를 처리하는 방법은 무엇인가요?
### 예외는 try-catch-finally 블록을 사용하여 처리할 수 있습니다. try 블록 내에 예외가 발생할 가능성이 있는 코드를 작성하고, catch 블록에서 예외를 처리합니다. finally 블록은 예외 발생 여부와 상관없이 항상 실행되는 코드를 포함합니다.

### 4. try-with-resources 문법은 무엇인가요?
### try-with-resources 문법은 자원을 자동으로 닫아주는 구조로, AutoCloseable 인터페이스를 구현한 자원을 사용할 때 유용합니다. try 블록이 종료될 때 자동으로 자원을 해제하여 리소스 누수를 방지합니다.

### 5. 예외 전환(Exception Translation)이란 무엇인가요?
### 예외 전환은 특정 예외를 더 의미 있는 예외로 변환하여 호출자에게 전달하는 것을 말합니다. 이를 통해 호출자가 예외를 더 쉽게 이해하고 적절히 처리할 수 있습니다. 예를 들어, IOException을 CustomException으로 전환할 수 있습니다.