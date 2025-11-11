# Optional

일반적으로 자바의 Optional에 대해 핵심 개념과 실전 활용법을 아래와 같이 정리.

## 🧠 자바 Optional 핵심 요약

| 기능 구분       | 설명                                                                 |
|----------------|----------------------------------------------------------------------|
| 생성 방식       | `Optional.of(value)` / `Optional.ofNullable(value)` / `Optional.empty()` |
| 존재 여부 확인  | `isPresent()` / `isEmpty()` / `ifPresent()`                          |
| 값 추출         | `get()` / `orElse()` / `orElseGet()` / `orElseThrow()`              |
| 값 변환 및 필터 | `map()` / `flatMap()` / `filter()`                                   |


## 🧪 기본 예제
### 1️⃣ 기본 사용
```java
Optional<String> name = Optional.of("JungHwan");

if (name.isPresent()) {
    System.out.println("이름: " + name.get());
}
```

### 2️⃣ null-safe 처리
```java
Optional<String> maybeName = Optional.ofNullable(null);

String result = maybeName.orElse("기본값");
System.out.println(result); // 출력: 기본값
```

### 3️⃣ map과 flatMap
```java
Optional<String> name = Optional.of("JungHwan");
int length = name.map(String::length).orElse(0);
System.out.println("이름 길이: " + length);
```

### 4️⃣ filter 활용
```java
Optional<String> name = Optional.of("JungHwan");

name.filter(n -> n.startsWith("J"))
    .ifPresent(n -> System.out.println("J로 시작: " + n));
```

## ✅ 언제 사용하면 좋을까?
- 메서드 반환값이 null일 수 있을 때 → Optional로 감싸서 명시적으로 처리
- 조건부 로직에서 ifPresent, filter, map을 활용해 깔끔한 코드 작성
- 복잡한 null 체크 로직을 함수형 스타일로 단순화

---

## 🧪 실전 예제
```java
public class OptionalExample {
    public static void main(String[] args) {
        Optional<String> name = Optional.ofNullable(getName());

        // 안전한 null 처리
        name.ifPresent(n -> System.out.println("Name: " + n));

        // 기본값 제공
        String result = name.orElse("Default Name");
        System.out.println("Result: " + result);

        // 함수형 처리
        name.map(String::toUpperCase)
            .ifPresent(n -> System.out.println("Upper: " + n));
    }

    private static String getName() {
        return null; // 또는 "JungHwan"
    }
}
```

### ⚠️ 사용 시 주의사항
- get()은 값이 없을 경우 예외 발생 → 반드시 isPresent() 또는 ifPresent()로 체크
- Optional은 필드로 사용 지양 → 메서드 리턴값에만 사용하는 것이 권장
- Optional.empty()는 명시적 null 표현 → 의미 있는 기본값이 있다면 orElse()로 처리

---


