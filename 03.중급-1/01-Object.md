
# Object

## 1️⃣ java.lang 패키지란?
- 자바 언어의 기본 구성 요소를 담고 있는 패키지
- lang은 Language의 줄임말
- 자바 프로그램에서 자동으로 import되므로 명시적 import 없이 사용 가능

## 📦 java.lang 패키지 — 대표 클래스 요약
| 클래스   | 설명                                                                 |
|----------|----------------------------------------------------------------------|
| Object   | 모든 자바 클래스의 최상위 부모. 공통 기능(toString, equals 등) 제공 |
| String   | 문자열을 다루는 클래스. 불변(immutable) 객체                        |
| Integer  | int 기본형을 객체로 감싼 래퍼 클래스. `parseInt()`, `valueOf()` 등  |
| Class    | 클래스의 메타 정보를 다루는 클래스. 리플렉션(reflection)에 사용됨    |
| System   | 시스템 입출력, 환경 변수, 시간 측정 등 기본 기능 제공 (`System.out`) |



## 2️⃣ Object 클래스의 역할
### ✅ 묵시적 상속
```java
public class Parent {
    public void parentMethod() { System.out.println("Parent"); }
}
```

- 위 코드는 사실상 extends Object가 생략된 형태
- 부모 클래스가 없으면 자바가 자동으로 Object를 상속시킴

### ✅ 명시적 상속
```java
public class Child extends Parent {
    public void childMethod() { System.out.println("Child"); }
}
```

- 이미 Parent를 상속했기 때문에 Object는 자동으로 상속되지 않음

## 3️⃣ Object 클래스의 주요 기능
| 메서드       | 설명                                                                 |
|--------------|----------------------------------------------------------------------|
| toString()   | 객체의 정보를 문자열로 반환. 디버깅이나 출력 시 유용함               |
| equals()     | 두 객체가 같은지 비교. 참조값이 아닌 내용 비교를 위해 오버라이딩 가능 |
| getClass()   | 객체의 클래스 정보를 반환. 리플렉션이나 타입 확인에 사용됨           |


### 예제
```java
Child child = new Child();
System.out.println(child.toString()); // Object의 메서드 호출
```


## 4️⃣ Object 다형성
### ✅ 모든 객체를 참조할 수 있음
```java
Object dog = new Dog();
Object car = new Car();
```

- Object는 모든 클래스의 부모 → 어떤 객체든 담을 수 있음
### ✅ 다형성 활용 예제
```java
private static void action(Object obj) {
    if (obj instanceof Dog dog) {
        dog.sound();
    } else if (obj instanceof Car car) {
        car.move();
    }
}
```

- Object 타입으로 받아서 다운캐스팅 후 실제 타입의 메서드 호출
### ❌ 다형성의 한계
```java
obj.sound(); // 컴파일 오류: Object에는 sound()가 없음
```

- Object 타입으로는 자식 클래스의 고유 메서드 호출 불가
- 해결: instanceof + 다운캐스팅

## 🧠 정리 — java.lang과 Object 클래스
| 항목          | 설명                                                                 |
|---------------|----------------------------------------------------------------------|
| java.lang     | 자바 언어의 핵심 클래스들이 포함된 기본 패키지. 자동 import됨         |
| Object        | 모든 자바 클래스의 최상위 부모. 공통 기능과 다형성 지원                |
| 상속 구조     | 부모 클래스가 없으면 묵시적으로 Object를 상속                         |
| 공통 기능     | 모든 객체가 `Object`의 메서드를 상속받아 사용 가능                     |
| 주요 메서드   | `toString()`, `equals()`, `getClass()` — 정보 제공, 비교, 타입 확인    |
| 다형성 지원   | `Object`는 모든 객체를 참조할 수 있어 다양한 타입을 하나로 처리 가능   |
| 한계          | `Object` 타입으로는 자식 클래스의 고유 메서드 호출 불가 → 다운캐스팅 필요 |

---

