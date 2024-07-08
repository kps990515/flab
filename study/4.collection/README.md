# Collection

## 기본개념
 - 컬렉션(Collection): 여러 개의 객체를 저장하고 관리하는 구조를 말합니다. 자바 컬렉션 프레임워크는 이러한 구조를 표준화하여 제공하는 API입니다.
 - 인터페이스(Interface): 컬렉션 프레임워크의 기본 인터페이스는 Collection, List, Set, Map, Queue 등이 있습니다.
    - Collection 인터페이스: 모든 컬렉션 클래스의 최상위 인터페이스입니다.
    - List 인터페이스: 순서가 있는 컬렉션으로, 중복된 요소를 허용합니다.
    - Set 인터페이스: 중복을 허용하지 않는 컬렉션입니다.
    - Map 인터페이스: 키-값 쌍으로 이루어진 컬렉션입니다.
    - Queue 인터페이스: FIFO(First-In-First-Out) 방식으로 요소를 처리하는 컬렉션입니다.

## List
1. ArrayList
    - 설명: 크기가 가변적으로 변하는 배열을 구현한 클래스입니다.
    - 특징: 빠른 랜덤 접근, 삽입과 삭제가 느림
2. LinkedList
    - 설명: 이중 연결 리스트로 구현된 클래스입니다.
    - 특징: 빠른 삽입과 삭제, 느린 랜덤 접근
3. Vector
    - 설명: 동기화된 ArrayList입니다.
    - 특징: 멀티스레드 환경에서 안전하지만, 성능이 떨어짐
4. Stack
    - 설명: 벡터를 상속받아 LIFO(Last-In-First-Out) 스택을 구현한 클래스입니다.
    - 특징: 기본적인 스택 연산(push, pop 등)을 제공

## Set
1. HashSet
    - 설명: 해시 테이블에 저장된 요소를 가진 집합입니다.
    - 특징: 중복 요소를 허용하지 않으며, 순서가 없음

2. LinkedHashSet
    - 설명: 해시 테이블과 연결 리스트를 결합한 집합입니다.
    - 특징: 요소의 삽입 순서를 유지

3. TreeSet
    - 설명: 이진 검색 트리를 기반으로 구현된 집합입니다.
    - 특징: 요소가 정렬된 순서로 저장

## Map
1. HashMap
    - 설명: 해시 테이블을 사용하는 맵입니다.
    - 특징: 키-값 쌍으로 이루어진 요소를 저장하며, 순서가 없음
2. LinkedHashMap
    - 설명: 해시 테이블과 연결 리스트를 결합한 맵입니다.
    - 특징: 요소의 삽입 순서를 유지
3. TreeMap
    - 설명: 이진 검색 트리를 기반으로 구현된 맵입니다.
    - 특징: 키가 정렬된 순서로 저장
4. Hashtable
    - 설명: 동기화된 해시 맵입니다.
    - 특징: 멀티스레드 환경에서 안전하지만, 성능이 떨어짐

## Queue
1. PriorityQueue
    - 설명: 우선순위가 있는 요소를 처리하는 큐입니다.
    - 특징: 요소가 우선순위에 따라 처리
2. Deque
    - 설명: 양쪽 끝에서 삽입과 삭제가 가능한 큐입니다.
    - 특징: 스택과 큐의 기능을 모두 가짐
3. ArrayDeque
    - 설명: 배열을 기반으로 한 Deque입니다.
    - 특징: 크기가 가변적이며, 성능이 우수

## Iterator
1. Iterator 인터페이스
    - 설명: 컬렉션 요소를 순회하기 위한 인터페이스입니다.
    - 메서드: hasNext(), next(), remove()
2. ListIterator 인터페이스
    - 설명: 리스트의 요소를 양방향으로 순회하기 위한 인터페이스입니다.
    - 메서드: hasNext(), next(), hasPrevious(), previous(), add(), set()

## 컬렉션의 동기화
 - Collections.synchronizedList: 기존 컬렉션을 동기화하여 멀티스레드 환경에서 안전하게 사용할 수 있습니다.
 ```java
    List<String> list = Collections.synchronizedList(new ArrayList<>());
    synchronized (list) {
        for (String item : list) {
            // 안전하게 접근
        }
    }
 ```

 - ConcurrentHashMap: 고성능의 동시성 맵으로, 세그먼트 락을 사용하여 높은 동시성을 제공합니다.
 - CopyOnWriteArrayList: 읽기 작업이 많은 경우에 유용한 동시성 리스트로, 쓰기 작업 시 전체 배열을 복사합니다.
 ```java
    ConcurrentMap<String, String> map = new ConcurrentHashMap<>();
    map.put("key", "value");

    CopyOnWriteArrayList<String> cowList = new CopyOnWriteArrayList<>();
    cowList.add("item");
 ```

## 성능최적화
- ArrayList vs. LinkedList: 삽입/삭제는 LinkedList가 유리하지만, 랜덤 액세스는 ArrayList가 유리합니다.
- HashMap vs. TreeMap: 검색과 삽입 속도는 HashMap이 유리하지만, 정렬된 데이터를 필요로 하면 TreeMap을 사용합니다.

