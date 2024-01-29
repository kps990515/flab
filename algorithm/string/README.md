## String
 - Java.lang 패키지로 제공
 - 별도 Import 필요없음
 - 한번 생성되면 수정 불가

 - charAt : 특정 문자 가져오기
 ```java
 String str = "ABCD"
 char three = str.charAt(3)
 ```

 - StringBuilder : 변경가능한 문자열 생성
 ```java
    StringBuilder stringBuilder = new StringBuilder();
    stringBuilder.append("문자열 ").append("연결");
//  String str = stringBuilder;   // String에 StringBuilder를 그대로 넣을 순 없다. toString()을 붙여야 한다
    String str = stringBuilder.toString();
    // 두 println()은 같은 값을 출력한다
    System.out.println(stringBuilder);
    System.out.println(str);
 ```

  - 생성방식에 따른 == 비교
    1. Literal : 값이 같으면 재활용(constant String pool 영역에 저장)
    ```java
    String str_literal1 = "test";
    String str_literal2 = "test";

    str_literal1 == str_literal2 // true
    ```

    2. new : 매번 새로운 주소에 저장(일반 heap영역 저장)
    ```java
    String object1 = new String("test");
    String object2 = new String("test");

    object1 == object2 // false
    ```

### 문제1(https://www.acmicpc.net/problem/2744)
 - 영어 소문자와 대문자로 이루어진 단어를 입력받은 뒤, 대문자는 소문자로, 소문자는 대문자로 바꾸어 출력하는 프로그램을 작성하시오.
```java
import java.util.Scanner;

public class Main {
    public static void main(String[] args){
        StringBuilder sb = new StringBuilder();
        Scanner sc = new Scanner(System.in);

        String str = sc.next();
        char[] charArray = str.toCharArray();
        for(char c : charArray){
            if(Character.isUpperCase(c)){
                sb.append(Character.toLowerCase(c));
            }else{
                sb.append(Character.toUpperCase(c));
            }
        }

        System.out.println(sb);
        sc.close();
    }
}
``` 