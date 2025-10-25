# Stream API 정리
Java의 Stream API를 한 번에 정리.  
다양한 자료구조에서 스트림을 생성하고, 이를 활용해 필터링, 매핑, 집계, 병합 등 다양한 작업을 수행하는 방식.

## 🌊 Java Stream API 한눈에 정리
### ✅ 스트림 생성 방법

| 자료구조 유형              | 생성 방식                              | 예시 코드                         |
|---------------------------|----------------------------------------|-----------------------------------|
| `List`, `Set`             | `.stream()`                            | `list.stream()`                   |
| `Map`                     | `.entrySet().stream()`                 | `map.entrySet().stream()`         |
| 배열 (`Object[]`)         | `Arrays.stream(array)` 또는 `Stream.of(array)` | `Stream.of("Java", "Groovy")`     |
| 기본형 배열 (`int[]`)     | `IntStream.of(...)`, `Arrays.stream(int[])` | `IntStream.of(1, 2, 3)`           |
| 숫자 범위                 | `IntStream.range(start, end)` 또는 `rangeClosed()` | `IntStream.range(0, 10)`          |


## 🔧 주요 연산자

| 연산자           | 설명                           | 예시 코드                                |
|------------------|--------------------------------|-------------------------------------------|
| `filter()`       | 조건에 맞는 요소만 추출         | `s -> s.startsWith("J")`                  |
| `map()`          | 요소를 다른 값으로 변환         | `s -> s.toUpperCase()`                    |
| `flatMap()`      | 중첩 구조를 펼쳐서 평탄화       | `s -> Stream.of(s.split(","))`           |
| `sorted()`       | 요소 정렬                       | `(a, b) -> a.length() - b.length()`       |
| `distinct()`     | 중복 제거                       | `list.stream().distinct()`                |
| `limit()`, `skip()` | 일부 요소 선택 또는 건너뛰기 | `stream.limit(5)`                         |
| `peek()`         | 중간 디버깅용 출력              | `peek(System.out::println)`               |

## 📊 집계 및 검사

| 연산자                          | 설명                             | 예시 코드                                 |
|---------------------------------|----------------------------------|--------------------------------------------|
| `count()`                       | 요소 개수를 반환                 | `stream.count()`                           |
| `sum()`, `reduce()`             | 합계 또는 누적 계산              | `stream.reduce((a, b) -> a * b)`           |
| `allMatch()`, `anyMatch()`, `noneMatch()` | 조건 만족 여부 검사     | `s -> s.startsWith("J")`                   |
| `collect()`                     | 결과를 컬렉션으로 수집           | `Collectors.toList()`                      |


## 🧪 샘플 코드 모음
### 1. 문자열 필터링, 대문자 변환, 길이 정렬
```java
List<String> list = Arrays.asList("Java", "Scalar", "Javascript", "Groovy");
list.stream()
    .filter(s -> s.startsWith("J"))
    .map(String::toUpperCase)
    .sorted(Comparator.comparingInt(String::length))
    .forEach(System.out::println);
```


### 2. 숫자 스트림 집계
```java
IntStream stream = IntStream.of(1, 2, 3, 4, 5);
int sum = stream.sum(); // 15
OptionalInt product = IntStream.of(1, 2, 3, 4, 5).reduce((a, b) -> a * b); // 120
```


### 3. 스트림 병합
```java
Stream<String> s1 = Stream.of("Java", "Groovy");
Stream<String> s2 = Stream.of("Javascript", "Clojure");
Stream<String> merged = Stream.concat(s1, s2);
merged.forEach(System.out::println);
```


### 4. 문자열 분할 후 평탄화
```java
List<String> list = Arrays.asList("Java,Groovy", "C#,VB.NET");
list.stream()
    .flatMap(s -> Stream.of(s.split(",")))
    .forEach(System.out::println);
```


### 5. 조건 검사
```java
List<String> list = Arrays.asList("Java", "Scalar", "Javascript", "Groovy");
System.out.println(list.stream().allMatch(s -> s.startsWith("J"))); // false
System.out.println(list.stream().anyMatch(s -> s.startsWith("J"))); // true
System.out.println(list.stream().noneMatch(s -> s.startsWith("J"))); // false
```


## 🧠 정리 포인트
- Stream은 데이터 처리 파이프라인을 구성하는 도구
- 중간 연산은 lazy하게 평가되고, 최종 연산이 실행될 때 실제 처리됨
- Stream은 한 번만 소비 가능 → 재사용하려면 다시 생성해야 함
- IntStream, LongStream, DoubleStream은 기본형 특화 스트림

---
# groupingBy / partitioningBy / parallelStream
Java Stream API의 고급 기능인 Collectors.groupingBy, partitioningBy, 그리고 병렬 스트림(parallelStream())까지 확장.  
이 기능들은 대량 데이터 처리, 조건 기반 분류, 성능 최적화에 매우 유용.

## 🧠 고급 Stream 기능 정리
### ✅ Collectors.groupingBy
- 특정 기준으로 그룹화하는 Collector
- 결과는 Map<기준, List<요소>> 형태
```java
List<String> list = Arrays.asList("Java", "Groovy", "Rust", "Ruby");
Map<Integer, List<String>> grouped = list.stream()
    .collect(Collectors.groupingBy(String::length));


grouped.forEach((len, words) -> System.out.println(len + " → " + words));
```

### 출력 예:
```
4 → [Java, Rust, Ruby]
6 → [Groovy]
```

### ✅ Collectors.partitioningBy
- 조건에 따라 true/false로 분할
- 결과는 Map<Boolean, List<요소>>
```java
List<String> list = Arrays.asList("Java", "Groovy", "Rust", "Ruby");
Map<Boolean, List<String>> partitioned = list.stream()
    .collect(Collectors.partitioningBy(s -> s.startsWith("J")));

partitioned.forEach((key, words) -> System.out.println(key + " → " + words));
```

### 출력 예:
```
true → [Java]
false → [Groovy, Rust, Ruby]
```

### ✅ 병렬 스트림 (parallelStream())
- 멀티코어 CPU를 활용해 병렬 처리
- 성능이 중요한 경우에 사용 (단, 순서 보장 X)
```java
List<String> list = Arrays.asList("Java", "Groovy", "Rust", "Ruby");
list.parallelStream()
    .map(String::toUpperCase)
    .forEach(System.out::println);
```
- 병렬 실행이므로 출력 순서는 매번 달라질 수 있음


## 📌 고급 Stream 기능 요약

| 기능                     | 설명 |
|--------------------------|------|
| `groupingBy()`           | 기준에 따라 그룹화 (`Map<K, List<V>>`) |
| `partitioningBy()`       | 조건에 따라 true/false 분할 (`Map<Boolean, List<V>>`) |
| `parallelStream()`       | 병렬 처리로 성능 향상 (순서 보장 X) |

---



