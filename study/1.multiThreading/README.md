# 멀티스레딩(Multi-threading)

## 멀티스레딩의 기본개념

1. 멀티스레딩이란
- 하나의 프로세스 내에서 두 개 이상의 스레드를 생성하고 관리하여 동시에 여러 작업을 수행하는 기술
- 여러 작업을 병렬로 처리할 수 있어 성능을 향상시키고 응답성을 높일 수 있습니다.
- Processs : 실행 중인 프로그램을 의미
- Thread : 프로세스 내에서 실행되는 하나의 실행 단위

2. 멀티스레딩의 필요성
- 성능향상 : 여러 작업 동시 처리
- 응답성 개선: 사용자 인터페이스(UI) 스레드가 블록되지 않도록 하여, 사용자 경험을 개선합니다.
- 자원 효율성: CPU의 사용률을 극대화하고, I/O 대기 시간을 활용하여 다른 작업을 수행

3. 멀티스레딩과 멀티프로세싱의 차이
- 멀티스레딩 
    - 하나의 프로세스 내에서 여러 스레드를 생성하여 병렬로 작업을 수행
    - 동일한 메모리 공간을 공유하기 때문에 문맥 전환이 빠름
    - 하지만 동기화 문제가 발생 가능

- 멀티프로세싱(Multi-processing)
    - 여러 프로세스를 생성하여 병렬로 작업을 수행
    - 독립적인 메모리 공간가짐
    - 동기화 문제가 적지만 문맥 전환이 상대적으로 느림

4. 스레드 생성방법
- Thread 클래스 상속
    - 장점 : 간결함, 구현쉬움
    - 단점 : 단일 상속 제한 -> 재사용성, 확장성 감소
```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Thread is running...");
    }

    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        t1.start(); // 스레드 시작
    }
}
```

- Runnable 인터페이스 구현
    - 장점 : 다중상속가능, 유연성 / 재사용성
    - 단점 : 구현복잡
```java
class MyRunnable implements Runnable {
    public void run() {
        System.out.println("Thread is running...");
    }

    public static void main(String[] args) {
        MyRunnable myRunnable = new MyRunnable();
        Thread t1 = new Thread(myRunnable);
        t1.start(); // 스레드 시작
    }
}
```

5. 스레드의 생명 주기
- New: 스레드가 생성되었지만 아직 start() 메서드가 호출되지 않은 상태.
- Runnable: 실행 준비가 된 상태로, 실제로 실행 중이거나 실행 대기 중인 상태.
- Blocked: 다른 스레드의 자원을 기다리며 블록된 상태.
- Waiting: 다른 스레드가 특정 조건을 만족시킬 때까지 기다리는 상태.
- Timed Waiting: 지정된 시간 동안 기다리는 상태.
- Terminated: 스레드의 실행이 완료된 상태.

## 스레드 주요 함수
- start(): 새로운 스레드를 시작하여 run() 메서드를 호출합니다.
- run(): 스레드가 실행할 작업을 정의합니다. start() 메서드에 의해 호출됩니다.
```java
class MyRunnable implements Runnable {
    public void run() { // 스레드가 실행할 작업
        System.out.println("Thread is running...");
    }

    public static void main(String[] args) {
        MyRunnable myRunnable = new MyRunnable();
        Thread t1 = new Thread(myRunnable);
        t1.start(); // 스레드 시작 -> run() 호출
    }
}
```
- sleep(long millis): 스레드를 지정된 시간 동안 일시 중지합니다.
- join(): 다른 스레드가 종료될 때까지 현재 스레드를 대기 상태로 만듭니다.
```java
class MyRunnable implements Runnable {
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println("Thread is running: " + i);
            try {
                Thread.sleep(1000); // 1초 대기
            } catch (InterruptedException e) {
                System.out.println(e);
            }
        }
    }

    public static void main(String[] args) {
        MyRunnable myRunnable = new MyRunnable();
        Thread t1 = new Thread(myRunnable);
        t1.start(); // 스레드 시작

        try {
            t1.join(); // t1스레드가 종료될때까지 메인스레드는 대기
        } catch (InterruptedException e) {
            System.out.println(e);
        }

        System.out.println("Main thread finished.");
    }
}
```
- interrupt(): 스레드를 중단시킵니다. InterruptedException 예외를 발생시킵니다.
- isAlive(): 스레드가 살아 있는지 확인합니다.

