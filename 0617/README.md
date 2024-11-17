## 06/17

### [Local cache VS Remote Cache](https://kk-programming.tistory.com/83)
- Local Cache : 로컬 장비 내에서만 사용되는 캐시
- 종류
  1. In-Memory Cache : 데이터를 애플리케이션의 메모리에 저장, 인스턴스별로 독립적(HashMap, Ehcache, Caffein)
- 장점
  1. 속도가 빠르다.
  2. 네트워크 지연, 단절 이슈에 자유로움
  3. 서버 어플리케이션과 라이프 사이클을 같이 하므로 사용하기 간편함
  4. 아주 간단한 캐시 등은 메모리 기반으로 동작하는 것이 효율적일 수 있음.
- 단점
  1. 휘발성 메모리(서버다운시 데이터 소멸)
  2. 서버간 데이터 불일치
  3. 애플리케이션 메모리 부족 발생 가능

- Global Cache(Redis, MemCached, CDN)
  - 장점
    1. 데이터 분산 저장(Replication, Shading)
    2. 서버간 데이터 공유 쉬움
    3. 확장성
  - 단점
    1. 네트워크 트래픽
    2. 네트워크 단절 가능

  ### [페이징 처리](https://velog.io/@ygreenb/Paginationoffset-vs-cursor)
- Offset : DB의 limit, offset 쿼리를 사용하여 구분하여 ‘페이지’ 단위로 구분하여 요청/응답하게 구현
  - 문제점
    1. CREATE 시 데이터 중복 출력
    2. DELETE 시 데이터 누락
    3. offset 개수에 따른 성능 문제
    4. 느린 속도(O(N), O(offset+limit))
  
  - 사용가능 상황
    1. row수 적은 경우
    2. 데이터 변화가 적은 경우
    3. 중복데이터 노출 문제 없는 경우

- Cursor : 클라이언트가 가져간 마지막 row의 순서상 다음 row들을 n개 요청/응답하게 구현
  - 장점 : 빠른속도(O(1), O(limit))

  - 단점
    1. 정렬 컬럼의 값이 unique해야함
    2. 구현 까다로움(cursor 컬럼 필요 등)

 ### [익명클래스](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%9D%B5%EB%AA%85-%ED%81%B4%EB%9E%98%EC%8A%A4Anonymous-Class-%EC%82%AC%EC%9A%A9%EB%B2%95-%EB%A7%88%EC%8A%A4%ED%84%B0%ED%95%98%EA%B8%B0)
  - Inner Class일종으로 이름이 없는 클래스
  - 일회용 클래스

  - 유의점 : 오버라이딩한 메소드만 사용가능 / 새로 정의한 메소드는 사용 불가능
```java
// 부모 클래스
class Animal {
    public String bark() {
        return "동물이 웁니다";
    }
}

public class Main {
    public static void main(String[] args) {
        // 익명 클래스 : 클래스 정의와 객체화를 동시에. 일회성으로 사용
        Animal dog = new Animal() {
            // @Override 메소드
            public String bark() {
                return "개가 짖습니다";
            }
            
            // 새로 정의한 메소드
            public String run() {
                return "달리기 ㄱㄱ싱";
            }
        }; // 익명클래스 끝에는 세미콜론 필요
        
        dog.bark();
        dog.run(); // ! Error - 외부에서 호출 불가능
    }
}
```

  - 선언위치
    1. 클래스 필드
    ```java
    class Animal { ... }

    class Creature {
        // 필드에 익명자식 객체를 생성 하여 이용
        Animal dog = new Animal() {
            public String bark() {
                return "멍멍";
            }
        }
    ```

    2. 지역변수
    ```java
    class Animal { ... }

    class Creature {
      // ...
        
        public void method() {
          // 지역 변수같이 클래스를 선언하여 일회용으로 사용
            Animal dog = new Animal() {
                public String bark() {
                    return "멍멍";
                }
            };
            dog.bark();
        }
    ```

    3. 메소드 아규먼트
    ```java
    class Animal { ... }

    class Creature {
      // ...
        
        public void method(Animal dog) { // 익명 객체 매개변수로 받아 사용
            dog.bark();
        }
    }

    public class Main {
        public static void main(String[] args) {
            Creature monster = new Creature();
            
            // 메소드 아규먼트에 익명 클래스 자체를 입력값으로 할당
            monster.method(new Animal() {
                public String bark() {
                    return "멍멍";
                }
            });
        }
    }
    ```

  - 인터페이스 익명 구현 객체(실무)
