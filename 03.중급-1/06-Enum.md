## 📘 Enum Type

- 아래는 자바의 열거형(Enum Type)이 왜 등장했는지, 어떤 문제를 해결하기 위해 만들어졌는지를 예제 흐름에 따라 설명한 문서입니다.  
- 타입 안정성과 값 제한의 필요성을 중심으로, Enum의 탄생 배경과 의미를 명확하게 전달하도록 구성했습니다:

### 📌 자바 Enum 타입의 탄생 배경과 필요성

#### 🔹 비즈니스 요구사항
- 고객은 3개의 등급으로 나뉘며, 상품 구매 시 등급에 따라 할인율이 적용됩니다.  
- 할인 금액은 소수점 이하를 버린 정수로 계산됩니다:

| 등급     | 할인율 | 예시 구매 금액 | 할인 금액 |
|----------|--------|----------------|------------|
| BASIC    | 10%    | 10,000원        | 1,000원     |
| GOLD     | 20%    | 10,000원        | 2,000원     |
| DIAMOND  | 30%    | 10,000원        | 3,000원     |


- 예: GOLD 고객이 10,000원을 구매하면 할인 금액은 2,000원입니다.

#### 🔹 구현 코드
```java
public class DiscountService {
    public int discount(String grade, int price) {
        int discountPercent = 0;
        if (grade.equals("BASIC")) {
            discountPercent = 10;
        } else if (grade.equals("GOLD")) {
            discountPercent = 20;
        } else if (grade.equals("DIAMOND")) {
            discountPercent = 30;
        } else {
            System.out.println(grade + ": 할인X");
        }
        return price * discountPercent / 100;
    }
}
```

### 📌 문제 1: 문자열로 등급을 표현한 방식의 한계
#### 🔹 구현 예시
```java
discount("GOLD", 10000); // → 2000
```

#### 🔹 발생 가능한 문제
- "VIP" : 존재하지 않는 등급
- "DIAMONDD" : 오타
- "gold" : 대소문자 불일치

#### ❌ 문자열 사용의 단점
- 타입 안정성 부족: 오타나 잘못된 값이 컴파일 시점에 감지되지 않음
- 데이터 일관성 저하: "GOLD", "gold", "Gold" 등 다양한 표현 가능
- 값 제한 불가능: 어떤 문자열이든 전달 가능
- 런타임 오류 발생 가능성: 잘못된 값은 실행 중에야 확인됨

### 📌 대안 1: 문자열 상수 사용
#### 🔹 개선된 방식
```java
public class StringGrade {
    public static final String BASIC = "BASIC";
    public static final String GOLD = "GOLD";
    public static final String DIAMOND = "DIAMOND";
}
```

#### 🔹 장점
- 오타 방지: IDE 자동완성 가능
- 코드 가독성 향상
#### 🔹 여전히 남는 문제
- 여전히 discount("VIP", 10000) 같은 잘못된 값은 전달 가능
- 컴파일러가 유효한 값인지 확인할 수 없음
- 상수 정의 위치를 개발자가 직접 알아야 함

### 📌 해결책: 타입 안전 열거형 패턴 (Type-Safe Enum Pattern)
#### 🔹 핵심 아이디어
- 정해진 값만 사용할 수 있도록 제한
- 컴파일 시점에 오류를 감지할 수 있음
- 객체로서의 기능도 함께 제공
#### 🔹 직접 구현한 타입 안전 열거형
```java
public class ClassGrade {
    public static final ClassGrade BASIC = new ClassGrade();
    public static final ClassGrade GOLD = new ClassGrade();
    public static final ClassGrade DIAMOND = new ClassGrade();

    private ClassGrade() {} // 외부 생성 방지
}
```

### 📌 장점
- ClassGrade 타입으로만 값 전달 가능
- BASIC, GOLD, DIAMOND 외의 값은 생성 불가
- == 비교로 참조값 일치 확인 가능

