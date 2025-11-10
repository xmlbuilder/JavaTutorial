# 메서드 참조
"메서드 참조" 문서를 기반으로 핵심 개념과 예제를 아래와 같이 정리.  
람다 표현식과 메서드 참조의 차이, 4가지 유형, 그리고 실전 활용까지 한눈에 볼 수 있도록 구성했습니다.

## 🧠 메서드 참조(Method Reference)란?
이미 정의된 메서드를 람다처럼 간결하게 참조하는 문법
```
(x, y) -> SomeClass.someMethod(x, y) → SomeClass::someMethod
```

## 🔢 메서드 참조의 4가지 유형

| 유형 번호 | 설명                             | 메서드 참조 문법               | 대응되는 람다 표현식                        |
|-----------|----------------------------------|-------------------------------|---------------------------------------------|
| ①        | 정적 메서드 참조                 | `ClassName::staticMethod`     | `(x, y) -> ClassName.staticMethod(x, y)`   |
| ②        | 특정 객체의 인스턴스 메서드 참조 | `object::instanceMethod`      | `() -> object.instanceMethod()`            |
| ③        | 생성자 참조                      | `ClassName::new`              | `() -> new ClassName()`                    |
| ④        | 임의 객체의 인스턴스 메서드 참조 | `ClassName::instanceMethod`   | `(obj) -> obj.instanceMethod()`            |



## ✅ 예제 요약
### 1. 정적 메서드 참조
```java
Supplier<String> s1 = () -> Person.greeting();
Supplier<String> s2 = Person::greeting;
```

### 2. 특정 객체의 인스턴스 메서드 참조
```java
Person person = new Person("Kim");
Supplier<String> s1 = () -> person.introduce();
Supplier<String> s2 = person::introduce;
```

### 3. 생성자 참조
```java
Supplier<Person> s1 = () -> new Person();
Supplier<Person> s2 = Person::new;

Function<String, Person> f1 = name -> new Person(name);
Function<String, Person> f2 = Person::new;
```

### 4. 임의 객체의 인스턴스 메서드 참조
```java
Function<Person, String> f1 = person -> person.introduce();
Function<Person, String> f2 = Person::introduce;
```

## 🎯 임의 객체의 인스턴스 메서드 참조 vs 특정 객체 참조

| 구분                           | 문법 예시             | 호출 대상 설명                     |
|--------------------------------|------------------------|------------------------------------|
| 특정 객체의 메서드 참조        | `person::introduce`    | 이미 생성된 특정 객체 `person`     |
| 임의 객체의 메서드 참조        | `Person::introduce`    | 실행 시점에 전달된 `Person` 객체   |

---

## 🧪 실전 활용 예시
```java
List<Person> people = List.of(new Person("Kim"), new Person("Lee"));

// 람다
List<String> names1 = people.stream()
    .map(p -> p.introduce())
    .collect(Collectors.toList());

// 메서드 참조
List<String> names2 = people.stream()
    .map(Person::introduce)
    .collect(Collectors.toList());

```

## ✨ 메서드 참조의 장점
- ✅ 코드 간결성 향상
- ✅ 가독성 증가
- ✅ 중복 제거 및 재사용성 향상
- ✅ 람다 표현식보다 의도가 더 명확하게 드러남

---

# 🧪 메서드 참조 실습 예제 모음


아래는 메서드 참조의 4가지 유형을 모두 체험할 수 있도록 구성한 실습 예제 모음입니다.  
각 예제는 람다 표현식 → 메서드 참조로 변환하는 흐름을 보여주며, 실무에서 자주 쓰이는 패턴을 기반으로 작성했습니다.

## ✅ 예제 1: 정적 메서드 참조
```java
public class MathUtil {
    public static int add(int x, int y) {
        return x + y;
    }
}

// 람다 표현식
BinaryOperator<Integer> op1 = (x, y) -> MathUtil.add(x, y);

// 메서드 참조
BinaryOperator<Integer> op2 = MathUtil::add;

System.out.println(op1.apply(2, 3)); // 5
System.out.println(op2.apply(2, 3)); // 5
```


## ✅ 예제 2: 특정 객체의 인스턴스 메서드 참조
```java
Person kim = new Person("Kim");

// 람다 표현식
Supplier<String> s1 = () -> kim.introduce();

// 메서드 참조
Supplier<String> s2 = kim::introduce;

System.out.println(s1.get()); // I am Kim
System.out.println(s2.get()); // I am Kim
```


## ✅ 예제 3: 생성자 참조
```java
// 람다 표현식
Supplier<Person> p1 = () -> new Person("Lee");

// 메서드 참조
Function<String, Person> p2 = Person::new;

System.out.println(p1.get());         // Person@xxxx
System.out.println(p2.apply("Lee"));  // Person@yyyy
```


## ✅ 예제 4: 임의 객체의 인스턴스 메서드 참조
```java
List<Person> list = List.of(
    new Person("Kim"),
    new Person("Park"),
    new Person("Lee")
);

// 람다 표현식
list.stream()
    .map(p -> p.introduce())
    .forEach(System.out::println);

// 메서드 참조
list.stream()
    .map(Person::introduce)
    .forEach(System.out::println);

```

## ✅ 예제 5: String 변환
```java
List<String> names = List.of("kim", "lee", "park");

// 람다 표현식
names.stream()
    .map(s -> s.toUpperCase())
    .forEach(System.out::println);

// 메서드 참조
names.stream()
    .map(String::toUpperCase)
    .forEach(System.out::println);
```


## ✅ 예제 6: 정렬 with Comparator
```java
List<String> names = List.of("banana", "apple", "cherry");

// 람다 표현식
names.sort((a, b) -> a.compareTo(b));

// 메서드 참조
names.sort(String::compareTo);
```


## ✅ 예제 7: Consumer 출력
```java
List<String> items = List.of("A", "B", "C");

// 람다 표현식
items.forEach(s -> System.out.println(s));

// 메서드 참조
items.forEach(System.out::println);
```


## ✅ 예제 8: Function 변환
```java
Function<String, Integer> f1 = s -> Integer.parseInt(s);
Function<String, Integer> f2 = Integer::parseInt;

System.out.println(f1.apply("100")); // 100
System.out.println(f2.apply("200")); // 200
```


## ✅ 예제 9: Predicate 필터링
```java
List<Integer> nums = List.of(1, 2, 3, 4, 5);

// 람다 표현식
nums.stream()
    .filter(n -> n % 2 == 0)
    .forEach(System.out::println);

// 메서드 참조 (정적 메서드 활용)
public class NumberUtil {
    public static boolean isEven(int n) {
        return n % 2 == 0;
    }
}

nums.stream()
    .filter(NumberUtil::isEven)
    .forEach(System.out::println);

```


## ✅ 예제 10: 생성자 참조 with List
```java
List<String> names = List.of("Kim", "Lee", "Park");

// 람다 표현식
List<Person> people1 = names.stream()
    .map(name -> new Person(name))
    .toList();

// 메서드 참조
List<Person> people2 = names.stream()
    .map(Person::new)
    .toList();

```
---
