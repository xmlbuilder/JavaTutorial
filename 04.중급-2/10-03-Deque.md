## 📘 Deque 자료 구조 요약
### 📌 기본 개념
- Deque는 양쪽 끝에서 데이터를 추가하거나 제거할 수 있는 자료 구조
- 스택(LIFO)과 큐(FIFO) 기능을 모두 포함
- 자바에서는 Deque 인터페이스를 통해 제공되며, 대표 구현체는:
  - ArrayDeque (추천)
  - LinkedList

### 📌 Deque 주요 메서드 요약

| 메서드             | 설명                                | 방향       |
|--------------------|-------------------------------------|------------|
| `offerFirst(E e)`  | 앞쪽에 요소 추가                     | 입력 (앞)  |
| `offerLast(E e)`   | 뒤쪽에 요소 추가                     | 입력 (뒤)  |
| `pollFirst()`      | 앞쪽 요소 제거 및 반환               | 출력 (앞)  |
| `pollLast()`       | 뒤쪽 요소 제거 및 반환               | 출력 (뒤)  |
| `peekFirst()`      | 앞쪽 요소 조회 (제거 안 함)          | 조회 (앞)  |
| `peekLast()`       | 뒤쪽 요소 조회 (제거 안 함)          | 조회 (뒤)  |
| `isEmpty()`        | 비어 있는지 확인                     | 상태 확인  |
| `size()`           | 저장된 요소 개수 반환                | 상태 확인  |



### 📌 🧪 메서드별 샘플 코드
#### 🔹 1. offerFirst() – 앞쪽에 요소 추가
```java
Deque<String> deque = new ArrayDeque<>();
deque.offerFirst("A");
System.out.println(deque); // [A]
```


#### 🔹 2. offerLast() – 뒤쪽에 요소 추가
```java
deque.offerLast("B");
System.out.println(deque); // [A, B]
```


#### 🔹 3. peekFirst() / peekLast() – 앞/뒤 조회
```java
System.out.println(deque.peekFirst()); // A
System.out.println(deque.peekLast());  // B
```


#### 🔹 4. pollFirst() / pollLast() – 앞/뒤 제거
```java
System.out.println(deque.pollFirst()); // A
System.out.println(deque.pollLast());  // B
System.out.println(deque);             // []
```


#### 🔹 5. isEmpty() / size() – 상태 확인
```java
System.out.println(deque.isEmpty()); // true
deque.offerLast("X");
deque.offerLast("Y");
System.out.println(deque.size());    // 2
```


### 📌 성능 비교 요약

| 구현체         | 입력 성능 (100만 건) | 조회 성능 (100만 건) | 시간 복잡도 | 내부 구조             | 특징 및 추천 이유                          |
|----------------|----------------------|------------------------|--------------|------------------------|-------------------------------------------|
| `ArrayDeque`   | 110ms                | 9ms                    | `O(1)` 평균  | 배열 기반 + 원형 큐    | 빠르고 캐시 친화적, 실무에서 가장 많이 사용 |
| `LinkedList`   | 480ms                | 20ms                   | `O(1)` 평균  | 노드 기반 링크드 리스트 | 삽입/삭제 많을 때 이론적으로 유리           |

### 📌 요약 포인트
- ArrayDeque는 배열 기반으로 메모리 접근이 빠르고 CPU 캐시 최적화에 유리
- LinkedList는 노드 기반으로 삽입/삭제가 자주 발생할 때 이론적으로 유리하지만 실제 성능은 느릴 수 있음
- 실무에서는 대부분 ArrayDeque를 사용하며, 큐와 스택 모두 구현 가능
- 실제 환경에서는 ArrayDeque가 메모리 접근 최적화 덕분에 더 빠른 성능을 보이는 경우가 많음.


### 📌 실무 팁
- 스택처럼 사용: push(), pop() → 내부적으로 offerFirst, pollFirst와 동일
- 큐처럼 사용: offerLast(), pollFirst() → 일반적인 FIFO 처리
- 양방향 처리: 앞/뒤 모두 자유롭게 조작 가능 → 캐시, 슬라이딩 윈도우, 트리 탐색 등에 활용

### 📌 슬라이딩
- Deque를 이용한 슬라이딩 윈도우 최대값 알고리즘은 O(n) 시간 복잡도로 각 윈도우의 최대값을 빠르게 구할 수 있는 효율적인 방법입니다.  
- 핵심은 Deque를 이용해 윈도우 내에서 최대값 후보만 유지하는 것입니다.

### 📌 문제 정의
- 입력: 정수 배열 nums, 윈도우 크기 k
- 목표: 배열을 왼쪽에서 오른쪽으로 k 크기의 슬라이딩 윈도우로 이동시키며, 각 윈도우의 최대값을 구함
- 예시:
```
nums = [1, 3, -1, -3, 5, 3, 6, 7], k = 3
결과 = [3, 3, 5, 5, 6, 7]
```