# 🧠 자바 Object 클래스의 다형성과 활용 정리
## 1️⃣ Object를 활용한 다형성
- Object는 모든 자바 클래스의 최상위 부모
- 모든 객체는 Object 타입으로 참조 가능
```java
Object dog = new Dog();
Object car = new Car();
```

## ✅ 장점
- 다양한 타입을 하나의 메서드나 배열로 처리 가능
- 예: void action(Object obj), Object[] objects
### ❌ 한계
- Object는 모든 객체의 부모지만, 자식 클래스의 고유 메서드는 알지 못함
- 예: obj.sound() → 컴파일 오류
- 해결: instanceof + 다운캐스팅 필요
```java
if (obj instanceof Dog dog) {
    dog.sound();
}
```


## 2️⃣ Object 배열 활용
```java
Object[] objects = {new Dog(), new Car(), new Object()};
size(objects);

private static void size(Object[] objects) {
    System.out.println("전달된 객체의 수는: " + objects.length);
}
```

- Object[]는 모든 타입의 객체를 담을 수 있는 범용 배열
- 새로운 클래스가 추가되어도 size() 메서드는 수정할 필요 없음

## 3️⃣ Object.toString() 메서드
### 기본 동작
```java
Object object = new Object();
System.out.println(object); // 내부적으로 object.toString() 호출
```

