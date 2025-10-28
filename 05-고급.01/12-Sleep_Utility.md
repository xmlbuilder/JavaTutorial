# 🛠️ Sleep 유틸리티 정리
## 🔹 문제점
자바에서 Thread.sleep()은 **체크 예외인 InterruptedException** 을 발생시킵니다.  
따라서 Runnable.run() 메서드 안에서 직접 사용할 경우 반드시 try-catch로 예외를 처리해야 하며,   
이는 반복적으로 작성하기 번거롭습니다.

### 기존 방식 예시
```java
void run() {
    try {
        Thread.sleep(3000);
    } catch (InterruptedException e) {
        throw new RuntimeException(e);
    }
}
```


## 🔹 해결 방법: ThreadUtils.sleep() 유틸리티
체크 예외를 런타임 예외로 감싸서 처리하는 유틸리티 메서드를 만들어 간편하게 사용할 수 있습니다.

### 유틸리티 클래스 정의
```java
package util;
import static util.MyLogger.log;

public abstract class ThreadUtils {
    public static void sleep(long millis) {
        try {
            Thread.sleep(millis);
        } catch (InterruptedException e) {
            log("인터럽트 발생, " + e.getMessage());
            throw new RuntimeException(e);
        }
    }
}
```

- InterruptedException을 RuntimeException으로 변환
- 로그 출력으로 디버깅 가능
- static 메서드로 어디서든 간편하게 호출 가능

## 🔹 유틸리티 사용 예시
```java
import static util.ThreadUtils.sleep;

void run() {
    sleep(3000); // try-catch 없이 간결하게 사용
}
```


## ✅ 핵심 요약: Sleep 유틸리티 사용 이유

| 항목                     | 설명                                                             |
|--------------------------|------------------------------------------------------------------|
| `Thread.sleep()`         | 일정 시간 동안 스레드를 일시 정지시키는 메서드                   |
| 체크 예외 → 런타임 예외  | `InterruptedException` → `RuntimeException`으로 변환하여 처리      |
| 기존 처리 방식           | `try-catch`로 직접 예외 처리 필요                                 |
| 유틸리티 사용 방식       | `ThreadUtils.sleep(ms)`로 간결하게 호출 가능                      |


---

