# Generic
## 📦 Java 제네릭(Generic) 개념 정리
### ✅ 제네릭이 필요한 이유
대부분의 최신 프로그래밍 언어는 제네릭(Generic) 개념을 제공합니다.  
제네릭은 코드 재사용성과 타입 안전성을 동시에 만족시키기 위한 핵심 기능입니다.

### 1️⃣ 타입별 클래스의 한계
```java
public class IntegerBox {
    private Integer value;
    public void set(Integer value) { this.value = value; }
    public Integer get() { return value; }
}

public class StringBox {
    private String value;
    public void set(String value) { this.value = value; }
    public String get() { return value; }
}
```

- 각각의 타입마다 클래스를 따로 만들어야 함
- DoubleBox, BooleanBox 등 수십 개의 클래스가 필요할 수 있음

### 2️⃣ Object를 활용한 다형성
```java
public class ObjectBox {
    private Object value;
    public void set(Object value) { this.value = value; }
    public Object get() { return value; }
}


ObjectBox box = new ObjectBox();
box.set(10); // OK
Integer value = (Integer) box.get(); // 다운캐스팅 필요
```

#### ❌ 문제점
- 타입 캐스팅 필요 → 런타임 오류 발생 가능
- 타입 안전성 부족 → 잘못된 타입 입력 가능

### 3️⃣ 제네릭 도입
```java
public class GenericBox<T> {
    private T value;
    public void set(T value) { this.value = value; }
    public T get() { return value; }
}
```

####  ✅ 장점
- 코드 재사용성: 하나의 클래스로 모든 타입 처리 가능
- 타입 안전성: 컴파일 시점에 타입 체크
- 캐스팅 불필요: 반환 타입이 명확함
```java
GenericBox<Integer> intBox = new GenericBox<>();
intBox.set(10);
Integer value = intBox.get(); // 캐스팅 없이 사용 가능

GenericBox<String> strBox = new GenericBox<>();
strBox.set("hello");
String text = strBox.get();
```


### 4️⃣ 타입 추론
```java
GenericBox<Integer> box1 = new GenericBox<Integer>();
GenericBox<Integer> box2 = new GenericBox<>(); // 타입 추론
```

- Java 컴파일러가 좌측 타입 정보를 기반으로 우측 타입을 추론함

## 🧠 정리
| 방식            | 코드 재사용 | 타입 안전성 | 캐스팅 필요 | 오류 가능성 |
|-----------------|--------------|--------------|---------------|----------------|
| 타입별 클래스    | ❌           | ✅           | ❌            | 낮음           |
| Object 활용      | ✅           | ❌           | ✅            | 높음           |
| 제네릭 사용      | ✅           | ✅           | ❌            | 낮음           |

## 💡 요약
- 타입별 클래스는 안전하지만 확장성이 떨어짐
- Object 활용은 유연하지만 타입 오류 위험이 큼
- 제네릭 사용은 재사용성과 안전성을 모두 만족시킴


---

# 📦 Java 제네릭(Generic) 개념 정리
## ✅ 제네릭이란?
제네릭(Generic)은 클래스나 메서드 내부에서 사용할 타입을 외부에서 지정할 수 있도록 하는 문법입니다.  
타입을 미리 결정하지 않고, 사용 시점에 타입을 지정함으로써 코드 재사용성과 타입 안전성을 동시에 만족시킵니다.

## 🔍 왜 제네릭이 필요한가?
### 1. 타입별 클래스의 한계
```java
class IntegerBox { Integer value; }
class StringBox { String value; }
```

- 타입마다 클래스를 따로 만들어야 함
- 확장성과 유지보수에 불리함

### 2. Object를 활용한 다형성
```java
class ObjectBox {
    Object value;
    void set(Object value) { this.value = value; }
    Object get() { return value; }
}
```

- 모든 타입을 담을 수 있지만
- 캐스팅 필요 → 런타임 오류 발생 가능
- 타입 안전성 부족
### 3. 제네릭 도입
```java
class GenericBox<T> {
    private T value;
    public void set(T value) { this.value = value; }
    public T get() { return value; }
}
```

- 하나의 클래스로 모든 타입 처리 가능
- 컴파일 시점에 타입 체크
- 캐스팅 불필요

## 🧠 제네릭 용어와 관례

| 구분             | 설명 또는 예시                              |
|------------------|---------------------------------------------|
| 제네릭 타입       | `GenericBox<T>` → 타입 매개변수를 사용하는 클래스 |
| 타입 매개변수     | `T`, `K`, `V` → 타입을 대표하는 변수 이름       |
| 타입 인자         | `Integer`, `String` → 실제 지정된 타입         |
| 명명 관례         | `T`, `E`, `K`, `V`, `N` → Type, Element, Key, Value, Number 등 |

### 💡 추가 설명
- GenericBox<T>에서 T는 타입 매개변수
- GenericBox<Integer>에서 Integer는 타입 인자
- TEKVN은 자주 쓰이는 타입 매개변수 약어 모음이에요:
- T: Type
- E: Element
- K: Key
- V: Value
- N: Number

## 📌 메서드와 제네릭의 비유

| 구분             | 메서드 예시                  | 제네릭 예시                  |
|------------------|------------------------------|------------------------------|
| 정의 시점        | `void method(String param)`  | `class Box<T>`              |
| 사용 시점        | `method("hello")`            | `new Box<String>()`         |
| 전달 대상        | `"hello"` (값 인자)          | `String` (타입 인자)        |