- 기본 구현:
```java
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

- 결과: 클래스명 + 해시코드(참조값)

## 4️⃣ toString() 오버라이딩
### 예제: Dog 클래스
```java
@Override
public String toString() {
    return "Dog{" + "dogName='" + dogName + '\'' + ", age=" + age + '}';
}
```

### 비교: Car 클래스는 오버라이딩하지 않음
```java
System.out.println(car);   // 기본 toString() → 클래스명@참조값
System.out.println(dog1);  // 오버라이딩된 toString() → 상태 출력
```


## 5️⃣ Object 다형성과 toString() 활용
```java
public class ObjectPrinter {
    public static void print(Object obj) {
        System.out.println("객체 정보 출력: " + obj.toString());
    }
}
```

- Object 타입으로 받아도 자식 클래스에서 toString()을 오버라이딩했다면 해당 메서드가 호출됨

## 6️⃣ 객체 참조값 직접 출력
```java
String refValue = Integer.toHexString(System.identityHashCode(dog1));
System.out.println("refValue = " + refValue);
```

- toString()이나 hashCode()를 오버라이딩한 경우 참조값을 확인하려면 System.identityHashCode() 사용

## 📚 요약 — Object 클래스 기능과 활용
| 항목                     | 설명                                                                 |
|--------------------------|----------------------------------------------------------------------|
| Object 클래스            | 자바의 모든 클래스가 상속받는 최상위 부모 클래스                     |
| 다형성 지원              | 다양한 타입의 객체를 하나의 `Object` 타입으로 참조 가능               |
| 다형성의 한계            | 자식 클래스의 고유 메서드는 `Object`에서 알 수 없음 → 다운캐스팅 필요 |
| Object 배열              | 모든 객체를 담을 수 있는 범용 배열 (`Object[]`)                       |
| toString()               | 객체 정보를 문자열로 반환. 디버깅, 로깅에 유용                         |
| System.identityHashCode()| 객체의 참조값(해시코드)을 직접 확인할 때 사용                         |



# Object / OCP
Object 클래스와 OCP 원칙, 그리고 equals()의 동일성과 동등성 개념까지 깔끔하게 정리한 문서.  
핵심 개념, 예제 흐름, 객체지향 원칙까지 포함.

## 🧠 자바 Object 클래스와 OCP 원칙 정리
### 1️⃣ Object 클래스가 없다면?
- 서로 다른 클래스(Car, Dog 등)에 공통 부모가 없다면 객체 정보를 출력하기 위해 클래스마다 전용 메서드를 만들어야 함
- 예: BadObjectPrinter 클래스는 각 타입마다 print() 메서드를 따로 정의해야 함
```java
public class BadObjectPrinter {
    public static void print(Car car) {
        System.out.println("객체 정보 출력: " + car.carInfo());
    }
    public static void print(Dog dog) {
        System.out.println("객체 정보 출력: " + dog.dogInfo());
    }
}
```

#### ❌ 문제점
- 구체적인 클래스(Car, Dog)에 의존
- 클래스가 늘어날수록 메서드도 계속 추가되어야 함 → 유지보수 어려움

### 2️⃣ Object 클래스와 toString()의 역할
- Object는 모든 클래스의 부모 → 다형적 참조 가능
- Object.toString()은 모든 객체가 오버라이딩할 수 있는 공통 정보 출력 메서드
```java
public class ObjectPrinter {
    public static void print(Object obj) {
        System.out.println("객체 정보 출력: " + obj.toString());
    }
}
```

#### ✅ 장점
- Object에 의존 → 추상적인 것에 의존
- toString() 오버라이딩을 통해 각 객체의 정보를 런타임에 출력 가능

### 3️⃣ OCP 원칙과 ObjectPrinter
| 항목             | 설명                                                                 |
|------------------|----------------------------------------------------------------------|
| toString()       | 모든 객체가 오버라이딩 가능. 객체 정보를 문자열로 표현하는 공통 메서드 |
| ObjectPrinter    | `Object` 타입에 의존. 모든 객체를 받아 `toString()`으로 출력 가능     |
| 다형적 참조      | `Object` 타입으로 다양한 객체를 참조. 확장에 유연함                    |
| 메서드 오버라이딩| 각 객체가 `toString()`을 재정의하여 고유 정보 제공                     |
| Open             | 새로운 클래스 추가 시 `toString()`만 오버라이딩하면 기능 확장 가능     |
| Closed           | `ObjectPrinter`는 변경 없이 그대로 사용 가능                           |

- ObjectPrinter는 Object에만 의존하므로 확장에는 열려 있고, 변경에는 닫혀 있음
- 자바의 System.out.println()도 내부적으로 Object.toString()을 호출함

### 4️⃣ 정적 vs 동적 의존관계
| 구분             | 설명                                                                 |
|------------------|----------------------------------------------------------------------|
| 정적 의존관계     | 컴파일 시점에 결정됨. 클래스 코드만 보고 어떤 타입에 의존하는지 알 수 있음 |
| 동적 의존관계     | 런타임 시점에 결정됨. 실제로 어떤 객체가 전달될지는 실행해봐야 알 수 있음 |
| 예시             | `ObjectPrinter`는 정적으로는 `Object`에 의존하지만, 동적으로는 `Car`, `Dog` 등 다양한 객체가 전달될 수 있음 |


#### 예: ObjectPrinter.print(Object obj)
- 정적: ObjectPrinter는 Object에 의존
- 동적: 실제로는 Car, Dog 인스턴스가 전달될 수 있음

### 5️⃣ equals() — 동일성과 동등성
| 비교 방식     | 설명                                                                 |
|---------------|----------------------------------------------------------------------|
| `==`          | 동일성 비교. 두 객체의 참조값(메모리 주소)이 같은지 확인              |
| `equals()`    | 동등성 비교. 두 객체가 논리적으로 같은지 확인. 오버라이딩 필요         |

#### 예제
```java
UserV1 user1 = new UserV1("id-100"); // 참조 x001
UserV1 user2 = new UserV1("id-100"); // 참조 x002

System.out.println(user1 == user2);         // false (동일성: 참조값 다름)
System.out.println(user1.equals(user2));    // false (동등성: equals 미오버라이딩)
```
- 동일성: x001 != x002 → false
- 동등성: equals()를 오버라이딩하지 않았으므로 기본적으로 참조값 비교 → false

## 📚 요약 — Object 클래스와 객체지향 핵심 개념
| 항목                 | 설명                                                                 |
|----------------------|----------------------------------------------------------------------|
| java.lang            | 자바의 기본 패키지. 자동 import되며 핵심 클래스들을 포함             |
| Object 클래스        | 모든 클래스의 최상위 부모. 공통 기능과 다형성 지원                    |
| toString()           | 객체 정보를 문자열로 반환. 오버라이딩 가능                            |
| equals()             | 논리적 동등성 비교. 오버라이딩을 통해 의미 기반 비교 가능             |
| ObjectPrinter        | 추상적인 Object에 의존. OCP 원칙을 만족하는 구조                      |
| OCP 원칙             | 확장에는 열려(Open), 변경에는 닫혀(Closed) → 유지보수에 유리           |
| == / equals()        | `==`: 동일성(참조값 비교), `equals()`: 동등성(논리적 비교)            |


----

# 🧠 자바 equals() 메서드와 동등성 비교 정리
## 1️⃣ 기본 equals() 동작 — 동일성 비교
```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