#### 🔹 예제 코드
```java
public class DiscountService {
    public int discount(ClassGrade classGrade, int price) {
        int discountPercent = 0;
        if (classGrade == ClassGrade.BASIC) {
            discountPercent = 10;
        } else if (classGrade == ClassGrade.GOLD) {
            discountPercent = 20;
        } else if (classGrade == ClassGrade.DIAMOND) {
            discountPercent = 30;
        } else {
            System.out.println("할인X");
        }
        return price * discountPercent / 100;
    }
}
```

### 📌 예제 흐름 요약
#### 🔹 1. 클래스 기반 열거형 정의
```java
ClassGrade.BASIC, ClassGrade.GOLD, ClassGrade.DIAMOND
```

#### 🔹 2. 할인 서비스 구현
```java
public int discount(ClassGrade grade, int price) {
    if (grade == ClassGrade.GOLD) return price * 20 / 100;
    ...
}
```

#### 🔹 3. 외부 생성 방지
```java
private ClassGrade() {} // 생성자 은닉
```

#### 🔹 4. 잘못된 값 전달 시 컴파일 오류
```java
new ClassGrade(); // ❌ 컴파일 오류
```

---

### 📌 자바 Enum 타입의 탄생 배경과 필요성

#### 🔹 Enum의 등장
- 자바 5부터 enum 키워드가 도입되면서, 위의 타입 안전 열거형 패턴을 더 간결하고 강력하게 사용할 수 있게 되었습니다.

#### 🔹 Enum의 특징
- 컴파일 시점에 값 제한 가능
- switch 문에서 사용 가능
- `메서드`, `필드`, `생성자` 도 정의 가능
- name(), ordinal() 등 유틸리티 제공

#### 🔹 Enum 예시
```java
public enum Grade {
    BASIC, GOLD, DIAMOND
}
```

#### 🔹 내부적으로는 다음과 유사
```java
public class Grade extends Enum {
    public static final Grade BASIC = new Grade();
    ...
    private Grade() {}
}
```
- Enum 클래스를 자동 상속
- 외부에서 인스턴스 생성 불가

#### 🔹 Enum 클래스 확인 예제
```java
System.out.println("class BASIC = " + Grade.BASIC.getClass());
System.out.println("ref BASIC = " + Integer.toHexString(System.identityHashCode(Grade.BASIC)));
```
- Grade.BASIC는 Grade 타입의 인스턴스
- 각 상수는 서로 다른 참조값을 가짐

### 📌 결론
| 방식            | 타입 안정성 | 값 제한 | 컴파일 오류 감지 | 유지보수 용이성 | 추천도 |
|-----------------|--------------|----------|-------------------|------------------|--------|
| 문자열          | ❌           | ❌       | ❌                | 낮음             | 👎     |
| 문자열 상수     | ⚠️           | ❌       | ❌                | 중간             | 🤔     |
| 클래스 기반 열거 | ✅           | ✅       | ✅                | 좋음             | 👍     |
| Enum            | ✅           | ✅       | ✅                | 매우 좋음        | ✅     |

- Enum은 값의 범위를 명확히 제한하고, 타입 안정성을 보장하기 위해 등장한 언어적 해결책입니다.



### 📌 할인 서비스 예제
```java
public int discount(Grade grade, int price) {
    return switch (grade) {
        case BASIC -> price * 10 / 100;
        case GOLD -> price * 20 / 100;
        case DIAMOND -> price * 30 / 100;
    };
}

public class EnumEx3_1 {
    public static void main(String[] args) {
        int price = 10000;
        DiscountService discountService = new DiscountService();
        int basic = discountService.discount(Grade.BASIC, price);
        int gold = discountService.discount(Grade.GOLD, price);
        int diamond = discountService.discount(Grade.DIAMOND, price);
        System.out.println("BASIC 등급의 할인 금액: " + basic);
        System.out.println("GOLD 등급의 할인 금액: " + gold);
        System.out.println("DIAMOND 등급의 할인 금액: " + diamond);
    }
}
```

