
# ✅ default method란?
default 메서드는 인터페이스에 구현을 포함할 수 있게 해주는 기능입니다.  
Java 8부터 도입되었고, 주요 목적은 기존 인터페이스를 깨지 않고 확장할 수 있도록 하기 위함입니다.

## 📌 특징
- 인터페이스에 default 키워드로 메서드 구현을 포함할 수 있음
- 인터페이스를 구현하는 클래스에서 선택적으로 오버라이드 가능
- 다중 인터페이스 상속 시 충돌이 발생하면 반드시 오버라이드해야 함

## 🔍 예제 코드 해석
### 1. Printable 인터페이스
```java
interface Printable {
    default void printString() {
        System.out.println("default implementation");
    }
}
```

- printString()은 기본 구현을 가진 default 메서드
- 이 인터페이스를 구현하는 클래스는 오버라이드하지 않아도 사용 가능

### 2. OverrideDefault 클래스
```java
class OverrideDefault implements Printable {
    @Override
    public void printString() {
        System.out.println("override implementation");
    }
}
```

- Printable을 구현하고 printString()을 오버라이드
- 기본 구현을 무시하고 자신만의 로직을 제공

### 3. BoundedGenericTest 실행
```java
public class BoundedGenericTest {
    public static void main(String[] args) {
        new OverrideDefault().printString(); // 출력: override implementation
    }
}
```

- OverrideDefault 객체를 생성하고 printString() 호출
- 오버라이드된 메서드가 실행됨

## 📦 전체 구조 요약

| 구성 요소             | 설명                                      |
|----------------------|-------------------------------------------|
| `Printable`          | `printString()` 기본 구현 포함된 인터페이스 |
| `OverrideDefault`    | `Printable` 구현, `printString()` 오버라이드 |
| `BoundedGenericTest` | 실행 클래스, 오버라이드된 메서드 호출       |



## 🧪 확장 예제: 오버라이드하지 않은 경우
```java
class UseDefault implements Printable {
    // printString() 오버라이드하지 않음
}

public class TestDefault {
    public static void main(String[] args) {
        new UseDefault().printString(); // 출력: default implementation
    }
}
```
- Printable의 기본 구현이 그대로 사용됨

---

