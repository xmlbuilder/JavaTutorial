# Java Stream 1

## 📘 자바 스트림 API 기본 정리

### ✅ 스트림 API란?

| 항목             | 설명                                                                 |
|------------------|----------------------------------------------------------------------|
| 정의             | 자바 8부터 도입된 기능으로, 데이터 흐름을 추상화하여 처리하는 도구 |
| 데이터 소스      | 컬렉션(Collection), 배열 등                                          |
| 처리 방식        | 중간 연산과 최종 연산으로 구성된 파이프라인 방식                    |
| 특징             | 불변성, 일회성, 지연 연산, 병렬 처리 가능                            |
| 목적             | 선언형 프로그래밍 방식으로 데이터 처리 로직을 간결하게 표현          |

### 🔧 스트림 생성
```rust
List<String> names = List.of("Apple", "Banana", "Berry", "Tomato");
Stream<String> stream = names.stream();
```


### 🔄 중간 연산 (Intermediate Operations)

| 연산 이름 | 설명                                       | 예시 코드                             |
|-----------|--------------------------------------------|----------------------------------------|
| `filter`    | 조건에 맞는 요소만 남김                    | `.filter(i -> i % 2 == 0)`             |
| `map`       | 요소를 다른 형태로 변환                    | `.map(i -> i * 10)`                    |
| `sorted`    | 요소를 정렬                                | `.sorted()` 또는 `.sorted(Comparator)`|
| `distinct`  | 중복 제거                                   | `.distinct()`                          |
| `peek`      | 중간에 요소를 엿보고 디버깅용으로 사용     | `.peek(System.out::println)`          |
| `limit`     | 지정한 개수만큼 요소를 제한                | `.limit(3)`                            |
| `skip`      | 지정한 개수만큼 요소를 건너뜀              | `.skip(2)`                             |

```java
names.stream()
     .filter(name -> name.startsWith("B"))
     .map(String::toUpperCase)
```


### 🏁 최종 연산 (Terminal Operations)

| 연산 이름   | 설명                                           | 예시 코드                              |
|-------------|------------------------------------------------|-----------------------------------------|
| `toList`      | 스트림 결과를 리스트로 수집                    | `.toList()`                             |
| `forEach`     | 각 요소에 대해 지정된 동작 수행                | `.forEach(System.out::println)`         |
| `findFirst`   | 첫 번째 요소를 Optional로 반환                 | `.findFirst().get()`                    |
| `count`       | 요소 개수를 반환                               | `.count()`                              |
| `anyMatch`    | 하나라도 조건을 만족하면 true 반환             | `.anyMatch(i -> i > 10)`                |
| `allMatch`    | 모든 요소가 조건을 만족하면 true 반환          | `.allMatch(i -> i > 0)`                 |
|` noneMatch`   | 모든 요소가 조건을 만족하지 않으면 true 반환   | `.noneMatch(i -> i < 0)`                |
| `reduce`      | 누적 연산을 통해 하나의 결과로 축소            | `.reduce((a, b) -> a + b)`              |
| `collect`     | Collector를 사용해 다양한 형태로 수집          | `.collect(Collectors.toSet())`          |

```java
List<String> result = names.stream()
     .filter(name -> name.startsWith("B"))
     .map(String::toUpperCase)
     .toList();
```


### 🔁 반복 방식

| 반복 방식     | 설명                                                   | 예시 코드                                 |
|---------------|--------------------------------------------------------|--------------------------------------------|
| 외부 반복     | 개발자가 직접 for/while 문을 작성하여 요소를 반복함   | `for (String s : result) { ... }`          |
| 내부 반복     | 스트림이 알아서 반복하며 람다 또는 메서드 참조를 수행 | `stream.forEach(System.out::println)`      |


### 🔗 메서드 참조

