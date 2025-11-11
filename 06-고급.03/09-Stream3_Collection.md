# 📦 자바 스트림 API3 – 컬렉터 정리
## ✅ 1. Collectors란?
- 스트림의 최종 연산 중 하나로, 결과를 수집하는 데 사용
- collect() 메서드와 함께 사용하며, 다양한 자료구조나 통계로 수집 가능
- 대부분의 기능은 Collectors 클래스에 구현되어 있음

## 🧰 2. 주요 수집 방식

| 목적             | 메서드 예시                                      | 설명                                             | 반환 타입                          |
|------------------|--------------------------------------------------|--------------------------------------------------|------------------------------------|
| 리스트 수집       | `toList()`, `toUnmodifiableList()`               | 스트림을 수정 가능/불가능 리스트로 수집           | `List<T>`                          |
| 집합 수집         | `toSet()`, `toCollection(TreeSet::new)`          | 중복 제거, 특정 Set 타입 지정 가능                | `Set<T>`                           |
| 맵 수집           | `toMap(keyMapper, valueMapper)`                  | 키-값 쌍으로 수집, 병합 함수 및 맵 타입 지정 가능 | `Map<K, V>`                        |
| 그룹화            | `groupingBy(classifier)`                         | 기준에 따라 그룹화                               | `Map<K, List<T>>`                  |
| 분할              | `partitioningBy(predicate)`                      | true/false 기준으로 2개 그룹으로 분할             | `Map<Boolean, List<T>>`            |
| 통계              | `counting()`, `summingInt()`, `averagingInt()`   | 개수, 합계, 평균, 통계 객체 수집                  | `Long`, `Double`, `IntSummaryStatistics` |
| 문자열 연결       | `joining(delimiter, prefix, suffix)`             | 문자열 스트림을 하나로 연결                      | `String`                           |
| 리듀싱            | `reducing(...)`                                  | 요소를 하나로 합침                               | `Optional<T>` 또는 지정 타입       |


## 🧪 3. 기본 수집 예제
```java
List<String> list = Stream.of("Java", "Spring", "JPA")
                          .collect(Collectors.toList());

Set<Integer> set = Stream.of(1, 2, 2, 3)
                         .collect(Collectors.toSet());

Set<Integer> treeSet = Stream.of(3, 1, 2)
                             .collect(Collectors.toCollection(TreeSet::new));
```

## 🗺️ 4. Map 수집 예제
```java
Map<String, Integer> map = Stream.of("Apple", "Banana")
    .collect(Collectors.toMap(
        name -> name,
        name -> name.length()
    ));
```
- 중복 키가 있을 경우 병합 함수 필요:
```
(oldVal, newVal) -> oldVal + newVal
```
- 맵 타입 지정:
```java
LinkedHashMap::new
```

## 🧮 5. 그룹화 & 분할 예제
```java
Map<String, List<String>> grouped = names.stream()
    .collect(Collectors.groupingBy(name -> name.substring(0, 1)));
```
```java
Map<Boolean, List<Integer>> partitioned = numbers.stream()
    .collect(Collectors.partitioningBy(n -> n % 2 == 0));
```

## 📊 6. 통계 수집 예제
```java
long count = Stream.of(1, 2, 3).collect(Collectors.counting());
```
```java
double avg = Stream.of(1, 2, 3).collect(Collectors.averagingInt(i -> i));
```
```java
IntSummaryStatistics stats = Stream.of("Apple", "Banana", "Tomato")
    .collect(Collectors.summarizingInt(String::length));
```


## 🔁 7. 리듀싱 & 문자열 연결
```java
String joined = names.stream()
    .collect(Collectors.reducing((s1, s2) -> s1 + "," + s2)).get();
```
```java
String joined2 = names.stream()
    .collect(Collectors.joining(", "));
```


## 🧩 8. 다운스트림 컬렉터

그룹화된 각 그룹 내부에서 추가 연산을 수행하는 컬렉터

| 목적                     | 예시 코드                                                                 | 반환 타입                    |
|--------------------------|---------------------------------------------------------------------------|------------------------------|
| 그룹별 요소 개수 세기     | `groupingBy(Student::getGrade, counting())`                              | `Map<Integer, Long>`         |
| 그룹별 평균 점수 계산     | `groupingBy(Student::getGrade, averagingInt(Student::getScore))`         | `Map<Integer, Double>`       |
| 그룹별 이름 리스트 수집   | `groupingBy(Student::getGrade, mapping(Student::getName, toList()))`     | `Map<Integer, List<String>>` |


## 🧠 핵심 요약

