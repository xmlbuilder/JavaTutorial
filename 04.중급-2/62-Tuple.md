# Tuple  구현
Java에서 튜플(Tuple)을 구현하는 대표적인 라이브러리인 **JavaTuples** 를 활용한 예제.  
아래에 튜플의 개념, Java에서의 구현 방식, 주요 클래스, 사용 예시를 표와 함께 정리.

## 🔢 Java에서 Tuple 구현 방법
Java는 기본적으로 튜플을 지원하지 않지만, 외부 라이브러리인 JavaTuples를 사용하면 간편하게 구현할 수 있음.
### ✅ 주요 튜플 클래스
| 클래스 이름   | 요소 개수 | 예시 생성 방식                  |
|---------------|-----------|---------------------------------|
| `Unit<A>`     | 1         | `Unit.with(a)`                 |
| `Pair<A,B>`   | 2         | `Pair.with(a, b)`              |
| `Triplet<A,B,C>` | 3      | `Triplet.with(a, b, c)`        |
| `Quartet<A,B,C,D>` | 4    | `Quartet.with(a, b, c, d)`     |
| `Quintet<A,B,C,D,E>` | 5  | `Quintet.with(a, b, c, d, e)`  |
| `Sextet` ~ `Decade` | 6~10 | 최대 10개까지 지원             |


### 🧪 예시 코드 요약
```java
Triplet<Integer, Integer, Float> values = Triplet.with(1, 2, 1.0f);
System.out.println(values.getValue0()); // 1
System.out.println(values.getValue1()); // 2
System.out.println(values.getValue2()); // 1.0

Quartet<Double, Double, Double, Double> data = Quartet.with(1.0, 2.0, 3.4, 4.0);
System.out.println(data.getValue(0)); // 1.0
System.out.println(data.getValue(1)); // 2.0
System.out.println(data.getValue(2)); // 3.4
System.out.println(data.getValue(3)); // 4.0
```

## 🧠 JavaTuples 주요 메서드 요약

| 메서드                      | 적용 대상 클래스         | 설명                                      |
|-----------------------------|---------------------------|-------------------------------------------|
| `getValue0()` ~ `getValueN()` | `Pair`, `Triplet`, `Quartet` 등 | 각 위치의 값을 직접 접근 (`getValue0()` 등) |
| `getValue(int index)`       | `Quartet` 이상             | 인덱스로 값 접근 (0부터 시작)             |
| `add(value)`                | 모든 튜플 클래스           | 요소 추가 → 새로운 튜플 반환              |
| `contains(value)`           | 모든 튜플 클래스           | 특정 값 포함 여부 확인                    |
| `toArray()` / `toList()`    | 모든 튜플 클래스           | 배열 또는 리스트로 변환                   |

### 📦 활용 팁
- JavaTuples는 불변 객체이므로 안전하게 공유 가능
- add()는 기존 튜플을 변경하지 않고 새로운 튜플을 반환
- 튜플을 반환하는 메서드는 여러 값을 한 번에 리턴할 때 유용

---

## 📚 대안: Java에서 튜플 없이 구현하는 방법
- Record (Java 14+): 간단한 데이터 구조 정의 가능
record Point(int x, int y, float weight) {}

- Map.Entry<K,V>: 2개 값만 필요할 때 사용 가능
- Custom class: 직접 클래스를 정의해 사용하는 방식

## 🧭 record Point(int x, int y, float weight) 설명
### ✅ 기본 개념
- record는 Java 14부터 도입된 불변 데이터 클래스입니다.
- 자동으로 생성자, getter, equals(), hashCode(), toString()을 만들어줍니다.
- Point는 x, y, weight라는 세 개의 필드를 가진 불변 객체입니다.

### 🧪 예시 코드
```java
public class RecordTest {
    public static void main(String[] args) {
        Point p = new Point(10, 20, 1.5f);

        System.out.println(p.x());       // 10
        System.out.println(p.y());       // 20
        System.out.println(p.weight());  // 1.5

        System.out.println(p);           // Point[x=10, y=20, weight=1.5]
    }
}

record Point(int x, int y, float weight) {}
```

## ✅ record 자동 생성 요소 요약

| 구성 요소       | 설명                                 | 예시 출력 또는 사용 방식                  |
|----------------|--------------------------------------|-------------------------------------------|
| 생성자         | 모든 필드를 포함한 생성자 자동 생성   | `new Point(x, y, weight)`                 |
| 접근자 메서드  | 각 필드에 대한 getter 생성            | `x()`, `y()`, `weight()`                  |
| `toString()`   | 필드 값을 포함한 문자열 반환           | `Point[x=10, y=20, weight=1.5]`           |
| `equals()`     | 필드 값 기반 동등성 비교               | `point1.equals(point2)` → `true/false`   |
| `hashCode()`   | 필드 값 기반 해시코드 생성             | `point.hashCode()`                        |