```java
// 인터페이스
interface IAnimal {
    public String bark(); // 추상 메소드
    public String run();
}

public class Main {
    public static void main(String[] args) {
        // 인터페이스 익명 구현 객체 생성
        IAnimal dog = new IAnimal() {
            @Override
            public String bark() {
                return "개가 짖습니다";
            }
            
            @Override
            public String run() {
                return "개가 달립니다";
            }
        };
        
        // 인터페이스 구현 객체 사용
        dog.bark();
        dog.run();
    }
}
```

- 익명구현 객체 활용(메소드의 아규먼트로 일회성 객체를 넘겨주는 방법으로 자주 애용)
  ```java
  // 연산식을 추상화한 인터페이스
  interface Operate {
      int operate(int a, int b);
  }
  ```
  ```java
  // 계산을 담당하는 클래스
  class Calculator {
    // 계산할 두 수를 저장하는 필드
      private final int a;
      private final int b;
    
      // 생성자
      public Calculator(int a, int b) {
          this.a = a;
          this.b = b;
      }

      // 인터페이스 타입을 매개변수로 받는 메소드 (다형성)
      public int caculate(Operate op) {
          return op.operate(this.a, this.b); // 매개변수 객체의 메서드 실행하여 리턴
      }
  }
  ```
  ```java
  public class Main {
    public static void main(String[] args) {
        // 계산할 두 수
        int num1 = 20;
        int num2 = 10;

        // Calculator 클래스 생성하며 계산 할 수를 클래스 필드에 저장
        Calculator calculator = new Calculator(num1, num2);

        // calculator.caculate() 메서드 인자로, operate() 추상 메소드를 더하기 연산이 되도록 재정의한 익명 구현 객체를 넘김
        // calculator.caculate() 매서드 내에서 재정의된 operate() 메소드가 실행되어 a + b가 리턴 됨
        int result = calculator.caculate(new Operate() {
            public int operate(int a, int b) {
                return a + b;
            }
        });

        System.out.println(result); // 30


        // calculator.caculate() 메서드 인자로, operate() 추상 메소드를 빼기 연산이 되도록 재정의한 익명 구현 객체를 넘김
        // calculator.caculate() 매서드 내에서 재정의된 operate() 메소드가 실행되어 a - b가 리턴 됨
        int result2 = calculator.caculate(new Operate() {
            public int operate(int a, int b) {
                return a - b;
            }
        });

        System.out.println(result2); // 10
    }
  ```

  - 람다식 표현의 익명 구현 객체
    1. 인터페이스로만 구현 가능
    2. 하나의 추상메서드로 선언한 인터페이스만 가능
  ```java
  public interface Operate {
    // 추상 메서드가 하나이다
    int operate(int a, int b);

    // default 메서드는 추상 메서드에 포함되지 않는다
    default void print() {
        System.out.println("출력");
    }
  }

    Operate operate = (a, b) -> {
      print();
      return a + b;
    };
  ```

```java
public class Main {
    public static void main(String[] args) {
        // 계산할 두 수
        int num1 = 20;
        int num2 = 10;

        // Calculator 클래스 생성하며 계산 할 수를 클래스 필드에 저장
        Calculator calculator = new Calculator(num1, num2);

        // operate() 추상 메소드를 더하기 연산이 되도록 재정의한 익명 구현 객체
        Operate operate = (a, b) -> a + b;
        
        // calculator.caculate() 매서드에 람다식을 넣음
        int result = calculator.caculate(operate);

        System.out.println(result); // 30


		// 아니면 람다식 자체를 메소드 인자로 바로 넘겨줄 수 도 있다.
        int result2 = calculator.caculate((a, b) -> {
            return a - b;
        });

        System.out.println(result2); // 10
    }
}
```

### [Java InnerClass](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EB%82%B4%EB%B6%80-%ED%81%B4%EB%9E%98%EC%8A%A4Inner-Class-%EC%9E%A5%EC%A0%90-%EC%A2%85%EB%A5%98)
- 클래스 내부에 선언된 또 다른 클래스
- 보통 두 클래스가 서로 긴밀한 관계가 있거나, 하나의 클래스또는 메소드에서만 사용되는 클래스일 때 이용되는 기법

- 장점
  1. 클래스 논리적 그룹화
  2. 타이트한 캡슐화 적용(내부 클래스 숨기기)
  3. 유지보수

- 종류
  1. 인스턴스 클래스 : static멤버 선언 불가
  2. 스태틱 클래스 : 외부클래스 생성없이도 생성 가능(메모리에 올라가는것은 아님)
  3. 지역 클래스 : 메소드 내부에서만 한정적으로 사용
  4. 익명 클래스

  ```java
  class Outer{
    class InstanceInner { ... } // 인스턴스 클래스
    static class StaticInner { ... } // 스태틱 클래스
      
      void method1(){
        class LocalInner { ... } // 지역 클래스
      }
  }
  ```
