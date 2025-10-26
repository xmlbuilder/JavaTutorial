# instanceof / Class.isAssignableFrom

instanceof와 Class.isAssignableFrom()은 Java에서 타입 검사를 할 때 자주 사용되는 도구.  
둘 다 상속/구현 관계를 확인하지만 적용 대상과 관점이 다릅니다.  
아래에 자세히 설명하고, 차이점도 표로 정리.

## 🔍 instanceof — 객체 기반 타입 검사
### ✅ 개념
- instanceof는 객체가 특정 클래스나 인터페이스의 인스턴스인지 확인합니다.
- 런타임 시점에 객체의 실제 타입을 기준으로 검사합니다.

### ✅ 예시
```java
MacPro obj = new MacPro();
if (obj instanceof Computer) {
    System.out.println("MacPro는 Computer 타입입니다.");
}
```
- obj는 MacPro 타입의 인스턴스
- MacPro가 Computer를 상속하거나 구현했다면 true

## 🧠 Class.isAssignableFrom() — 클래스 기반 타입 검사
### ✅ 개념
- Class.isAssignableFrom()은 클래스 간의 상속/구현 관계를 확인합니다.
- 왼쪽 클래스가 오른쪽 클래스의 상위 타입인지 판단합니다.
### ✅ 예시
```java
if (Computer.class.isAssignableFrom(MacPro.class)) {
    System.out.println("MacPro는 Computer로 업캐스팅 가능");
}
```
- MacPro.class는 Computer.class로 업캐스팅 가능 → true
- 객체 없이도 클래스 간 관계를 확인할 수 있음

## 📊 차이점 요약
| 항목                     | `instanceof`                                | `Class.isAssignableFrom()`                        |
|--------------------------|----------------------------------------------|--------------------------------------------------|
| 검사 대상                | 객체 (`Object`)                              | 클래스 (`Class`)                                 |
| 검사 시점                | 런타임                                       | 런타임                                            |
| 용도                     | 객체가 특정 타입인지 확인                    | 클래스 간 상속/구현 관계 확인                    |
| null 검사                | `null instanceof` → 항상 false               | `Class.isAssignableFrom(null)` → NPE 발생 가능   |
| 예시                     | `obj instanceof InterfaceOrClass`            | `A.class.isAssignableFrom(B.class)`              |
| 활용                     | 조건 분기, 타입 캐스팅 전 확인               | 리플렉션 기반 타입 검사, 동적 로딩 시 유용       |

## ✅ 실전 활용 팁
- instanceof는 객체가 존재할 때 사용
- isAssignableFrom()은 클래스만 있을 때 또는 리플렉션 기반 처리에 적합
- 둘 다 상속/구현 관계를 확인하지만, 관점이 다르다는 점이 핵심

---

