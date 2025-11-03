# 📦 BlockingQueue란?
BlockingQueue는 멀티스레드 환경에서 생산자-소비자 문제를 해결하기 위한 큐 인터페이스입니다.  
스레드가 데이터를 추가하거나 제거할 때 큐의 상태에 따라 자동으로 대기하거나 실패 처리를 지원합니다.

## 소스 코드

```java
public class BoundedQueueV6_2 implements BoundedQueue {
    private BlockingQueue<String> queue;
    
    public BoundedQueueV6_2(int max) {
        queue = new ArrayBlockingQueue<>(max);
    }
    
    public void put(String data) {
        boolean result = queue.offer(data);
        log("저장 시도 결과 = " + result);
    }
    
    public String take() {
        return queue.poll();
    }
    
    @Override
    public String toString() {
        return queue.toString();
    }
}
```

```java
public class BoundedQueueV6_3 implements BoundedQueue {
    private BlockingQueue<String> queue;
    public BoundedQueueV6_3(int max) {
        queue = new ArrayBlockingQueue<>(max);
    }

    public void put(String data) {
        try {
            // 대기 시간 설정 가능
            boolean result = queue.offer(data, 1, TimeUnit.NANOSECONDS);
            log("저장 시도 결과 = " + result);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }

    public String take() {
    try {
        // 대기 시간 설정 가능
        return queue.poll(2, TimeUnit.SECONDS);
    } catch (InterruptedException e) {
        throw new RuntimeException(e);
    }
    }

    @Override
    public String toString() {
        return queue.toString();
    }
}
```

```java
public class BoundedQueueV6_4 implements BoundedQueue {
    
    private BlockingQueue<String> queue;
    
    public BoundedQueueV6_4(int max) {
        queue = new ArrayBlockingQueue<>(max);
    }
    
    public void put(String data) {
        queue.add(data); // java.lang.IllegalStateException: Queue full
    }
    
    public String take() {
        return queue.remove(); // java.util.NoSuchElementException
    }
    
    @Override
    public String toString() {
        return queue.toString();
    }
}
```

```java
public static void main(String[] args) {
    //1. BoundedQueue 선택
    BlockingQueue<String> queue = new ArrayBlockingQueue<>(2);
    //2. 생산자, 소비자 실행 순서 선택, 반드시 하나만 선택!
    producerFirst(queue); //생산자 먼저 실행
    //consumerFirst(queue); // 소비자 먼저 실행
}
```

## 🔁 실무에서 필요한 기능

| 상황                         | 처리 방식                          | 메서드 예시                        |
|------------------------------|------------------------------------|------------------------------------|
| 큐가 가득 찼을 때            | 예외 발생                          | add(e)                             |
| 큐가 가득 찼을 때            | 즉시 실패                          | offer(e)                           |
| 큐가 가득 찼을 때            | 무한 대기                          | put(e)                             |
| 큐가 가득 찼을 때            | 시간 제한 대기                     | offer(e, time, unit)               |
| 큐가 비어 있을 때            | 예외 발생                          | remove(), element()                |
| 큐가 비어 있을 때            | 즉시 실패                          | poll(), peek()                     |
| 큐가 비어 있을 때            | 무한 대기                          | take()                             |
| 큐가 비어 있을 때            | 시간 제한 대기                     | poll(time, unit)                   |

## 🧠 핵심 요약
- 실무에서는 무한 대기보다 실패 처리 또는 시간 제한 대기가 더 적절한 경우가 많습니다
- offer(e, time)와 poll(time)은 응답성을 유지하면서도 대기 전략을 적용할 수 있는 핵심 메서드
- 모든 대기 메서드는 인터럽트 처리 가능 → 스레드가 중단 요청을 받을 수 있음


## 🧪 BlockingQueue 기능별 구현 예제
### ✅ 1. 즉시 반환 (offer, poll)
```java
public class BoundedQueueV6_2 implements BoundedQueue {
    
    private BlockingQueue<String> queue;
    
    public BoundedQueueV6_2(int max) {
        queue = new ArrayBlockingQueue<>(max);
    }
    
    public void put(String data) {
        boolean result = queue.offer(data); // 즉시 시도
        log("저장 시도 결과 = " + result);
    }
    
    public String take() {
        return queue.poll(); // 즉시 시도
    }
}
```
- 큐가 가득 차면 offer() → false 반환
- 큐가 비어 있으면 poll() → null 반환