#### 🔹 실행 결과
```
BASIC 등급의 할인 금액: 1000
GOLD 등급의 할인 금액: 2000
DIAMOND 등급의 할인 금액: 3000
```

#### 🔹 Enum 인스턴스 생성 불가
```java
Grade myGrade = new Grade(); // ❌ 컴파일 오류
```

```
오류 메시지: enum classes may not be instantiated
```

### 📌 Enum의 장점 요약

| 항목             | 설명                                                                 |
|------------------|----------------------------------------------------------------------|
| 타입 안정성 향상 | 사전에 정의된 값만 사용 가능. 잘못된 값은 컴파일 시점에 오류로 감지됨         |
| 데이터 일관성    | enum 상수만 사용되므로 오타나 형식 불일치 없이 일관된 값 유지 가능              |
| 코드 간결성      | enum을 사용하면 코드가 더 명확하고 읽기 쉬움                              |
| 확장성           | 새로운 값 추가 시 enum에 상수만 추가하면 되므로 유지보수 용이                  |
| switch 문 지원   | enum은 switch 문에서 직접 사용할 수 있어 조건 분기 처리에 유리함               |
| 외부 생성 불가   | enum은 자동으로 생성자 은닉 처리되어 외부에서 인스턴스 생성 불가능 (보안 및 안정성) |
- static import를 활용하면 Grade.BASIC 대신 BASIC처럼 더 간결한 코드 작성 가능

---

### 📌 자바 열거형(Enum Type) - 주요 메서드와 구조 정리
#### 🔹 기본 개념
- 자바의 모든 열거형은 자동으로 java.lang.Enum 클래스를 상속받습니다.  
- 이로 인해 열거형은 다양한 유틸리티 메서드를 사용할 수 있으며, 클래스처럼 동작하면서도 값의 범위를 제한할 수 있습니다.

#### 🔹 주요 메서드
| 메서드             | 설명                                                                 |
|--------------------|----------------------------------------------------------------------|
| values()           | 모든 열거형 상수를 배열로 반환합니다.                                 |
| valueOf(String)    | 문자열과 일치하는 열거형 상수를 반환합니다. 일치하지 않으면 예외 발생 |
| name()             | 열거형 상수의 이름을 문자열로 반환합니다.                            |
| ordinal()          | 열거형 상수의 선언 순서를 반환합니다. (0부터 시작)                   |
| toString()         | 열거형 상수의 이름을 문자열로 반환합니다. `name()`과 유사하지만 오버라이드 가능 |


#### 🔹 예제 코드
```java
Grade[] values = Grade.values();
System.out.println("values = " + Arrays.toString(values));

for (Grade value : values) {
    System.out.println("name=" + value.name() + ", ordinal=" + value.ordinal());
}

String input = "GOLD";
Grade gold = Grade.valueOf(input);
System.out.println("gold = " + gold);
```

#### 🔹 실행 결과
```
values = [BASIC, GOLD, DIAMOND]
name=BASIC, ordinal=0
name=GOLD, ordinal=1
name=DIAMOND, ordinal=2
gold = GOLD
```


#### 🔹 ordinal() 사용 주의
- ordinal()은 열거형 상수의 선언 순서를 반환하지만, 데이터 저장이나 비교에 사용하는 것은 위험합니다.
#### 🔹 예시 문제 상황
- 기존: GOLD.ordinal() = 1
- 새로운 등급 추가: SILVER → GOLD.ordinal() = 2
- 결과: 기존에 저장된 1은 SILVER로 해석될 수 있음 → 데이터 오류 발생
- 따라서 ordinal()은 로직이나 저장용으로 사용하지 말고, 단순 출력이나 디버깅 용도로만 사용하는 것이 좋습니다.


### 📌 열거형의 구조적 특징