## 기술면접
### Q: 자바 컬렉션 프레임워크란 무엇인가요?
### A: 자바 컬렉션 프레임워크는 여러 개의 객체를 저장하고 관리하는 표준화된 방법을 제공하는 클래스와 인터페이스의 집합입니다. 주요 인터페이스로는 Collection, List, Set, Map, Queue 등이 있습니다.

### Q: ArrayList와 LinkedList의 차이점은 무엇인가요?
### A
 - ArrayList: 동적 배열로 구현되어 있으며, 랜덤 액세스가 빠르지만, 중간에 요소를 삽입하거나 삭제할 때 성능이 떨어질 수 있습니다.
 - LinkedList: 이중 연결 리스트로 구현되어 있으며, 삽입과 삭제가 빠르지만, 랜덤 액세스 성능이 떨어집니다.

### Q: ArrayList와 Vector의 차이점은 무엇인가요?
### A: ArrayList는 비동기화된 컬렉션으로 단일 스레드 환경에서 빠릅니다. 반면 Vector는 동기화된 컬렉션으로 멀티스레드 환경에서 안전하지만 성능이 떨어집니다.

### Q: HashSet과 TreeSet의 차이점은 무엇인가요?
### A:
 - HashSet: 해시 테이블을 사용하여 요소를 저장하며, 순서가 없습니다. 검색과 삽입이 빠릅니다.
 - TreeSet: 이진 검색 트리를 사용하여 요소를 저장하며, 요소가 정렬된 순서로 저장됩니다. 검색과 삽입 속도가 HashSet보다 느립니다.

### Q: HashMap과 Hashtable의 차이점은 무엇인가요?
### A:
 - HashMap: 비동기화된 맵으로, 단일 스레드 환경에서 빠릅니다. null 키와 null 값을 허용합니다.
 - Hashtable: 동기화된 맵으로, 멀티스레드 환경에서 안전하지만 성능이 떨어집니다. null 키와 null 값을 허용하지 않습니다.

### Q: HashMap과 TreeMap의 차이점은 무엇인가요?
### A:
 - HashMap: 해시 테이블을 사용하여 요소를 저장하며, 순서가 없습니다. 검색과 삽입이 빠릅니다.
 - TreeMap: 이진 검색 트리를 사용하여 요소를 저장하며, 요소가 정렬된 순서로 저장됩니다. 검색과 삽입 속도가 HashMap보다 느립니다.

### Q: PriorityQueue와 ArrayDeque의 차이점은 무엇인가요?
### A:
 - PriorityQueue: 우선순위가 있는 큐로, 요소가 우선순위에 따라 처리됩니다.
 - ArrayDeque: 양쪽 끝에서 삽입과 삭제가 가능한 큐로, 스택과 큐의 기능을 모두 제공합니다.

### Q: Iterator와 ListIterator의 차이점은 무엇인가요?
### A:
 - Iterator: 컬렉션의 요소를 단방향으로 순회할 수 있는 인터페이스입니다.
 - ListIterator: Iterator를 확장한 인터페이스로, 양방향 순회가 가능하며, 요소를 추가, 수정, 삭제할 수 있습니다.

### Q: Stream API란 무엇인가요?
### A: Stream API는 자바 8에서 도입된 기능으로, 컬렉션의 요소를 함수형 프로그래밍 방식으로 처리할 수 있게 해줍니다. 스트림을 통해 데이터를 필터링, 변환, 수집할 수 있습니다.

### Q: Stream과 Parallel Stream의 차이점은 무엇인가요?
### A:
 - Stream: 단일 스레드에서 순차적으로 처리됩니다.
 - Parallel Stream: 여러 스레드를 사용하여 병렬로 처리되며, 대용량 데이터를 처리할 때 성능이 향상될 수 있습니다.

### Q: 동기화된 컬렉션과 동시성 컬렉션의 차이점은 무엇인가요?
### A:
 - 동기화된 컬렉션: Collections.synchronizedList와 같이 동기화를 제공하여 멀티스레드 환경에서 안전하게 사용할 수 있습니다.
 - 동시성 컬렉션: ConcurrentHashMap과 같이 높은 동시성을 제공하는 컬렉션으로, 성능이 향상됩니다.

### Q: 커스텀 컬렉션을 구현할 때 어떤 클래스를 상속받아야 하나요?
### A: 커스텀 컬렉션을 구현할 때는 AbstractList, AbstractSet, AbstractMap 등의 추상 클래스를 상속받아 필요한 메서드를 구현할 수 있습니다.

### Q: 컬렉션의 성능 최적화를 위해 어떤 점을 고려해야 하나요?
### A: 컬렉션의 성능 최적화를 위해 초기 용량 설정, 메모리 사용 패턴 분석, 불필요한 요소 제거 등을 고려해야 합니다. 또한, 특정 상황에 맞는 적절한 컬렉션을 선택하는 것이 중요합니다.