### ⏱️ 2. 시간 대기 (offer(time), poll(time))
```java
public class BoundedQueueV6_3 implements BoundedQueue {
    private BlockingQueue<String> queue;
    public BoundedQueueV6_3(int max) {
        queue = new ArrayBlockingQueue<>(max);
    }
    public void put(String data) {
        try {
            boolean result = queue.offer(data, 1, TimeUnit.NANOSECONDS);
            log("저장 시도 결과 = " + result);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }
    public String take() {
        try {
            return queue.poll(2, TimeUnit.SECONDS);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }
}
```
- 지정된 시간 동안만 대기
- 시간이 초과되면 false 또는 null 반환

### ❗ 3. 예외 발생 (add, remove)
```java
public class BoundedQueueV6_4 implements BoundedQueue {
    private BlockingQueue<String> queue;
    public BoundedQueueV6_4(int max) {
        queue = new ArrayBlockingQueue<>(max);
    }
    public void put(String data) {
        queue.add(data); // 큐가 full이면 예외 발생
    }
    public String take() {
        return queue.remove(); // 큐가 empty면 예외 발생
    }
}
```
- add() → IllegalStateException: Queue full
- remove() → NoSuchElementException

### 🧹 4. 직접 BlockingQueue 사용
```java
public static void main(String[] args) {
    BlockingQueue<String> queue = new ArrayBlockingQueue<>(2);
    producerFirst(queue); // 생산자 먼저 실행
    // consumerFirst(queue); // 소비자 먼저 실행
}
```
- BoundedQueue 인터페이스 없이 직접 BlockingQueue 사용 가능
- 실무에서는 LinkedBlockingQueue, PriorityBlockingQueue 등 다양한 구현체 활용 가능

## 📘 BlockingQueue 기능 요약표

| 기능 유형       | 메서드 예시                          | 실패 시 반환값 / 예외       | 설명                                 |
|----------------|--------------------------------------|-----------------------------|--------------------------------------|
| 예외 발생       | add(e), remove(), element()          | 예외 발생 (`IllegalStateException`, `NoSuchElementException`) | 큐 상태가 적절하지 않으면 예외 발생 |
| 즉시 반환       | offer(e), poll(), peek()             | `false`, `null`             | 대기하지 않고 즉시 결과 반환         |
| 무한 대기       | put(e), take()                       | 없음 (대기)                 | 큐 상태가 바뀔 때까지 무한 대기      |
| 시간 제한 대기   | offer(e, time), poll(time)           | `false`, `null`             | 지정된 시간까지만 대기 후 반환       |

## 🧠 핵심 요약
- 예외 발생: 큐가 가득 차거나 비어 있으면 예외를 던짐
- 즉시 반환: 실패 시 false 또는 null 반환 → 응답성 우선
- 무한 대기: 큐 상태가 바뀔 때까지 블로킹
- 시간 제한 대기: 일정 시간까지만 대기 → 응답성과 안정성 절충

## ✅ 결론
- BlockingQueue는 멀티스레드 환경에서 안정성과 응답성을 동시에 제공하는 큐 인터페이스
- 다양한 상황에 맞는 메서드를 제공하여 무한 대기, 예외, 시간 초과, 즉시 실패 등 유연한 처리 가능
- 우리가 만든 BoundedQueueV5는 BlockingQueue의 원리를 직접 구현한 것이며, 실무에서는 BlockingQueue를 바로 활용하면 됩니다

---

# 생산자-소비자 문제를 쉽게 해결 지에 대한 설명

지금까지는 BlockingQueue의 기능을 중심으로 설명했지만, 왜 이 구조가 생산자-소비자 문제를 쉽게 해결하는지에 대한 원리적인 설명이 부족.  
아래에 그 핵심 원리를 구조적, 동작적, 실무적 관점에서 자세히 정리.