## 스레드의 생명주기

1. 스레드 상태
    - New (새로 생성됨): 스레드 객체가 생성된 상태로, 아직 start() 메서드가 호출되지 않은 상태입니다.
    - Runnable (실행 가능): start() 메서드가 호출되어 실행 대기 중이거나, 실제로 실행 중인 상태입니다.
    - Blocked (블록됨): 스레드가 모니터 락을 기다리며 블록된 상태입니다.
    - Waiting (대기): 스레드가 다른 스레드가 특정 조건을 만족시킬 때까지 기다리는 상태입니다. wait(), join(), park() 메서드에 의해 대기 상태로 전이됩니다.
    - Timed Waiting (시간 지정 대기): 지정된 시간 동안 대기하는 상태입니다. sleep(long millis), wait(long timeout), join(long millis) 메서드에 의해 시간 지정 대기 상태로 전이됩니다.
    - Terminated (종료됨): 스레드가 실행을 완료한 상태입니다. run() 메서드가 종료되면 이 상태로 전이됩니다.

2. 상태전이
    1. New -> Runnable: start() 메서드를 호출하여 스레드를 시작합니다.
    2. Runnable -> Blocked: 스레드가 모니터 락을 기다리며 블록된 상태로 전이됩니다.
    3. Blocked -> Runnable: 모니터 락을 획득하여 실행 가능한 상태로 전이됩니다.
    4. Runnable -> Waiting: wait(), join(), park() 메서드 호출로 대기 상태로 전이됩니다.
    5. Waiting -> Runnable: notify(), notifyAll(), unpark() 메서드 호출로 실행 가능한 상태로 전이됩니다.
    6. Runnable -> Timed Waiting: sleep(long millis), wait(long timeout), join(long millis) 메서드 호출로 시간 지정 대기 상태로 전이됩니다.
    7. Timed Waiting -> Runnable: 지정된 시간이 경과하거나 notify(), notifyAll(), unpark() 메서드 호출로 실행 가능한 상태로 전이됩니다.
    8. Runnable -> Terminated: run() 메서드가 종료되면 스레드가 종료 상태로 전이됩니다.
    9. Blocked -> Terminated: 드물지만 블록된 상태에서 예외가 발생하여 종료 상태로 전이될 수 있습니다.
```java
class MyRunnable implements Runnable {
    public void run() {
        try {
            System.out.println("Thread is in Runnable state");
            Thread.sleep(2000); // 6.Runnable -> Timed Waiting
            synchronized(this) {
                wait(); // 4. Runnable -> Waiting
            }
        } catch (InterruptedException e) {
            System.out.println("Thread was interrupted: " + e);
        }
        System.out.println("Thread is running again and about to terminate");
    }

    public static void main(String[] args) {
        MyRunnable myRunnable = new MyRunnable();
        Thread t1 = new Thread(myRunnable);

        System.out.println("Thread is in New state");
        t1.start(); // 1.New -> Runnable

        try {
            Thread.sleep(1000); // 6.Runnable -> Timed Waiting
            synchronized(myRunnable) {
                myRunnable.notify(); // 5. Waiting -> Runnable
            }
            t1.join(); // 4. Runnable -> Waiting
        } catch (InterruptedException e) {
            System.out.println(e);
        }

        System.out.println("Main thread finished."); // 8. Runnable -> Terminated
    }
}

```

