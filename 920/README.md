## 9/20 JAVA

### final을 붙였을떄 장점
1. final 변수 (상수)
  - 불변성 유지
  - 읽기 전용 값 표현
2. final 메서드
  - 오버라이딩 방지
  - 최적화 가능성: final로 선언된 메서드는 런타임에 컴파일러가 인라인으로 최적화할 가능성이 높아집니다. 따라서 성능이 조금 향상될 수 있습니다.
3. final 클래스
  - 상속 방지
  - 보안 강화

- Thread Safe 
- 실패원자적 메소드 제공 : 메소드가 에러가 나도 메소드 호출전의 상태가 유지되어 다른 메소드 실행 가능
- Cache, Map, Set의 원소값 정합성을 보장하여 갱신이 날 염려 줄어듬
- GC성능 향상 
  - final 객체 생성과정
  1. Object타입의 Value 생성
  2. ImmutableHolder 타입의 컨테이너 객체 생성
  3. ImmutableHolder가 value를 참조
  - 결론적으로 ImmutableHolder는 무조건 value보다 young 영역에 속하게됨

  - GC가 Young Generation 스캔하면서 ImmutableHolder를 먼저 체크하고 그 하위 final value들의 생존 확인가능
  - GC가 스캔해야되는 객체가 여러개에서 ImuutableHolder로 축소됨

### Immutable Object
- 데이터 변경이 불가한 객체(코드상으로는 변경이 가능하다)
- Heap영역에 저장된 값을 수정할 수 없는 것
```java 
Integer i = 1;
i = 3; // 데이터 변경이 아닌 새로운 객체가 생성된 것
```
- 장점 : Thread-safe, 다른 Thread에 의해 final 객체 변경 금지
- 단점 : 객체 값 할당될때 마다 새로운 객체 필요(메모리 누수)

### [복사](https://ttl-blog.tistory.com/1206)
1. 얕은 복사 : 객체의 주소값만을 복사
```java
Person a = new Person()
Person b = Person a
```
2. 방어적 복사 : 객체 주소의 복사가 아닌 객체의 내부값만을 참조 복사
```java
// 방어적 복사가 안되는 케이스
private final List<Friend> friends;

public Person(final Name name, final List<Friend> friends) {
  this.name = name;
  this.friends = friends;
}

List<Friend> friends = new ArrayList<>();
Person 말랑 = new Person(Name.of("말랑"), friends);
// friends의 주소값을 복사 해 Person 말랑의 내부 friends에 사용
Friend 친구1 = new Friend(Name.of("친구1"));
Friend 친구2 = new Friend(Name.of("친구2"));
Friend 친구3 = new Friend(Name.of("친구3"));
friends.addAll(List.of(친구1, 친구2, 친구3));
// 그래서 밖에서 add를 해도 같은 friends로 인식해 3이 나옴
System.out.println("친구의 수: " + 말랑.friends().size());
```

```java
private final List<Friend> friends;

public Person(final Name name, final List<Friend> friends) {
    this.name = name;
    this.friends = new ArrayList<>(friends);  // 방어적 복사, friends의 내부 값만을 참조 복사
}

friends.addAll(List.of(친구1, 친구2, 친구3));
// add를 해도 말랑의 friends와 밖의 friends는 다른 객체 -> 0이 나옴
System.out.println("친구의 수: " + 말랑.friends().size());

// 하지만 내부의 값을 바꿔버리면 같이 바뀜
friends.forEach(it -> it.changeName(Name.of(it.name().value() + " 이름 바꿔버리기~")));
```
3. 깊은 복사 : 객체의 모든 내부상태를 복사하여 새로운 객체 생성
```java
private final List<Friend> friends;

public Person(final Name name, final List<Friend> friends) {
    this.name = name;
    this.friends = friends.stream()
            .map(it -> new Friend(it.name()))
            .collect(Collectors.toList());  // 깊은 복사
    }
```

### [call by Value, call by Reference](https://bcp0109.tistory.com/360)
1. Call by Value : 호출자 (Caller) 의 변수와 호출 당하는 수신자 (Callee) 의 파라미터는 복사된 서로 다른 변수입니다.
값만을 전달하기 때문에 수신자의 파라미터를 수정해도 호출자의 변수에는 아무런 영향이 없습니다.

2. Call by Reference : 참조 (주소) 를 직접 전달, 참조를 직접 넘기기 때문에 호출자의 변수와 수신자의 파라미터는 완전히 동일한 변수입니다.
메서드 내에서 파라미터를 수정하면 그대로 원본 변수에도 반영됩니다.

3. JAVA : call by Value, 참조 객체를 넘기는것이 아닌 참조 객체의 주소값만을 전달
```java
public class PrimitiveTypeTest {

@Test
@DisplayName("Primitive Type 은 Stack 메모리에 저장되어서 변경해도 원본 변수에 영향이 없다")
void test() {
    int a = 1;
    int b = 2;

    // Before
    assertEquals(a, 1);
    assertEquals(b, 2);

    modify(a, b);

    // After: modify(a, b) 호출 후에도 값이 변하지 않음
    assertEquals(a, 1);
    assertEquals(b, 2);
}

private void modify(int a, int b) {
    // 여기 있는 파라미터 a, b 는 이름만 같을 뿐 test() 에 있는 a, b 와 다른 변수
    a = 5;
    b = 10;
}
}
```
```java
public class ReferenceTypeTest {

    @Test
    @DisplayName("Reference Type 은 주소값을 넘겨 받아서 같은 객체를 바라본다" +
                 "그래서 변경하면 원본 변수에도 영향이 있다")
    void test() {
        User a = new User(10);
        User b = new User(20);

        // Before
        assertEquals(a.age, 10);
        assertEquals(b.age, 20);

        modify(a, b);

        // After
        assertEquals(a.age, 11);
        assertEquals(b.age, 20);
    }

    private void modify(User a, User b) {
        // a, b 와 이름이 같고 같은 객체를 바라본다.
        // 하지만 test 에 있는 변수와 확실히 다른 변수다.

        // modify 의 a 와 test 의 a 는 같은 객체를 바라봐서 영향이 있음
        a.age++;

        // b 에 새로운 객체를 할당하면 가리키는 객체가 달라지고 원본에는 영향 없음
        b = new User(30);
        b.age++;
    }
}
```

### final, finally, finalize
- final
  1. final 변수 : 값 변경 금지
  2. final 함수 : override 금지
  3. final 클래스 : 상속금지

- finally : try, catch, finally
- finalize : GC가 수행될때 사용되지 않는 자원을 정리할때 사용하는 메소드

### list의 최대용량과 사이즈 증가 범위
- 실제로는 가지고 있던 용량이 꽉 찼을 때, 용량이 기존의 1.5배를 늘린 새로운 배열에 기존 배열을 copy하는 것이다.
- MAX_ARRAY_SIZE = Integer.MAX_VALUE-8
- 넘어갈 경우 interger max value를 주고 넘어가면 outOfMemory

1. 첫 element 를 add 할때 배열의 resize 가 발생하고 배열 크기는 10 으로 설정 된다.
2. ArrayList 에 10개의 데이터가 있고 데이터를 추가 하려고 하면 resize 가 발생하여 15 가 된다
3. element를 add하려고 할 때, capacity가 elementData(배열)의 길이와 같아지면 일반적인 상황에서 기존의 용량 + 기존 용량/2 만큼 크기가 늘어난 배열에 기존 elementData를 copy한다.   