| 참조 형태                        | 예시 코드               | 설명                                       | 람다 변환 방식                          |
|----------------------------------|--------------------------|--------------------------------------------|----------------------------------------|
| 특정 객체의 인스턴스 메서드 참조 | `System.out::println`    | 고정된 객체(System.out)의 메서드 참조      | `s -> System.out.println(s)`           |
| 임의 객체의 인스턴스 메서드 참조 | `String::toUpperCase`    | 매개변수로 전달된 String 객체의 메서드 참조 | `s -> s.toUpperCase()`                 |
| 정적 메서드 참조                 | `Math::max`              | 클래스의 정적 메서드 참조                  | `(a, b) -> Math.max(a, b)`             |
| 생성자 참조                      | `ArrayList::new`         | 객체 생성자 참조                           | `() -> new ArrayList()`                |

---


## 🧪 샘플 예제 모음
### 1. 기본 필터 + 매핑 + 수집
```java
List<String> result = names.stream()
    .filter(name -> name.startsWith("B"))
    .map(String::toUpperCase)
    .toList();
```


### 2. 외부 반복 vs 내부 반복
```java
// 외부 반복
for (String s : result) {
    System.out.println(s);
}
```
```java
// 내부 반복
names.stream()
    .filter(name -> name.startsWith("B"))
    .map(String::toUpperCase)
    .forEach(System.out::println);
```

### 3. 즉시 연산 vs 지연 연산
```java
// MyStreamV3는 즉시 연산
MyStreamV3.of(data)
    .filter(...)
    .map(...)
    .getFirst(); // 모든 연산이 즉시 수행됨

// 자바 스트림은 지연 연산
data.stream()
    .filter(...)
    .map(...)
    .findFirst().get(); // 최종 연산 시점에만 수행됨
```


### 4. 단축 평가 (Short-Circuiting)
```java
Integer result = data.stream()
    .filter(i -> i % 2 == 0)
    .map(i -> i * 10)
    .findFirst()
    .get(); // 조건 만족 시점에 바로 종료
```

## 🧠 핵심 개념 요약

| 개념             | 설명                                                                 |
|------------------|----------------------------------------------------------------------|
| 선언형 프로그래밍 | "무엇을 할지"에 집중하는 방식. 반복 방식보다 결과 중심의 코드 작성 가능 |
| 파이프라인 처리   | 요소가 중간 연산을 거쳐 최종 연산까지 흐르듯 처리됨                 |
| 지연 연산         | 최종 연산이 호출되기 전까지 중간 연산은 실행되지 않음               |
| 단축 평가         | 조건을 만족하는 순간 연산을 종료하여 불필요한 계산을 줄임           |
| 불변성            | 스트림은 원본 데이터를 변경하지 않고 새로운 결과를 생성함           |

---


## 실전 샘플 함수 모음
### 1. 필터 + 매핑 + 수집 (기본 흐름)
```java
public static List<String> filterAndMapToList(List<String> names) {
    return names.stream()
                .filter(name -> name.startsWith("B"))
                .map(String::toUpperCase)
                .toList();
}
```


### 2. 내부 반복 (forEach)
```java
public static void printFilteredNames(List<String> names) {
    names.stream()
         .filter(name -> name.startsWith("B"))
         .map(String::toUpperCase)
         .forEach(System.out::println);
}
```


### 3. 외부 반복
```java
public static void printWithForLoop(List<String> names) {
    List<String> result = names.stream()
                               .filter(name -> name.startsWith("B"))
                               .map(String::toUpperCase)
                               .toList();
    for (String name : result) {
        System.out.println(name);
    }
}
```


### 4. 단축 평가 (findFirst)
```java
public static Optional<Integer> findFirstEvenMultiplied(List<Integer> numbers) {
    return numbers.stream()
                  .filter(n -> n % 2 == 0)
                  .map(n -> n * 10)
                  .findFirst();
}
```