## 🧠 특징 요약
- record는 불변 객체 → 필드 수정 불가
- 모든 필드는 private final로 선언됨
- 상속 불가 (암묵적으로 final)
- 인터페이스는 구현 가능


# record / Tuple 비교
Java의 record는 튜플처럼 여러 값을 하나의 객체로 묶어서 반환할 수 있는 훌륭한 대안.  
특히 Java에서는 튜플을 기본적으로 지원하지 않기 때문에 record를 사용하면 가독성, 타입 안정성, 유지보수성 면에서 더 유리합니다.

## 🔁 record vs Tuple 비교
| 항목               | `record`                           | `Triplet` / `Quartet` (JavaTuples)       |
|--------------------|-------------------------------------|-------------------------------------------|
| 사용 목적          | 명시적 데이터 구조 정의             | 간단한 다중 값 반환 또는 전달             |
| 필드 접근 방식     | `point.x()`, `point.y()`            | `tuple.getValue0()`, `getValue1()`        |
| 타입 안정성        | 강함 (필드 이름 기반)               | 강함 (제네릭 기반)                        |
| 가독성             | 높음 (의미 있는 필드 이름)          | 낮음 (위치 기반 접근)                     |
| 유지보수           | 쉬움 (필드 이름으로 의미 파악 가능) | 어려움 (값 위치 기억 필요)                |
| 불변성             | 기본적으로 불변                     | 기본적으로 불변                           |
| 표준 지원 여부     | Java 14+ 표준                       | 외부 라이브러리 필요 (`javatuples`)       |
| 생성 방식          | `new Point(x, y, weight)`           | `Triplet.with(a, b, c)`                   |
| 직렬화 호환성      | Jackson 등과 잘 호환됨              | 일부 라이브러리에서 커스터마이징 필요     |



## 🧪 예시: 튜플 대신 record 사용
```java
record UserStats(int id, String name, float score) {}

public UserStats getUserStats() {
    return new UserStats(1, "JungHwan", 98.5f);
}

public static void main(String[] args) {
    UserStats stats = new TupleTest().getUserStats();
    System.out.println(stats.id());    // 1
    System.out.println(stats.name());  // JungHwan
    System.out.println(stats.score()); // 98.5
}
```
- Triplet<Integer, String, Float> 대신 record UserStats(int id, String name, float score) 사용
- 각 필드에 이름이 있으므로 의미 파악이 쉬움

## 📦 활용 팁
- 반환값이 여러 개인 메서드에서 record를 사용하면 코드가 훨씬 명확.
- JSON 직렬화/역직렬화에도 잘 작동하며, Lombok 없이도 toString(), equals() 등이 자동 생성됩니다.
- 컬렉션과 함께 사용할 때도 record는 타입 안정성과 필드 접근성이 뛰어남.


----

# JSON 과 연동

Java에서 record와 JSON을 연동하는 방법은 매우 직관적이며,  
특히 Jackson 같은 라이브러리를 사용하면 record를 일반 클래스처럼  
쉽게 직렬화(Record → JSON) 및 역직렬화(JSON → Record)할 수 있음.  
아래에 전체 흐름과 예제, 설정 방법을 정리.

## 🔁 JSON ↔ record 변환 흐름
| 변환 방향         | 설명                             | Jackson 메서드 예시                                 |
|------------------|----------------------------------|-----------------------------------------------------|
| `record → JSON`  | record 객체를 JSON 문자열로 변환  | `objectMapper.writeValueAsString(record)`           |
| `JSON → record`  | JSON 문자열을 record 객체로 변환  | `objectMapper.readValue(json, Record.class)`        |


## 🧪 예시: record ↔ JSON 변환
### ✅ 1. record 정의
```java
record User(int id, String name, float score) {}
```

### ✅ 2. 직렬화 (record → JSON)
```java
ObjectMapper mapper = new ObjectMapper();
User user = new User(1, "JungHwan", 98.5f);

String json = mapper.writeValueAsString(user);
System.out.println(json);
// 출력: {"id":1,"name":"JungHwan","score":98.5}
```

### ✅ 3. 역직렬화 (JSON → record)
```java
String json = "{\"id\":1,\"name\":\"JungHwan\",\"score\":98.5}";
User user = mapper.readValue(json, User.class);

System.out.println(user.name()); // JungHwan
```


## ⚙️ Gradle 설정 방법
Jackson 라이브러리를 사용하려면 build.gradle에 다음 의존성을 추가:
```
dependencies {
    implementation 'com.fasterxml.jackson.core:jackson-databind:2.17.0'
}
```
- 💡 최신 버전은 Maven Central에서 확인 가능


## 📦 활용 팁
- record는 기본적으로 Jackson과 잘 호환되며, 별도 설정 없이도 JSON 변환이 가능합니다.
- 필드 이름이 JSON 키로 자동 매핑되므로 DTO처럼 사용하기 좋습니다.




