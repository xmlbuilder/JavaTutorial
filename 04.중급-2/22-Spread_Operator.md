# Spread Operator

Java에는 JavaScript나 Kotlin처럼 명시적인 spread operator (... 또는 *)는 없지만,  
`varargs` 와 배열을 활용해 유사한 기능을 구현할 수 있습니다.

## 🧠 Java에서의 "스프레드" 개념
Java는 spread operator를 직접 지원하지 않지만, varargs(가변 인자)와 배열을 통해 배열을 개별 인자로 전달하는 방식을 제공합니다.

### ✅ varargs 문법
```java
public void printMany(String... elements) {
    for (String e : elements) {
        System.out.println(e);
    }
}
```

- String... elements는 String[] 배열처럼 동작하지만, 호출 시 개별 인자를 나열할 수 있음
- 호출 예:
```java
printMany("apple", "banana", "kiwi");
printMany(new String[]{"apple", "banana", "kiwi"});
```

## 🔍 Java에서 배열을 "펼쳐서" 전달하는 방식
Java에서는 다음과 같이 배열을 varargs에 전달할 수 있습니다:
```java
String[] fruits = {"apple", "banana", "kiwi"};
printMany(fruits); // 배열을 그대로 전달
```

- 이 방식은 Kotlin의 *fruits 또는 JavaScript의 ...fruits와 유사한 효과를 냅니다
- 단, 메서드가 varargs로 선언되어 있어야 배열을 펼쳐서 전달할 수 있습니다

## ❌ Java에서 직접적인 spread operator는 없음
Java에서는 다음과 같은 문법은 불가능합니다:

``java
// ❌ Java에서는 지원되지 않음
doSomething(...args);
```

- JavaScript에서는 ...args로 배열을 펼칠 수 있지만
- Java에서는 반드시 args[0], args[1], args[2]처럼 명시적으로 인자를 전달하거나
- 메서드가 varargs를 사용해야 배열을 직접 전달할 수 있습니다

## 📌 요약 표
### 📌 Java에서의 spread operator 요약

| 기능                     | Java에서의 구현 방식                     |
|--------------------------|------------------------------------------|
| 배열을 인자로 펼치기     | `varargs` 사용 (`String... args`)         |
| 배열을 그대로 전달       | `printMany(array)` → 내부적으로 `String[]` |
| 직접적인 spread 문법     | ❌ 없음 (`...args` 같은 문법은 불가능)     |

---