| 개념               | 설명                                                                 |
|--------------------|----------------------------------------------------------------------|
| 스트림은 1회성      | 한 번 소비된 스트림은 재사용할 수 없으며, 다시 생성해야 함            |
| 지연 연산           | 중간 연산은 즉시 실행되지 않고, 최종 연산이 호출될 때 한 번에 처리됨   |
| flatMap            | 중첩 구조를 평탄화하여 단일 스트림으로 변환함                         |
| Optional           | 값의 존재 유무를 명시적으로 표현하여 null 관련 오류를 방지함         |
| Collector          | 스트림 결과를 리스트, 맵, 통계 등 다양한 형태로 수집하는 도구         |
| 다운스트림 컬렉터   | 그룹화된 각 그룹 내부에서 추가 연산을 수행하는 컬렉터                 |

---

# groupingBy

자바 스트림 API에서 groupingBy()는 실전에서 가장 자주 쓰이는 Collector 중 하나입니다.  
아래에 기초부터 고급까지 단계별로 설명하고, 실전 샘플 예제도 함께 정리.

## 🧩 groupingBy()란?
- Collectors.groupingBy(classifier)는 스트림 요소를 **특정 기준(classifier)** 에 따라 그룹화합니다.
- 반환 결과는 `Map<K, List<T>>` 형태이며, K는 그룹 키, T는 원래 요소입니다.
- 다운스트림 컬렉터를 함께 사용하면 그룹 내부에서 추가 연산도 가능합니다.

## 🧪 기본 예제: 문자열 첫 글자 기준 그룹화
```java
List<String> names = List.of("Apple", "Avocado", "Banana", "Blueberry", "Cherry");

Map<String, List<String>> grouped = names.stream()
    .collect(Collectors.groupingBy(name -> name.substring(0, 1)));

System.out.println(grouped);
// 결과: {A=[Apple, Avocado], B=[Banana, Blueberry], C=[Cherry]}
```
- name -> name.substring(0, 1)은 첫 글자를 기준으로 그룹화
- 결과는 `Map<String, List<String>>`

## 🧪 다운스트림 예제 1: 그룹별 개수 세기
```java
Map<Integer, Long> countByGrade = students.stream()
    .collect(Collectors.groupingBy(
        Student::getGrade,
        Collectors.counting()
    ));
```
- Map<Integer, Long> 형태로 학년별 학생 수를 반환

## 🧪 다운스트림 예제 2: 그룹별 평균 점수
```java
Map<Integer, Double> avgScoreByGrade = students.stream()
    .collect(Collectors.groupingBy(
        Student::getGrade,
        Collectors.averagingInt(Student::getScore)
    ));
```
- Map<Integer, Double> 형태로 학년별 평균 점수 반환

## 🧪 다운스트림 예제 3: 그룹별 이름 리스트
```java
Map<Integer, List<String>> namesByGrade = students.stream()
    .collect(Collectors.groupingBy(
        Student::getGrade,
        Collectors.mapping(Student::getName, Collectors.toList())
    ));
```

- mapping()을 통해 Student → String으로 변환 후 리스트로 수집

## 🧪 다운스트림 예제 4: 그룹별 통계 객체 수집
```java
Map<Integer, IntSummaryStatistics> statsByGrade = students.stream()
    .collect(Collectors.groupingBy(
        Student::getGrade,
        Collectors.summarizingInt(Student::getScore)
    ));
```
- IntSummaryStatistics 객체로 개수, 합계, 평균, 최솟값, 최댓값을 한 번에 수집

## 🧠 핵심 요약

| 기능               | 설명                                                                 |
|--------------------|----------------------------------------------------------------------|
| `groupingBy()`       | 기준 함수(classifier)에 따라 스트림 요소를 그룹화하여 Map으로 수집함 |
| `counting()`         | 그룹 또는 전체 스트림의 요소 개수를 셈                                |
| `averagingInt()`     | 그룹 또는 전체 요소의 정수 속성 평균값을 계산                         |
| `mapping()`          | 요소를 다른 값으로 변환한 후, 변환된 값들을 다운스트림으로 수집함     |
| `summarizingInt()`   | 개수, 합계, 평균, 최솟값, 최댓값을 포함한 통계 객체(IntSummaryStatistics)로 수집 |

---
# Collection

자바 스트림 API에서 Collectors.toCollection()을 활용하면 다양한 컬렉션 구현체로 스트림 결과를 수집할 수 있습니다.  
아래에 대표적인 컬렉션별로 실전 샘플 예제를 정리.

## 🧺 다양한 컬렉션 수집 예제

