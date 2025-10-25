# sealed / final 
sealed와 final의 차이를 명확하게 설명.  
이 두 키워드는 클래스의 상속 제어와 관련된 기능이지만, 의도와 범위가 다릅니다.

## 🔐 sealed vs final 차이점 요약
| 키워드  | 목적                          | 상속 가능 여부         | 사용 위치             | 예시 클래스 구조                      |
|---------|-------------------------------|-------------------------|------------------------|----------------------------------------|
| sealed | 특정 클래스만 상속 허용        | ✅ 허용 (단, 제한적)     | 클래스, 인터페이스     | `sealed class A permits B, C {}`       |
| final  | 상속 자체 금지                 | ❌ 불가능                | 클래스, 메서드, 변수   | `final class B extends A {}`           |


## 🔍 소스 기반 설명
### ✅ sealed 사용 예
```java
abstract static sealed class Vehicle permits Car, Truck
```

- Vehicle 클래스는 sealed로 선언되어 있어요.
- permits Car, Truck → 오직 Car와 Truck만 Vehicle을 상속할 수 있어요.
- 다른 클래스가 Vehicle을 상속하려고 하면 컴파일 오류 발생

```java
sealed interface Service permits Car, Truck
```
- Service 인터페이스도 sealed로 선언됨
- Car, Truck만 구현 가능
즉, 상속은 허용하지만 명시된 클래스만 가능하다는 점이 핵심이에요.


### ✅ final 사용 예
```java
public static final class Car extends Vehicle implements Service
```

- Car 클래스는 final로 선언됨
- 따라서 다른 클래스가 Car를 상속할 수 없음
- Truck도 마찬가지로 final → 상속 불가
- final은 확장 금지를 의미하며, 보안성이나 불변성을 유지할 때 사용.


### 🧠 비유로 이해하기
- sealed는 “이 문은 일부 사람만 들어올 수 있어” → 특정 클래스만 상속 허용
- final은 “이 문은 누구도 들어올 수 없어” → 상속 자체 금지

✨ 함께 쓰는 이유
- Vehicle은 sealed → 확장 가능하지만 제한적
- Car, Truck은 final → 더 이상 확장 불가
- 이렇게 하면 상속 구조를 명확하게 통제하면서도, 필요한 클래스만 확장 가능하게 설계.

---
# sealed / non-sealed / abstract
Java 17에서는 sealed, non-sealed, abstract 키워드를 조합해 클래스 계층을 정밀하게 설계할 수 있으며,  
패턴 매칭과 함께 사용하면 타입 안전성과 가독성이 크게 향상됩니다.

## 🔐 sealed, non-sealed, abstract 조합
### ✅ 기본 구조
```java
public sealed abstract class Shape permits Circle, Rectangle, Polygon { }

public final class Circle extends Shape { }

public final class Rectangle extends Shape { }

public sealed class Polygon extends Shape permits Square, Triangle { }

public non-sealed class Square extends Polygon { }

public final class Triangle extends Polygon { }
```

🔍 설명
- Shape는 sealed abstract → 직접 인스턴스화는 불가능하며, Circle, Rectangle, Polygon만 상속 가능
- Polygon은 sealed → Square, Triangle만 상속 가능
- Square는 non-sealed → 누구든지 상속 가능
- Circle, Rectangle, Triangle는 final → 상속 불가
- 이 구조는 도메인 모델링에 매우 유용하며, 패턴 매칭의 exhaustiveness를 보장합니다.


## 🧠 Java 17 패턴 매칭과의 시너지
### ✅ 예제: instanceof 패턴 매칭
```java
void printShapeInfo(Shape shape) {
    if (shape instanceof Circle c) {
        System.out.println("Circle");
    } else if (shape instanceof Rectangle r) {
        System.out.println("Rectangle");
    } else if (shape instanceof Square s) {
        System.out.println("Square");
    } else if (shape instanceof Triangle t) {
        System.out.println("Triangle");
    }
}
```

- instanceof와 변수 바인딩을 동시에 수행
- sealed 타입 덕분에 컴파일러가 모든 하위 타입을 알고 있어 누락된 분기에 대해 경고 가능

### ✅ 예제: switch 패턴 매칭 (Java 21 이후 정식)
```java
String describeShape(Shape shape) {
    return switch (shape) {
        case Circle c -> "Circle";
        case Rectangle r -> "Rectangle";
        case Square s -> "Square";
        case Triangle t -> "Triangle";
    };
}
```

sealed 타입이기 때문에 switch 문이 완전한 분기를 갖추었는지 컴파일러가 검사할 수 있어요.

## ✨ 요약
| 키워드     | 설명                                                                 |
|------------|----------------------------------------------------------------------|
| sealed     | 특정 클래스만 상속 가능. `permits`로 허용된 하위 클래스만 확장 가능.     |
| non-sealed | `sealed` 계층에서 벗어나 자유롭게 상속 가능하도록 허용.                 |
| abstract   | 직접 인스턴스화 불가. 반드시 하위 클래스에서 구현해야 함.               |
| final      | 상속 불가. 더 이상 확장하거나 오버라이드할 수 없음.                    |

- 패턴 매칭과 함께 쓰면 타입 안정성과 유지보수성이 크게 향상됩니다.


