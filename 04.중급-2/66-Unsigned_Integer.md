# Unsigned integer
Java에서 부호 없는 정수(Unsigned Integer)를 다루는 건 꽤 불편한 편.  
Java는 C/C++처럼 unsigned int 타입을 직접 지원하지 않기 때문에,  
Java 8부터 도입된 유틸리티 메서드를 활용하거나 대체 전략을 써야 함.


## 🔍 Java에서 부호 없는 정수 다루는 방식
### ✅ 기본 원리
- Java의 int, long은 항상 부호 있는 타입입니다.
- 부호 없는 정수는 오버플로우를 이용하거나 Integer.toUnsignedX() 같은 메서드로 처리해야 합니다.

### 🔧 Java 8 이후 지원 메서드
| 메서드                          | 입력 타입 | 설명                                                       |
|---------------------------------|-----------|------------------------------------------------------------|
| `Integer.toUnsignedLong(int)`   | `int`     | `int` 값을 부호 없는 `long`으로 변환 (음수도 양수처럼 표현) |
| `Integer.toUnsignedString(int)` | `int`     | `int` 값을 부호 없는 문자열로 변환                          |
| `Integer.parseUnsignedInt(str)` | `String`  | 문자열을 부호 없는 `int`로 파싱 (최대 2³²-1까지 처리 가능)  |
| `Long.toUnsignedString(long)`   | `long`    | `long` 값을 부호 없는 문자열로 변환 (최대 2⁶⁴-1까지 표현)   |


### 🧪 예시
```java
int a = Integer.MAX_VALUE + 1; // -2147483648 (오버플로우)
System.out.println(a); // 음수처럼 보임
System.out.println(Integer.toUnsignedLong(a)); // 2147483648
System.out.println(Integer.toUnsignedString(a)); // "2147483648"

int b = Integer.parseUnsignedInt("4294967295"); // 최대 unsigned int
System.out.println(b); // -1 (2^32 - 1 → 오버플로우된 int)

long l = Long.MAX_VALUE + 1; // 오버플로우
System.out.println(Long.toUnsignedString(l)); // "9223372036854775808"
```


### 🧠 불편한 점
- int와 long은 오버플로우 시 음수로 보이기 때문에 직관적이지 않음
- 비교, 정렬, 범위 체크 시 주의가 필요함
- parseUnsignedInt()는 int 범위를 초과한 값을 int로 강제 캐스팅 → 음수처럼 보임

## ✅ 더 나은 대안
### 1. BigInteger 사용
```java
BigInteger unsignedInt = new BigInteger("4294967295"); // 2^32 - 1
System.out.println(unsignedInt); // 정확한 값 유지
```

- BigInteger는 부호 없는 정수처럼 동작하며, 범위 제한 없음
- 연산, 비교, 출력 모두 정확하게 처리 가능

### 2. 직접 래퍼 클래스 만들기
```java
public class UnsignedInt {
    private final int value;

    public UnsignedInt(int value) {
        this.value = value;
    }

    public long toLong() {
        return Integer.toUnsignedLong(value);
    }

    public String toString() {
        return Integer.toUnsignedString(value);
    }
}
```
- 의미 있는 타입으로 감싸면 실수 방지 가능

## 📦 결론: 부호 없는 정수 처리 방식 비교

| 방법               | 장점                              | 단점                                | 추천 상황                           |
|--------------------|-----------------------------------|-------------------------------------|-------------------------------------|
| Java 8 유틸리티 메서드 (`toUnsignedX`) | 표준 API, 간단한 변환 가능             | 오버플로우 직접 관리 필요            | 간단한 변환, 출력용                 |
| BigInteger         | 정밀도 유지, 범위 무제한           | 성능/메모리 부담, 타입 변환 필요     | 정확한 계산, 대용량 숫자 처리       |
| 커스텀 래퍼 클래스 | 의미 있는 타입, 실수 방지           | 직접 구현 필요, 유지보수 부담        | 도메인 모델에 부호 없는 의미가 있을 때 |



## 🧠 요약 팁
- int나 long을 부호 없이 다루려면 의식적으로 변환해야 함
- BigInteger는 가장 안전하지만 성능이 중요한 경우엔 주의
- 커스텀 클래스를 쓰면 의미를 명확히 표현할 수 있어 실수 방지에 효과적

## ✅ 불변 UnsignedInt 클래스 예제
```java
public final class UnsignedInt {
    private final int value;

    // 생성자: 부호 없는 범위 체크 포함
    public UnsignedInt(int value) {
        this.value = value;
    }

    public static UnsignedInt fromString(String unsignedStr) {
        return new UnsignedInt(Integer.parseUnsignedInt(unsignedStr));
    }

    public long toLong() {
        return Integer.toUnsignedLong(value);
    }

    public String toString() {
        return Integer.toUnsignedString(value);
    }

    public int toSignedInt() {
        return value;
    }

    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (!(obj instanceof UnsignedInt)) return false;
        return this.value == ((UnsignedInt) obj).value;
    }

    public int hashCode() {
        return Integer.hashCode(value);
    }

    public int compareTo(UnsignedInt other) {
        return Integer.compareUnsigned(this.value, other.value);
    }
}
```

## 🔒 불변 객체 특징
- final 클래스: 상속 불가
- final 필드: 생성 후 변경 불가
- setter 없음: 상태 변경 불가
- 모든 메서드는 읽기 전용

## 🧪 사용 예시
```java
UnsignedInt a = new UnsignedInt(-1); // 0xFFFFFFFF
System.out.println(a.toLong());      // 4294967295
System.out.println(a.toString());    // "4294967295"

UnsignedInt b = UnsignedInt.fromString("2147483648");
System.out.println(b.toSignedInt()); // -2147483648

```