## 📦 BlockingQueue가 생산자-소비자 문제를 쉽게 해결하는 원리
### 🔧 1. 자동 동기화와 대기 제어
- BlockingQueue는 내부적으로 스레드 안전한 큐입니다.
- 생산자 스레드가 put()을 호출하면:
    - 큐가 가득 찼을 경우 → 자동으로 대기 (락을 반납하고 조건 대기)
- 소비자 스레드가 take()를 호출하면:
    - 큐가 비어 있을 경우 → 자동으로 대기
- 이 모든 과정은 명시적인 wait() / notify() 없이도 자동으로 처리됩니다.
    - 즉, 개발자가 직접 락을 관리하거나 조건을 깨우는 코드를 작성할 필요 없이, BlockingQueue가 알아서 처리해줍니다.

### 🔁 2. 대기와 깨어남의 흐름이 내장되어 있음

| 스레드 역할 | 호출 메서드 | 동작 설명                                 |
|-------------|--------------|--------------------------------------------|
| 생산자      | put()        | 큐가 가득 차면 자동으로 대기               |
| 소비자      | take()       | 큐가 비어 있으면 자동으로 대기             |
| 생산자      | put()        | 소비자가 데이터를 꺼내면 → 공간 생김 → 깨어남 |
| 소비자      | take()       | 생산자가 데이터를 넣으면 → 데이터 생김 → 깨어남 |

### 🧠 핵심 요약
- BlockingQueue는 내부적으로 ReentrantLock + Condition을 사용하여
- 생산자는 큐가 가득 차면 자동으로 put() 대기
- 소비자는 큐가 비어 있으면 자동으로 take() 대기
- 큐의 상태가 바뀌면 → 대기 중인 스레드가 자동으로 깨어나서 실행
    - 생산자와 소비자는 큐의 상태에 따라 자동으로 블로킹 또는 실행되며,
    - 이 흐름은 내부적으로 ReentrantLock + Condition을 사용해 구현되어 있음

### 🧠 3. 2단계 대기소 구조 내장
- BlockingQueue는 내부적으로 다음과 같은 구조를 가짐:
```
[ 생산자 스레드 ] → put() → [ 락 대기 큐 ] → [ 조건 대기 큐 (큐가 가득 찼을 때) ]
[ 소비자 스레드 ] → take() → [ 락 대기 큐 ] → [ 조건 대기 큐 (큐가 비어 있을 때) ]
```
- 이 구조는 우리가 직접 구현했던 BoundedQueueV5와 거의 동일하며,
- 생산자와 소비자 각각의 대기 공간을 분리해서 효율적으로 스레드를 제어함

### 🧩 4. 다양한 대기 전략 제공
- 실무에서는 무한 대기보다 즉시 실패, 시간 제한 대기, 예외 처리가 중요함
- BlockingQueue는 이를 위해 다음과 같은 메서드를 제공:

| 대기 전략       | 관련 메서드                         |
|----------------|-------------------------------------|
| 무한 대기       | put(), take()                       |
| 즉시 반환       | offer(), poll()                     |
| 시간 제한 대기   | offer(e, time), poll(time)          |
| 예외 발생       | add(), remove()                     |

### 🧠 설명 요약
- put() / take(): 큐가 가득 차거나 비어 있으면 자동으로 대기 → 공간 또는 데이터가 생길 때까지 기다림
- offer() / poll(): 큐 상태에 따라 즉시 성공 또는 실패 → 응답성이 중요할 때 사용
- offer(e, time) / poll(time): 지정된 시간까지만 대기 → 일정 시간 후 실패 처리 가능
- add() / remove(): 큐 상태가 적절하지 않으면 즉시 예외 발생 → 명확한 실패 처리가 필요할 때 사용
    - 이 덕분에 고객 응답성, 시스템 안정성, 예외 처리까지 모두 커버 가능


### 🧪 5. 실전 예시: 주문 폭주 상황
- 고객이 주문을 넣으면 → 생산자 스레드가 put() 호출
- 큐가 가득 차면 → 생산자 스레드는 자동으로 대기
- 소비자 스레드가 주문을 처리하면 → 큐에 공간 생김 → 생산자 스레드가 깨어남
    - 만약 너무 오래 대기하면 → offer(e, time)으로 실패 처리 → 고객에게 안내 가능