| 특징 항목             | 설명                                                                 |
|------------------------|----------------------------------------------------------------------|
| 자동 상속              | 모든 열거형은 `java.lang.Enum` 클래스를 자동으로 상속받음              |
| 클래스 상속 불가       | 이미 `Enum`을 상속받았기 때문에 다른 클래스를 추가로 상속할 수 없음     |
| 인터페이스 구현 가능   | 열거형은 인터페이스를 구현할 수 있음                                  |
| 추상 메서드 구현 가능  | 열거형 상수별로 서로 다른 동작을 정의할 수 있음 (익명 클래스 방식 사용) |
| 외부 인스턴스 생성 불가| 열거형은 외부에서 직접 인스턴스를 생성할 수 없음 (컴파일 오류 발생)     |


### 📌 요약
- 열거형은 타입 안정성과 값 제한을 동시에 제공하는 강력한 구조
- values(), valueOf(), name() 등 유용한 메서드 제공
- ordinal()은 사용에 주의가 필요함
- 열거형은 클래스처럼 동작하지만, 외부에서 인스턴스 생성이 불가능하고 상속 구조가 제한됨
- 열거형은 코드의 안정성과 가독성을 높이는 데 매우 유용한 도구입니다.  
- 특히 상태, 등급, 타입 등을 표현할 때 적극 활용.

---

### 📌 열거형 리팩토링 흐름 정리
#### 🔹 리팩토링 목적
- 중복된 if 조건 제거
- 할인율 로직을 등급 클래스 내부로 이동
- 캡슐화 원칙 적용
- 열거형(Enum)을 활용한 구조 개선

### 📌 리팩토링 1: 클래스 기반 열거형 패턴 개선
#### 🔹 기존 문제
```java
if (classGrade == ClassGrade.BASIC) {
    discountPercent = 10;
} else if (...) { ... }
```

- 등급별 할인율을 if 문으로 분기
- 할인율이 등급에 따라 결정되는데, 로직이 분산되어 있음

#### 🔹 개선 방법
- ClassGrade 클래스에 discountPercent 필드 추가
- 생성자에서 할인율 설정 → 불변 객체로 설계
- DiscountService는 단순 계산만 수행
```java
public class ClassGrade {
    public static final ClassGrade BASIC = new ClassGrade(10);
    ...
    private final int discountPercent;
    private ClassGrade(int discountPercent) { ... }
    public int getDiscountPercent() { ... }
}

public int discount(ClassGrade classGrade, int price) {
    return price * classGrade.getDiscountPercent() / 100;
}
```


### 📌 리팩토링 2: 열거형(Enum)으로 구조 개선
#### 🔹 열거형 적용
- Grade enum에 discountPercent 필드 추가
- 생성자에서 할인율 설정
- getDiscountPercent() 메서드로 조회

```java
public enum Grade {
    BASIC(10), GOLD(20), DIAMOND(30);
    private final int discountPercent;
    Grade(int discountPercent) { ... }
    public int getDiscountPercent() { ... }
}

public class DiscountService {
    public int discount(Grade grade, int price) {
        return price * grade.getDiscountPercent() / 100;
    }
}

public class EnumRefMain2 {
    public static void main(String[] args) {
        int price = 10000;
        DiscountService discountService = new DiscountService();
        int basic = discountService.discount(Grade.BASIC, price);
        int gold = discountService.discount(Grade.GOLD, price);
        int diamond = discountService.discount(Grade.DIAMOND, price);
        System.out.println("BASIC 등급의 할인 금액: " + basic);
        System.out.println("GOLD 등급의 할인 금액: " + gold);
        System.out.println("DIAMOND 등급의 할인 금액: " + diamond);
    }
}

```
#### 🔹 결과
- if 문 제거
- 할인율은 Grade 내부에서 관리
- 코드 간결성 및 타입 안정성 향상

