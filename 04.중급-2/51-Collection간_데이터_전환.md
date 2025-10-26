# Collection간 데이터 전환
Java에서 Collection, Array, Stream 간의 전환은 매우 자주 사용되는 기능.  
아래에 주요 전환 방식들을 정리하고, 예시와 함께 표로 깔끔하게 정리.  

## 🔁 Java Collection/Array/Stream 전환 요약
| 변환 방향                     | 코드 예시                                                       | 설명                                      |
|------------------------------|------------------------------------------------------------------|-------------------------------------------|
| `List → Array`               | `String[] arr = list.toArray(new String[0]);`                   | 리스트를 배열로 변환                       |
| `List → Array (Stream)`      | `String[] arr = list.stream().toArray(String[]::new);`          | 스트림을 활용해 배열로 변환                |
| `Array → List`               | `List<String> list = Arrays.asList(array);`                     | 배열을 리스트로 변환 (고정 크기)           |
| `Array → ArrayList`          | `ArrayList<String> list = new ArrayList<>(Arrays.asList(array));`| 배열을 가변 크기 리스트로 변환             |
| `Set → List`                 | `List<String> list = new ArrayList<>(set);`                     | Set을 List로 변환                          |
| `List → Set`                 | `Set<String> set = new HashSet<>(list);`                        | 중복 제거를 위해 리스트를 Set으로 변환     |
| `Collection → Stream`        | `list.stream()`                                                 | 컬렉션을 스트림으로 변환                   |
| `Stream → List`              | `List<String> list = stream.collect(Collectors.toList());`      | 스트림 결과를 리스트로 수집                |
| `Stream → Set`               | `Set<String> set = stream.collect(Collectors.toSet());`         | 스트림 결과를 Set으로 수집                 |
| `Array → Stream`             | `Stream<String> stream = Arrays.stream(array);`                 | 배열을 스트림으로 변환                     |

## 예시
```java
String[] result = list.stream().toArray(String[]::new);
String[] array = arrayList.toArray(new String[arrayList.size()]);
ArrayList<String> arrayList = new ArrayList<>(Arrays.asList(array));
```

## 🧠 팁
- Arrays.asList()는 `고정 크기 리스트` 를 반환하므로 add()나 remove()는 예외 발생할 수 있음.
- new ArrayList<>(Arrays.asList(...))를 사용하면 가변 리스트로 안전하게 변환 가능.
- toArray(new String[0])는 타입 안전하고 성능도 좋습니다.
- Stream은 중간 연산과 최종 연산을 통해 유연한 데이터 처리 가능

---
