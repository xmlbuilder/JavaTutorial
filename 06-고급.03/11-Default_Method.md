# 디폴트 메소드

📘 디폴트 메서드 문서를 기반으로 핵심 내용을 구조적으로 정리.  
자바 8에서 도입된 디폴트 메서드의 등장 배경부터 실전 예제, 주의사항까지 모두 포함했습니다.

## 🧠 자바 디폴트 메서드 핵심 요약  
### ✅ 1. 등장 배경

| 문제 상황                          | 설명                                                                 |
|-----------------------------------|----------------------------------------------------------------------|
| 인터페이스 확장 불가              | 기존 인터페이스에 메서드 추가 시 모든 구현 클래스에서 컴파일 오류 발생 |
| 하위 호환성 문제                  | 이미 배포된 코드나 외부 라이브러리까지 수정해야 하는 부담             |
| 자바 표준 라이브러리 확장 어려움 | Collection, List 등 확장 시 전 세계적으로 오류 발생 가능             |
| 해결책: default 메서드 도입       | 기본 구현을 제공하여 기존 구현체가 깨지지 않도록 함                  |


### 🧪 2. 실전 예제 흐름

#### 📍 예제 1: 기존 인터페이스 사용
```java
public interface Notifier {
    void notify(String message);
}
```
- 구현 클래스: EmailNotifier, SMSNotifier, AppPushNotifier
- 정상 동작: notify()만 구현하면 됨
#### 📍 예제 2: 새로운 메서드 추가 → 컴파일 오류 발생
```java
void scheduleNotification(String message, LocalDateTime time);
```
- 기존 구현체들이 이 메서드를 구현하지 않아 컴파일 오류 발생
#### 📍 예제 3: 디폴트 메서드로 해결
```java
default void scheduleNotification(String message, LocalDateTime time) {
    System.out.println("[기본스케줄링] message: " + message + ", time: " + time);
}
```
- EmailNotifier: 재정의하여 커스터마이징 가능
- SMSNotifier, AppPushNotifier: 기본 구현 사용

### 📌 3. 디폴트 메서드의 장점
- 하위 호환성 유지
- 기존 코드 수정 없이 기능 확장 가능
- 자바 표준 라이브러리 확장 가능 (예: List.sort(), Collection.stream())

### ⚠️ 4. 올바른 사용법과 주의사항

| 주의 항목                     | 설명                                                                 |
|------------------------------|----------------------------------------------------------------------|
| 최소한의 사용                | 하위 호환이 필요한 경우에만 제한적으로 사용. 남용 시 코드 복잡도 증가 |
| 추상화 역할 유지             | 인터페이스는 계약(Contract)에 집중. 복잡한 로직은 구현 클래스에 배치 |
| 다중 상속 충돌 주의          | 동일 시그니처의 디폴트 메서드가 여러 인터페이스에 존재 시 충돌 발생 → 재정의 필수 |
| 상태(state) 보관 금지        | 디폴트 메서드는 상태 없이 동작만 정의해야 함. 인스턴스 변수 사용 지양 |
| 명확한 설계 기준 필요        | 공통 로직 제공은 가능하나, 설계 목적과 책임을 흐리지 않도록 주의 필요 |


### 🧪 다중 상속 충돌 예시
```java
interface A {
    default void hello() { System.out.println("Hello from A"); }
}

interface B {
    default void hello() { System.out.println("Hello from B"); }
}

class MyClass implements A, B {
    @Override
    public void hello() {
        A.super.hello(); // 또는 B.super.hello()
    }
}
```
## 🧾 디폴트 메서드 정리

| 항목               | 설명                                                                 |
|--------------------|----------------------------------------------------------------------|
| 도입 버전          | 자바 8                                                               |
| 키워드             | `default`                                                            |
| 정의 위치          | 인터페이스 내부에서 메서드 구현부를 포함하여 정의 가능               |
| 주요 목적          | 하위 호환성 확보, 기존 구현체 수정 없이 기능 확장 가능               |
| 사용 시 주의사항   | 최소한의 활용, 추상화 유지, 다중 상속 충돌 해결, 상태 보관 금지       |


---

# 실전 예제

자바의 디폴트 메서드를 활용한 실전 샘플을 아래에 구성.  
이 예제는 인터페이스 확장, 기본 구현 제공, 그리고 구현 클래스의 선택적 재정의를 보여줍니다.

## 🧪 실전 샘플: 알림 시스템 확장
### 🎯 시나리오
기존 알림 시스템에 Notifier 인터페이스가 있고, notify() 메서드만 정의되어 있습니다.
새로운 요구사항으로 예약 알림 기능이 추가되었고, 이를 디폴트 메서드로 구현하여 기존 클래스의 호환성을 유지합니다.

### 📁 인터페이스 정의
```java
import java.time.LocalDateTime;

public interface Notifier {
    void notify(String message);

    default void scheduleNotification(String message, LocalDateTime time) {
        System.out.println("[기본 스케줄링] message: " + message + ", time: " + time);
    }
}
```

### 📁 구현 클래스 1 – EmailNotifier (기능 확장)
```java
public class EmailNotifier implements Notifier {
    @Override
    public void notify(String message) {
        System.out.println("[EMAIL] " + message);
    }

    @Override
    public void scheduleNotification(String message, LocalDateTime time) {
        System.out.println("[EMAIL 예약] message: " + message + ", time: " + time);
    }
}
```


### 📁 구현 클래스 2 – SMSNotifier (기본 구현 사용)
```java
public class SMSNotifier implements Notifier {
    @Override
    public void notify(String message) {
        System.out.println("[SMS] " + message);
    }
    // scheduleNotification() 생략 → 기본 구현 사용
}
```


### 📁 구현 클래스 3 – AppPushNotifier (기본 구현 사용)
```java
public class AppPushNotifier implements Notifier {
    @Override
    public void notify(String message) {
        System.out.println("[APP] " + message);
    }
}
```


### 📁 실행 클래스 – NotifierMain
```java
import java.time.LocalDateTime;
import java.util.List;

public class NotifierMain {
    public static void main(String[] args) {
        List<Notifier> notifiers = List.of(
            new EmailNotifier(),
            new SMSNotifier(),
            new AppPushNotifier()
        );

        notifiers.forEach(n -> n.notify("서비스 가입을 환영합니다!"));

        LocalDateTime tomorrow = LocalDateTime.now().plusDays(1);
        notifiers.forEach(n -> n.scheduleNotification("예약 알림입니다", tomorrow));
    }
}
```

### 🖨️ 실행 결과
```
[EMAIL] 서비스 가입을 환영합니다!
[SMS] 서비스 가입을 환영합니다!
[APP] 서비스 가입을 환영합니다!

[EMAIL 예약] message: 예약 알림입니다, time: 2025-11-12T14:56
[기본 스케줄링] message: 예약 알림입니다, time: 2025-11-12T14:56
[기본 스케줄링] message: 예약 알림입니다, time: 2025-11-12T14:56
```


## ✅ 디폴트 메서드 핵심 포인트

| 항목               | 설명                                                                 |
|--------------------|----------------------------------------------------------------------|
| `default` 키워드    | 인터페이스에서 메서드 구현부를 정의할 때 사용                        |
| 하위 호환성 유지    | 기존 구현체를 수정하지 않고도 인터페이스에 새 기능 추가 가능          |
| 선택적 재정의       | 구현 클래스에서 필요 시 재정의 가능, 그렇지 않으면 기본 구현 사용      |
| 설계 유연성 향상    | 공통 로직을 인터페이스에 제공함으로써 코드 중복 감소 및 유지보수 용이 |


---