- Object 클래스의 기본 equals()는 == 연산자를 사용해 참조값(주소) 비교
- 즉, 동일성(Identity) 비교만 수행
### 실행 흐름 예시
```
user1.equals(user2)
→ return (user1 == user2)
→ return (x001 == x002)
→ return false
```


## 2️⃣ 동등성 비교의 필요성
- **동등성(Equality)**은 객체의 논리적 동일함을 의미
- 예: 회원 번호, 주민등록번호, 이메일 등으로 비교
- 클래스마다 동등성 기준이 다르므로 equals()를 재정의(오버라이딩) 해야 함

## 3️⃣ 간단한 equals() 구현 예 — UserV2
```java
@Override
public boolean equals(Object obj) {
    UserV2 user = (UserV2) obj;
    return id.equals(user.id);
}
```

- UserV2 클래스는 id 값이 같으면 논리적으로 같은 객체로 간주
- 문자열 비교는 반드시 ==이 아닌 equals() 사용
### 결과 예시
```java
System.out.println(user1 == user2);         // false (동일성)
System.out.println(user1.equals(user2));    // true (동등성)
```

## 4️⃣ 정확한 equals() 구현 예 — IDE 자동 생성
```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    User user = (User) o;
    return Objects.equals(id, user.id);
}
```

- null 체크, 클래스 타입 비교, 필드 비교까지 포함
- 대부분의 IDE에서 자동 생성 가능 (⌘N / Alt+Insert)

## 5️⃣ equals() 구현 시 지켜야 할 규칙
| 규칙 이름     | 설명                                                                 |
|---------------|----------------------------------------------------------------------|
| 반사성        | `x.equals(x)`는 항상 `true`                                          |
| 대칭성        | `x.equals(y)`가 `true`이면 `y.equals(x)`도 `true`                    |
| 추이성        | `x.equals(y)`와 `y.equals(z)`가 `true`이면 `x.equals(z)`도 `true`    |
| 일관성        | 객체의 상태가 변하지 않으면 `equals()` 결과도 변하지 않아야 함       |
| null 비교     | `x.equals(null)`은 항상 `false`                                      |


## 6️⃣ 실습 예제 — Rectangle 클래스
### 요구사항
- width, height가 같으면 동등한 객체로 간주
- toString()과 equals() 오버라이딩 필요
### 코드
```java
public class Rectangle {
    private int width;
    private int height;

    public Rectangle(int width, int height) {
        this.width = width;
        this.height = height;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Rectangle rectangle = (Rectangle) o;
        return width == rectangle.width && height == rectangle.height;
    }

    @Override
    public String toString() {
        return "Rectangle{" +
                "width=" + width +
                ", height=" + height +
                '}';
    }
}
```

### 실행 결과
```
Rectangle{width=100, height=20}
Rectangle{width=100, height=20}
false
true
```
## 7️⃣ Object의 기타 메서드 요약
| 메서드             | 설명                                                                 |
|--------------------|----------------------------------------------------------------------|
| clone()            | 객체 복사. `Cloneable` 인터페이스 구현 필요. 실무에서는 잘 사용하지 않음 |
| hashCode()         | 객체의 해시값 반환. `equals()`와 함께 사용. 컬렉션에서 중요             |
| equals()           | 논리적 동등성 비교. 오버라이딩 필요                                   |
| getClass()         | 객체의 클래스 정보 반환. 리플렉션 등에 사용                            |
| notify(), wait()   | 멀티쓰레드에서 동기화 제어에 사용. `synchronized` 블록과 함께 사용       |

----



