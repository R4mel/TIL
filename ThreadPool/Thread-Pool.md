# Thread-Pool

**학습 목표**

- 멀티스레딩 이해 및 활용
    - Thread Pool: 스레드 풀의 개념과 필요성 이해
    - Thread Management: 스레드 생성, 실행, 종료 관리
    - Synchronization: 동기화 메커니즘 이해 및 활용
- 생산자-소비자 패턴 이해
    - Producer-Consumer Pattern: 생산자와 소비자 간의 작업 분배 및 동기화 방법 이해
    - BlockingQueue 사용: 생산자-소비자 패턴 구현 시 BlockingQueue의 역할과 사용법 학습

# **Collections.synchronizedList()**

- 자바에서 제공하는 유틸리티 메서드로, 주어진 리스트를 동기화된(스레드로부터 안전한) 리스트로 변환합니다. 이 메서드를 사용하면 여러 스레드가 동시에 리스트에 접근할 때 발생할 수 있는 문제를 방지할 수 있습니다.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class SynchronizedListExample {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("Apple");
        list.add("Banana");
        list.add("Cherry");

        // 동기화된 리스트 생성
        List<String> synchronizedList = Collections.synchronizedList(list);

        // 동기화된 리스트 사용
        synchronized (synchronizedList) {
            for (String item : synchronizedList) {
                System.out.println(item);
            }
        }
    }
}
```

in Collections.java

```java
public static <T> List<T> synchronizedList(List<T> list) {
        return (list instanceof RandomAccess ?
                new SynchronizedRandomAccessList<>(list) :
                new SynchronizedList<>(list));
}
```

What is RandomAccess?

![image.png](Thread-Pool%2025cdbeb48cf580f5b329d1738f5b7851/image.png)

RandomAccess interface

- 마커 인터페이스(Marker Interface)
    - 아무 메서드도 없고, “이 클래스가 특정 성격을 가졌다”는 표식 역할만 한다.
- 목적: List 구현체가 빠른 임의 접근(Random Access)을 지원하는지 표시하기 위함

필요한 이유?

List 계열은 두 가지 성격으로 나뉜다.

1. Random Access 지원(ArrayList)
    1. list.get(index) → 보통 O(1) (상수 시간)
2. Sequential Access 지원(LinkedList)
    1. list.get(index) → 보통 O(n) (순차 탐색 필요)

문제점: 정렬, 검색과 같은 제네릭 알고리즘이 List 타입으로만 작성되면, ArrayList 같은 랜덤 접근 리스트에는 잘 맞는 알고리즘이 LinkedList에 적용되면 성능이 급격히 나빠질 수 있음

그래서 JDK는 list instanceof RandomAccess 로 체크해서

- 빠른 임의 접근 지원이면 for(int i=0, n=list.size(); i<n; i++) list.get(i);로 사용
- 순차 접근만 빠르면 Iterator 기반 순회 방식 사용

# BlockingQueue

BlockingQueue는 Java의 `java.util.concurrent` 패키지에 포함된 인터페이스로, **생산자-소비자 패턴**에서 주로 사용됩니다. 이 큐는 요소를 추가하거나 제거할 때, 큐가 비어 있거나 가득 찬 경우에 자동으로 대기 상태에 들어가게 됩니다. 이를 통해 **동기화 문제**를 해결하고, **스레드 간의 안전한 통신**을 보장합니다.

**주요 메서드**

- `put(E e)`: 큐가 가득 찬 경우, 공간이 생길 때까지 대기 후 요소를 추가합니다.
- `take()`: 큐가 비어 있는 경우, 요소가 추가될 때까지 대기 후 요소를 제거합니다.
- `offer(E e, long timeout, TimeUnit unit)`: 지정된 시간 동안 대기 후 요소를 추가합니다.
- `poll(long timeout, TimeUnit unit)`: 지정된 시간 동안 대기 후 요소를 제거합니다.

```java
import java.util.concurrent.*;