### 5. 지연 연산 확인용 (최종 연산 없이)
```java
public static void lazyEvaluationDemo(List<Integer> numbers) {
    numbers.stream()
           .filter(n -> {
               System.out.println("filter: " + n);
               return n % 2 == 0;
           })
           .map(n -> {
               System.out.println("map: " + n);
               return n * 10;
           });
    System.out.println("최종 연산이 없으므로 아무 것도 실행되지 않음");
}
```


### 6. 병렬 스트림 예시
```java
public static void parallelProcessing(List<Integer> numbers) {
    numbers.parallelStream()
           .filter(n -> n % 2 == 0)
           .map(n -> n * 10)
           .forEach(System.out::println);
}
```


### 7. reduce 사용 예시
```java
public static int sumEvenNumbers(List<Integer> numbers) {
    return numbers.stream()
                  .filter(n -> n % 2 == 0)
                  .reduce(0, Integer::sum);
}
```


## 🧪 전체 테스트 코드 예시
```java
import java.util.*;
import java.util.stream.*;
import static java.util.stream.Collectors.*;

public class StreamApiExamples {

    // 1. 필터 + 매핑 + 수집
    public static List<String> filterAndMapToList(List<String> names) {
        return names.stream()
                    .filter(name -> name.startsWith("B"))
                    .map(String::toUpperCase)
                    .toList();
    }

    // 2. 내부 반복
    public static void printFilteredNames(List<String> names) {
        names.stream()
             .filter(name -> name.startsWith("B"))
             .map(String::toUpperCase)
             .forEach(System.out::println);
    }

    // 3. 외부 반복
    public static void printWithForLoop(List<String> names) {
        List<String> result = filterAndMapToList(names);
        for (String name : result) {
            System.out.println(name);
        }
    }

    // 4. 단축 평가
    public static Optional<Integer> findFirstEvenMultiplied(List<Integer> numbers) {
        return numbers.stream()
                      .filter(n -> n % 2 == 0)
                      .map(n -> n * 10)
                      .findFirst();
    }

    // 5. 지연 연산 확인용
    public static void lazyEvaluationDemo(List<Integer> numbers) {
        numbers.stream()
               .filter(n -> {
                   System.out.println("filter: " + n);
                   return n % 2 == 0;
               })
               .map(n -> {
                   System.out.println("map: " + n);
                   return n * 10;
               });
        System.out.println("최종 연산이 없으므로 아무 것도 실행되지 않음");
    }

    // 6. 병렬 스트림
    public static void parallelProcessing(List<Integer> numbers) {
        numbers.parallelStream()
               .filter(n -> n % 2 == 0)
               .map(n -> n * 10)
               .forEach(System.out::println);
    }

    // 7. reduce 사용
    public static int sumEvenNumbers(List<Integer> numbers) {
        return numbers.stream()
                      .filter(n -> n % 2 == 0)
                      .reduce(0, Integer::sum);
    }

    // 8. collect 사용
    public static Set<String> collectToSet(List<String> names) {
        return names.stream()
                    .filter(name -> name.length() > 5)
                    .collect(toSet());
    }

    // main() 메서드
    public static void main(String[] args) {
        List<String> fruits = List.of("Apple", "Banana", "Berry", "Tomato");
        List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6);

        System.out.println("1. filterAndMapToList:");
        System.out.println(filterAndMapToList(fruits));

        System.out.println("\n2. printFilteredNames:");
        printFilteredNames(fruits);

        System.out.println("\n3. printWithForLoop:");
        printWithForLoop(fruits);

        System.out.println("\n4. findFirstEvenMultiplied:");
        System.out.println(findFirstEvenMultiplied(numbers).orElse(-1));

        System.out.println("\n5. lazyEvaluationDemo:");
        lazyEvaluationDemo(numbers);

        System.out.println("\n6. parallelProcessing:");
        parallelProcessing(numbers);

        System.out.println("\n7. sumEvenNumbers:");
        System.out.println(sumEvenNumbers(numbers));

        System.out.println("\n8. collectToSet:");
        System.out.println(collectToSet(fruits));
    }
}
```

----