| 윈도우	| 데이터	| 최대값 |
|-------|-------|------------|
| 0~2	| 1, 3, -1	| 3 |
| 1~3	| 3, -1, -3	| 3 |
| 2~4	| -1, -3, 5	| 5 |
| 3~5	| -3, 5, 3	| 5 |
| 4~6	| 5, 3, 6	| 6 |
| 5~7	| 3, 6, 7	| 7 |

- 결과는 [3, 3, 5, 5, 6, 7]입니다.


#### 🔹 Deque를 이용한 핵심 아이디어
- Deque에는 인덱스만 저장하며, 항상 값이 큰 순서대로 유지
- 윈도우를 오른쪽으로 이동할 때:
- 현재 값보다 작은 값의 인덱스는 뒤에서 제거 → 최대값 후보만 유지
- 현재 인덱스를 뒤에 추가
- 윈도우 범위를 벗어난 인덱스는 앞에서 제거
- 윈도우가 k 크기에 도달하면 맨 앞 인덱스의 값이 최대값

#### 🔹 자바 코드 예시
```java
public List<Integer> maxSlidingWindow(int[] nums, int k) {
    List<Integer> result = new ArrayList<>();
    Deque<Integer> deque = new ArrayDeque<>();

    for (int i = 0; i < nums.length; i++) {
        // 1. 윈도우 범위 밖 인덱스 제거
        if (!deque.isEmpty() && deque.peekFirst() <= i - k) {
            deque.pollFirst();
        }

        // 2. 현재 값보다 작은 값 제거
        while (!deque.isEmpty() && nums[deque.peekLast()] < nums[i]) {
            deque.pollLast();
        }

        // 3. 현재 인덱스 추가
        deque.offerLast(i);

        // 4. 윈도우가 k 크기일 때 결과 추가
        if (i >= k - 1) {
            result.add(nums[deque.peekFirst()]);
        }
    }

    return result;
}
```


#### 🔹시간 복잡도 분석
- 각 요소는 최대 1번 Deque에 들어가고 1번 나옴 → O(n)
- 공간 복잡도는 최대 k개의 인덱스를 저장 → O(k)

#### 🔹 실무 활용 팁
- 슬라이딩 윈도우 최대값은 모니터링 시스템, 주식 가격 분석, 센서 데이터 처리 등에 자주 사용됨
- Deque를 활용하면 우선순위 큐보다 빠르고 메모리 효율적

---


### 📌 Deque = Stack + Queue
#### 🔹 기본 개념
- Deque는 양쪽 끝에서 데이터 삽입/삭제가 가능한 자료 구조
- Stack처럼 사용 → 앞쪽에서 push, pop
- Queue처럼 사용 → 뒤쪽에서 offer, 앞쪽에서 poll
- 자바에서는 ArrayDeque를 가장 많이 사용함

#### 🔹 Deque 주요 메서드 요약

| 역할      | 메서드           | 설명                          | 방향       |
|-----------|------------------|-------------------------------|------------|
| Stack     | `push(E e)`      | 앞쪽에 요소 추가              | 입력 (앞)  |
| Stack     | `pop()`          | 앞쪽 요소 제거 및 반환        | 출력 (앞)  |
| Stack     | `peek()`         | 앞쪽 요소 조회 (제거 안 함)   | 조회 (앞)  |
| Queue     | `offer(E e)`     | 뒤쪽에 요소 추가              | 입력 (뒤)  |
| Queue     | `poll()`         | 앞쪽 요소 제거 및 반환        | 출력 (앞)  |
| Queue     | `peek()`         | 앞쪽 요소 조회 (제거 안 함)   | 조회 (앞)  |


#### 🔹 Stack 방식 샘플 (push, pop, peek)
```java
Deque<Integer> deque = new ArrayDeque<>();
deque.push(1);
deque.push(2);
deque.push(3);
System.out.println(deque);           // [3, 2, 1]
System.out.println(deque.peek());   // 3
System.out.println(deque.pop());    // 3
System.out.println(deque.pop());    // 2
System.out.println(deque.pop());    // 1
System.out.println(deque);          // []
```
- 후입 선출 (LIFO): 나중에 넣은 3이 먼저 나옴


#### 🔹 Queue 방식 샘플 (offer, poll, peek)
```java
Deque<Integer> deque = new ArrayDeque<>();
deque.offer(1);
deque.offer(2);
deque.offer(3);
System.out.println(deque);           // [1, 2, 3]
System.out.println(deque.peek());   // 1
System.out.println(deque.poll());   // 1
System.out.println(deque.poll());   // 2
System.out.println(deque.poll());   // 3
System.out.println(deque);          // []
```
- 선입 선출 (FIFO): 먼저 넣은 1이 먼저 나옴


### 📌 실무 팁
- Deque는 Stack과 Queue를 모두 구현할 수 있는 유연한 구조
- ArrayDeque는 빠르고 가볍고 캐시 친화적이므로 실무에서 가장 많이 사용됨
- LinkedList도 Deque을 구현하지만 성능 면에서는 ArrayDeque가 우수

---