### 📌 리팩토링 3: 할인 계산 책임을 열거형으로 이동
#### 🔹 캡슐화 적용
- Grade 내부에 discount(price) 메서드 추가
- 할인율 계산 책임을 Grade가 직접 수행
```java
public enum Grade {
    BASIC(10), GOLD(20), DIAMOND(30);
    private final int discountPercent;
    Grade(int discountPercent) {
        this.discountPercent = discountPercent;
    }
    public int getDiscountPercent() {
        return discountPercent;
    }
}

public int discount(int price) {
    return price * discountPercent / 100;
}
```


### 📌 결과
- DiscountService는 단순 위임만 수행
- 할인 로직이 등급 객체 내부로 이동 → 객체지향적 설계

### 📌 추가 개선: 서비스 제거 및 출력 리팩토링
#### 🔹 서비스 제거
- Grade.discount(price) 직접 호출 가능
- DiscountService 클래스 제거 가능
```java
System.out.println("BASIC 등급의 할인 금액: " + Grade.BASIC.discount(price));
```

#### 🔹 출력 중복 제거
```java
private static void printDiscount(Grade grade, int price) {
    System.out.println(grade.name() + " 등급의 할인 금액: " + grade.discount(price));
}
```


#### 🔹 ENUM 목록 활용
- Grade.values()로 모든 등급 순회 가능
- 새로운 등급 추가 시 main() 코드 수정 불필요
```java
for (Grade grade : Grade.values()) {
    printDiscount(grade, price);
}
```

### 📌 최종 구조의 장점
| 항목             | 설명                                                                 |
|------------------|----------------------------------------------------------------------|
| Grade 중심 설계  | 할인율 로직을 `Grade` 내부로 이동하여 객체가 자신의 책임을 수행함         |
| if 제거          | 조건문 없이 할인율을 직접 조회하거나 계산함 → 코드 간결성 향상             |
| 캡슐화 적용      | 할인율 계산을 외부가 아닌 `Grade` 내부에서 처리 → 정보 은닉 실현           |
| enum 활용        | 타입 안정성과 값 제한을 동시에 제공 → 잘못된 값은 컴파일 시점에 차단 가능 |
| 확장성           | 새로운 등급 추가 시 기존 로직 수정 없이 자동 반영 가능                    |

- 열거형은 단순한 상수 집합을 넘어서, 행위와 데이터를 함께 관리하는 객체로 활용할 수 있습니다.
- 이번 리팩토링은 열거형을 통해 객체지향 설계 원칙을 실현한 대표적인 예시입니다.

---

### 📌 문제와 풀이 1: 인증 등급 열거형
#### 🔹 문제 1: 인증 등급 만들기
#### 🔹 목표
- AuthGrade 열거형 생성
- 각 등급에 level과 description 속성 부여
- getLevel(), getDescription() 메서드로 조회 가능
#### 🔹 코드 구조
```java
public enum AuthGrade {
    GUEST(1, "손님"),
    LOGIN(2, "로그인 회원"),
    ADMIN(3, "관리자");

    private final int level;
    private final String description;

    AuthGrade(int level, String description) {
        this.level = level;
        this.description = description;
    }

    public int getLevel() { return level; }
    public String getDescription() { return description; }
}
```


### 📌 문제 2: 인증 등급 열거형 조회하기
#### 🔹 목표
- 모든 AuthGrade 상수를 순회하며 정보 출력
#### 🔹 코드 구조
```java
public class AuthGradeMain1 {
    public static void main(String[] args) {
        for (AuthGrade grade : AuthGrade.values()) {
            System.out.println("grade=" + grade.name() +
                ", level=" + grade.getLevel() +
                ", 설명=" + grade.getDescription());
        }
    }
}
```

#### 🔹 실행 결과
```java
grade=GUEST, level=1, 설명=손님  
grade=LOGIN, level=2, 설명=로그인 회원  
grade=ADMIN, level=3, 설명=관리자
```


