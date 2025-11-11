# 📘 자바 스트림 API2 – 기능 정리

## ✅ 1. 스트림 생성 방법

| 생성 방식               | 예시 코드                              | 설명                                      | 특징                      |
|------------------------|-----------------------------------------|-------------------------------------------|---------------------------|
| 컬렉션 기반             | `list.stream()`                         | List, Set 등 컬렉션에서 스트림 생성       | 유한 스트림               |
| 배열 기반               | `Arrays.stream(arr)`                    | 배열에서 스트림 생성                      | 유한 스트림               |
| 직접 요소 입력          | `Stream.of("a", "b", "c")`              | 직접 요소를 입력해 스트림 생성            | 유한 스트림               |
| 무한 스트림 (iterate)   | `Stream.iterate(0, n -> n + 2)`         | 초기값과 함수로 무한 스트림 생성          | 무한 스트림, 지연 연산 필요 |
| 무한 스트림 (generate)  | `Stream.generate(Math::random)`         | Supplier로 무한 스트림 생성               | 무한 스트림, 지연 연산 필요 |

- ⚠️ 무한 스트림은 반드시 .limit(n)으로 제한해서 사용해야 합니다.


## 🔄 2. 중간 연산 (Intermediate Operations)

| 연산 이름     | 설명                                           | 예시 코드                                      |
|---------------|------------------------------------------------|------------------------------------------------|
| `filter`        | 조건에 맞는 요소만 남김                        | `.filter(n -> n > 5)`                          |
| `map`           | 요소를 다른 형태로 변환                        | `.map(n -> n * 2)`                             |
| `flatMap`       | 중첩 구조를 평탄화하여 단일 스트림으로 변환    | `.flatMap(list -> list.stream())`             |
| `distinct`      | 중복 요소 제거                                 | `.distinct()`                                  |
| `sorted`        | 요소 정렬 (기본 또는 커스텀 Comparator 사용)   | `.sorted()`, `.sorted(Comparator.reverseOrder())` |
| `peek`          | 중간 처리 (디버깅, 로깅 등)                    | `.peek(System.out::println)`                  |
| `limit`         | 앞에서 N개의 요소만 추출                       | `.limit(5)`                                    |
| `skip`          | 앞에서 N개의 요소를 건너뛰고 이후 요소 추출    | `.skip(5)`                                     |
| `takeWhile`     | 조건을 만족하는 동안 요소 추출 (Java 9+)       | `.takeWhile(n -> n < 5)`                       |
| `dropWhile`     | 조건을 만족하는 동안 요소를 건너뛰고 이후 추출 | `.dropWhile(n -> n < 5)`                       |


## 🧩 3. flatMap vs map
```java
List<List<Integer>> outerList = List.of(
    List.of(1, 2),
    List.of(3, 4),
    List.of(5, 6)
);
```
```java
// map → Stream<Stream<Integer>>
List<Stream<Integer>> mapResult = outerList.stream()
    .map(list -> list.stream())
    .toList();
```
```java
// flatMap → Stream<Integer>
List<Integer> flatMapResult = outerList.stream()
    .flatMap(list -> list.stream())
    .toList();
```
- map()은 중첩 스트림을 유지 → List<Stream<Integer>>
- flatMap()은 내부 스트림을 평탄화 → List<Integer>


## 📦 4. Optional 간단 설명

| 메서드                | 설명                                                                 |
|------------------------|----------------------------------------------------------------------|
| `Optional.of()`        | null이 아닌 값을 감싸는 Optional 생성. null이면 예외 발생            |
| `Optional.ofNullable()`| null 여부와 관계없이 Optional 생성. null이면 Optional.empty 반환     |
| `isPresent()`          | 내부에 값이 존재하는지 여부를 boolean으로 반환                        |
| `get()`                | 내부 값을 꺼냄. 값이 없으면 NoSuchElementException 예외 발생          |

```java
Optional<Integer> optional = Optional.of(10);
if (optional.isPresent()) {
    System.out.println(optional.get());
}
```

## 🏁 5. 최종 연산 (Terminal Operations)