### ✅ 결론
BlockingQueue는 생산자-소비자 문제를 쉽게 해결하는 이유는 다음과 같습니다:
- 스레드 안전한 큐 구조
- 자동 대기/깨어남 처리
- 내장된 2단계 대기소 구조
- 다양한 대기 전략 제공
- 실무에서 필요한 응답성과 예외 처리까지 지원

---

# BlockingQueue 함수 정리

아래는 자바의 BlockingQueue 인터페이스에서 제공하는 주요 함수들을 기능별로 정리한 표와 함께,  
각 기능을 확인할 수 있는 샘플 코드 예제를 제공합니다.

## 📘 BlockingQueue 기능 요약표

| 기능 유형       | 메서드 예시                          | 실패 시 반환값 / 예외                          | 설명                           |
|----------------|--------------------------------------|------------------------------------------------|--------------------------------|
| 예외 발생       | add(e), remove(), element()          | IllegalStateException, NoSuchElementException  | 큐 상태가 적절하지 않으면 예외 발생 |
| 즉시 반환       | offer(e), poll(), peek()             | false, null                                    | 대기하지 않고 즉시 결과 반환       |
| 무한 대기       | put(e), take()                       | 없음                                           | 큐 상태가 바뀔 때까지 무한 대기    |
| 시간 제한 대기   | offer(e, time), poll(time)           | false, null                                    | 지정된 시간까지만 대기 후 반환     |


## 🧪 기능별 샘플 코드
### ✅ 1. 예외 발생: add(), remove(), element()
```java
BlockingQueue<String> queue = new ArrayBlockingQueue<>(1);

// add: 큐가 가득 차면 예외 발생
queue.add("A");
queue.add("B"); // IllegalStateException: Queue full

// remove: 큐가 비어 있으면 예외 발생
queue.remove(); // 제거 성공
queue.remove(); // NoSuchElementException

// element: 큐가 비어 있으면 예외 발생
queue.element(); // NoSuchElementException
```


## ✅ 2. 즉시 반환: offer(), poll(), peek()
```java
BlockingQueue<String> queue = new ArrayBlockingQueue<>(1);

// offer: 큐가 가득 차면 false 반환
boolean result = queue.offer("A");
System.out.println("offer 결과: " + result); // true
result = queue.offer("B");
System.out.println("offer 결과: " + result); // false

// poll: 큐가 비어 있으면 null 반환
String item = queue.poll();
System.out.println("poll 결과: " + item); // "A"
item = queue.poll();
System.out.println("poll 결과: " + item); // null

// peek: 큐의 머리 요소 확인 (제거 안 함)
queue.offer("X");
System.out.println("peek 결과: " + queue.peek()); // "X"
```


## ✅ 3. 무한 대기: put(), take()
```java
BlockingQueue<String> queue = new ArrayBlockingQueue<>(1);

// put: 큐가 가득 차면 공간 생길 때까지 대기
new Thread(() -> {
    try {
        queue.put("A");
        System.out.println("put 완료");
        queue.put("B"); // 대기
        System.out.println("put 완료");
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}).start();

Thread.sleep(1000);
System.out.println("take: " + queue.take()); // "A" → put("B")가 깨어남
```


## ✅ 4. 시간 제한 대기: offer(e, time), poll(time)
```java
BlockingQueue<String> queue = new ArrayBlockingQueue<>(1);

// offer with timeout
boolean result = queue.offer("A", 1, TimeUnit.SECONDS);
System.out.println("offer 결과: " + result); // true
result = queue.offer("B", 1, TimeUnit.SECONDS);
System.out.println("offer 결과: " + result); // false (1초 후 실패)

// poll with timeout
String item = queue.poll(2, TimeUnit.SECONDS);
System.out.println("poll 결과: " + item); // "A"
item = queue.poll(2, TimeUnit.SECONDS);
System.out.println("poll 결과: " + item); // null (2초 후 실패)
```

## 🧠 실무 팁
- 즉시 반환: 응답성이 중요한 API 서버에 적합
- 예외 발생: 테스트나 명확한 실패 처리가 필요한 경우
- 무한 대기: 백그라운드 작업자 스레드에 적합
- 시간 제한 대기: 사용자 요청에 타임아웃을 적용할 때 유용

---
