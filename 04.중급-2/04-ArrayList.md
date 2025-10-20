## 🧰 ArrayList 주요 메서드 정리

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


## 🧪 샘플 예제: 기본 사용법
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

## 🧑‍💻 실전 예제: 정렬, 비교, 필터링
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
###  출력 결과
```
Sorted: [78, 85, 88, 90, 92]
Top Scores: [88, 90, 92]
Filtered: [85, 88, 90, 92]
Cloned: [85, 88, 90, 92]

```

## 📌 팁
- ArrayList는 내부적으로 배열을 사용하며, 자동으로 크기를 조절함
- LinkedList와 비교하면 검색 속도는 빠르지만 삽입/삭제는 느릴 수 있음
- ArrayList는 null 값을 허용하며, 스레드 안전하지 않음