## 스레드 동기화
- 여러 스레드가 동시에 공유 자원에 접근할 때 발생할 수 있는 문제를 방지하기 위해 사용
- 동기화는 주로 synchronized 키워드를 사용

1. 동기화의 필요성
```java
class Counter {
    private int count = 0;
    public void increment() {
        count++;
    }
    public int getCount() {
        return count;
    }
}

class MyRunnable implements Runnable {
    private Counter counter;

    MyRunnable(Counter counter) {
        this.counter = counter;
    }

    public void run() {
        for (int i = 0; i < 1000; i++) {
            counter.increment();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        Thread t1 = new Thread(new MyRunnable(counter));
        Thread t2 = new Thread(new MyRunnable(counter));

        t1.start();
        t2.start(); // 둘다 count변수에 접근

        t1.join();
        t2.join();

        System.out.println("Final count: " + counter.getCount());
    }
}
```

2. synchronized 사용법
    1. 동기화 메서드
        - 해당 메서드에는 하나의 스레드만 접근 가능
        ```java
        class Counter {
            private int count = 0;

            public synchronized void increment() {
                count++; // 한번에 하나의 스레드만 접근 가능
            }

            public int getCount() {
                return count;
            }
        }
        ```

    2. 동기화 블록
        - 특정 코드 블록에 하나의 스레드만 접근가능
        - 메서드보다 동기화 범위 줄여 성능 향상 가능
        ```java
        class Counter {
            private int count = 0;

            public void increment() {
                synchronized(this) {
                    count++;
                }
            }

            public int getCount() {
                return count;
            }
        }
        ```

3. 동기화 문제 및 해결 방법
    - 데드락 : 두 개 이상의 스레드가 서로를 기다리며 영원히 대기 상태에 빠지는 문제
    ```java
    class Resource {
        public synchronized void method1(Resource other) {
            System.out.println(Thread.currentThread().getName() + ": method1");
            other.method2();
        }

        public synchronized void method2() {
            System.out.println(Thread.currentThread().getName() + ": method2");
        }
    }

    class MyRunnable implements Runnable {
        private Resource resource1;
        private Resource resource2;

        MyRunnable(Resource resource1, Resource resource2) {
            this.resource1 = resource1;
            this.resource2 = resource2;
        }

        public void run() {
            resource1.method1(resource2);
        }

        public static void main(String[] args) {
            Resource resource1 = new Resource();
            Resource resource2 = new Resource();

            Thread t1 = new Thread(new MyRunnable(resource1, resource2), "Thread 1");
            Thread t2 = new Thread(new MyRunnable(resource2, resource1), "Thread 2");

            t1.start(); // resource1.method1(resource2) -> resource2.method2
            t2.start(); // resource2.method1(resource1) -> resource1.method2
            // t1 : resource1 락 획득, resource2 호출 시도
            // t2 : resource2 락 획득, resource1 호출 시도
            // 서로가 서로의 자원을 획득하기 위해 기다림
        }
    }
    ```

4. 해결방법
    1. 순서강제 : 모든 스레드가 자원을 동일한 순서로 요청하도록 강제
        - 무조건 resource1.method1(resource2) -> resource2.method2 순서대로 하도록
    2. 타임아웃 사용 : 자원을 기다리는 시간을 제한하여, 타임아웃이 발생하면 자원 요청을 포기하고 다른 작업을 수행
    3. 데드락 회피 알고리즘

## 스레드간 통신
- wait(), notify(), notifyAll() 메서드를 사용하여 스레드 간의 통신을 구현

1. 메서드
    - wait(): 현재 스레드를 일시 정지 상태로 전환하고, 다른 스레드가 notify() 또는 notifyAll()을 호출할 때까지 기다립니다. 이 메서드는 반드시 synchronized 블록 내에서 호출되어야 합니다.
    - notify(): 일시 정지된 스레드 중 하나를 깨워 실행 대기 상태로 전환합니다. 이 메서드도 반드시 synchronized 블록 내에서 호출되어야 합니다.
    - notifyAll(): 일시 정지된 모든 스레드를 깨워 실행 대기 상태로 전환합니다. 이 메서드 역시 반드시 synchronized 블록 내에서 호출되어야 합니다.