### 📌 문제 3: 인증 등급 열거형 활용하기
#### 🔹 목표
- 사용자 입력을 통해 AuthGrade 변환
- 등급별로 접근 가능한 메뉴 출력
#### 🔹 코드 구조
```java
Scanner scanner = new Scanner(System.in);
System.out.print("당신의 등급을 입력하세요[GUEST, LOGIN, ADMIN]: ");
String input = scanner.nextLine();
AuthGrade grade = AuthGrade.valueOf(input.toUpperCase());

System.out.println("당신의 등급은 " + grade.getDescription() + "입니다.");
System.out.println("==메뉴 목록==");
if (grade.getLevel() > 0) System.out.println("- 메인 화면");
if (grade.getLevel() > 1) System.out.println("- 이메일 관리 화면");
if (grade.getLevel() > 2) System.out.println("- 관리자 화면");
```

#### 🔹 실행 예시
```
- 입력: LOGIN
출력:
당신의 등급은 로그인 회원입니다.
==메뉴 목록==
- 메인 화면
- 이메일 관리 화면
```

#### 🔹 예외 처리 참고
- Enum.valueOf()는 잘못된 값 입력 시 IllegalArgumentException 발생
- 예외 처리로 복구 가능 (추후 학습)

### 📌 문제와 풀이 2: HTTP 상태 코드 열거형
#### 🔹 문제: HttpStatus 열거형 만들기
#### 🔹 목표
- HTTP 상태 코드를 열거형으로 정의
- 각 상태에 code, message 속성 부여
- findByCode()로 코드 기반 조회
- isSuccess()로 성공 여부 판단
#### 🔹 코드 구조
```java
public enum HttpStatus {
    OK(200, "OK"),
    BAD_REQUEST(400, "Bad Request"),
    NOT_FOUND(404, "Not Found"),
    INTERNAL_SERVER_ERROR(500, "Internal Server Error");

    private final int code;
    private final String message;

    HttpStatus(int code, String message) {
        this.code = code;
        this.message = message;
    }

    public int getCode() { return code; }
    public String getMessage() { return message; }

    public boolean isSuccess() {
        return code >= 200 && code <= 299;
    }

    public static HttpStatus findByCode(int code) {
        for (HttpStatus status : values()) {
            if (status.getCode() == code) return status;
        }
        return null;
    }
}
```


#### 🔹 활용 예제: HttpStatusMain
#### 🔹 코드 구조
```java
Scanner scanner = new Scanner(System.in);
System.out.print("HTTP CODE: ");
int input = scanner.nextInt();

HttpStatus status = HttpStatus.findByCode(input);
if (status == null) {
    System.out.println("정의되지 않은 코드");
} else {
    System.out.println(status.getCode() + " " + status.getMessage());
    System.out.println("isSuccess = " + status.isSuccess());
}
```

#### 🔹 실행 예시
```
- 입력: 200
출력:
200 OK
isSuccess = true
- 입력: 404
출력:
404 Not Found
isSuccess = false
```

### 📌 열거형 설계의 장점 요약

| 항목               | 설명                                                                 |
|--------------------|----------------------------------------------------------------------|
| 타입 안정성        | 사전에 정의된 값만 사용 가능. 잘못된 값은 컴파일 시점에 오류로 감지됨         |
| 데이터 일관성      | 열거형 상수만 사용되므로 오타나 형식 불일치 없이 일관된 값 유지 가능              |
| 캡슐화             | 열거형 내부에서 데이터와 로직을 함께 관리. 외부에 불필요한 정보 노출 방지         |
| 확장성             | 새로운 값 추가 시 기존 로직 수정 없이 자동 반영 가능                            |
| 코드 간결성        | 조건문 없이 속성 조회 및 로직 처리 가능. 가독성과 유지보수성 향상                 |
| Enum.valueOf()     | 문자열을 열거형으로 안전하게 변환 가능. 잘못된 값은 예외로 처리 가능              |

---

