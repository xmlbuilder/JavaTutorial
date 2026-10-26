## 📘 HashSet
- Java의 HashSet은 중복을 허용하지 않는 데이터를 저장하는 집합(Set) 자료구조입니다.
- 내부적으로 해시 테이블(HashMap)을 사용하여 데이터를 관리합니다.
- 주요 특징
    - 중복 불가: 동일한 값은 한 번만 저장
    - 순서 보장 안 함: 입력한 순서대로 저장되지 않음
    - 빠른 검색: 추가, 삭제, 검색의 평균 시간 복잡도는 O(1)
    - null 허용: null을 하나까지 저장 가능
    - 동등성 판단: hashCode()와 equals() 사용

### 📌 MyHashSetV1 핵심 요약
| 항목               | 설명                                               |
|--------------------|----------------------------------------------------|
| 개선 대상           | `MyHashSetV0` (O(n) 성능 문제)                    |
| 저장 구조           | `LinkedList<Integer>[]` 배열 기반 체이닝 방식     |
| 해시 인덱스 계산    | `value % capacity`                                 |
| 주요 메서드         | `add(value)`, `contains(value)`, `remove(value)`  |
| 초기 용량           | `DEFAULT_INITIAL_CAPACITY = 16`                    |
| 생성 방법           | `new MyHashSetV1(10)` 으로 용량 지정 가능         |


### 📌 클래스 구조 요약
```java
public class MyHashSetV1 {
    static final int DEFAULT_INITIAL_CAPACITY = 16;
    LinkedList<Integer>[] buckets;
    private int size = 0;
    private int capacity;

    public MyHashSetV1() {
        this.capacity = DEFAULT_INITIAL_CAPACITY;
        initBuckets();
    }

    public MyHashSetV1(int capacity) {
        this.capacity = capacity;
        initBuckets();
    }

    private void initBuckets() {
        buckets = new LinkedList[capacity];
        for (int i = 0; i < capacity; i++) {
            buckets[i] = new LinkedList<>();
        }
    }

    public boolean add(int value) {
        int hashIndex = value % capacity;
        LinkedList<Integer> bucket = buckets[hashIndex];
        if (bucket.contains(value)) return false;
        bucket.add(value);
        size++;
        return true;
    }

    public boolean contains(int value) {
        int hashIndex = value % capacity;
        return buckets[hashIndex].contains(value);
    }

    public boolean remove(int value) {
        int hashIndex = value % capacity;
        boolean removed = buckets[hashIndex].remove(Integer.valueOf(value));
        if (removed) size--;
        return removed;
    }

    public int getSize() {
        return size;
    }

    @Override
    public String toString() {
        return "MyHashSetV1{" +
               "buckets=" + Arrays.toString(buckets) +
               ", size=" + size +
               '}';
    }
}
```


### 📌 테스트 예제
```java
public class MyHashSetV1Main {
    public static void main(String[] args) {
        MyHashSetV1 set = new MyHashSetV1(10);
        set.add(1);
        set.add(2);
        set.add(5);
        set.add(8);
        set.add(14);
        set.add(99);
        set.add(9); // hashIndex 충돌

        System.out.println(set);

        int searchValue = 9;
        System.out.println("set.contains(" + searchValue + ") = " + set.contains(searchValue));

        boolean removeResult = set.remove(searchValue);
        System.out.println("removeResult = " + removeResult);
        System.out.println(set);
    }
}
```


#### 🔹 실행 결과 해설
```
MyHashSetV1{buckets=[[], [1], [2], [], [14], [5], [], [], [8], [99, 9]], size=7}
set.contains(9) = true
removeResult = true
MyHashSetV1{buckets=[[], [1], [2], [], [14], [5], [], [], [8], [99]], size=6}
```

- 99와 9는 hashIndex = 9로 충돌 → 같은 버킷에 저장됨
- contains(9)는 해당 버킷에서 순차 탐색
- remove(9)는 해당 버킷에서 제거 후 size 감소