2.  wait()와 notify() 예제
```java
class SharedResource {
    private int number = 0;
    private boolean available = false;

    public synchronized void produce(int value) {
        while (available) { 
            try {
                wait(); // 3. 대기
            } catch (InterruptedException e) {
                System.out.println(e);
            }
        }
        number = value; // 1. availble이 false라서 여기부터 실행 
        available = true;
        System.out.println("Produced: " + number);
        notify(); // 2. consumerThread깨움
    }

    public synchronized void consume() {
        while (!available) {
            try {
                wait();
            } catch (InterruptedException e) {
                System.out.println(e);
            }
        }
        System.out.println("Consumed: " + number); // 3. 출력
        available = false;
        notify(); // 4. produce깨움
    }
}

class Producer implements Runnable {
    private SharedResource resource;

    Producer(SharedResource resource) {
        this.resource = resource;
    }

    public void run() {
        for (int i = 1; i <= 5; i++) {
            resource.produce(i);
        }
    }
}

class Consumer implements Runnable {
    private SharedResource resource;

    Consumer(SharedResource resource) {
        this.resource = resource;
    }

    public void run() {
        for (int i = 1; i <= 5; i++) {
            resource.consume();
        }
    }
}

public class Main {
    public static void main(String[] args) {
        SharedResource resource = new SharedResource();
        Thread producerThread = new Thread(new Producer(resource));
        Thread consumerThread = new Thread(new Consumer(resource));

        producerThread.start();
        consumerThread.start();
    }
}
```

3. notifyAll() 예제
```java
class SharedResource {
    private int number = 0;
    private boolean available = false;

    public synchronized void produce(int value) {
        while (available) {
            try {
                wait();
            } catch (InterruptedException e) {
                System.out.println(e);
            }
        }
        number = value;
        available = true;
        System.out.println("Produced: " + number);
        notifyAll(); // consume 2개를 모두 깨움
    }

    public synchronized void consume() {
        while (!available) {
            try {
                wait();
            } catch (InterruptedException e) {
                System.out.println(e);
            }
        }
        System.out.println("Consumed: " + number); // consume 2개중 먼저 획득한 아이가 실행
        available = false;
        notifyAll(); // produce 깨움
    }
}

class Producer implements Runnable {
    private SharedResource resource;

    Producer(SharedResource resource) {
        this.resource = resource;
    }

    public void run() {
        for (int i = 1; i <= 5; i++) {
            resource.produce(i);
        }
    }
}

class Consumer implements Runnable {
    private SharedResource resource;

    Consumer(SharedResource resource) {
        this.resource = resource;
    }

    public void run() {
        for (int i = 1; i <= 5; i++) {
            resource.consume();
        }
    }
}

public class Main {
    public static void main(String[] args) {
        SharedResource resource = new SharedResource();
        Thread producerThread = new Thread(new Producer(resource));
        Thread consumerThread1 = new Thread(new Consumer(resource));
        Thread consumerThread2 = new Thread(new Consumer(resource));

        producerThread.start();
        consumerThread1.start();
        consumerThread2.start();
    }
}
```

## 스레드 풀
- 여러 개의 스레드를 미리 생성해 두고, 작업이 발생할 때마다 이를 재사용
- 생성하는 비용을 절감
- 스레드 수를 제한하여 시스템 리소스를 효율적으로 사용

1. 스레드 풀의 개념과 필요성
    - 미리 일정한 수 생성 / 스레드 할당 / 작업 종료 시 반환
    - 효율성 : 생성비용 절감
    - 자원 관리 : 스레드 수 제한으로 리소스 효율적 사용
    - 응답시간 개선 : 스레드 미리 생성해서 작업 들어오면 즉시 처리 가능