## 💡 핵심 비유
- 메서드는 값을 나중에 전달하기 위해 매개변수를 사용하고, 실행 시점에 인자를 넘깁니다.
- 제네릭은 타입을 나중에 결정하기 위해 타입 매개변수를 사용하고, 객체 생성 시점에 타입 인자를 넘깁니다.


## 🚫 Raw Type (원시 타입)
```java
GenericBox box = new GenericBox(); // 타입 미지정
```

- 내부적으로 Object로 처리됨
- 타입 안전성 없음
- 하위 호환용으로만 존재 → 사용 지양
## ✅ 권장 방식:
```java
GenericBox<Object> box = new GenericBox<>();
```


## 🐾 제네릭 활용 예제: Animal 클래스
### 1. 기본 클래스 구조
```java
class Animal { String name; int size; }
class Dog extends Animal { ... }
class Cat extends Animal { ... }
```

### 2. 제네릭 Box 클래스
```java
class Box<T> {
    private T value;
    public void set(T value) { this.value = value; }
    public T get() { return value; }
}
```

## 3. 다양한 타입 저장
```java
Box<Dog> dogBox = new Box<>();
dogBox.set(new Dog("멍멍이", 100));

Box<Cat> catBox = new Box<>();
catBox.set(new Cat("냐옹이", 50));

Box<Animal> animalBox = new Box<>();
animalBox.set(new Dog("멍멍이", 100));
animalBox.set(new Cat("냐옹이", 50));
```

- Box<Dog> → Dog만 저장 가능
- Box<Animal> → Dog, Cat 모두 저장 가능
- 꺼낼 때는 Animal 타입으로 반환됨

## ✅ 요약
| 방식             | 코드 재사용 | 타입 안전성 | 캐스팅 필요 | 오류 가능성 | 설명 요약                          |
|------------------|--------------|--------------|---------------|----------------|-----------------------------------|
| 타입별 클래스     | ❌           | ✅           | ❌            | 낮음           | 타입마다 클래스 생성 필요          |
| Object 활용       | ✅           | ❌           | ✅            | 높음           | 모든 타입 처리 가능하지만 위험함   |
| 제네릭 사용       | ✅           | ✅           | ❌            | 낮음           | 타입 지정 가능, 안전하고 재사용 가능 |

---

# 🧪 문제와 풀이 1: 자바 제네릭 기본
## ✅ 문제1 - 제네릭 기본 1: Container 클래스
### 📌 문제 설명
- Container<T> 클래스는 하나의 값을 저장하고 꺼낼 수 있어야 함
- 제네릭을 사용하여 다양한 타입을 처리할 수 있어야 함
- isEmpty() 메서드를 통해 값이 비어 있는지 확인 가능해야 함
### 💡 테스트 코드
```rust
Container<String> stringContainer = new Container<>();
System.out.println("빈값 확인1: " + stringContainer.isEmpty());
stringContainer.setItem("data1");
System.out.println("저장 데이터: " + stringContainer.getItem());
System.out.println("빈값 확인2: " + stringContainer.isEmpty());

Container<Integer> integerContainer = new Container<>();
integerContainer.setItem(10);
System.out.println("저장 데이터: " + integerContainer.getItem());
```

### ✅ 실행 결과
```
빈값 확인1: true
저장 데이터: data1
빈값 확인2: false
저장 데이터: 10
```

### 🧩 정답 코드
```java
package generic.test.ex1;

public class Container<T> {
    private T item;

    public void setItem(T item) {
        this.item = item;
    }

    public T getItem() {
        return item;
    }

    public boolean isEmpty() {
        return item == null;
    }
}
```


## ✅ 문제2 - 제네릭 기본 2: Pair 클래스
### 📌 문제 설명
- Pair<T1, T2> 클래스는 두 개의 값을 저장하고 꺼낼 수 있어야 함
- 서로 다른 타입을 처리할 수 있어야 함
- toString() 메서드를 통해 객체 내용을 문자열로 출력 가능해야 함
### 💡 테스트 코드
```java
Pair<Integer, String> pair1 = new Pair<>();
pair1.setFirst(1);
pair1.setSecond("data");
System.out.println(pair1.getFirst());
System.out.println(pair1.getSecond());
System.out.println("pair1 = " + pair1);

Pair<String, String> pair2 = new Pair<>();
pair2.setFirst("key");
pair2.setSecond("value");
System.out.println(pair2.getFirst());
System.out.println(pair2.getSecond());
System.out.println("pair2 = " + pair2);
```

### ✅ 실행 결과
```
1
data
pair1 = Pair{first=1, second=data}
key
value
pair2 = Pair{first=key, second=value}
```

### 🧩 정답 코드
```java
package generic.test.ex2;

public class Pair<T1, T2> {
    private T1 first;
    private T2 second;

    public void setFirst(T1 first) {
        this.first = first;
    }

    public void setSecond(T2 second) {
        this.second = second;
    }

    public T1 getFirst() {
        return first;
    }

    public T2 getSecond() {
        return second;
    }

    @Override
    public String toString() {
        return "Pair{" +
               "first=" + first +
               ", second=" + second +
               '}';
    }
}

```


---
