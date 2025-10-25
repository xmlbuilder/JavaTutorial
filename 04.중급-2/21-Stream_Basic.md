# 🚀 Java Stream API란?
Java Stream은 데이터 처리 파이프라인을 구성하는 방식으로, 컬렉션이나 배열의 요소를 함수형 스타일로 처리할 수 있게 해줍니다.
- 선언적: 반복문 없이 처리 흐름을 선언
- 체이닝: 여러 연산을 연결해 처리
- 병렬 처리 가능: `.parallelStream()` 으로 병렬화 가능

## 🔍 예제 코드 해석
### 1. 문자열 스트림 처리
```java
Stream<String> fruitStream = Stream.of("apple", "banana", "kiwi", "orange");

fruitStream
    .filter(s -> s.contains("a"))         // "apple", "banana", "orange"
    .map(String::toUpperCase)             // "APPLE", "BANANA", "ORANGE"
    .sorted()                             // "APPLE", "BANANA", "ORANGE" (정렬됨)
    .forEach(System.out::println);        // 각 요소 출력

```

- filter() → 조건에 맞는 요소만 통과
- map() → 요소 변환
- sorted() → 정렬
- forEach() → 최종 처리 (출력)

## 2. 정수 리스트에서 홀수 개수 세기
```java
List<Integer> integerList = Arrays.asList(0, 1, 2, 3, 42);

long howManyCount = integerList.stream()
    .filter(e -> (e % 2) == 1)  // 홀수만 필터링: 1, 3
    .count();                   // 개수 세기 → 2

System.out.println(howManyCount); // 출력: 2
```


## 📦 Java Stream 기본 메서드 요약

| 메서드        | 설명                                      |
|---------------|-------------------------------------------|
| `Stream.of()` | 스트림 생성 (배열, 값 등으로부터)         |
| `filter()`    | 조건에 맞는 요소만 통과                   |
| `map()`       | 요소를 다른 값으로 변환                   |
| `sorted()`    | 요소 정렬                                 |
| `forEach()`   | 각 요소에 대해 작업 수행 (최종 연산)      |
| `count()`     | 요소 개수 반환 (최종 연산)                |

## 🧠 핵심 요약
- Stream은 데이터 흐름을 함수형으로 처리하는 도구
- 중간 연산 (filter, map, sorted)은 스트림을 변형
- 최종 연산 (forEach, count)은 결과를 소비

---

# collect()

Java Stream의 고급 연산인 collect(), reduce(), flatMap()은 데이터를 집계하거나 변형할 때 매우 강력한 도구입니다.  
각각의 역할과 예제를 아래에 정리.

## 🧲 collect() — 결과를 모으는 최종 연산
- 역할: 스트림의 결과를 List, Set, Map 등으로 수집
- 자주 사용하는 Collector:
- Collectors.toList()
- Collectors.toSet()
- Collectors.joining(", ")

### ✅ 예제
```java
List<String> fruits = Stream.of("apple", "banana", "kiwi")
    .filter(f -> f.contains("a"))
    .collect(Collectors.toList());
```

- 결과: `["apple", "banana"]`

## 🧮 reduce() — 누적 계산
- 역할: 스트림의 모든 요소를 하나의 결과로 축소
- 형식: reduce(identity, accumulator)
- 자주 사용되는 용도: 합계, 곱셈, 문자열 연결 등
### ✅ 예제
```java
int sum = Stream.of(1, 2, 3, 4, 5)
    .reduce(0, (a, b) -> a + b);
```

- 결과: `15`

## 🧹 flatMap() — 중첩 구조 평탄화
- 역할: 스트림의 각 요소를 또 다른 스트림으로 변환하고, 이를 하나의 스트림으로 병합
- 차이점: map()은 1:1 변환, flatMap()은 1:N 변환
### ✅ 예제 1: 리스트 평탄화
```java
List<List<String>> listOfLists = Arrays.asList(
    Arrays.asList("apple", "banana"),
    Arrays.asList("kiwi", "orange")
);

List<String> flatList = listOfLists.stream()
    .flatMap(List::stream)
    .collect(Collectors.toList());
```

- 결과: `["apple", "banana", "kiwi", "orange"]`

## ✅ 예제 2: 문자열 → 문자 스트림
```java
List<String> words = Arrays.asList("java", "stream");

List<Character> chars = words.stream()
    .flatMap(word -> word.chars().mapToObj(c -> (char) c))
    .collect(Collectors.toList());
```

- 결과: `['j','a','v','a','s','t','r','e','a','m']`

## 📌 요약 표
### 📌 Stream 고급 연산 요약

| 메서드      | 역할                          | 예시 결과                      |
|-------------|-------------------------------|--------------------------------|
| `collect()` | 스트림 → 컬렉션                | `List`, `Set`, `Map` 등으로 수집 |
| `reduce()`  | 누적 계산                      | 합계, 곱셈, 문자열 연결 등     |
| `flatMap()` | 중첩 구조 → 평탄화된 스트림     | 리스트 → 요소 스트림           |

---