### 📌 문자열을 저장하려면?
- 현재 구현은 int 기반이므로 문자열은 직접 저장할 수 없습니다.  
- 문자열을 저장하려면 다음과 같이 hashCode()를 활용해야 합니다:  
```java
int hashIndex = Math.abs(value.hashCode()) % capacity;
```


### 📌 문자열 및 객체 기반 해시 인덱스 정리

#### 🔹 기본 개념

| 항목             | 설명                                                                 |
|------------------|----------------------------------------------------------------------|
| 해시 코드         | 데이터를 대표하는 정수값. 해시 함수로 생성됨                        |
| 해시 인덱스       | `해시 코드 % 배열 크기` → 배열의 저장 위치 결정                      |
| 해시 함수         | 문자열 또는 객체 → 정수 해시 코드로 변환                             |
| 저장 구조         | 배열 + 연결 리스트 (체이닝 방식)                                     |
| 성능             | 평균 O(1), 최악 O(n) (충돌 시)                                       |
| 적용 대상         | 숫자, 문자열, 사용자 정의 객체 등 모든 타입                          |



#### 🔹 문자열 해시 처리
```java
static int hashCode(String str) {
    char[] charArray = str.toCharArray();
    int sum = 0;
    for (char c : charArray) {
        sum += c; // ASCII 값 합산
    }
    return sum;
}

static int hashIndex(int value) {
    return value % CAPACITY;
}
```

- "A" → 65 → 65 % 10 = 5
- "B" → 66 → 66 % 10 = 6
- "AB" → 131 → 131 % 10 = 1

#### 🔹 ASCII 기반 문자 인코딩

| 문자 | ASCII | 문자 | ASCII | 문자 | ASCII |
|------|--------|------|--------|------|--------|
| A    | 65     | B    | 66     | C    | 67     |
| a    | 97     | b    | 98     | c    | 99     |
| 0    | 48     | 1    | 49     | 2    | 50     |
| !    | 33     | @    | 64     | #    | 35     |

- 모든 문자는 고유한 숫자(ASCII)로 표현됨
- 문자열은 각 문자의 ASCII 값을 합산하여 해시 코드 생성 가능

#### 🔹 사용자 정의 객체 해시 처리
```java
@Override
public int hashCode() {
    return Objects.hash(id); // id 기반 해시 코드 생성
}

@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    Member member = (Member) o;
    return Objects.equals(id, member.id);
}
```

- equals()는 논리적 동등성 비교
- hashCode()는 동일한 id에 대해 같은 해시 코드 반환
- Member("idA")와 Member("idA")는 서로 다른 인스턴스지만 같은 해시 코드

#### 🔹 동일성과 동등성

| 비교 방식   | 의미                          | 예시           |
|-------------|-------------------------------|----------------|
| `==`        | 동일성 (같은 객체 참조)       | `a == b` → false |
| `equals()`  | 동등성 (논리적으로 같은 값)   | `a.equals(b)` → true |


#### 🔹 핵심 요약
- 문자열은 ASCII 기반으로 해시 코드 생성 가능
- 모든 객체는 hashCode()를 재정의하면 해시 자료구조에 저장 가능
- 자바의 기본 클래스(String, Integer 등)는 이미 hashCode()를 재정의함
- 사용자 정의 클래스는 equals()와 hashCode()를 함께 재정의해야 논리적 동등성과 해시 일관성을 확보할 수 있음

---


### 📌 MyHashSetV2 수정 핵심 요약

| 항목               | 설명                                                              |
|--------------------|-------------------------------------------------------------------|
| 개선 대상           | `MyHashSetV1`는 `Integer`만 저장 가능                            |
| 저장 구조           | `LinkedList<Object>[]` 배열 기반 체이닝 방식                      |
| 해시 인덱스 계산    | `Math.abs(value.hashCode()) % capacity`                          |
| 주요 메서드         | `add(Object)`, `contains(Object)`, `remove(Object)`              |
| 해시 코드 생성      | `Object.hashCode()` → 다형성으로 실제 타입의 `hashCode()` 호출   |
| 음수 처리           | `hashCode()` 결과가 음수일 수 있어 `Math.abs()`로 양수화         |
| 적용 대상           | `String`, `Integer`, 사용자 정의 객체 등 모든 타입                |