### 1️⃣ ArrayList로 수집 (기본 toList와 동일)
```java
List<String> list = Stream.of("Java", "Spring", "JPA")
    .collect(Collectors.toCollection(ArrayList::new));

System.out.println(list); // [Java, Spring, JPA]
```
- Collectors.toList()와 결과는 같지만, 명시적으로 ArrayList를 지정할 수 있음

### 2️⃣ LinkedList로 수집
```java
List<String> linkedList = Stream.of("A", "B", "C")
    .collect(Collectors.toCollection(LinkedList::new));

System.out.println(linkedList); // [A, B, C]
```
- 큐나 덱 구조로 활용할 때 유용

### 3️⃣ TreeSet으로 수집 (정렬된 Set)
```java
Set<Integer> sortedSet = Stream.of(5, 3, 1, 4, 2)
    .collect(Collectors.toCollection(TreeSet::new));

System.out.println(sortedSet); // [1, 2, 3, 4, 5]
```
- 자동 정렬됨 (Comparable 기준)

### 4️⃣ LinkedHashSet으로 수집 (중복 제거 + 순서 유지)
```java
Set<String> orderedSet = Stream.of("B", "A", "C", "A")
    .collect(Collectors.toCollection(LinkedHashSet::new));

System.out.println(orderedSet); // [B, A, C]
```
- 중복 제거하면서 입력 순서 유지

### 5️⃣ PriorityQueue로 수집 (우선순위 큐)
```java
Queue<Integer> priorityQueue = Stream.of(5, 1, 3, 2, 4)
    .collect(Collectors.toCollection(PriorityQueue::new));

System.out.println(priorityQueue.poll()); // 1 (가장 작은 값부터 꺼냄)
```
- 내부적으로 힙 구조로 정렬됨

### 6️⃣ Stack으로 수집 (LIFO 구조)
```java
Stack<String> stack = Stream.of("first", "second", "third")
    .collect(Collectors.toCollection(Stack::new));

System.out.println(stack.pop()); // third
```

- 후입선출(LIFO) 방식으로 활용 가능

## 🧠 핵심 요약 – 다양한 컬렉션 수집

| 컬렉션 타입         | 설명                                      | 수집 방식 예시                            |
|----------------------|-------------------------------------------|--------------------------------------------|
| `ArrayList`            | 기본 리스트, 빠른 인덱스 접근              | `toCollection(ArrayList::new)`             |
| `LinkedList`           | 큐/덱 구조에 적합, 삽입/삭제에 유리        | `toCollection(LinkedList::new)`            |
| `TreeSet`              | 자동 정렬된 Set, 중복 제거                | `toCollection(TreeSet::new)`               |
| `LinkedHashSet`        | 중복 제거 + 입력 순서 유지                | `toCollection(LinkedHashSet::new)`         |
| `PriorityQueue`        | 우선순위 기반 정렬 큐                     | `toCollection(PriorityQueue::new)`         |
| `Stack`                | 후입선출(LIFO) 구조, 스택 연산에 적합      | `toCollection(Stack::new)`                 |

---
# partitioningBy

아래는 Collectors.partitioningBy()의 실전 예제와 함께 코드 설명 + 출력 결과를 정리한 내용입니다.

## 🧩 partitioningBy() 실전 예제
### 🎯 목적
조건식(Predicate)에 따라 스트림 요소를 true / false 두 그룹으로 분할합니다.
반환 타입은 항상 `Map<Boolean, List<T>>` 또는 `Map<Boolean, R>` 입니다.

### 💡 예제: 짝수와 홀수 분할
```java
import java.util.*;
import java.util.stream.Collectors;

public class PartitioningExample {
    public static void main(String[] args) {
        List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6);

        Map<Boolean, List<Integer>> partitioned = numbers.stream()
            .collect(Collectors.partitioningBy(n -> n % 2 == 0));

        System.out.println("짝수 그룹: " + partitioned.get(true));
        System.out.println("홀수 그룹: " + partitioned.get(false));
        System.out.println("전체 맵: " + partitioned);
    }
}
```

### 🖨️ 출력 결과
```
짝수 그룹: [2, 4, 6]
홀수 그룹: [1, 3, 5]
전체 맵: {false=[1, 3, 5], true=[2, 4, 6]}
```

## 🧠 핵심 요약 – partitioningBy()

| 기능               | 설명                                                                 |
|--------------------|----------------------------------------------------------------------|
| partitioningBy()   | 조건식(Predicate)에 따라 요소를 true/false 두 그룹으로 분할           |
| 반환 타입          | `Map<Boolean, List<T>>` 형태로 결과를 저장                            |
| 활용 예시          | 짝수/홀수, 성인/미성년자, 유효/무효 등 이진 분류에 적합               |

---


