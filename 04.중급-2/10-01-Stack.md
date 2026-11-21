## 📘 스택(Stack) 자료 구조 요약
### 📌 기본 개념
- 스택은 데이터를 한쪽 방향으로만 넣고 빼는 자료 구조
- 후입 선출(LIFO): 나중에 넣은 데이터가 먼저 나온다
- 대표적인 동작:
    - push: 데이터를 스택에 넣기
    - pop: 데이터를 스택에서 꺼내기
    - peek: 가장 위의 데이터를 꺼내지 않고 조회

### 📌 동작 예시
```
push(1) → push(2) → push(3)
pop() → 3
pop() → 2
pop() → 1

넣은 순서: 1 → 2 → 3
꺼낸 순서: 3 → 2 → 1
```


### 📌 자바 Stack 클래스 사용 예제
```java
Stack<Integer> stack = new Stack<>();
stack.push(1);
stack.push(2);
stack.push(3);

System.out.println(stack);           // [1, 2, 3]
System.out.println(stack.peek());   // 3
System.out.println(stack.pop());    // 3
System.out.println(stack.pop());    // 2
System.out.println(stack.pop());    // 1
System.out.println(stack);          // []
```

### 📌 Stack 클래스 사용 주의

| 항목               | 구현체 또는 구조         | 설명 및 특징                                  |
|--------------------|--------------------------|-----------------------------------------------|
| 스택 클래스        | `Stack` (`Vector` 기반)  | 오래된 구조, 동기화됨 → 성능 저하 가능성 있음 |
| 내부 구조          | `Vector`                 | 자바 1.0 시절 구조, 현재는 사용 권장되지 않음  |
| 권장 대안          | `Deque` (`ArrayDeque`)   | 더 빠르고 유연한 구조, 스택/큐 모두 지원      |
| 사용 권장 여부     | `Stack`: ❌ 사용 지양     | `Deque`: ✅ 실무에서 권장                      |

### 📌 요약 포인트
- Stack은 내부적으로 Vector를 사용 → 동기화로 인해 성능 저하 가능
- Deque는 ArrayDeque 구현체를 통해 스택처럼 push, pop을 지원하면서 더 빠름
- 실무에서는 Deque를 사용하여 스택을 구현하는 것이 표준



### 📌 실무 권장: Deque로 스택 구현
```java
Deque<Integer> stack = new ArrayDeque<>();
stack.push(1);
stack.push(2);
stack.push(3);

System.out.println(stack.pop()); // 3
System.out.println(stack.pop()); // 2
System.out.println(stack.pop()); // 1
```
- Deque는 양방향 자료 구조지만 push/pop을 사용하면 스택처럼 동작함

### 📌 요약 포인트
- 스택은 후입 선출(LIFO) 구조
- 자바의 Stack 클래스는 사용하지 않는 것이 좋음
- 대신 Deque 인터페이스의 구현체인 ArrayDeque를 사용하면 더 안전하고 빠름

---