### 📌 주요 변경 사항
- 제네릭 미사용: Object 타입으로 모든 데이터 수용
- 해시 인덱스 계산 방식 변경: value.hashCode() → Math.abs() → % capacity
- 버킷 구조: 각 인덱스에 LinkedList<Object>를 저장하여 충돌 처리

### 📌 예제 실행 흐름
```java
MyHashSetV2 set = new MyHashSetV2(10);
set.add("A"); // hashCode = 65 → index 5
set.add("B"); // hashCode = 66 → index 6
set.add("AB"); // hashCode = 2081 → index 1
set.add("SET"); // hashCode = 81986 → index 6 (충돌)
```

- "SET"과 "B"는 같은 인덱스(6)에 저장됨 → 체이닝으로 처리
- contains("SET")는 인덱스 6의 리스트에서 순차 검색

### 📌 실행 결과 요약
```
MyHashSetV2{buckets=[[], [AB], [], [], [], [A], [B, SET], [C], [D], []], size=6, capacity=10}
A.hashCode=65
B.hashCode=66
AB.hashCode=2081
SET.hashCode=81986
set.contains(SET) = true
```

- 해시 인덱스는 hashCode % 10으로 계산됨
- "SET"은 "B"와 충돌하지만 리스트에 함께 저장되어 검색 가능

### 📌 실무적 인사이트
- 자바의 모든 객체는 hashCode()를 가지고 있으며, 대부분의 클래스는 이를 재정의함
- 사용자 정의 클래스도 equals()와 hashCode()를 재정의하면 해시 기반 자료구조에 안전하게 저장 가능
- Object.hashCode()는 참조값 기반이므로 논리적 비교를 위해 반드시 오버라이딩 필요

---


# ✅ MyHashSetV2와 hashCode / equals 핵심 요약

## 📌 MyHashSetV2의 특징

| 항목               | 설명                                                              |
|--------------------|-------------------------------------------------------------------|
| 저장 구조           | `LinkedList<Object>[]` 배열 기반 체이닝 방식                      |
| 해시 인덱스 계산    | `Math.abs(value.hashCode()) % capacity`                          |
| 저장 대상           | 모든 타입(Object) 저장 가능                                       |
| 중복 체크 방식      | `bucket.contains(value)` 내부에서 `equals()`로 비교               |
| 다형성 활용         | `Object.hashCode()` → 실제 타입의 `hashCode()` 호출               |


## 🧠 hashCode()와 equals()의 역할

| 메서드       | 역할                                                              |
|--------------|-------------------------------------------------------------------|
| `hashCode()` | 객체를 정수로 표현해 해시 인덱스를 계산하는 데 사용됨            |
| `equals()`   | 해시 충돌 시 동일한 객체인지 비교하는 데 사용됨                  |

- 해시 자료구조는 hashCode()로 인덱스를 찾고, equals()로 정확한 객체를 판별함
- 두 메서드는 반드시 함께 재정의해야 정확한 저장/검색이 가능함

## ❌ 구현하지 않았을 때 발생하는 문제
### 1. hashCode()와 equals() 모두 미구현
- Object의 기본 기능 사용 → 참조값 기반 비교
- 같은 값이라도 다른 인스턴스는 다른 해시 코드, 다른 비교 결과
- 결과: 중복 저장됨, 검색 실패
### 2. hashCode()만 구현, equals() 미구현
- 해시 인덱스는 정확히 계산됨
- 하지만 equals()가 참조값 비교이므로 중복 체크 실패
- 결과: 중복 저장됨, 검색 실패
### 3. hashCode()와 equals() 모두 구현
- id 값 기준으로 해시 코드 생성 및 논리적 비교
- 결과: 중복 저장 방지, 검색 성공

