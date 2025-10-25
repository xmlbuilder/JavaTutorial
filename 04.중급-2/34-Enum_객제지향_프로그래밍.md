# 📌 Java Enum 개념 정리 및 객체 지향 프로그래밍
## ✅ 기본 개념
- enum은 열거형 타입으로, 고정된 상수 집합을 정의할 때 사용
- `클래스` 처럼 동작하며, 메서드, 필드, 생성자도 가질 수 있음
- enum은 암묵적으로 final, static, extends java.lang.Enum을 포함

## 🧪 예제 1: 기본 열거형과 메서드 활용
```java
public enum Sex {
    MAN, WOMAN
}
```

### 📌 주요 메서드 요약: enum 관련

| 메서드                        | 설명 |
|------------------------------|------|
| `Sex.valueOf("MAN")`         | 문자열을 해당 enum 값으로 변환 (`"MAN"` → `Sex.MAN`) |
| `Sex.values()`               | 모든 enum 값을 배열로 반환 (`[Sex.MAN, Sex.WOMAN]`) |
| `sex.name()`                 | enum 이름을 문자열로 반환 (`"MAN"`, `"WOMAN"`) |
| `sex.ordinal()`              | enum 선언 순서 인덱스 반환 (`MAN` → 0, `WOMAN` → 1) |
| `sex.compareTo(Sex.MAN)`     | 선언 순서 기준으로 비교 (`ordinal()` 기반 비교) |

### switch 문과 함께 사용
```java
switch (sex) {
    case MAN -> System.out.println("Man");
    case WOMAN -> System.out.println("Woman");
}
```


## 🧪 예제 2: 필드와 인터페이스 구현

### 필드 포함 enum
```java
public enum Sex {
    MAN(1), WOMAN(2);
    private final int code;
    Sex(int code) { this.code = code; }
    public int getCode() { return code; }
}
```

- 각 enum 값에 **속성값(code)** 을 부여
- 생성자와 getter를 통해 객체처럼 사용

### 인터페이스 구현 enum
```java
public interface Color {
    String getColor();
}

public enum SexColor implements Color {
    MAN { public String getColor() { return "Blue"; } },
    WOMAN { public String getColor() { return "Red"; } }
}
```
- 각 enum 값이 인터페이스를 구현하여 개별 동작 정의
- 전략 패턴처럼 활용 가능

## 🧪 예제 3: EnumSet & EnumMap 활용

### EnumSet
```java
EnumSet<Flag> flags = EnumSet.of(Flag.A, Flag.C);
flags.contains(Flag.C); // true
```
- EnumSet은 enum 전용 Set
- 메모리 효율 높고 빠름

### EnumMap
```java
EnumMap<Flag, Integer> map = new EnumMap<>(Flag.class);
map.put(Flag.A, 1);
```
- EnumMap은 enum 키 전용 Map
- 순서 보장, 성능 우수

## ✅ Java Enum 도입 시 장점

| 항목                     | 설명 |
|--------------------------|------|
| 타입 안정성              | 잘못된 값 사용 방지 → 컴파일 시점에서 오류 검출 가능 |
| 코드 가독성 향상         | 의미 있는 이름으로 상태 표현 → 유지보수 쉬움 |
| 기능 확장 가능           | 필드, 생성자, 메서드, 인터페이스 구현 가능 |
| 조건 분기 명확화         | `switch` 문과 함께 사용 시 분기 처리 간결 |
| 컬렉션 최적화            | `EnumSet`, `EnumMap` 사용 시 성능 및 메모리 효율 우수 |

## 🚀 실전 활용 전략

| 상황 또는 목적           | 활용 방식 |
|--------------------------|-----------|
| 상태 관리                | 사용자 성별, 주문 상태, 권한 등을 `enum`으로 표현하여 코드 안정성 향상 |
| 전략 패턴 구현           | 각 `enum` 값에 개별 동작 정의 → `getColor()`처럼 메서드 오버라이딩 |
| 조건 분기 처리           | `switch` 문으로 `enum` 값에 따라 명확한 분기 처리 가능 |
| 설정값 집합 표현         | 고정된 설정값을 `enum`으로 선언하여 중앙 집중 관리 |
| 컬렉션 최적화            | `EnumSet`, `EnumMap`을 활용해 성능과 메모리 효율 개선 |


## 📌 Java Enum 요약

| 항목             | 설명 |
|------------------|------|
| 기본 정의        | `enum`은 상수 집합을 표현하는 클래스 |
| 기능 확장        | 필드, 생성자, 메서드, 인터페이스 구현 가능 |
| 주요 메서드      | `valueOf`, `values`, `ordinal`, `name` 등 |
| 컬렉션 활용      | `EnumSet`, `EnumMap`으로 성능 최적화 |
| 실전 활용 전략   | 상태 관리, 전략 패턴, 설정값, 조건 분기 등 |


# enum 객체 지향 프로그래밍
Java의 enum은 단순한 상수 집합을 넘어서 **전략 패턴(Strategy Pattern)** 이나 **상태 패턴(State Pattern)** 처럼  
객체 지향 설계의 핵심 도구로 활용될 수 있음.  
아래는 그 대표적인 예시로, enum을 통해 **다형성(polymorphism)** 을 구현한 전략 패턴 스타일의 샘플입니다.

## 🎯 예제: 계산기 전략을 enum으로 구현
```java
public class EnumStrategyExample {
    public enum Operation {
        ADD {
            @Override
            public double apply(double x, double y) {
                return x + y;
            }
        },
        SUBTRACT {
            @Override
            public double apply(double x, double y) {
                return x - y;
            }
        },
        MULTIPLY {
            @Override
            public double apply(double x, double y) {
                return x * y;
            }
        },
        DIVIDE {
            @Override
            public double apply(double x, double y) {
                if (y == 0) throw new ArithmeticException("Divide by zero");
                return x / y;
            }
        };

        public abstract double apply(double x, double y);
    }

    public static void main(String[] args) {
        double a = 10;
        double b = 5;

        for (Operation op : Operation.values()) {
            double result = op.apply(a, b);
            System.out.printf("%s: %.2f %s %.2f = %.2f%n",
                    op.name(), a, symbol(op), b, result);
        }
    }

    private static String symbol(Operation op) {
        return switch (op) {
            case ADD -> "+";
            case SUBTRACT -> "-";
            case MULTIPLY -> "*";
            case DIVIDE -> "/";
        };
    }
}
```


## ✅ 이 예제에서의 객체 지향적 특징

| 항목                     | 설명 |
|--------------------------|------|
| 다형성 (Polymorphism)     | 각 `enum` 상수가 `apply()` 메서드를 다르게 구현하여 동작을 분리함 |
| 캡슐화 (Encapsulation)   | 연산 로직이 각 `enum` 내부에 숨겨져 있어 외부에서 조건문 없이 사용 가능 |
| 전략 패턴 구현            | `Operation`이 전략 객체처럼 동작하며, 동적으로 선택 가능 |
| OCP 원칙 만족             | 새로운 연산 추가 시 기존 코드 수정 없이 `enum` 상수만 추가하면 됨 |
| enum 자체가 객체처럼 동작 | `Operation`은 단순 상수가 아니라 메서드를 가진 객체로 활용됨 |

## 📌 요약
Java의 enum은 단순한 상수 이상의 역할을 할 수 있습니다:
- 메서드 오버라이딩을 통해 전략별 동작 정의
- 인터페이스 구현 가능 → 전략 패턴, 상태 패턴에 적합
- switch 없이도 다형성으로 깔끔한 코드 구성 가능

---