2. Java에서의 스레드 풀 사용 (Executors)
    - java.util.concurrent 패키지의 Executors 클래스를 사용
    - 스레드 풀 종류
        1. Fixed Thread Pool: 고정된 크기의 스레드 풀을 생성합니다.
        ```java
        class Task implements Runnable {
            private String name;

            Task(String name) {
                this.name = name;
            }

            public void run() {
                System.out.println(Thread.currentThread().getName() + " is executing task: " + name);
                try {
                    Thread.sleep(2000); // 작업이 수행되는 동안 대기
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }

        public class ThreadPoolExample {
            public static void main(String[] args) {
                // 고정된 크기의 스레드 풀 생성 (3개의 스레드)
                ExecutorService executorService = Executors.newFixedThreadPool(3);

                // 5개의 작업 제출
                for (int i = 1; i <= 5; i++) {
                    Task task = new Task("Task " + i);
                    executorService.execute(task);
                }

                // 스레드 풀 종료
                executorService.shutdown();
            }
        }
        ```
        2. Cached Thread Pool: 필요에 따라 새로운 스레드를 생성하며, 일정 시간 동안 사용되지 않은 스레드를 제거합니다.
        ```java
        public class CachedThreadPoolExample {
            public static void main(String[] args) {
                // 캐시된 스레드 풀 생성
                ExecutorService executorService = Executors.newCachedThreadPool();

                // 5개의 작업 제출
                for (int i = 1; i <= 5; i++) {
                    Task task = new Task("Task " + i);
                    executorService.execute(task);
                }

                // 스레드 풀 종료
                executorService.shutdown();
            }
        }
        ```
        3. Single Thread Executor: 단일 스레드로 작업을 처리합니다.
        4. Scheduled Thread Pool: 일정한 시간 간격으로 작업을 예약 실행할 수 있는 스레드 풀을 생성합니다.
        ```java
        public class ScheduledThreadPoolExample {
            public static void main(String[] args) {
                // 스케줄된 스레드 풀 생성
                ScheduledExecutorService scheduledExecutorService = Executors.newScheduledThreadPool(2);

                // 3초 후에 작업 실행
                scheduledExecutorService.schedule(new Task("Scheduled Task 1"), 3, TimeUnit.SECONDS);

                // 1초 후에 작업 실행, 이후 2초마다 반복 실행
                scheduledExecutorService.scheduleAtFixedRate(new Task("Scheduled Task 2"), 1, 2, TimeUnit.SECONDS);

                // 2초 후에 작업 실행, 이후 이전 작업 완료 후 3초마다 반복 실행
                scheduledExecutorService.scheduleWithFixedDelay(new Task("Scheduled Task 3"), 2, 3, TimeUnit.SECONDS);

                // 10초 후에 스레드 풀 종료
                scheduledExecutorService.schedule(() -> {
                    scheduledExecutorService.shutdown();
                }, 10, TimeUnit.SECONDS);
            }
        }
        ```

## 고급스레드제어
1. Lock과 ReentrantLock
    - 유연성 : 락을 얻고 해제하는 시점을 코드에서 명확히 제어(lock, unlock)
    - 명시적 락 해제
    - Condition(조건변수)를 사용해 스레드 간의 대기 및 알림 메커니즘을 구현
```java
class SharedResource {
    private int number = 0;
    private final Lock lock = new ReentrantLock();

    public void increment() {
        lock.lock(); // 락을 획득할 때까지 현재 스레드를 대기 상태로 변환
        try {
            number++; 
            System.out.println(Thread.currentThread().getName() + " incremented the number to: " + number);
        } finally {
            lock.unlock(); // 락을 해제
        }
    }

    public int getNumber() {
        return number;
    }
}

public class LockExample {
    public static void main(String[] args) {
        SharedResource resource = new SharedResource();
        Runnable task = () -> {
            for (int i = 0; i < 5; i++) {
                resource.increment();
            }
        };

        Thread t1 = new Thread(task, "Thread 1");
        Thread t2 = new Thread(task, "Thread 2");

        t1.start();
        t2.start();
    }
}
```

