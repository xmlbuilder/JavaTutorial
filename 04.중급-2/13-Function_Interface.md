## 📘 Java의 Function 인터페이스란?
- Java의 Function<T, R> 인터페이스는 입력값 T를 받아 결과값 R을 반환하는 함수형 인터페이스입니다.
- apply(T t) 메서드를 통해 동작합니다.
- Java 8부터 도입된 람다식과 함께 사용되며, 코드의 간결성과 가독성을 높여줍니다.

### 📌 함수형 인터페이스는 다음과 같은 종류들이 있습니다:
| 인터페이스         | 설명                                                        | 주요메서드     | 예시 코드                       |
|--------------------|-------------------------------------------------------------|------------------|-------------------------------|
|`Function<T, R>`|T를 받아 R을 반환             |apply(T)|Function<String, Integer> f = s -> s.length();|
|`Consumer<T>`|T를 받아 소비하고 반환값은 없음            |accept(T)|Consumer<String> c = s -> System.out.println(s);|
|`Predicate<T>`|T를 받아 조건을 검사하고 boolean을 반환 |test(T)|Predicate<String> p = s -> s.startsWith("Java");|
|`UnaryOperator<T>`|T를 받아 T를 반환하는 Function의 특수형                      |apply(T)|UnaryOperator<String> u = s -> s.toUpperCase();|


### 📌 예제 코드 분석 (단계별 설명)
#### 1️⃣ Function<String, Date>: 문자열을 날짜로 변환
```java
Function<String, Date> toDate = s -> {
    try {
        return new SimpleDateFormat("yyyy/MM/dd").parse(s);
    } catch (ParseException e) {
        e.printStackTrace();
        return null;
    }
};
Date date = toDate.apply("2015/09/27");
System.out.println(date);
```
- toDate는 문자열을 Date 객체로 변환하는 함수입니다.
- apply() 메서드를 통해 "2015/09/27"을 Date로 파싱합니다.
- 예외 처리도 포함되어 있어 안정적입니다.

#### 2️⃣ UnaryOperator<String>: 문자열을 대문자로 변환
```java
UnaryOperator<String> toUpper = s -> s.toUpperCase();
String s = toUpper.apply("java");
System.out.println(s); // JAVA
```

- UnaryOperator<T>는 Function<T, T>의 특수한 형태로, 입력과 출력 타입이 동일합니다.
- 여기서는 "java"를 "JAVA"로 변환합니다.

#### 3️⃣ Consumer<Date>: 날짜를 포맷해서 출력
```java
Consumer<Date> printDate = date -> {
    String s = new SimpleDateFormat("yyyy/MM/dd").format(date);
    System.out.println(s);
};
printDate.accept(new Date());
```

- Consumer<T>는 값을 받아서 소비하는 함수형 인터페이스입니다.
- accept() 메서드를 통해 현재 날짜를 "yyyy/MM/dd" 형식으로 출력합니다.

#### 4️⃣ Predicate<String>: 문자열이 "Java"로 시작하는지 검사
```java
Predicate<String> condition = s -> s.startsWith("Java");
System.out.println(condition.test("javaScript")); // false
```

- Predicate<T>는 조건을 검사하여 true 또는 false를 반환합니다.
- "javaScript"는 "Java"로 시작하지 않기 때문에 false가 출력됩니다.

---