| 연산 이름     | 설명                                                   | 예시 코드                                 |
|---------------|--------------------------------------------------------|--------------------------------------------|
| `collect`       | Collector를 사용해 결과를 다양한 형태로 수집          | `.collect(Collectors.toList())`            |
| `toList`        | 불변 리스트로 수집 (Java 16+)                         | `.toList()`                                |
| `toArray`       | 스트림을 배열로 변환                                   | `.toArray(Integer[]::new)`                 |
| `forEach`       | 각 요소에 대해 지정된 동작 수행                        | `.forEach(System.out::println)`            |
| `count`         | 요소 개수를 반환                                       | `.count()`                                 |
| `reduce`        | 누적 연산을 통해 하나의 결과로 축소                   | `.reduce(0, Integer::sum)`                 |
| `min` / `max`     | 최솟값 또는 최댓값을 Optional로 반환                   | `.min()`, `.max()`                         |
| `findFirst`     | 조건에 맞는 첫 번째 요소 반환 (Optional)              | `.findFirst()`                             |
| `findAny`       | 조건에 맞는 아무 요소 하나 반환 (Optional)            | `.findAny()`                               |
| `anyMatch`      | 하나라도 조건을 만족하면 true 반환                    | `.anyMatch(n -> n > 5)`                    |
| `allMatch`      | 모든 요소가 조건을 만족하면 true 반환                 | `.allMatch(n -> n > 0)`                    |
| `noneMatch`     | 모든 요소가 조건을 만족하지 않으면 true 반환          | `.noneMatch(n -> n < 0)`                   |


## 🧠 핵심 요약
- `스트림은 1회성`: 한 번 사용하면 재사용 불가
- `중간 연산은 지연(Lazy)`: 최종 연산이 실행될 때만 동작
- `flatMap은 평탄화 전용`: 중첩 구조를 단일 스트림으로 변환
- Optional은 안전한 null 처리 도구
- 최종 연산이 실행되어야 스트림이 작동

## ✅ 스트림 생성 예제
```java
public static void createStreams() {
    List<String> list = List.of("a", "b", "c");
    String[] arr = {"x", "y", "z"};

    // 컬렉션 기반
    list.stream().forEach(System.out::println);

    // 배열 기반
    Arrays.stream(arr).forEach(System.out::println);

    // Stream.of
    Stream.of("1", "2", "3").forEach(System.out::println);

    // 무한 스트림 iterate
    Stream.iterate(0, n -> n + 2)
          .limit(5)
          .forEach(System.out::println);

    // 무한 스트림 generate
    Stream.generate(Math::random)
          .limit(3)
          .forEach(System.out::println);
}
```


## 🔄 중간 연산 예제
```rust
public static void intermediateOperations() {
    List<Integer> numbers = List.of(1, 2, 2, 3, 4, 5, 6, 7, 8);

    numbers.stream()
           .filter(n -> n % 2 == 0)
           .map(n -> n * n)
           .distinct()
           .sorted(Comparator.reverseOrder())
           .peek(n -> System.out.println("중간값: " + n))
           .skip(1)
           .limit(3)
           .forEach(System.out::println);
}
```

### 🔍 입력 데이터
```java
List<Integer> numbers = List.of(1, 2, 2, 3, 4, 5, 6, 7, 8);
```


### 🔄 연산 흐름 분석
- filter(n -> n % 2 == 0)
    - 짝수만 남김 → [2, 2, 4, 6, 8]
- map(n -> n * n)
    - 제곱 → [4, 4, 16, 36, 64]
- distinct()
    - 중복 제거 → [4, 16, 36, 64]
- sorted(Comparator.reverseOrder())
    - 내림차순 정렬 → [64, 36, 16, 4]
- peek(...)
    - 중간값 출력 (디버깅용)
```
출력:
중간값: 64
중간값: 36
중간값: 16
중간값: 4
```
- skip(1)
    - 첫 번째 요소(64) 건너뜀 → [36, 16, 4]
- limit(3)
    - 최대 3개 → [36, 16, 4]
- forEach(System.out::println)
    - 최종 출력:
```
36
16
4
```
### ✅ 최종 출력 요약
```
중간값: 64
중간값: 36
중간값: 16
중간값: 4
36
16
4
```

