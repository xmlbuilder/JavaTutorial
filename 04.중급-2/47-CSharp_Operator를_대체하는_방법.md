# C# operator를 대체 하는 방법

C#에서는 operator 키워드를 통해 산술 연산자(+, -), 비교 연산자(==, !=),  
암시적 변환(implicit) 등을 직접 오버로드할 수 있지만,  
Java는 연산자 오버로딩을 지원하지 않습니다.  
하지만 Java에서도 비슷한 기능을 객체 지향적으로 구현할 수 있음.  
아래에 C#의 Vector32 구조체와 유사한 기능을 Java에서 어떻게 표현할 수 있는지 정리.

## 🔁 Java에서 연산자 오버로딩 대체 방법
### ✅ 1. 클래스 정의
```java
public class Vector32 {
    private final int x;
    private final int y;

    public Vector32(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int getX() { return x; }
    public int getY() { return y; }

    @Override
    public String toString() {
        return x + " : " + y;
    }
}
```


## ✅ 2. 산술 연산 메서드 (+, -)
```java
public Vector32 add(Vector32 other) {
    return new Vector32(this.x + other.x, this.y + other.y);
}

public Vector32 add(int scalar) {
    return new Vector32(this.x + scalar, this.y + scalar);
}

public Vector32 subtract(Vector32 other) {
    return new Vector32(this.x - other.x, this.y - other.y);
}

public Vector32 subtract(int scalar) {
    return new Vector32(this.x - scalar, this.y - scalar);
}
```


## ✅ 3. 비교 연산 (==, !=)
```java
@Override
public boolean equals(Object obj) {
    if (this == obj) return true;
    if (obj == null || getClass() != obj.getClass()) return false;
    Vector32 other = (Vector32) obj;
    return this.x == other.x && this.y == other.y;
}

@Override
public int hashCode() {
    return Objects.hash(x, y);
}
```

- Java에서는 `==` 는 참조 비교이므로 `equals()` 를 오버라이드해야 의미 있는 값 비교가 가능.


## ✅ 4. 암시적 변환 (implicit operator)
Java에서는 암시적 변환은 없지만, 명시적 변환 메서드를 만들 수 있음:
``` java
public Vector64 toVector64() {
    return new Vector64((long) x, (long) y);
}


public class Vector64 {
    private final long x;
    private final long y;

    public Vector64(long x, long y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public String toString() {
        return x + " : " + y;
    }
}
```


## ✨ 요약 비교
| 기능 구분             | C# (`operator`)                  | Java 대체 방식                 |
|----------------------|----------------------------------|-------------------------------|
| 산술 연산자 오버로드 | `operator +`, `operator -`       | `add()`, `subtract()` 메서드  |
| 비교 연산자 오버로드 | `operator ==`, `operator !=`     | `equals()`, `hashCode()` 오버라이드 |
| 암시적 타입 변환     | `implicit operator`              | `toOtherType()` 명시적 변환 메서드 |

---

Java에서는 C#처럼 +, -, == 같은 연산자 오버로딩은 직접적으로 사용할 수 없습니다.  
즉, Vector32 a = new Vector32(1,2); Vector32 b = new Vector32(3,4);  
Vector32 c = a + b; 같은 코드는 컴파일 오류가 납니다.

## 🔍 왜 Java는 연산자 오버로딩을 지원하지 않을까?
- 단순성과 명확성을 위해 Java는 연산자 오버로딩을 의도적으로 제외.
- C++이나 C#에서는 연산자 오버로딩이 유용하지만, 오용되면 코드 가독성과 유지보수성이 떨어질 수 있음
- Java는 대신 명시적인 메서드 호출을 통해 기능을 표현하도록 유도

## ✅ Java에서 대체하는 방식
```java
Vector32 a = new Vector32(1, 2);
Vector32 b = new Vector32(3, 4);

// 대신 이렇게 사용
Vector32 c = a.add(b);
Vector32 d = a.subtract(1);
```

- add(), subtract() 같은 메서드를 정의해서 연산자처럼 사용
- equals()를 오버라이드해서 == 대신 .equals()로 비교

## 🧠 요약
| 연산자 | C# 표현식 | Java 대체 방식     |
|--------|------------|--------------------|
| `+`    | `a + b`    | `a.add(b)`         |
| `-`    | `a - b`    | `a.subtract(b)`    |
| `==`   | `a == b`   | `a.equals(b)`      |

---





