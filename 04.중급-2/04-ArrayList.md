## 📘 ArrayList 주요 메서드 정리

| 메서드                  | 설명                                                  |
|-------------------------|-------------------------------------------------------|
| `add(E e)`              | 리스트 끝에 요소 추가                                 |
| `add(int index, E e)`   | 지정한 위치에 요소 삽입                               |
| `get(int index)`        | 특정 위치의 요소 반환                                 |
| `set(int index, E e)`   | 특정 위치의 요소를 새 값으로 변경                     |
| `remove(int index)`     | 특정 위치의 요소 삭제                                 |
| `remove(Object o)`      | 특정 객체와 같은 요소 삭제                            |
| `size()`                | 리스트의 현재 크기 반환                               |
| `isEmpty()`             | 리스트가 비어 있는지 확인                             |
| `contains(Object o)`    | 특정 요소가 포함되어 있는지 확인                      |
| `indexOf(Object o)`     | 특정 요소의 첫 번째 인덱스 반환                       |
| `clear()`               | 모든 요소 제거                                        |
| `subList(from, to)`     | 부분 리스트 반환 (`from` 포함, `to` 미포함)           |
| `sort()`                | `Collections.sort()`로 정렬                           |
| `equals(Object o)`      | 두 리스트가 같은지 비교                               |
| `clone()`               | 리스트 복제                                           |


### 📌 샘플 예제: 기본 사용법
```java
import java.util.*;

public class ArrayListDemo {
    public static void main(String[] args) {
        ArrayList<String> fruits = new ArrayList<>();

        fruits.add("Apple");
        fruits.add("Banana");
        fruits.add("Cherry");

        System.out.println(fruits.get(1)); // Banana
        fruits.set(1, "Blueberry");
        fruits.remove("Apple");

        System.out.println(fruits); // [Blueberry, Cherry]
        System.out.println("Contains Cherry? " + fruits.contains("Cherry"));
        System.out.println("Size: " + fruits.size());
    }
}
```

### 📌 실전 예제: 정렬, 비교, 필터링
```java
import java.util.*;

public class ArrayListAdvanced {
    public static void main(String[] args) {
        ArrayList<Integer> scores = new ArrayList<>(Arrays.asList(85, 92, 78, 90, 88));

        Collections.sort(scores); // 오름차순 정렬
        System.out.println("Sorted: " + scores);

        ArrayList<Integer> topScores = new ArrayList<>(scores.subList(2, 5));
        System.out.println("Top Scores: " + topScores);

        scores.removeIf(score -> score < 80); // 80 미만 제거
        System.out.println("Filtered: " + scores);

        ArrayList<Integer> clone = (ArrayList<Integer>) scores.clone();
        System.out.println("Cloned: " + clone);
    }
}
```
####  🔹 출력 결과
```
Sorted: [78, 85, 88, 90, 92]
Top Scores: [88, 90, 92]
Filtered: [85, 88, 90, 92]
Cloned: [85, 88, 90, 92]
```

### 📌 팁
- ArrayList는 내부적으로 배열을 사용하며, 자동으로 크기를 조절함
- LinkedList와 비교하면 검색 속도는 빠르지만 삽입/삭제는 느릴 수 있음
- ArrayList는 null 값을 허용하며, 스레드 안전하지 않음

---

### 📌 추가할 주요 메서드
| 메서드 | 설명 |
|-------|------|
| removeIf(Predicate) | 조건을 만족하는 모든 요소 삭제 |
| removeAll(Collection) | 지정한 컬렉션에 포함된 요소 모두 삭제 |
| retainAll(Collection) | 지정한 컬렉션에 포함된 요소만 남기고 삭제 |
| removeFirst() | 첫 번째 요소 삭제 (Java 21+) |
| removeLast() | 마지막 요소 삭제 (Java 21+) |
| getFirst() | 첫 번째 요소 반환 (Java 21+) |
| getLast() | 마지막 요소 반환 (Java 21+) |
| forEach(Consumer) | 모든 요소에 지정한 작업 수행 |
| replaceAll(UnaryOperator) | 모든 요소를 지정한 함수의 결과로 변경 |
| addAll(Collection) | 다른 컬렉션의 모든 요소 추가 |
| addAll(int, Collection) | 지정한 위치에 다른 컬렉션의 요소 삽입 |
| containsAll(Collection) | 지정한 컬렉션의 모든 요소가 포함되어 있는지 확인 |
| lastIndexOf(Object) | 특정 요소의 마지막 인덱스 반환 |
| toArray() | 리스트를 배열로 변환 |
| toArray(T[]) | 지정한 타입의 배열로 변환 |
| iterator() | 순방향 반복자 반환 |
| listIterator() | 양방향 리스트 반복자 반환 |
| stream() | 순차 스트림 생성 |
| parallelStream() | 병렬 스트림 생성 |
| spliterator() | 요소 순회 및 분할을 위한 Spliterator 반환 |
| ensureCapacity(int) | 내부 배열의 최소 용량 확보 |
| trimToSize() | 내부 배열의 용량을 현재 요소 수에 맞게 축소 |
| isEmpty() | 리스트가 비어 있는지 확인 (기존 표에 포함됨) |

### 📌 1. removeIf() — 조건에 따른 삭제
```java
ArrayList<Integer> values =
    new ArrayList<>(List.of(1, 2, 3, 4, 5, 6));

values.removeIf(x -> x % 2 == 0);

System.out.println(values);
// [1, 3, 5]
```
### 📌 2. replaceAll() — 전체 요소 변환
```java
ArrayList<Integer> values =
    new ArrayList<>(List.of(1, 2, 3));

values.replaceAll(x -> x * 10);

System.out.println(values);
// [10, 20, 30]
```
### 📌 3. retainAll() — 교집합에 해당하는 요소만 유지
```java
ArrayList<Integer> values =
    new ArrayList<>(List.of(1, 2, 3, 4, 5));

values.retainAll(List.of(2, 4, 6));

System.out.println(values);
// [2, 4]
```
----



