## 📘 큐(Queue) 자료 구조 요약
### 📌 기본 개념
- **큐(Queue)** 는 데이터를 선입 선출(FIFO) 방식으로 처리하는 자료 구조
- 먼저 들어온 데이터가 먼저 나간다
- 대표적인 동작:
  - offer: 큐에 데이터 추가
  - poll: 큐에서 데이터 꺼내기
  - peek: 큐의 맨 앞 데이터 조회 (꺼내지 않음)

### 📌 동작 예시
```
offer(1) → offer(2) → offer(3)
poll() → 1
poll() → 2
poll() → 3

넣은 순서: 1 → 2 → 3
꺼낸 순서: 1 → 2 → 3
```

### 📌 Queue 주요 메서드 정리

| 메서드           | 설명                                 | 반환값 예시       |
|------------------|--------------------------------------|-------------------|
| `offer(E e)`     | 큐에 요소 추가                        | `true` 또는 `false` |
| `poll()`         | 큐에서 요소 제거 및 반환              | 가장 앞의 요소     |
| `peek()`         | 큐의 가장 앞 요소 조회 (제거 안 함)   | 가장 앞의 요소     |
| `isEmpty()`      | 큐가 비어 있는지 확인                 | `true` 또는 `false` |
| `size()`         | 큐에 저장된 요소 개수 반환            | `int` 값           |


### 📌 메서드별 샘플 코드
#### 🔹 1. offer(E e) – 요소 추가
```java
Queue<String> queue = new ArrayDeque<>();
queue.offer("A");
queue.offer("B");
System.out.println(queue); // [A, B]
```


#### 🔹 2. poll() – 요소 제거 및 반환
```java
String item = queue.poll();
System.out.println("poll = " + item); // poll = A
System.out.println(queue);            // [B]
```


#### 🔹 3. peek() – 요소 조회 (제거 안 함)
```java
String front = queue.peek();
System.out.println("peek = " + front); // peek = B
System.out.println(queue);             // [B]
```


#### 🔹 4. isEmpty() – 큐 비어 있는지 확인
```java
System.out.println(queue.isEmpty()); // false
queue.poll(); // B 제거
System.out.println(queue.isEmpty()); // true
```


#### 🔹 5. size() – 큐 크기 확인
```java
queue.offer("X");
queue.offer("Y");
System.out.println(queue.size()); // 2
```


### 📌 실무 팁
- ArrayDeque는 큐 구현에 가장 많이 사용됨 (빠르고 가볍고 동기화 없음)
- LinkedList도 큐로 사용 가능하지만, 일반적으로 ArrayDeque이 성능 면에서 더 우수
- 큐는 작업 순서 관리, 이벤트 처리, BFS 탐색 등에 자주 사용됨

---
