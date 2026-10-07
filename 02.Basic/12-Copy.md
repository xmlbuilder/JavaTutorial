## 📘 Copy

### 1️⃣ 얕은 복사 (Shallow Copy)
- 객체의 필드 값을 그대로 복사하지만, 참조형 필드는 주소만 복사됨
- 즉, 내부 객체는 공유됨 → 사이드 이펙트 발생 가능
#### 🔹 방법
- clone() 메서드 사용 (단, Cloneable 인터페이스 구현 필요)
- 또는 생성자나 팩토리 메서드로 직접 복사
#### 🔹 예시
```java
public class Address {
    private String city;

    public Address(String city) {
        this.city = city;
    }

    public Address(Address other) {
        this.city = other.city; // 얕은 복사
    }
}
```


### 2️⃣ 깊은 복사 (Deep Copy)
- 객체의 모든 필드뿐 아니라 내부 참조 객체까지 새로 생성해서 복사
- 원본과 복사본이 완전히 독립됨
#### 🔹 방법
- 수동으로 모든 필드를 복사
- 또는 직렬화/역직렬화 방식 사용 (예: ObjectOutputStream)
#### 🔹 예시
```java
public class User {
    private Address address;

    public User(Address address) {
        this.address = new Address(address); // 깊은 복사
    }
}
```


### 3️⃣ clone() 메서드의 한계
- Cloneable 인터페이스를 구현해야 함
- Object.clone()은 얕은 복사만 수행
- 예외 처리 필요 (CloneNotSupportedException)
- 실무에서는 잘 사용되지 않음 → 대신 생성자나 팩토리 메서드 선호

### 📌 요약 — Java 객체 복사 방식
| 복사 방식       | 설명                                                                 |
|----------------|----------------------------------------------------------------------|
| 얕은 복사       | 필드 값만 복사. 참조형 필드는 주소만 복사됨 → 내부 객체 공유됨         |
| 깊은 복사       | 내부 객체까지 새로 생성하여 완전히 독립된 객체 생성                     |
| `clone()`       | `Cloneable` 인터페이스 구현 필요. 기본적으로 얕은 복사 수행              |
| 생성자 복사     | 직접 복사 로직 구현. 가장 일반적이고 안전한 방식                        |
| 직렬화 복사     | `ObjectOutputStream`을 활용한 깊은 복사. 성능과 복잡도 고려 필요         |


### 📌 자바 clone() 구현 예제 — 얕은 복사
```java
public class User implements Cloneable {
    private String name;
    private int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // clone() 메서드 오버라이딩
    @Override
    public User clone() {
        try {
            return (User) super.clone(); // 얕은 복사
        } catch (CloneNotSupportedException e) {
            throw new AssertionError(); // 예외 처리
        }
    }

    @Override
    public String toString() {
        return "User{name='" + name + "', age=" + age + "}";
    }
}
```

#### 🔹 사용 예
```java
User user1 = new User("JungHwan", 30);
User user2 = user1.clone();

System.out.println(user1); // User{name='JungHwan', age=30}
System.out.println(user2); // User{name='JungHwan', age=30}
System.out.println(user1 == user2); // false (다른 객체)
```


### 📌 주의사항
- super.clone()은 얕은 복사만 수행합니다.
- 참조형 필드가 있을 경우 내부 객체는 공유됩니다.
- 깊은 복사가 필요하면 내부 필드도 직접 복사해야 합니다.

---

### 📌 clone()에서 new를 사용하는 경우
#### 🔹 예시

```java
@Override
public User clone() {
    return new User(this.name, this.age); // 직접 복사
}
```

#### 🔹 결과
- super.clone()을 사용하지 않음 → Cloneable 인터페이스 없어도 동작함
- 내부 필드를 수동으로 복사 → 얕은 복사 또는 깊은 복사 가능
- CloneNotSupportedException 예외 처리 불필요

#### 🔹 장점
- 더 안전하고 명확한 복사 방식
- Cloneable 인터페이스나 Object.clone()의 불편함을 피할 수 있음
- 깊은 복사도 자유롭게 구현 가능

#### 🔹 단점
- clone()이라는 이름을 쓰지만, 자바의 표준 clone() 규약을 따르지 않음
- 일부 라이브러리나 프레임워크에서 Object.clone() 기반 동작을 기대할 경우 호환성 문제 발생 가능

### 📌 요약 — Java에서 clone() 구현 방식 비교

| 방식             | 설명                                                                 |
|------------------|----------------------------------------------------------------------|
| `super.clone()`  | 자바의 표준 clone 방식. `Cloneable` 인터페이스 구현 필요. 얕은 복사 수행 |
| `new` 생성 방식  | 직접 복사 로직 구현. 깊은 복사 가능. `Cloneable` 없이도 사용 가능         |
| `clone()` 이름 유지 | 메서드 이름은 같지만 내부 구현 방식에 따라 의미가 달라질 수 있음         |

---