2. Condition(조건변수) 객체
    - await(), signal(), signalAll() 사용해서 조건에 따른 스레드 간 대기 , 알림 구현 가능
    - Lock과 함께 사용
```java
class SharedResource {
    private int number = 0;
    private final Lock lock = new ReentrantLock();
    private final Condition condition = lock.newCondition();
    private boolean available = false;

    public void produce(int value) throws InterruptedException {
        lock.lock();
        try {
            while (available) {
                condition.await(); //wait 대체, 조건을 기다림
            }
            number = value;
            available = true;
            System.out.println("Produced: " + number);
            condition.signalAll(); // notifyAll 대체, 조건을 만족
        } finally {
            lock.unlock(); 
        }
    }

    public void consume() throws InterruptedException {
        lock.lock();
        try {
            while (!available) {
                condition.await();
            }
            System.out.println("Consumed: " + number);
            available = false;
            condition.signalAll();
        } finally {
            lock.unlock();
        }
    }
}

public class ConditionExample {
    public static void main(String[] args) {
        SharedResource resource = new SharedResource();
        Runnable producerTask = () -> {
            try {
                for (int i = 1; i <= 5; i++) {
                    resource.produce(i);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        };

        Runnable consumerTask = () -> {
            try {
                for (int i = 1; i <= 5; i++) {
                    resource.consume();
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        };

        Thread producer = new Thread(producerTask, "Producer");
        Thread consumer = new Thread(consumerTask, "Consumer");

        producer.start();
        consumer.start();
    }
}
```

3. ReadWriteLock
    - 읽기/쓰기 작업을 구분하여 동시에 여러 스레드가 읽기 작업을 수행할 수 있게 하고, 쓰기 작업이 있을 때는 단독으로 수행할 수 있게 합니다.
```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

class SharedData {
    private int number = 0;
    private final ReadWriteLock readWriteLock = new ReentrantReadWriteLock();
    private final Lock readLock = readWriteLock.readLock();
    private final Lock writeLock = readWriteLock.writeLock();

    public void write(int value) {
        writeLock.lock(); // 쓰기 락이 있을 때는 다른 읽기/쓰기 락을 획득할 수 없습니다.
        try {
            number = value;
            System.out.println(Thread.currentThread().getName() + " wrote: " + number);
        } finally {
            writeLock.unlock();
        }
    }

    public void read() {
        readLock.lock(); // 여러 스레드가 동시에 읽기 작업을 수행, write는 멈춤
        try {
            System.out.println(Thread.currentThread().getName() + " read: " + number);
        } finally {
            readLock.unlock();
        }
    }
}

public class ReadWriteLockExample {
    public static void main(String[] args) {
        SharedData sharedData = new SharedData();
        Runnable writeTask = () -> {
            for (int i = 1; i <= 5; i++) {
                sharedData.write(i);
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Runnable readTask = () -> {
            for (int i = 1; i <= 5; i++) {
                sharedData.read();
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Thread writer = new Thread(writeTask, "Writer");
        Thread reader1 = new Thread(readTask, "Reader 1");
        Thread reader2 = new Thread(readTask, "Reader 2");

        writer.start();
        reader1.start();
        reader2.start();
    }
}
```
```yaml
Writer wrote: 1
Reader 1 read: 1
Reader 2 read: 1
Writer wrote: 2
Reader 1 read: 2
Reader 2 read: 2
Writer wrote: 3
Reader 1 read: 3
Reader 2 read: 3
Writer wrote: 4
Reader 1 read: 4
Reader 2 read: 4
Writer wrote: 5
Reader 1 read: 5
Reader 2 read: 5
```

## 기술면접
### Q1: 멀티스레딩이란 무엇인가요?

