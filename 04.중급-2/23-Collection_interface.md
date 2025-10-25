# Collection Interface

Java에는 Collection이라는 인터페이스가 분명히 존재합니다.  
다만 직접 사용하는 일이 드물어서 눈에 띄지 않았을 수 있음.  
대부분은 List, Set, Queue 같은 하위 인터페이스나 구현 클래스(ArrayList, HashSet 등)를 통해 간접적으로 사용됩니다.

## 🧠 Java의 Collection 인터페이스란?
- Collection<E>는 Java Collection Framework의 루트 인터페이스입니다.
- java.util 패키지에 포함되어 있으며, 모든 컬렉션 타입의 공통 기능을 정의합니다.
- Iterable<E>를 확장하므로 for-each 루프에서 사용할 수 있습니다.
  
## 📦 주요 기능 — Java Collection 인터페이스

| 메서드               | 설명                                      |
|----------------------|-------------------------------------------|
| `add(E e)`           | 컬렉션에 요소를 추가                      |
| `remove(Object o)`   | 특정 요소를 제거                          |
| `size()`             | 현재 컬렉션의 요소 개수를 반환             |
| `isEmpty()`          | 컬렉션이 비어 있는지 여부 확인             |
| `contains(Object o)` | 특정 요소가 포함되어 있는지 확인           |
| `iterator()`         | 컬렉션을 반복할 수 있는 `Iterator` 반환     |

## 🔗 주요 하위 인터페이스

| 인터페이스 | 특징                         | 대표 구현 클래스               |
|-------------|------------------------------|--------------------------------|
| `List<E>`   | 순서 O, 중복 O                | `ArrayList`, `LinkedList`      |
| `Set<E>`    | 순서 X, 중복 X                | `HashSet`, `TreeSet`           |
| `Queue<E>`  | FIFO 구조, 순서 O             | `LinkedList`, `PriorityQueue`  |

- 이들은 모두 Collection<E>를 확장하며, 각각의 목적에 맞는 자료구조를 제공합니다.


## ✅ 사용 예시
```java
import java.util.Collection;
import java.util.ArrayList;

public class Example {
    public static void main(String[] args) {
        Collection<String> items = new ArrayList<>();
        items.add("apple");
        items.add("banana");

        for (String item : items) {
            System.out.println(item);
        }
    }
}
```

- 여기서 items는 Collection<String> 타입이지만 실제 객체는 ArrayList
- 이렇게 하면 **다형성(polymorphism)** 을 활용해 유연한 코드 작성이 가능

## 🤔 왜 자주 못 봤을까?
- 대부분의 개발자는 List, Set, Map 같은 구체적인 타입을 직접 사용합니다.
- Collection은 공통 인터페이스로서 추상화에 사용되며, 실무에서는 특정 기능에 맞는 하위 타입을 선택하는 경우가 많습니다.
- 예: List<String> names = new ArrayList<>(); → Collection보다 List가 더 구체적이고 직관적
- 결론적으로 Java에서는 Collection<E> 인터페이스를 사용해서 List, Set, Queue 같은 다양한 컬렉션을 하나의 변수 타입으로 받을 수 있습니다.

### ✅ 예시로 설명
```java
import java.util.*;

public class Example {
    public static void main(String[] args) {
        Collection<String> items1 = new ArrayList<>();
        Collection<String> items2 = new HashSet<>();
        Collection<String> items3 = new LinkedList<>();

        items1.add("apple");
        items2.add("banana");
        items3.add("kiwi");

        for (String item : items1) System.out.println(item);
        for (String item : items2) System.out.println(item);
        for (String item : items3) System.out.println(item);
    }
}
```

- items1, items2, items3는 모두 Collection<String> 타입으로 선언
- 각각은 ArrayList, HashSet, LinkedList라는 서로 다른 구현 클래스
- 공통된 인터페이스인 Collection으로 다형성(polymorphism)을 활용해 하나처럼 다룰 수 있음

## 📌 핵심 요약
### 📌 Java Collection 인터페이스 활용 요약

| 특징                         | 설명 |
|------------------------------|------|
| `Collection<E>`는 인터페이스 | 객체가 아니라 공통 타입 정의 |
| 다양한 구현체를 받을 수 있음 | `List`, `Set`, `Queue` 등 |
| 다형성으로 유연한 코드 가능  | 하나의 변수로 여러 타입 처리 |


---


## 🧠 핵심 개념: Stream은 Collection의 확장이 아니다
- Stream은 Collection 인터페이스를 확장하지 않습니다
- 대신, Collection 구현체들은 stream() 메서드를 통해 Stream을 생성할 수 있습니다
- 즉, Stream은 별도의 추상화이며, 데이터 처리 파이프라인을 위한 도구입니다

## ✅ 관계 요약

| 개념       | 설명 또는 예시                          |
|------------|------------------------------------------|
| `Collection` | 데이터를 저장하는 구조 (`List`, `Set`, `Queue`) |
| `Stream`     | 데이터를 처리하는 구조 (`map`, `filter`, `reduce`) |
| 연결 방식    | `Collection.stream()` → `Stream` 생성 가능       |



## 🔍 예시
```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

Stream<String> nameStream = names.stream(); // List → Stream

nameStream
    .filter(name -> name.startsWith("A"))
    .map(String::toUpperCase)
    .forEach(System.out::println);

```
- names.stream()은 List에서 Stream을 생성
- 이후 filter, map, forEach 등으로 처리

## 📦 Stream을 생성할 수 있는 주요 타입

| 타입              | Stream 생성 방식                  |
|-------------------|-----------------------------------|
| `Collection`      | `stream()` → `List`, `Set`, `Queue` 등 |
| `Map`             | `entrySet().stream()` 또는 `keySet().stream()` |
| `Arrays`          | `Arrays.stream(array)` 또는 `Stream.of(array)` |
| `Stream.of(...)`  | 직접 요소를 나열하여 스트림 생성 (`Stream.of(1, 2, 3)`) |


## 📌 결론
### 📌 Stream과 Collection의 관계 요약

- `Stream`은 `Collection`을 확장하지 않음
- `Collection`은 `stream()` 메서드를 통해 `Stream`을 생성할 수 있음
- `Stream`은 데이터를 저장하지 않고, 처리만 담당함

---