## 🔍 해시 충돌과 자바의 해시 함수

| 항목             | 설명                                                                 |
|------------------|----------------------------------------------------------------------|
| 해시 충돌         | 서로 다른 데이터가 같은 해시 코드를 가질 수 있음                     |
| 충돌 예시         | `"AB"`와 `"SET"` → 서로 다른 문자열이지만 해시 인덱스가 같을 수 있음 |
| 특수 사례         | `"Aa".hashCode() = 2112`, `"BB".hashCode() = 2112` → 충돌 발생       |
| 충돌 해결 방식    | `equals()`를 통해 실제 객체가 같은지 비교                            |
| 자바 해시 함수     | 복잡한 연산으로 해시 코드 분포를 균일하게 설계 → 충돌 확률 최소화     |


## ✅ 정리
- 해시 자료구조를 사용할 때는 반드시 hashCode()와 equals()를 함께 재정의해야 함
- hashCode()는 해시 인덱스를 결정하고, equals()는 충돌 시 정확한 객체를 판별함
- 자바의 기본 클래스(String, Integer 등)는 이미 두 메서드를 재정의해둠
- 사용자 정의 클래스는 IDE의 자동 생성 기능을 활용해 쉽게 구현 가능
- 좋은 해시 함수는 해시 코드가 고르게 분포되도록 설계되어야 하며, 자바는 이를 잘 제공함

---

# ✅ MyHashSetV3 핵심 요약
## 📌 주요 특징

| 항목               | 설명                                                              |
|--------------------|-------------------------------------------------------------------|
| 인터페이스 구조     | `MySet<E>` 인터페이스로 `add`, `remove`, `contains` 추상화       |
| 제네릭 적용         | `E` 타입 매개변수로 타입 안정성 확보                              |
| 저장 구조           | `LinkedList<E>[]` 배열 기반 체이닝 방식                           |
| 해시 인덱스 계산    | `Math.abs(value.hashCode()) % capacity`                           |
| 타입 안전성         | 컴파일 타임에 타입 오류 방지 가능                                 |
| 확장 가능성         | 다양한 자료구조 기반의 `Set` 구현 가능                            |

## 🧠 코드 구조 요약
### 인터페이스 정의
```java
public interface MySet<E> {
    boolean add(E element);
    boolean remove(E value);
    boolean contains(E value);
}
```

### 제네릭 해시셋 구현
```java
public class MyHashSetV3<E> implements MySet<E> {
    private LinkedList<E>[] buckets;
    private int size;
    private int capacity;

    public boolean add(E value) { ... }
    public boolean contains(E value) { ... }
    public boolean remove(E value) { ... }
    private int hashIndex(Object value) { ... }
}
```


### 🧪 실행 예제
```java
MySet<String> set = new MyHashSetV3<>(10);
set.add("A");
set.add("B");
set.add("C");
System.out.println(set);
System.out.println("set.contains(\"A\") = " + set.contains("A"));
```

#### 실행 결과
```java
MyHashSetV3{buckets=[[], [], [], [], [], [A], [B], [C], [], []], size=3, capacity=10}
set.contains("A") = true
```

- "A"의 hashCode()는 65 → 65 % 10 = 5 → 인덱스 5에 저장
- 제네릭 덕분에 String 타입만 저장 가능 → 타입 안정성 확보

## ✅ 정리
- MyHashSetV3는 제네릭과 인터페이스를 도입해 타입 안정성과 확장성을 확보한 해시셋 구현체입니다.
- MySet<E> 인터페이스를 통해 다양한 자료구조 기반의 Set 구현이 가능해졌습니다.
- 제네릭을 사용함으로써 컴파일 타임에 타입 오류를 방지할 수 있고, 코드의 재사용성과 안정성이 높아졌습니다.