### A1: 멀티스레딩은 하나의 프로세스 내에서 여러 스레드를 사용하여 동시에 여러 작업을 수행할 수 있도록 하는 기술입니다. 이는 CPU의 활용도를 높이고, 프로그램의 성능을 향상시키며, 응답 시간을 줄이는 데 도움이 됩니다. 자바에서는 Thread 클래스나 Runnable 인터페이스를 사용하여 멀티스레딩을 구현할 수 있습니다.

### Q2: Java에서 스레드를 생성하는 두 가지 방법은 무엇인가요?

### A2: Java에서 스레드를 생성하는 두 가지 주요 방법은 Thread 클래스를 상속받는 것과 Runnable 인터페이스를 구현하는 것입니다.

### Q3: 스레드의 생명 주기에 대해 설명해 주세요.

### A3: 스레드는 다음과 같은 생명 주기 상태를 가집니다:
- New (신규 상태): 스레드 객체가 생성되었지만 아직 start() 메서드가 호출되지 않은 상태입니다.
- Runnable (실행 가능 상태): start() 메서드가 호출된 후 실행 준비가 된 상태입니다. 실행 중이거나 실행 대기 중일 수 있습니다.
- Blocked (차단 상태): 스레드가 실행 중 다른 스레드에 의해 차단된 상태입니다. 예를 들어, 동기화된 블록에 진입하려고 할 때 다른 스레드가 이미 해당 블록을 점유하고 있을 경우 차단됩니다.
- Waiting (대기 상태): 스레드가 다른 스레드의 작업이 완료되기를 기다리는 상태입니다. wait(), join() 또는 sleep() 메서드에 의해 진입할 수 있습니다.
- Timed Waiting (시간 지정 대기 상태): 지정된 시간 동안 대기하는 상태입니다. sleep(milliseconds), wait(milliseconds) 메서드에 의해 진입할 수 있습니다.
- Terminated (종료 상태): 스레드가 실행을 마치고 종료된 상태입니다.

### Q4: 스레드 동기화가 필요한 이유는 무엇인가요?

### A4: 스레드 동기화는 여러 스레드가 공유 자원에 동시에 접근할 때 발생할 수 있는 데이터 불일치를 방지하기 위해 필요합니다. 동기화는 한 번에 하나의 스레드만 공유 자원에 접근할 수 있도록 하여 데이터의 일관성을 유지합니다. 자바에서는 synchronized 키워드를 사용하여 동기화를 구현할 수 있습니다.

### Q5: 스레드 간의 통신 방법에 대해 설명해 주세요.

### A5: 스레드 간의 통신은 wait(), notify(), notifyAll() 메서드를 사용하여 이루어집니다. 이러한 메서드는 모두 Object 클래스에서 제공되며, 반드시 synchronized 블록 내에서 호출되어야 합니다.

### Q6: 스레드 풀이란 무엇이며, 왜 사용하나요?

### A6: 스레드 풀은 여러 개의 스레드를 미리 생성해 두고, 작업이 발생할 때마다 이를 재사용하는 구조입니다. 이를 통해 스레드를 생성하는 비용을 줄이고, 시스템 리소스를 효율적으로 사용할 수 있습니다. 자바에서는 Executors 클래스를 사용하여 다양한 유형의 스레드 풀을 생성할 수 있습니다.

### Q7: ReentrantLock과 synchronized 키워드의 차이점은 무엇인가요?

### A7: ReentrantLock은 synchronized 키워드에 비해 더 많은 기능과 유연성을 제공합니다.
- ReentrantLock은 명시적으로 락을 획득하고 해제해야 합니다 (lock() 및 unlock()).
- ReentrantLock은 락의 시도 (tryLock())와 같은 추가 메서드를 제공합니다.
- ReentrantLock은 공정성 정책을 설정할 수 있습니다.
- ReentrantLock은 하나 이상의 Condition 객체를 생성하여 더 세밀한 대기/알림 메커니즘을 구현할 수 있습니다 (newCondition()).