## 🔀 flatMap 예제
```java
public static void flatMapExample() {
    List<List<String>> nested = List.of(
        List.of("A", "B"),
        List.of("C", "D"),
        List.of("E")
    );

    List<String> flattened = nested.stream()
                                   .flatMap(List::stream)
                                   .toList();

    System.out.println(flattened); // [A, B, C, D, E]
}
```

###  🔀 flatMap 단계별 흐름
#### 📥 입력 데이터
```java
List<List<String>> nested = List.of(
    List.of("A", "B"),
    List.of("C", "D"),
    List.of("E")
);
```

#### 구조:
```
[
  ["A", "B"],
  ["C", "D"],
  ["E"]
]
```

#### 🔄 스트림 처리 단계
- 1. nested.stream()
    - List<List<String>> → Stream<List<String>>
    - 스트림 내부에는 3개의 리스트가 존재
```
Stream [
  List.of("A", "B"),
  List.of("C", "D"),
  List.of("E")
]
```


- 2. .flatMap(List::stream)
    - 각 내부 리스트를 스트림으로 변환하고, 모든 요소를 하나의 스트림으로 평탄화
```java
Stream.of("A", "B", "C", "D", "E")
```
    - 🔍 flatMap은 내부 스트림을 꺼내서 외부 스트림에 병합하는 역할을 합니다.


- 3. .toList()
    - 최종적으로 하나의 리스트로 수집
```java
List<String> flattened = List.of("A", "B", "C", "D", "E");
```

####  ✅ 최종 출력
```
[A, B, C, D, E]
```

## 🧠 핵심 요약

| 개념             | 설명                                                                 |
|------------------|----------------------------------------------------------------------|
| 스트림은 1회성    | 한 번 소비된 스트림은 재사용할 수 없으며, 다시 생성해야 함            |
| 지연 연산         | 중간 연산은 즉시 실행되지 않고, 최종 연산이 호출될 때 한 번에 처리됨   |
| flatMap           | 중첩된 스트림 또는 컬렉션을 평탄화하여 단일 스트림으로 변환함         |
| Optional          | 값의 존재 유무를 명시적으로 표현하여 null 관련 오류를 방지함         |
| 최종 연산         | 스트림 파이프라인을 종료하고 실제 연산을 수행하여 결과를 생성함       |

## 📦 Optional 예제
```java
public static void optionalExample() {
    Optional<String> name = Optional.of("JungHwan");

    if (name.isPresent()) {
        System.out.println("이름: " + name.get());
    }

    Optional<String> empty = Optional.ofNullable(null);
    System.out.println("비어 있음? " + empty.isEmpty());
}
```


## 🏁 최종 연산 예제
```java
public static void terminalOperations() {
    List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6);

    // collect
    List<Integer> evens = numbers.stream()
                                 .filter(n -> n % 2 == 0)
                                 .collect(Collectors.toList());
    System.out.println("짝수: " + evens);

    // toArray
    Integer[] array = numbers.stream().toArray(Integer[]::new);
    System.out.println("배열: " + Arrays.toString(array));

    // reduce
    int sum = numbers.stream().reduce(0, Integer::sum);
    System.out.println("합계: " + sum);

    // min/max
    numbers.stream().min(Integer::compareTo).ifPresent(min -> System.out.println("최솟값: " + min));
    numbers.stream().max(Integer::compareTo).ifPresent(max -> System.out.println("최댓값: " + max));

    // findFirst
    numbers.stream().filter(n -> n > 3).findFirst().ifPresent(n -> System.out.println("첫 번째 4 이상: " + n));

    // match
    System.out.println("모두 양수? " + numbers.stream().allMatch(n -> n > 0));
}
```


## 📊 기본형 특화 스트림 예제
```java
public static void primitiveStreamExample() {
    IntStream.range(1, 6)
             .map(n -> n * 10)
             .forEach(System.out::println); // 10, 20, 30, 40, 50

    long count = LongStream.of(10L, 20L, 30L)
                           .filter(n -> n > 15)
                           .count();
    System.out.println("15 초과 개수: " + count);
}
```

---





