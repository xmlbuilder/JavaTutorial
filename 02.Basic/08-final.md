# final

## 🧠 final 변수와 static final 상수 정리
### 📌 1. final 키워드란?
- "더 이상 변경 불가"를 의미
- 변수, 메서드, 클래스에 사용 가능

### 🧪 2. final 지역 변수
```java
final int data1;
data1 = 10;     // ✅ 최초 1회 할당 가능
data1 = 20;     // ❌ 컴파일 오류

final int data2 = 10;
data2 = 20;     // ❌ 컴파일 오류
```
- 초기화 전: 한 번만 값을 할당 가능
- 초기화 후: 값 변경 불가
### 📌 final 매개변수
```java
static void method(final int parameter) {
    parameter = 20; // ❌ 컴파일 오류
}
```
- 메서드 내부에서 값 변경 불가

### 🧩 3. final 필드 (멤버 변수)
#### ✅ 생성자 초기화
```java
class ConstructInit {
    final int value;
    public ConstructInit(int value) {
        this.value = value;
    }
}
```

- 각 인스턴스마다 다른 값 할당 가능
- 이후 값 변경 불가

#### ✅ 필드에서 직접 초기화
```java
class FieldInit {
    final int value = 10;
}
```

- 모든 인스턴스가 동일한 값 가짐
- 생성자에서 다시 초기화 불가

### 🔒 4. static final 상수
```java
class Constant {
    public static final double PI = 3.14;
    public static final int MAX_USERS = 1000;
}
```

- static: 클래스에 하나만 존재
- final: 값 변경 불가
- 상수 작명 관례: 대문자 + 언더스코어 (MAX_USERS, PI)
- 접근 방식: Constant.MAX_USERS

### ⚠️ 5. 매직 넘버 문제 해결

#### ❌ 상수 없이 직접 숫자 사용
```java
if (currentUserCount > 1000) { ... }
```

- 의미 불명확
- 변경 시 여러 곳 수정 필요
### ✅ 상수 사용
```java
if (currentUserCount > Constant.MAX_USERS) { ... }
```

- 의미 명확 (MAX_USERS)
- 변경 시 한 곳만 수정

### ✅ 핵심 요약
| 키워드/개념       | 설명                                                                 |
|------------------|----------------------------------------------------------------------|
| final 지역 변수   | 최초 1회만 할당 가능, 이후 값 변경 불가                              |
| final 필드        | 생성자 또는 필드에서 초기화, 이후 변경 불가                           |
| static final 상수 | 클래스에 하나만 존재, 변경 불가, 상수로 활용                          |
| 상수 작명 관례     | 모두 대문자 + 언더스코어 (`MAX_USERS`, `PI`)                          |
| 상수의 장점        | 중앙 관리 가능, 매직 넘버 제거, 유지보수 용이                         |


---

## 🧠 final 변수와 참조형 변수 정리
### 📌 1. final의 의미
- final은 값 변경 금지를 의미
- 변수에 한 번 값이 할당되면 다시 할당할 수 없음

### 🧩 2. 기본형 vs 참조형 변수
| 변수 종류     | 저장 내용         | final 적용 시 의미                          |
|--------------|------------------|----------------------------------------------|
| 기본형 변수   | 값 자체 (예: 10)   | 값을 변경할 수 없음                           |
| 참조형 변수   | 객체의 참조값      | 참조값 변경 불가 (다른 객체로 변경 불가), 하지만 객체 내부 값은 변경 가능 |


### 🧪 3. 참조형 변수 예제
```java
final Data data = new Data();   // 참조값 고정
data.value = 10;                // ✅ 객체 내부 값은 변경 가능
data.value = 20;                // ✅ 계속 변경 가능
// data = new Data();           // ❌ 참조값 변경 불가 (컴파일 오류)
```

- data는 final이므로 다른 객체를 참조할 수 없음
- 하지만 data.value는 final이 아니므로 값 변경 가능

### 🔐 4. 실전 예제: 고객 ID 보호
```java
public class Member {
    private final String id;   // 변경 불가
    private String name;       // 변경 가능

    public Member(String id, String name) {
        this.id = id;
        this.name = name;
    }

    public void changeData(String id, String name) {
        // this.id = id;       // ❌ 컴파일 오류
        this.name = name;      // ✅ 변경 가능
    }

    public void print() {
        System.out.println("id:" + id + ", name:" + name);
    }
}
```

#### 🖥️ 실행 결과
```
id:myId, name:kim
id:myId, name:seo
```

- id는 final이라 변경 불가
- name은 변경 가능

### ✅ 핵심 요약
| 키워드/개념       | 설명                                                                 |
|------------------|----------------------------------------------------------------------|
| final 기본형 변수 | 값 자체를 변경할 수 없음                                              |
| final 참조형 변수 | 참조값 변경 불가 (다른 객체로 변경 불가), 객체 내부 값은 변경 가능       |
| 중요한 정보 보호   | `id`, `주민번호`, `고유키` 등은 `final`로 선언해 실수 방지 가능           |
| 컴파일러 역할      | `final` 위반 시 컴파일 오류로 실수 방지 가능                            |

---