public class BlockingQueueExample {
    public static void main(String[] args) throws InterruptedException {
        BlockingQueue<Integer> queue = new ArrayBlockingQueue<>(10);

        // 생산자 스레드
        Thread producer = new Thread(() -> {
            try {
                for (int i = 0; i < 20; i++) {
                    queue.put(i);
                    System.out.println("Produced: " + i);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        // 소비자 스레드
        Thread consumer = new Thread(() -> {
            try {
                for (int i = 0; i < 20; i++) {
                    Integer item = queue.take();
                    System.out.println("Consumed: " + item);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        producer.start();
        consumer.start();

        producer.join();
        consumer.join();
    }
}
```

BlockingQueue는 멀티스레딩 환경에서 안전하고 효율적인 큐를 제공하여, 생산자-소비자 패턴을 구현하는 데 매우 유용하다.

BlockingQueue의 구현체

1. ArrayBlockingQueue: 고정 크기의 배열을 기반으로 한 구현, 크기가 일단 설정되면 변경할 수 없고, 구현 시 크기를 지정해 주어야 한다.
2. LinkedBlockingQueue: 연결 노드를 기반으로 한 구현, 선택적으로 최대 크기를 설정할 수 있다.
3. PriorityBlockingQueue: 요소를 우선순위에 따라 저장하는 구현
4. SynchronousQueue: 단 하나의 항목만 저장할 수 있는 블로킹 큐, 이 큐에 항목을 넣으면 다른 스레드가 그 항목을 꺼낼 때까지 현재 스레드는 대기(블록)한다.

# 생산자-소비자 패턴

생산자-소비자 패턴은 **멀티스레드 환경에서 여러 스레드가 공유 자원에 접근할 때 발생하는 동기화 문제를 해결하기 위한** 디자인 패턴이다.

생산자-소비자 패턴은 멀티스레드 환경에서 **생산자(Producer) 스레드가 데이터를 생성하고, 소비자(Consumer) 스레드가 해당 데이터를 소비**하도록 구성되고, 효율적으로 자원을 관리할 수 있도록 한다.

![image.png](Thread-Pool%2025cdbeb48cf580f5b329d1738f5b7851/image%201.png)

위 그림처럼 작업을 생성하는 **생산자 스레드**와 Queue로 설계된 **공유자원**, 그리고 작업을 수행하는 **소비자 스레드**로 구성되어 있는것이 일반적인 생산자-소비자 패턴의 구조이다.

생산자 소비자 패턴의 구성 요소

1. 생산자(Producer): 데이터를 생성하고 공유자원에 저장하는 역할을 수행한다. 생산자는 새로운 데이터를 생성하여 공유자원에 추가하거나 기존의 데이터를 갱신한다.
2. 소비자(Consumer): 공유 자원에서 데이터를 가져와서 처리하는 역할을 수행한다.
3. 공유 자원(Buffer 또는 Queue): 생산자와 소비자 간의 데이터 교환을 위한 공유 자원이다. 일반적으로 버퍼(Buffer)나 큐(Queue)로 구현된다.
4. 동기화 메커니즘: 생산자와 소비자 간의 동기화를 이루기 위한 메커니즘이 존재한다. 생산자측은 공유자원이 가득차있는지 확인하고 데이터를 추가하기 위해 대기해야 하고, 소비자측은 공유자원이 비어 있는지 확인하고 데이터를 가져오기 위해 대기하는 등의 동작을 수행한다. 일반적으로 mutex, semaphore, condition vaiable 등이 있다.
5. mutex: 상호 배제를 통해 한번에 한 스레드만 공유자원(critical section)에 접근 가능하도록 함
6. semaphore 공유 자원에 접근할 수 있는 스레드의 수를 제어
7. conditional variable: 특정 조건을 만족할 때까지 스레드를 대기시킴

생산자 소비자 패턴을 사용하는 이유

1. 자원관리: 여러 스레드가 동시에 공유 자원에 접근하는 상황에서 자원을 효율적으로 관리할 수 있다. 즉, 공유 자원에 대한 동시 접근 문제를 효과적으로 해결한다.
2. 동기화: 멀티 스레드 환경에서 생산자와 소비자가 공유 데이터에 접근할 때 동기화 메커니즘을 사용하면, 데이터의 일관성과 안정성을 보장할 수 있다. 동기화 덕분에 생산자가 큐에 작업을 추가하면 소비자는 안전하게 이를 가져올 수 있으며, 소비자가 데이터를 꺼내 처리할 때는 다른 소비자가 동일한 데이터를 중복해서 처리하지 않도록 보장된다.
3. 효율성: 생산자와 소비자가 독립적으로 동작하면서 작업을 병렬로 처리할 수 있어 시스템 자원 활용도를 높이고 성능을 향상시킬 수 있다.
4. 작업 속도 조절 가능: 버퍼 크기나 생산, 소비자의 속도 및 개수르 조절하여 시스템 처리 속도를 제어할 수 있으며, 이를 통해 과부하를 방지하고 안정성과 효율성을 최적화할 수 있다.

생산자 소비자 패턴 적용 상황

스레드 풀(Thread Pool): 생산자는 작업을 생성하고 버퍼, 큐에 추가하며, 소비자는 버퍼, 큐에서 작업을 가져와 처리한다. 이를 통해 작업을 병렬로 처리할 수 있어 시스템의 처리량을 향상시킬 수 있다.

스레드 풀의 필요성

서버는 동시에 여러 사용자가 접속할 수 있다.

자바에서는 스레드를 운영 체제의 자원으로 사용한다. 우리가 스레드를 계속해서 만들면, 운영체제의 자원이 빨리 소진될 수 있다.

서버는 동시 접속자가 많아지면 스레드가 무한대로 생성되면서 서버가 다운될 위험이 있다.

애플리케이션 프로세스에서 사용되고 있는 스레드의 개수를 관리하기 위해서 스레드 풀을 도입할 수 있다.

![image.png](Thread-Pool%2025cdbeb48cf580f5b329d1738f5b7851/image%202.png)

스레드 풀은 미리 일정 개수의 스레드를 생성하여 관리하는 기법이다.

이렇게 생성된 스레드들은 작업을 할당받기 위해 대기 상태에 있게 되는데, 작업이 발생하면 대기 중인 스레드 중 하나를 선택하여 작업을 수행한다. 작업이 완료되면 해당 스레드는 다시 대기 상태로 돌아가고, 새로운 작업을 할당받을 준비를 한다.

스레드 풀을 사용하면 스레드 생성 및 삭제에 따른 오버헤드를 줄일 수 있으며, 특정 시점에 동시에 처리할 수 있는 작업의 개수를 제한할 수 있다.

**장점**

- Thread 생성과 삭제에 따른 시간과 resource를 절약할 수 있다.

**Thread Pool 사용 시 주의할 점**

- Deadlock
    - Multi-thread와 관련된 모든 프로그램에서 교착 상태가 발생할 수 있다.
    - 작업 간 데이터 교환이 필요한 경우, 실행 작업과 대기 작업의 교착 상태가 발생할 수 있다.
- Thread Leakage
    - 비정상 종료에 의한 실행 종료
- Resource Thrashing
    - 지나치게 큰 thread pool을 사용할 경우, thread 간 context 전환 시 느려지는 문제가 발생할 수 있다.

```java
import lombok.extern.slf4j.Slf4j;

import java.util.ArrayList;
import java.util.List;
import java.util.Objects;

@Slf4j
public class ThreadPool {

    //thread-pool 기본 size
    private static final int DEFAULT_POOL_SIZE = 10;

    //thread pool size
    private final int poolSize;

    //thread에 의해서 실행될 Runnable 구현체
    private final Runnable runnable;

    //thread-pool에 생성된 thread list
    private final List<Thread> threadList;

    public ThreadPool(Runnable runnable){
        //default 생성자 구현, poolSize = DEFAULT_POOL_SIZE를 사용합니다.
        this(DEFAULT_POOL_SIZE,runnable);
    }

    public ThreadPool(int poolSize, Runnable runnable) {
        //thread pool size < 0이면 IllegalArgumentException이 발생합니다.
        if(poolSize<0){
            throw new IllegalArgumentException();
        }

        //runnable == null이면 IllegalArgumentException이 발생합니다.
        if(Objects.isNull(runnable)){
            throw new IllegalArgumentException();
        }

        //runnable이 Runnable의 구현체가 아니라면 IllegalArgumentException이 발생합니다.
        if(!(runnable instanceof Runnable)){
            throw new IllegalArgumentException();
        }

        //poolSize, runnable, threadList 초기화
        this.poolSize = poolSize;
        this.runnable = runnable;
        threadList = new ArrayList<>(poolSize);

        //thread를 미리 poolSize만큼 생성합니다.
        createThread();
    }

    private void createThread(){
        synchronized (this) {
            for (int i = 0; i < poolSize; i++) {
                threadList.add(new Thread(runnable));
            }
        }
    }

    public synchronized void start(){
        /*생성된 thread를 시작합니다.*/
        for(int i=0; i<poolSize; i++){
            Thread thread = threadList.get(i);
            thread.start();
        }
    }

    public synchronized void stop(){
        /*interrupt()를 실행해서 thread를 종료합니다.
            - thread가 종료되는 과정에서 동기화되어야 합니다.
            - 우선 모든 thread interrupt 호출
         */

        for(Thread thread : threadList){
            if(Objects.nonNull(thread) && thread.isAlive() ){
                thread.interrupt();
            }
        }

        //join()을 이용해서 모든 thread가 종료될 때까지 대기 상태로 만듭니다.
        for(Thread thread : threadList){
            try {
                //join method는 해당 thread가 종료될 때까지 현재 thread를 대기상태로 만듭니다.
                thread.join();
            } catch (InterruptedException e) {
                //join method는 InterruptedException을 발생시킬 수 있습니다.
                Thread.currentThread().interrupt();
                log.debug("{}",e.getMessage(),e);
            }
        }
    }
}
```

ThreadLocal

https://mangkyu.tistory.com/333